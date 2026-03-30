<picture>
  <source media="(prefers-color-scheme: dark)" srcset="../resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="../resources/logos/claude-howto-logo.svg">
</picture>

# Hooks

Hooks — это автоматические скрипты, которые запускаются в ответ на конкретные события во время сессий Claude Code. Они обеспечивают автоматизацию, валидацию, управление разрешениями и пользовательские workflow.

<a id="overview"></a>
## Обзор

Hooks — это автоматические действия (shell-команды, HTTP webhooks, LLM prompts или оценки subagent), которые запускаются автоматически при наступлении определённых событий в Claude Code. Они получают JSON-ввод и передают результаты через exit code и JSON-вывод.

**Ключевые возможности:**
- Автоматизация, основанная на событиях
- JSON-ввод и JSON-вывод
- Поддержка типов hooks: command, prompt, HTTP и agent
- Сопоставление по шаблонам для hooks, привязанных к конкретным инструментам

## Конфигурация

Hooks настраиваются в файлах settings с определённой структурой:

- `~/.claude/settings.json` - пользовательские настройки (все проекты)
- `.claude/settings.json` - настройки проекта (можно шарить, коммитятся)
- `.claude/settings.local.json` - локальные настройки проекта (не коммитятся)
- Managed policy - настройки на уровне организации
- Plugin `hooks/hooks.json` - hooks уровня плагина
- Skill/Agent frontmatter - hooks на время жизни компонента

### Базовая структура конфигурации

```json
{
  "hooks": {
    "EventName": [
      {
        "matcher": "ToolPattern",
        "hooks": [
          {
            "type": "command",
            "command": "your-command-here",
            "timeout": 60
          }
        ]
      }
    ]
  }
}
```

**Ключевые поля:**

| Поле | Описание | Пример |
|-------|-------------|---------|
| `matcher` | Шаблон для сопоставления имён инструментов (с учётом регистра) | `"Write"`, `"Edit\|Write"`, `"*"` |
| `hooks` | Массив определений hook | `[{ "type": "command", ... }]` |
| `type` | Тип hook: `"command"` (bash), `"prompt"` (LLM), `"http"` (webhook) или `"agent"` (subagent) | `"command"` |
| `command` | Shell-команда для выполнения | `"$CLAUDE_PROJECT_DIR/.claude/hooks/format.sh"` |
| `timeout` | Необязательный таймаут в секундах (по умолчанию 60) | `30` |
| `once` | Если `true`, запускать hook только один раз за сессию | `true` |

### Шаблоны matcher

| Шаблон | Описание | Пример |
|---------|-------------|---------|
| Точное совпадение | Совпадает с конкретным инструментом | `"Write"` |
| Regex-шаблон | Совпадает с несколькими инструментами | `"Edit\|Write"` |
| Wildcard | Совпадает со всеми инструментами | `"*"` or `""` |
| MCP tools | Шаблон сервера и инструмента | `"mcp__memory__.*"` |

## Типы hooks

Claude Code поддерживает четыре типа hooks:

### Command hooks

Тип hook по умолчанию. Выполняет shell-команду и обменивается данными через JSON stdin/stdout и коды выхода.

```json
{
  "type": "command",
  "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/validate.py\"",
  "timeout": 60
}
```

### HTTP hooks

> Added in v2.1.63.

Удалённые webhook-endpoint'ы, которые получают тот же JSON-ввод, что и command hooks. HTTP hooks отправляют JSON через POST на URL и получают JSON-ответ. При включённом sandboxing HTTP hooks проходят через sandbox. Для подстановки переменных окружения в URL требуется явный список `allowedEnvVars` в целях безопасности.

```json
{
  "hooks": {
    "PostToolUse": [{
      "type": "http",
      "url": "https://my-webhook.example.com/hook",
      "matcher": "Write"
    }]
  }
}
```

**Ключевые свойства:**
- `"type": "http"` -- указывает, что это HTTP hook
- `"url"` -- URL webhook endpoint'а
- Проходит через sandbox при включённом sandboxing
- Для любой подстановки переменных окружения в URL требуется явный список `allowedEnvVars`

### Prompt hooks

Prompt'ы, оцениваемые LLM, где содержимое hook'а - это prompt, который Claude анализирует. Чаще всего используются с событиями `Stop` и `SubagentStop` для интеллектуальной проверки завершения задачи.

```json
{
  "type": "prompt",
  "prompt": "Evaluate if Claude completed all requested tasks.",
  "timeout": 30
}
```

LLM оценивает prompt и возвращает структурированное решение (подробности см. в разделе [Prompt-Based Hooks](#prompt-based-hooks)).

### Agent hooks

Hooks проверки на основе subagent, которые запускают отдельного agent'а для оценки условий или выполнения сложных проверок. В отличие от prompt hooks (одношаговой оценки LLM), agent hooks могут использовать tools и выполнять многошаговое рассуждение.

```json
{
  "type": "agent",
  "prompt": "Verify the code changes follow our architecture guidelines. Check the relevant design docs and compare.",
  "timeout": 120
}
```

**Ключевые свойства:**
- `"type": "agent"` -- указывает, что это agent hook
- `"prompt"` -- описание задачи для subagent
- Agent может использовать tools (Read, Grep, Bash и т. д.) для выполнения проверки
- Возвращает структурированное решение, похожее на prompt hooks

## События hooks

Claude Code поддерживает **25 hook events**:

| Событие | Когда срабатывает | Вход matcher | Может блокировать | Типичное использование |
|-------|---------------|---------------|-----------|------------|
| **SessionStart** | Сессия начинается, возобновляется, очищается или compact'ится | startup/resume/clear/compact | Нет | Настройка окружения |
| **InstructionsLoaded** | После загрузки CLAUDE.md или файла правил | (нет) | Нет | Изменение/фильтрация инструкций |
| **UserPromptSubmit** | Пользователь отправляет prompt | (нет) | Да | Проверка prompt'ов |
| **PreToolUse** | До выполнения tool | Имя tool | Да (allow/deny/ask) | Проверка и изменение входных данных |
| **PermissionRequest** | Показывается диалог разрешения | Имя tool | Да | Авто-approve/deny |
| **PostToolUse** | После успешного выполнения tool | Имя tool | Нет | Добавление контекста, feedback |
| **PostToolUseFailure** | Выполнение tool завершается ошибкой | Имя tool | Нет | Обработка ошибок, logging |
| **Notification** | Отправлено уведомление | Тип уведомления | Нет | Пользовательские уведомления |
| **SubagentStart** | Запущен subagent | Имя типа agent | Нет | Настройка subagent |
| **SubagentStop** | Subagent завершает работу | Имя типа agent | Да | Проверка subagent |
| **Stop** | Claude завершает ответ | (нет) | Да | Проверка завершения задачи |
| **StopFailure** | Ошибка API завершает ход | (нет) | Нет | Восстановление после ошибки, logging |
| **TeammateIdle** | Участник команды agent'ов простаивает | (нет) | Да | Координация участников |
| **TaskCompleted** | Задача помечена завершённой | (нет) | Да | Действия после задачи |
| **TaskCreated** | Задача создана через TaskCreate | (нет) | Нет | Отслеживание и logging задач |
| **ConfigChange** | Изменяется файл конфигурации | (нет) | Да (кроме policy) | Реакция на обновления конфигурации |
| **CwdChanged** | Меняется рабочий каталог | (нет) | Нет | Настройка для конкретного каталога |
| **FileChanged** | Изменяется отслеживаемый файл | (нет) | Нет | Мониторинг файлов, rebuild |
| **PreCompact** | Перед context compaction | manual/auto | Нет | Действия перед compaction |
| **PostCompact** | После завершения compaction | (нет) | Нет | Действия после compaction |
| **WorktreeCreate** | Создаётся worktree | (нет) | Да (path return) | Инициализация worktree |
| **WorktreeRemove** | Worktree удаляется | (нет) | Нет | Очистка worktree |
| **Elicitation** | MCP server запрашивает ввод пользователя | (нет) | Да | Проверка ввода |
| **ElicitationResult** | Пользователь отвечает на elicitation | (нет) | Да | Обработка ответа |
| **SessionEnd** | Сессия завершается | (нет) | Нет | Очистка, финальное logging |

### PreToolUse

Срабатывает после того, как Claude создаёт параметры tool, и до обработки. Используйте это, чтобы проверить или изменить входные данные tool.

**Configuration:**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/validate-bash.py"
          }
        ]
      }
    ]
  }
}
```

**Common matchers:** `Task`, `Bash`, `Glob`, `Grep`, `Read`, `Edit`, `Write`, `WebFetch`, `WebSearch`

**Output control:**
- `permissionDecision`: `"allow"`, `"deny"`, or `"ask"`
- `permissionDecisionReason`: Explanation for decision
- `updatedInput`: Modified tool input parameters

### PostToolUse

Срабатывает сразу после завершения tool. Используйте для проверки, logging или передачи контекста обратно Claude.

**Configuration:**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/security-scan.py"
          }
        ]
      }
    ]
  }
}
```

**Output control:**
- `"block"` decision prompts Claude with feedback
- `additionalContext`: Context added for Claude

### UserPromptSubmit

Срабатывает, когда пользователь отправляет prompt, до того как Claude начнёт его обрабатывать.

**Configuration:**
```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/validate-prompt.py"
          }
        ]
      }
    ]
  }
}
```

**Output control:**
- `decision`: `"block"` to prevent processing
- `reason`: Explanation if blocked
- `additionalContext`: Context added to prompt

### Stop и SubagentStop

Срабатывает, когда Claude завершает ответ (Stop) или subagent заканчивает работу (SubagentStop). Поддерживает оценку на основе prompt для умной проверки завершения задачи.

**Дополнительное поле ввода:** hooks `Stop` и `SubagentStop` получают поле `last_assistant_message` в JSON-вводе, содержащее финальное сообщение Claude или subagent перед остановкой. Это полезно для оценки завершённости задачи.

**Configuration:**
```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Evaluate if Claude completed all requested tasks.",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

### SubagentStart

Срабатывает, когда subagent начинает выполнение. Входное значение matcher - это имя типа agent, что позволяет нацеливать hooks на конкретные типы subagent.

**Configuration:**
```json
{
  "hooks": {
    "SubagentStart": [
      {
        "matcher": "code-review",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/subagent-init.sh"
          }
        ]
      }
    ]
  }
}
```

### SessionStart

Срабатывает при запуске или возобновлении сессии. Может сохранять переменные окружения.

**Matchers:** `startup`, `resume`, `clear`, `compact`

**Особенность:** используйте `CLAUDE_ENV_FILE`, чтобы сохранять переменные окружения (также доступно в hooks `CwdChanged` и `FileChanged`):

```bash
#!/bin/bash
if [ -n "$CLAUDE_ENV_FILE" ]; then
  echo 'export NODE_ENV=development' >> "$CLAUDE_ENV_FILE"
fi
exit 0
```

### SessionEnd

Срабатывает при завершении сессии для очистки или финального логирования. Не может блокировать завершение.

**Значения поля reason:**
- `clear` - пользователь очистил сессию
- `logout` - пользователь вышел из аккаунта
- `prompt_input_exit` - пользователь вышел через prompt input
- `other` - другая причина

**Configuration:**
```json
{
  "hooks": {
    "SessionEnd": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR/.claude/hooks/session-cleanup.sh\""
          }
        ]
      }
    ]
  }
}
```

### Notification

Обновлённые matcher для notification events:
- `permission_prompt` - уведомление о запросе разрешения
- `idle_prompt` - уведомление о простое
- `auth_success` - успешная аутентификация
- `elicitation_dialog` - диалог, показанный пользователю

## Hooks уровня компонента

Hooks можно привязывать к конкретным компонентам (skills, agents, commands) в их frontmatter:

**В `SKILL.md`, `agent.md` или `command.md`:**

```yaml
---
name: secure-operations
description: Perform operations with security checks
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/check.sh"
          once: true  # Only run once per session
---
```

**Поддерживаемые события для component hooks:** `PreToolUse`, `PostToolUse`, `Stop`

Это позволяет определять hooks прямо в компоненте, который их использует, и держать связанный код рядом.

### Hooks in Subagent Frontmatter

Когда hook `Stop` определён во frontmatter subagent, он автоматически преобразуется в hook `SubagentStop`, привязанный к этому subagent. Это гарантирует, что hook остановки срабатывает только при завершении именно этого subagent, а не когда заканчивается основная сессия.

```yaml
---
name: code-review-agent
description: Automated code review subagent
hooks:
  Stop:
    - hooks:
        - type: prompt
          prompt: "Verify the code review is thorough and complete."
  # The above Stop hook auto-converts to SubagentStop for this subagent
---
```

## PermissionRequest

Обрабатывает запросы разрешений с пользовательским форматом вывода:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PermissionRequest",
    "decision": {
      "behavior": "allow|deny",
      "updatedInput": {},
      "message": "Custom message",
      "interrupt": false
    }
  }
}
```

## Ввод и вывод hooks

### JSON input (через stdin)

Все hooks получают JSON-ввод через stdin:

```json
{
  "session_id": "abc123",
  "transcript_path": "/path/to/transcript.jsonl",
  "cwd": "/current/working/directory",
  "permission_mode": "default",
  "hook_event_name": "PreToolUse",
  "tool_name": "Write",
  "tool_input": {
    "file_path": "/path/to/file.js",
    "content": "..."
  },
  "tool_use_id": "toolu_01ABC123...",
  "agent_id": "agent-abc123",
  "agent_type": "main",
  "worktree": "/path/to/worktree"
}
```

**Общие поля:**

| Поле | Описание |
|-------|-------------|
| `session_id` | Уникальный идентификатор сессии |
| `transcript_path` | Путь к файлу транскрипта разговора |
| `cwd` | Текущая рабочая директория |
| `hook_event_name` | Имя события, запустившего hook |
| `agent_id` | Идентификатор agent, выполняющего этот hook |
| `agent_type` | Тип agent (`"main"`, имя типа subagent и т. д.) |
| `worktree` | Путь к git worktree, если agent работает в нём |

### Exit codes

| Exit code | Значение | Поведение |
|-----------|---------|----------|
| **0** | Успех | Продолжить, разобрать JSON stdout |
| **2** | Блокирующая ошибка | Заблокировать операцию, stderr показать как ошибку |
| **Other** | Неблокирующая ошибка | Продолжить, stderr показать в verbose mode |

### JSON output (stdout, exit code 0)

```json
{
  "continue": true,
  "stopReason": "Optional message if stopping",
  "suppressOutput": false,
  "systemMessage": "Optional warning message",
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow",
    "permissionDecisionReason": "File is in allowed directory",
    "updatedInput": {
      "file_path": "/modified/path.js"
    }
  }
}
```

## Переменные окружения

| Variable | Availability | Description |
|----------|-------------|-------------|
| `CLAUDE_PROJECT_DIR` | All hooks | Absolute path to project root |
| `CLAUDE_ENV_FILE` | SessionStart, CwdChanged, FileChanged | File path for persisting env vars |
| `CLAUDE_CODE_REMOTE` | All hooks | `"true"` if running in remote environments |
| `${CLAUDE_PLUGIN_ROOT}` | Plugin hooks | Path to plugin directory |
| `${CLAUDE_PLUGIN_DATA}` | Plugin hooks | Path to plugin data directory |
| `CLAUDE_CODE_SESSIONEND_HOOKS_TIMEOUT_MS` | SessionEnd hooks | Configurable timeout in milliseconds for SessionEnd hooks (overrides default) |

## Prompt-Based Hooks

Для событий `Stop` и `SubagentStop` можно использовать оценку на основе LLM:

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Review if all tasks are complete. Return your decision.",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

**Схема ответа LLM:**
```json
{
  "decision": "approve",
  "reason": "All tasks completed successfully",
  "continue": false,
  "stopReason": "Task complete"
}
```

## Примеры

### Пример 1: валидатор Bash-команд (PreToolUse)

**File:** `.claude/hooks/validate-bash.py`

```python
#!/usr/bin/env python3
import json
import sys
import re

BLOCKED_PATTERNS = [
    (r"\brm\s+-rf\s+/", "Blocking dangerous rm -rf / command"),
    (r"\bsudo\s+rm", "Blocking sudo rm command"),
]

def main():
    input_data = json.load(sys.stdin)

    tool_name = input_data.get("tool_name", "")
    if tool_name != "Bash":
        sys.exit(0)

    command = input_data.get("tool_input", {}).get("command", "")

    for pattern, message in BLOCKED_PATTERNS:
        if re.search(pattern, command):
            print(message, file=sys.stderr)
            sys.exit(2)  # Exit 2 = blocking error

    sys.exit(0)

if __name__ == "__main__":
    main()
```

**Конфигурация:**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/validate-bash.py\""
          }
        ]
      }
    ]
  }
}
```

### Пример 2: сканер безопасности (PostToolUse)

**File:** `.claude/hooks/security-scan.py`

```python
#!/usr/bin/env python3
import json
import sys
import re

SECRET_PATTERNS = [
    (r"password\s*=\s*['\"][^'\"]+['\"]", "Potential hardcoded password"),
    (r"api[_-]?key\s*=\s*['\"][^'\"]+['\"]", "Potential hardcoded API key"),
]

def main():
    input_data = json.load(sys.stdin)

    tool_name = input_data.get("tool_name", "")
    if tool_name not in ["Write", "Edit"]:
        sys.exit(0)

    tool_input = input_data.get("tool_input", {})
    content = tool_input.get("content", "") or tool_input.get("new_string", "")
    file_path = tool_input.get("file_path", "")

    warnings = []
    for pattern, message in SECRET_PATTERNS:
        if re.search(pattern, content, re.IGNORECASE):
            warnings.append(message)

    if warnings:
        output = {
            "hookSpecificOutput": {
                "hookEventName": "PostToolUse",
                "additionalContext": f"Security warnings for {file_path}: " + "; ".join(warnings)
            }
        }
        print(json.dumps(output))

    sys.exit(0)

if __name__ == "__main__":
    main()
```

### Пример 3: автоформатирование кода (PostToolUse)

**File:** `.claude/hooks/format-code.sh`

```bash
#!/bin/bash

# Read JSON from stdin
INPUT=$(cat)
TOOL_NAME=$(echo "$INPUT" | python3 -c "import sys, json; print(json.load(sys.stdin).get('tool_name', ''))")
FILE_PATH=$(echo "$INPUT" | python3 -c "import sys, json; print(json.load(sys.stdin).get('tool_input', {}).get('file_path', ''))")

if [ "$TOOL_NAME" != "Write" ] && [ "$TOOL_NAME" != "Edit" ]; then
    exit 0
fi

# Format based on file extension
case "$FILE_PATH" in
    *.js|*.jsx|*.ts|*.tsx|*.json)
        command -v prettier &>/dev/null && prettier --write "$FILE_PATH" 2>/dev/null
        ;;
    *.py)
        command -v black &>/dev/null && black "$FILE_PATH" 2>/dev/null
        ;;
    *.go)
        command -v gofmt &>/dev/null && gofmt -w "$FILE_PATH" 2>/dev/null
        ;;
esac

exit 0
```

### Пример 4: валидатор prompt (UserPromptSubmit)

**File:** `.claude/hooks/validate-prompt.py`

```python
#!/usr/bin/env python3
import json
import sys
import re

BLOCKED_PATTERNS = [
    (r"delete\s+(all\s+)?database", "Dangerous: database deletion"),
    (r"rm\s+-rf\s+/", "Dangerous: root deletion"),
]

def main():
    input_data = json.load(sys.stdin)
    prompt = input_data.get("user_prompt", "") or input_data.get("prompt", "")

    for pattern, message in BLOCKED_PATTERNS:
        if re.search(pattern, prompt, re.IGNORECASE):
            output = {
                "decision": "block",
                "reason": f"Blocked: {message}"
            }
            print(json.dumps(output))
            sys.exit(0)

    sys.exit(0)

if __name__ == "__main__":
    main()
```

### Пример 5: интеллектуальный hook остановки (на основе prompt)

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Review if Claude completed all requested tasks. Check: 1) Were all files created/modified? 2) Were there unresolved errors? If incomplete, explain what's missing.",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

### Пример 6: трекер использования контекста (пара hooks)

Отслеживайте расход токенов на запрос с помощью пары hooks `UserPromptSubmit` (до сообщения) и `Stop` (после ответа).

**File:** `.claude/hooks/context-tracker.py`

```python
#!/usr/bin/env python3
"""
Context Usage Tracker - отслеживает расход токенов на каждый запрос.

Использует `UserPromptSubmit` как hook "до сообщения" и `Stop` как hook "после ответа"
для расчёта разницы в расходе токенов на каждый запрос.

Методы подсчёта токенов:
1. Character estimation (default): ~4 chars per token, no dependencies
2. tiktoken (optional): More accurate (~90-95%), requires: pip install tiktoken
"""
import json
import os
import sys
import tempfile

# Configuration
CONTEXT_LIMIT = 128000  # Claude's context window (adjust for your model)
USE_TIKTOKEN = False    # Set True if tiktoken is installed for better accuracy


def get_state_file(session_id: str) -> str:
    """Get temp file path for storing pre-message token count, isolated by session."""
    return os.path.join(tempfile.gettempdir(), f"claude-context-{session_id}.json")


def count_tokens(text: str) -> int:
    """
    Count tokens in text.

    Uses tiktoken with p50k_base encoding if available (~90-95% accuracy),
    otherwise falls back to character estimation (~80-90% accuracy).
    """
    if USE_TIKTOKEN:
        try:
            import tiktoken
            enc = tiktoken.get_encoding("p50k_base")
            return len(enc.encode(text))
        except ImportError:
            pass  # Fall back to estimation

    # Character-based estimation: ~4 characters per token for English
    return len(text) // 4


def read_transcript(transcript_path: str) -> str:
    """Read and concatenate all content from transcript file."""
    if not transcript_path or not os.path.exists(transcript_path):
        return ""

    content = []
    with open(transcript_path, "r") as f:
        for line in f:
            try:
                entry = json.loads(line.strip())
                # Extract text content from various message formats
                if "message" in entry:
                    msg = entry["message"]
                    if isinstance(msg.get("content"), str):
                        content.append(msg["content"])
                    elif isinstance(msg.get("content"), list):
                        for block in msg["content"]:
                            if isinstance(block, dict) and block.get("type") == "text":
                                content.append(block.get("text", ""))
            except json.JSONDecodeError:
                continue

    return "\n".join(content)


def handle_user_prompt_submit(data: dict) -> None:
    """Pre-message hook: Save current token count before request."""
    session_id = data.get("session_id", "unknown")
    transcript_path = data.get("transcript_path", "")

    transcript_content = read_transcript(transcript_path)
    current_tokens = count_tokens(transcript_content)

    # Save to temp file for later comparison
    state_file = get_state_file(session_id)
    with open(state_file, "w") as f:
        json.dump({"pre_tokens": current_tokens}, f)


def handle_stop(data: dict) -> None:
    """Post-response hook: Calculate and report token delta."""
    session_id = data.get("session_id", "unknown")
    transcript_path = data.get("transcript_path", "")

    transcript_content = read_transcript(transcript_path)
    current_tokens = count_tokens(transcript_content)

    # Load pre-message count
    state_file = get_state_file(session_id)
    pre_tokens = 0
    if os.path.exists(state_file):
        try:
            with open(state_file, "r") as f:
                state = json.load(f)
                pre_tokens = state.get("pre_tokens", 0)
        except (json.JSONDecodeError, IOError):
            pass

    # Calculate delta
    delta_tokens = current_tokens - pre_tokens
    remaining = CONTEXT_LIMIT - current_tokens
    percentage = (current_tokens / CONTEXT_LIMIT) * 100

    # Report usage
    method = "tiktoken" if USE_TIKTOKEN else "estimated"
    print(f"Context ({method}): ~{current_tokens:,} tokens ({percentage:.1f}% used, ~{remaining:,} remaining)", file=sys.stderr)
    if delta_tokens > 0:
        print(f"This request: ~{delta_tokens:,} tokens", file=sys.stderr)


def main():
    data = json.load(sys.stdin)
    event = data.get("hook_event_name", "")

    if event == "UserPromptSubmit":
        handle_user_prompt_submit(data)
    elif event == "Stop":
        handle_stop(data)

    sys.exit(0)


if __name__ == "__main__":
    main()
```

**Configuration:**
```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/context-tracker.py\""
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/context-tracker.py\""
          }
        ]
      }
    ]
  }
}
```

**How it works:**
1. `UserPromptSubmit` fires before your prompt is processed - saves current token count
2. `Stop` fires after Claude responds - calculates delta and reports usage
3. Each session is isolated via `session_id` in the temp filename

**Token Counting Methods:**

| Method | Accuracy | Dependencies | Speed |
|--------|----------|--------------|-------|
| Character estimation | ~80-90% | None | <1ms |
| tiktoken (p50k_base) | ~90-95% | `pip install tiktoken` | <10ms |

> **Note:** Anthropic hasn't released an official offline tokenizer. Both methods are approximations. The transcript includes user prompts, Claude's responses, and tool outputs, but NOT system prompts or internal context.

### Пример 7: режим Auto-Adapt (PostToolUse)

Автоматически учится на ваших разрешениях для tools и обновляет permissions в `~/.claude/settings.json`. Каждый раз, когда вы разрешаете выполнение tool, hook обобщает команду в переиспользуемое правило разрешений - так вам не придётся дважды одобрять один и тот же тип команды. Опасные и разрушительные команды **никогда** не запоминаются.

При первом запуске он заполняет конфиг базовым набором разрешений, эквивалентным auto mode (чтение/запись файлов, git-операции, package managers, распространённые CLI tools).

**File:** `.claude/hooks/auto-adapt-mode.py`

```python
#!/usr/bin/env python3
"""
auto-adapt-mode: учится на разрешениях пользователя для tools и обновляет конфигурацию Claude.

Тип hook: `PostToolUse`
Событие: срабатывает после успешного выполнения tool (то есть после того, как пользователь его одобрил)
"""

import json
import os
import sys
import re
from pathlib import Path

SETTINGS_PATH = Path.home() / ".claude" / "settings.json"
LOG_PATH = Path.home() / ".claude" / "auto-adapt-mode.log"

# Auto-mode baseline: safe, local, reversible operations
AUTO_MODE_BASELINE = [
    "Read(*)", "Edit(*)", "Write(*)", "Glob(*)", "Grep(*)",
    "Bash(git status:*)", "Bash(git log:*)", "Bash(git diff:*)",
    "Bash(git add:*)", "Bash(git commit:*)", "Bash(git checkout:*)",
    "Bash(npm install:*)", "Bash(npm test:*)", "Bash(npm run:*)",
    "Bash(pip install:*)", "Bash(pytest:*)",
    "Bash(ls:*)", "Bash(cat:*)", "Bash(find:*)", "Bash(mkdir:*)",
    "Bash(cp:*)", "Bash(mv:*)", "Bash(chmod:*)",
    "Bash(gh pr view:*)", "Bash(gh issue list:*)",
    "Agent(*)", "Skill(*)", "WebSearch(*)", "WebFetch(*)",
    # ... (full list includes 70+ safe patterns)
]

# Commands that are NEVER auto-remembered
DANGEROUS_PATTERNS = [
    r"rm\s+(-[a-zA-Z]*r[a-zA-Z]*|--recursive)",   # rm -rf
    r"git\s+push\s+(-[a-zA-Z]*f|--force)",          # force push
    r"git\s+reset\s+--hard",                         # hard reset
    r"DROP\s+(TABLE|DATABASE)",                       # SQL destructive
    r"curl\s+.*\|\s*(bash|sh)",                       # pipe to shell
    r"sudo\b",                                        # privilege escalation
    r"docker\s+(rm|rmi|system\s+prune)",              # container destructive
    r"kubectl\s+delete",                              # k8s destructive
    r"terraform\s+destroy",                           # infra destructive
    r"npm\s+publish",                                 # irreversible publish
    r"deploy\s+.*prod",                               # production deploy
    # ... (full list includes 25+ patterns)
]


def is_dangerous_command(command: str) -> bool:
    """Check if a bash command matches any dangerous pattern."""
    return any(re.search(p, command, re.IGNORECASE) for p in DANGEROUS_PATTERNS)


def generalize_tool_permission(tool_name: str, tool_input: dict) -> str | None:
    """Convert a specific tool invocation into a generalized permission rule."""
    if tool_name == "Bash":
        command = tool_input.get("command", "")
        if not command or is_dangerous_command(command):
            return None
        parts = command.strip().split()
        base = parts[0]
        # Compound commands: "git push" -> "Bash(git push:*)"
        compound = ["git", "npm", "npx", "pip", "cargo", "go", "gh", "python3"]
        if base in compound and len(parts) > 1:
            sub = parts[1]
            if sub.lower() in {"rm", "delete", "destroy", "publish"}:
                return None
            return f"Bash({base} {sub}:*)"
        return f"Bash({base}:*)"
    elif tool_name == "Bash":  # Never allow generic Bash(*)
        return None
    else:
        return f"{tool_name}(*)"


def main():
    try:
        hook_input = json.load(sys.stdin)
    except (json.JSONDecodeError, EOFError):
        sys.exit(0)

    tool_name = hook_input.get("tool_name", "")
    tool_input = hook_input.get("tool_input", {})
    if not tool_name:
        sys.exit(0)

    # Load settings, ensure baseline, add new rule if safe
    settings = json.load(open(SETTINGS_PATH)) if SETTINGS_PATH.exists() else {}
    allow = settings.setdefault("permissions", {}).setdefault("allow", [])

    # Seed baseline on first run
    marker = Path.home() / ".claude" / ".auto-adapt-mode-initialized"
    if not marker.exists():
        existing = set(allow)
        for rule in AUTO_MODE_BASELINE:
            if rule not in existing:
                allow.append(rule)
        marker.touch()

    # Generalize and add the new rule
    rule = generalize_tool_permission(tool_name, tool_input)
    if rule and rule not in allow:
        allow.append(rule)
        with open(SETTINGS_PATH, "w") as f:
            json.dump(settings, f, indent=2)
            f.write("\n")

    sys.exit(0)

if __name__ == "__main__":
    main()
```

**Configuration:**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "*",
        "hooks": [
          {
            "type": "command",
            "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/auto-adapt-mode.py\"",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

**Как это работает:**
1. `PostToolUse` срабатывает после **каждого** успешного выполнения tool (то есть вы уже его одобрили)
2. Hook извлекает имя tool и входные данные, затем обобщает их в правило разрешений
3. Составные команды вроде `git push origin main` превращаются в `Bash(git push:*)` - это покрывает любой вариант `git push`
4. Правило добавляется в `~/.claude/settings.json` → `permissions.allow`, если там его ещё нет
5. При первом запуске задаётся примерно 70 базовых разрешений, эквивалентных auto mode

**Гарантии безопасности:**
- Опасные команды (force push, `rm -rf`, `sudo`, `DROP TABLE` и т. п.) **никогда** не запоминаются
- Необратимые операции (`npm publish`, `terraform destroy`, prod deploys) **всегда** блокируются
- Команды из списка `deny` никогда не переопределяются
- Hook никогда не блокирует выполнение tool (всегда завершается с кодом 0)
- Файл журнала `~/.claude/auto-adapt-mode.log` фиксирует все решения для аудита

**Примеры обобщения:**

| You approve | Rule added | Covers |
|-------------|-----------|--------|
| `git push origin main` | `Bash(git push:*)` | All git push variants |
| `npm run build` | `Bash(npm run:*)` | All npm scripts |
| `ls -la src/` | `Bash(ls:*)` | All ls invocations |
| `rm -rf /tmp/test` | *(blocked)* | Never remembered |
| `git push --force` | *(blocked)* | Never remembered |
| `Write` tool | `Write(*)` | All file writes |

> **Совет:** удалите `~/.claude/.auto-adapt-mode-initialized`, чтобы заново заполнить базовые permissions. Проверьте `~/.claude/auto-adapt-mode.log`, чтобы увидеть, какие правила были добавлены и какие заблокированы.

## Plugin Hooks

Плагины могут включать hooks в файле `hooks/hooks.json`:

**File:** `plugins/hooks/hooks.json`

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh"
          }
        ]
      }
    ]
  }
}
```

**Переменные окружения в plugin hooks:**
- `${CLAUDE_PLUGIN_ROOT}` - Path to the plugin directory
- `${CLAUDE_PLUGIN_DATA}` - Path to the plugin data directory

Это позволяет плагинам включать собственные hooks для валидации и автоматизации.

## MCP Tool Hooks

MCP tools следуют шаблону `mcp__<server>__<tool>`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "mcp__memory__.*",
        "hooks": [
          {
            "type": "command",
            "command": "echo '{\"systemMessage\": \"Memory operation logged\"}'"
          }
        ]
      }
    ]
  }
}
```

## Соображения по безопасности

### Отказ от ответственности

**ИСПОЛЬЗУЙТЕ НА СВОЙ СТРАХ И РИСК**: hooks выполняют произвольные shell-команды. Вы несёте полную ответственность за:
- Команды, которые вы настраиваете
- Права доступа и изменения файлов
- Потенциальную потерю данных или повреждение системы
- Проверку hooks в безопасной среде до использования в production

### Замечания по безопасности

- **Требуется доверие к workspace:** output commands хуков `statusLine` и `fileSuggestion` теперь требуют принятия workspace trust, прежде чем вступят в силу.
- **HTTP hooks и переменные окружения:** HTTP hooks требуют явного списка `allowedEnvVars`, чтобы использовать подстановку переменных окружения в URL. Это предотвращает случайную утечку чувствительных переменных на удалённые endpoints.
- **Иерархия managed settings:** настройка `disableAllHooks` теперь учитывает иерархию managed settings, поэтому настройки уровня организации могут принудительно отключать hooks, а отдельные пользователи не могут это переопределить.

### Лучшие практики

| Do | Don't |
|-----|-------|
| Валидируйте и очищайте все входные данные | Безоговорочно доверяйте входу |
| Квотируйте shell-переменные: `"$VAR"` | Используйте без кавычек: `$VAR` |
| Блокируйте path traversal (`..`) | Разрешайте произвольные пути |
| Используйте абсолютные пути с `$CLAUDE_PROJECT_DIR` | Захардкоживайте пути |
| Пропускайте чувствительные файлы (`.env`, `.git/`, keys) | Обрабатывайте все файлы |
| Сначала тестируйте hooks изолированно | Разворачивайте непроверенные hooks |
| Для HTTP hooks задавайте явный `allowedEnvVars` | Открывайте все env vars для webhooks |

## Отладка

### Включить debug mode

Запустите Claude с debug flag, чтобы получать подробные логи hooks:

```bash
claude --debug
```

### Verbose Mode

Используйте `Ctrl+O` в Claude Code, чтобы включить verbose mode и видеть ход выполнения hooks.

### Тестирование hooks отдельно

```bash
# Тест с примером JSON input
echo '{"tool_name": "Bash", "tool_input": {"command": "ls -la"}}' | python3 .claude/hooks/validate-bash.py

# Проверка exit code
echo $?
```

## Полный пример конфигурации

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/validate-bash.py\"",
            "timeout": 10
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR/.claude/hooks/format-code.sh\"",
            "timeout": 30
          },
          {
            "type": "command",
            "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/security-scan.py\"",
            "timeout": 10
          }
        ]
      }
    ],
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/validate-prompt.py\""
          }
        ]
      }
    ],
    "SessionStart": [
      {
        "matcher": "startup",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR/.claude/hooks/session-init.sh\""
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Verify all tasks are complete before stopping.",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

## Детали выполнения hooks

| Aspect | Behavior |
|--------|----------|
| **Timeout** | По умолчанию 60 секунд, настраивается для каждой команды |
| **Параллельность** | Все подходящие hooks запускаются параллельно |
| **Дедупликация** | Идентичные hook-команды дедуплицируются |
| **Окружение** | Запускаются в текущей директории с окружением Claude Code |

## Устранение неполадок

### Hook не выполняется
- Проверьте, что синтаксис JSON-конфигурации корректен
- Убедитесь, что pattern matcher совпадает с именем tool
- Проверьте, что скрипт существует и исполняемый: `chmod +x script.sh`
- Запустите `claude --debug`, чтобы увидеть логи выполнения hooks
- Убедитесь, что hook читает JSON из stdin, а не из аргументов команды

### Hook блокирует неожиданно
- Протестируйте hook на примере JSON: `echo '{"tool_name": "Write", ...}' | ./hook.py`
- Проверьте exit code: должен быть 0 для разрешения и 2 для блокировки
- Проверьте stderr output (показывается при exit code 2)

### Ошибки разбора JSON
- Всегда читайте из stdin, а не из аргументов команды
- Используйте полноценный JSON parsing, а не манипуляции строками
- Аккуратно обрабатывайте отсутствующие поля

## Установка

### Шаг 1: создайте каталог hooks
```bash
mkdir -p ~/.claude/hooks
```

### Шаг 2: скопируйте примеры hooks
```bash
cp 06-hooks/*.sh ~/.claude/hooks/
chmod +x ~/.claude/hooks/*.sh
```

### Шаг 3: настройте в settings
Отредактируйте `~/.claude/settings.json` или `.claude/settings.json`, добавив конфигурацию hooks, показанную выше.

## Связанные концепции

- **[Checkpoints and Rewind](../08-checkpoints/)** - сохранение и восстановление состояния разговора
- **[Slash Commands](../01-slash-commands/)** - создание собственных slash-команд
- **[Skills](../03-skills/)** - переиспользуемые автономные возможности
- **[Subagents](../04-subagents/)** - делегированное выполнение задач
- **[Plugins](../07-plugins/)** - упакованные расширения
- **[Advanced Features](../09-advanced-features/)** - изучение продвинутых возможностей Claude Code

## Дополнительные ресурсы

- **[Official Hooks Documentation](https://code.claude.com/docs/en/hooks)** - полная справка по hooks
- **[CLI Reference](https://code.claude.com/docs/en/cli-reference)** - документация по командной строке
- **[Memory Guide](../02-memory/)** - настройка постоянного контекста
