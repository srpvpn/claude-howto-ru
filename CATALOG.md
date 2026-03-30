<picture>
  <source media="(prefers-color-scheme: dark)" srcset="resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="resources/logos/claude-howto-logo.svg">
</picture>

# Каталог возможностей Claude Code

> Краткая справка по всем возможностям Claude Code: команды, агенты, навыки, плагины и hooks.

**Навигация**: [Команды](#slash-commands) | [Режимы разрешений](#permission-modes) | [Подагенты](#subagents) | [Навыки](#skills) | [Плагины](#plugins) | [Серверы MCP](#mcp-servers) | [Hooks](#hooks) | [Файлы памяти](#memory-files) | [Новые возможности](#new-features-march-2026)

---

## Сводка

| Возможность | Встроено | Примеры | Всего | Справка |
|---------|----------|----------|-------|-----------|
| **Slash-команды** | 55+ | 8 | 63+ | [01-slash-commands/](01-slash-commands/) |
| **Subagents** | 6 | 10 | 16 | [04-subagents/](04-subagents/) |
| **Skills** | 5 встроенных | 4 | 9 | [03-skills/](03-skills/) |
| **Plugins** | - | 3 | 3 | [07-plugins/](07-plugins/) |
| **MCP Servers** | 1 | 8 | 9 | [05-mcp/](05-mcp/) |
| **Hooks** | 25 событий | 7 | 7 | [06-hooks/](06-hooks/) |
| **Memory** | 7 типов | 3 | 3 | [02-memory/](02-memory/) |
| **Итого** | **99** | **43** | **117** | |

---

## Slash Commands

Команды — это ярлыки, которые пользователь запускает вручную и которые выполняют конкретные действия.

### Встроенные команды

| Команда | Описание | Когда использовать |
|---------|-------------|-------------|
| `/help` | Показать справку | Начать работу, изучить команды |
| `/btw` | Боковой вопрос без добавления в контекст | Быстрые отступления |
| `/chrome` | Настроить интеграцию с Chrome | Автоматизация браузера |
| `/clear` | Очистить историю беседы | Начать заново, уменьшить контекст |
| `/diff` | Интерактивный просмотр diff | Проверка изменений |
| `/config` | Просмотреть/изменить конфигурацию | Настроить поведение |
| `/status` | Показать статус сессии | Проверить текущее состояние |
| `/agents` | Показать доступных агентов | Посмотреть варианты делегирования |
| `/skills` | Показать доступные skills | Посмотреть возможности авто-запуска |
| `/hooks` | Показать настроенные hooks | Отладка автоматизации |
| `/insights` | Анализировать шаблоны сессии | Оптимизация сессии |
| `/install-slack-app` | Установить Slack-приложение Claude | Интеграция со Slack |
| `/keybindings` | Настроить горячие клавиши | Кастомизация клавиш |
| `/mcp` | Показать серверы MCP | Проверить внешние интеграции |
| `/memory` | Показать загруженные файлы памяти | Отладка загрузки контекста |
| `/mobile` | Сгенерировать QR-код для мобильного | Доступ с телефона |
| `/passes` | Показать usage passes | Информация о подписке |
| `/plugin` | Управлять плагинами | Установка и удаление расширений |
| `/plan` | Войти в режим планирования | Сложные реализации |
| `/rewind` | Откатиться к checkpoint | Отменить изменения, изучить альтернативы |
| `/checkpoint` | Управлять checkpoints | Сохранение и восстановление состояний |
| `/cost` | Показать стоимость токенов | Контроль расходов |
| `/context` | Показать использование окна контекста | Управление длиной беседы |
| `/export` | Экспортировать беседу | Сохранить для справки |
| `/extra-usage` | Настроить дополнительные лимиты | Управление rate limits |
| `/feedback` | Отправить отзыв или баг-репорт | Сообщить о проблемах |
| `/login` | Аутентифицироваться через Anthropic | Доступ к функциям |
| `/logout` | Выйти из аккаунта | Сменить учётную запись |
| `/sandbox` | Переключить sandbox mode | Безопасное выполнение команд |
| `/vim` | Переключить vim mode | Редактирование в стиле Vim |
| `/doctor` | Запустить диагностику | Поиск и устранение проблем |
| `/reload-plugins` | Перезагрузить установленные плагины | Управление плагинами |
| `/release-notes` | Показать release notes | Проверить новые возможности |
| `/remote-control` | Включить remote control | Удалённый доступ |
| `/permissions` | Управлять разрешениями | Контроль доступа |
| `/session` | Управлять сессиями | Многосессионные workflows |
| `/rename` | Переименовать текущую сессию | Организация сессий |
| `/resume` | Возобновить предыдущую сессию | Продолжить работу |
| `/todo` | Просмотреть и управлять списком задач | Отслеживание задач |
| `/tasks` | Просмотреть фоновые задачи | Мониторинг асинхронных операций |
| `/copy` | Скопировать последний ответ в буфер | Быстрое распространение вывода |
| `/teleport` | Передать сессию на другой компьютер | Продолжить работу удалённо |
| `/desktop` | Открыть приложение Claude Desktop | Перейти в desktop-интерфейс |
| `/theme` | Изменить цветовую тему | Настроить внешний вид |
| `/usage` | Показать статистику использования API | Контроль квоты и расходов |
| `/fork` | Разветвить текущую беседу | Изучить альтернативы |
| `/stats` | Показать статистику сессии | Просмотр метрик сессии |
| `/statusline` | Настроить status line | Настроить отображение статуса |
| `/stickers` | Показать stickers сессии | Забавные награды |
| `/fast` | Переключить быстрый режим вывода | Ускорить ответы |
| `/terminal-setup` | Настроить интеграцию с терминалом | Настройка функций терминала |
| `/upgrade` | Проверить обновления | Управление версиями |

### Пользовательские команды (примеры)

| Команда | Описание | Когда использовать | Область | Установка |
|---------|-------------|-------------|-------|--------------|
| `/optimize` | Анализировать код на предмет оптимизации | Улучшение производительности | Project | `cp 01-slash-commands/optimize.md .claude/commands/` |
| `/pr` | Подготовить pull request | Перед отправкой PR | Project | `cp 01-slash-commands/pr.md .claude/commands/` |
| `/generate-api-docs` | Сгенерировать документацию API | Документировать API | Project | `cp 01-slash-commands/generate-api-docs.md .claude/commands/` |
| `/commit` | Создать git-commit с контекстом | Фиксация изменений | User | `cp 01-slash-commands/commit.md .claude/commands/` |
| `/push-all` | Подготовить, закоммитить и запушить | Быстрый деплой | User | `cp 01-slash-commands/push-all.md .claude/commands/` |
| `/doc-refactor` | Перестроить документацию | Улучшение docs | Project | `cp 01-slash-commands/doc-refactor.md .claude/commands/` |
| `/setup-ci-cd` | Настроить CI/CD pipeline | Новые проекты | Project | `cp 01-slash-commands/setup-ci-cd.md .claude/commands/` |
| `/unit-test-expand` | Расширить покрытие тестами | Улучшение тестирования | Project | `cp 01-slash-commands/unit-test-expand.md .claude/commands/` |

> **Область**: `User` = личные workflows (`~/.claude/commands/`), `Project` = общие для команды (`.claude/commands/`)

**Справка**: [01-slash-commands/](01-slash-commands/) | [Официальная документация](https://code.claude.com/docs/en/interactive-mode)

**Быстрая установка (все пользовательские команды)**:
```bash
cp 01-slash-commands/*.md .claude/commands/
```

---

## Режимы разрешений

Claude Code поддерживает 6 режимов разрешений, которые определяют, как авторизуется использование инструментов.

| Режим | Описание | Когда использовать |
|------|-------------|-------------|
| `default` | Запрашивать подтверждение для каждого вызова инструмента | Обычное интерактивное использование |
| `acceptEdits` | Автоматически принимать правки файлов, спрашивать для остальных | Доверенные сценарии редактирования |
| `plan` | Только инструменты чтения, без записи | Планирование и исследование |
| `auto` | Принимать все инструменты без запросов | Полностью автономная работа (Research Preview) |
| `bypassPermissions` | Пропускать все проверки разрешений | CI/CD, headless-окружения |
| `dontAsk` | Пропускать инструменты, которым нужно разрешение | Неинтерактивные сценарии |

> **Примечание**: режим `auto` — это функция Research Preview (март 2026). Используйте `bypassPermissions` только в доверенных, sandboxed-окружениях.

**Справка**: [Официальная документация](https://code.claude.com/docs/en/permissions)

---

## Подагенты

Специализированные AI-помощники с изолированным контекстом для конкретных задач.

### Встроенные подагенты

| Агенты | Описание | Инструменты | Модель | Когда использовать |
|-------|-------------|-------|-------|-------------|
| **general-purpose** | Multi-step tasks, research | All tools | Inherits model | Complex research, multi-file tasks |
| **Plan** | Implementation planning | Read, Glob, Grep, Bash | Inherits model | Architecture design, planning |
| **Explore** | Codebase exploration | Read, Glob, Grep | Haiku 4.5 | Quick searches, understanding code |
| **Bash** | Command execution | Bash | Inherits model | Git operations, terminal tasks |
| **statusline-setup** | Status line configuration | Bash, Read, Write | Sonnet 4.6 | Configure status line display |
| **Claude Code Guide** | Справка и документация | Read, Glob, Grep | Haiku 4.5 | Получение помощи, изучение возможностей |

### Поля конфигурации подагентов

| Поле | Тип | Описание |
|-------|------|-------------|
| `name` | string | Agent identifier |
| `description` | string | What the agent does |
| `model` | string | Model override (e.g., `haiku-4.5`) |
| `tools` | array | Allowed tools list |
| `effort` | string | Reasoning effort level (`low`, `medium`, `high`) |
| `initialPrompt` | string | System prompt injected at agent start |
| `disallowedTools` | array | Tools explicitly denied to this agent |

### Пользовательские подагенты (примеры)

| Агент | Описание | Когда использовать | Область | Установка |
|-------|-------------|-------------|-------|--------------|
| `code-reviewer` | Comprehensive code quality | Code review sessions | Project | `cp 04-subagents/code-reviewer.md .claude/agents/` |
| `code-architect` | Feature architecture design | New feature planning | Project | `cp 04-subagents/code-architect.md .claude/agents/` |
| `code-explorer` | Deep codebase analysis | Understanding existing features | Project | `cp 04-subagents/code-explorer.md .claude/agents/` |
| `clean-code-reviewer` | Clean Code principles review | Maintainability review | Project | `cp 04-subagents/clean-code-reviewer.md .claude/agents/` |
| `test-engineer` | Test strategy & coverage | Test planning | Project | `cp 04-subagents/test-engineer.md .claude/agents/` |
| `documentation-writer` | Техническая документация | API docs, guides | Project | `cp 04-subagents/documentation-writer.md .claude/agents/` |
| `secure-reviewer` | Security-focused review | Security audits | Project | `cp 04-subagents/secure-reviewer.md .claude/agents/` |
| `implementation-agent` | Full feature implementation | Feature development | Project | `cp 04-subagents/implementation-agent.md .claude/agents/` |
| `debugger` | Root cause analysis | Bug investigation | User | `cp 04-subagents/debugger.md .claude/agents/` |
| `data-scientist` | SQL queries, data analysis | Data tasks | User | `cp 04-subagents/data-scientist.md .claude/agents/` |

> **Scope**: `User` = personal (`~/.claude/agents/`), `Project` = team-shared (`.claude/agents/`)

**Справка**: [04-subagents/](04-subagents/) | [Официальная документация](https://code.claude.com/docs/en/sub-agents)

**Быстрая установка (все пользовательские агенты)**:
```bash
cp 04-subagents/*.md .claude/agents/
```

---

## Навыки

Автоматически вызываемые возможности с инструкциями, скриптами и шаблонами.

### Примеры навыков

| Навык | Описание | Когда вызывается автоматически | Область | Установка |
|-------|-------------|-------------------|-------|--------------|
| `code-review` | Полный code review | "Review this code", "Check quality" | Project | `cp -r 03-skills/code-review .claude/skills/` |
| `brand-voice` | Проверка единообразия бренда | Написание маркетинговых текстов | Project | `cp -r 03-skills/brand-voice .claude/skills/` |
| `doc-generator` | Генератор API-документации | "Generate docs", "Document API" | Project | `cp -r 03-skills/doc-generator .claude/skills/` |
| `refactor` | Систематический рефакторинг кода (Martin Fowler) | "Refactor this", "Clean up code" | User | `cp -r 03-skills/refactor ~/.claude/skills/` |

> **Scope**: `User` = personal (`~/.claude/skills/`), `Project` = team-shared (`.claude/skills/`)

### Структура skill

```
~/.claude/skills/skill-name/
├── SKILL.md          # Определение и инструкции skill
├── scripts/          # Вспомогательные скрипты
└── templates/        # Шаблоны вывода
```

### Поля frontmatter skill

Навыки поддерживают YAML frontmatter в `SKILL.md` для конфигурации:

| Поле | Тип | Описание |
|-------|------|-------------|
| `name` | string | Отображаемое имя skill |
| `description` | string | Что делает skill |
| `autoInvoke` | array | Триггерные фразы для автоматического вызова |
| `effort` | string | Уровень усилия рассуждения (`low`, `medium`, `high`) |
| `shell` | string | Shell для скриптов (`bash`, `zsh`, `sh`) |

**Справка**: [03-skills/](03-skills/) | [Официальная документация](https://code.claude.com/docs/en/skills)

**Быстрая установка (все навыки)**:
```bash
cp -r 03-skills/* ~/.claude/skills/
```

### Встроенные навыки

| Навык | Описание | Когда вызывается автоматически |
|-------|-------------|-------------------|
| `/simplify` | Проверить код на качество | После написания кода |
| `/batch` | Запускать запросы по множеству файлов | Пакетные операции |
| `/debug` | Отладка упавших тестов/ошибок | Сессии отладки |
| `/loop` | Запускать запросы по интервалу | Повторяющиеся задачи |
| `/claude-api` | Создавать приложения с Claude API | Разработка API |

---

## Плагины

Собранные вместе наборы команд, агентов, MCP-серверов и hooks.

### Примеры плагинов

| Плагин | Описание | Компоненты | Когда использовать | Область | Установка |
|--------|-------------|------------|-------------|-------|--------------|
| `pr-review` | Workflow ревью PR | 3 commands, 3 agents, GitHub MCP | Code reviews | Project | `/plugin install pr-review` |
| `devops-automation` | Развёртывание и мониторинг | 4 commands, 3 agents, K8s MCP | DevOps tasks | Project | `/plugin install devops-automation` |
| `documentation` | Набор для генерации документации | 4 commands, 3 agents, templates | Documentation | Project | `/plugin install documentation` |

> **Scope**: `Project` = team-shared, `User` = personal workflows

### Структура плагина

```
.claude-plugin/
├── plugin.json       # Файл манифеста
├── commands/         # Slash-команды
├── agents/           # Subagents
├── skills/           # Skills
├── mcp/              # Конфигурации MCP
├── hooks/            # Hook-скрипты
└── scripts/          # Вспомогательные скрипты
```

**Справка**: [07-plugins/](07-plugins/) | [Официальная документация](https://code.claude.com/docs/en/plugins)

**Команды управления плагинами**:
```bash
/plugin list              # Показать установленные плагины
/plugin install <name>    # Установить плагин
/plugin remove <name>     # Удалить плагин
/plugin update <name>     # Обновить плагин
```

---

## MCP-серверы

MCP-серверы Model Context Protocol для доступа к внешним инструментам и API.

### Распространённые MCP-серверы

| Сервер | Описание | Когда использовать | Область | Установка |
|--------|-------------|-------------|-------|--------------|
| **GitHub** | Управление PR, issues и кодом | GitHub-workflow | Project | `claude mcp add github -- npx -y @modelcontextprotocol/server-github` |
| **Database** | SQL-запросы и доступ к данным | Операции с базой данных | Project | `claude mcp add db -- npx -y @modelcontextprotocol/server-postgres` |
| **Filesystem** | Расширенные операции с файлами | Сложные файловые задачи | User | `claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem` |
| **Slack** | Коммуникация в команде | Уведомления и обновления | Project | Настроить в параметрах |
| **Google Docs** | Доступ к документам | Редактирование и ревью документов | Project | Настроить в параметрах |
| **Asana** | Управление проектами | Отслеживание задач | Project | Настроить в параметрах |
| **Stripe** | Платёжные данные | Финансовый анализ | Project | Настроить в параметрах |
| **Memory** | Постоянная память | Доступ к контексту между сессиями | User | Настроить в параметрах |
| **Context7** | Документация библиотек | Поиск актуальной документации | Built-in | Встроено |

> **Область**: `Project` = командная (`.mcp.json`), `User` = личная (`~/.claude.json`), `Built-in` = предустановлено

### Пример конфигурации MCP

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

**Справка**: [05-mcp/](05-mcp/) | [Документация протокола MCP](https://modelcontextprotocol.io)

**Быстрая установка (GitHub MCP)**:
```bash
export GITHUB_TOKEN="your_token" && claude mcp add github -- npx -y @modelcontextprotocol/server-github
```

---

## Hooks

Автоматизация на событиях, выполняющая shell-команды при событиях Claude Code.

### События hooks

| Событие | Описание | Когда срабатывает | Примеры использования |
|-------|-------------|----------------|-----------|
| `SessionStart` | Сессия начинается/возобновляется | Инициализация сессии | Настройка задач |
| `InstructionsLoaded` | Инструкции загружены | Загружен `CLAUDE.md` или файл правил | Обработка пользовательских инструкций |
| `UserPromptSubmit` | Перед обработкой запроса | Пользователь отправляет сообщение | Проверка ввода |
| `PreToolUse` | Перед выполнением инструмента | Перед запуском любого инструмента | Валидация, логирование |
| `PermissionRequest` | Показан диалог разрешения | Перед чувствительными действиями | Сценарии авто-одобрения |
| `PostToolUse` | Инструмент успешно завершён | После завершения любого инструмента | Форматирование, уведомления |
| `PostToolUseFailure` | Сбой выполнения инструмента | После ошибки инструмента | Обработка ошибок, логирование |
| `Notification` | Отправлено уведомление | Claude отправляет уведомление | Внешние оповещения |
| `SubagentStart` | Subagent запускается | Задача subagent начинается | Инициализация контекста subagent |
| `SubagentStop` | Subagent завершается | Задача subagent завершена | Последовательность действий |
| `Stop` | Claude заканчивает ответ | Ответ завершён | Очистка, отчётность |
| `StopFailure` | Ошибка API завершает ход | Происходит ошибка API | Восстановление после ошибки, логирование |
| `TeammateIdle` | Напарник в команде агентов простаивает | Координация команды агентов | Распределение работы |
| `TaskCompleted` | Задача помечена как завершённая | Задача выполнена | Постобработка задачи |
| `TaskCreated` | Задача создана через TaskCreate | Создана новая задача | Отслеживание задач, логирование |
| `ConfigChange` | Конфигурация обновлена | Параметры изменены | Реакция на изменения конфигурации |
| `CwdChanged` | Изменился рабочий каталог | Каталог изменён | Настройка под каталог |
| `FileChanged` | Отслеживаемый файл изменился | Файл изменён | Мониторинг файлов, перестройка |
| `PreCompact` | Перед операцией compact | Сжатие контекста | Сохранение состояния |
| `PostCompact` | После завершения compact | Сжатие завершено | Действия после compact |
| `WorktreeCreate` | Worktree создаётся | Git worktree создан | Настройка окружения worktree |
| `WorktreeRemove` | Worktree удаляется | Git worktree удалён | Очистка ресурсов worktree |
| `Elicitation` | MCP-сервер запрашивает ввод | MCP elicitation | Проверка ввода |
| `ElicitationResult` | Пользователь отвечает на elicitation | Пользователь отвечает | Обработка ответа |
| `SessionEnd` | Сессия завершается | Сессия завершена | Очистка, сохранение состояния |

### Примеры hooks

| Hook | Описание | Событие | Область | Установка |
|------|-------------|-------|-------|--------------|
| `validate-bash.py` | Command validation | PreToolUse:Bash | Project | `cp 06-hooks/validate-bash.py .claude/hooks/` |
| `security-scan.py` | Security scanning | PostToolUse:Write | Project | `cp 06-hooks/security-scan.py .claude/hooks/` |
| `format-code.sh` | Auto-formatting | PostToolUse:Write | User | `cp 06-hooks/format-code.sh ~/.claude/hooks/` |
| `validate-prompt.py` | Prompt validation | UserPromptSubmit | Project | `cp 06-hooks/validate-prompt.py .claude/hooks/` |
| `context-tracker.py` | Token usage tracking | Stop | User | `cp 06-hooks/context-tracker.py ~/.claude/hooks/` |
| `pre-commit.sh` | Pre-commit validation | PreToolUse:Bash | Project | `cp 06-hooks/pre-commit.sh .claude/hooks/` |
| `log-bash.sh` | Command logging | PostToolUse:Bash | User | `cp 06-hooks/log-bash.sh ~/.claude/hooks/` |

> **Область**: `Project` = командная (`.claude/settings.json`), `User` = личная (`~/.claude/settings.json`)

### Конфигурация hooks

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "command": "~/.claude/hooks/validate-bash.py"
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write",
        "command": "~/.claude/hooks/format-code.sh"
      }
    ]
  }
}
```

**Справка**: [06-hooks/](06-hooks/) | [Официальная документация](https://code.claude.com/docs/en/hooks)

**Быстрая установка (все hooks)**:
```bash
mkdir -p ~/.claude/hooks && cp 06-hooks/*.sh ~/.claude/hooks/ && chmod +x ~/.claude/hooks/*.sh
```

---

## Файлы памяти

Постоянный контекст, который автоматически загружается между сессиями.

### Типы памяти

| Тип | Расположение | Область | Когда использовать |
|------|----------|-------|-------------|
| **Managed Policy** | Политики под управлением организации | Organization | Принудительное соблюдение стандартов организации |
| **Project** | `./CLAUDE.md` | Project (team) | Стандарты команды, контекст проекта |
| **Project Rules** | `.claude/rules/` | Project (team) | Модульные правила проекта |
| **User** | `~/.claude/CLAUDE.md` | User (personal) | Личные предпочтения |
| **User Rules** | `~/.claude/rules/` | User (personal) | Модульные личные правила |
| **Local** | `./CLAUDE.local.md` | Local (git-ignored) | Переопределения для конкретной машины (в официальной документации по состоянию на март 2026 года не указано; возможно, legacy) |
| **Auto Memory** | Автоматически | Session | Автоматически собранные выводы и исправления |

> **Область**: `Organization` = управляется администраторами, `Project` = общий для команды через git, `User` = личные предпочтения, `Local` = не коммитится, `Session` = автоматически управляется

**Справка**: [02-memory/](02-memory/) | [Официальная документация](https://code.claude.com/docs/en/memory)

**Быстрая установка**:
```bash
cp 02-memory/project-CLAUDE.md ./CLAUDE.md
cp 02-memory/personal-CLAUDE.md ~/.claude/CLAUDE.md
```

---

## Новые возможности (март 2026)

| Возможность | Описание | Как использовать |
|---------|-------------|------------|
| **Remote Control** | Удалённое управление сессиями Claude Code через API | Используйте API remote control, чтобы программно отправлять запросы и получать ответы |
| **Web Sessions** | Запуск Claude Code в браузерной среде | Доступ через `claude web` или Anthropic Console |
| **Desktop App** | Нативное приложение Claude Code для рабочего стола | Используйте `/desktop` или скачайте с сайта Anthropic |
| **Agent Teams** | Координация нескольких агентов над связанными задачами | Настройте командных агентов, которые сотрудничают и делят контекст |
| **Task List** | Управление и мониторинг фоновых задач | Используйте `/tasks`, чтобы просматривать и управлять фоновыми операциями |
| **Prompt Suggestions** | Подсказки команд с учётом контекста | Подсказки появляются автоматически в зависимости от текущего контекста |
| **Git Worktrees** | Изолированные git worktrees для параллельной разработки | Используйте команды worktree для безопасной параллельной работы в ветках |
| **Sandboxing** | Изолированные среды выполнения для безопасности | Используйте `/sandbox` для переключения; команды выполняются в ограниченной среде |
| **MCP OAuth** | OAuth-аутентификация для MCP-серверов | Настройте OAuth-учётные данные в параметрах MCP-сервера для безопасного доступа |
| **MCP Tool Search** | Динамический поиск и обнаружение MCP-инструментов | Используйте поиск инструментов, чтобы находить доступные MCP-инструменты на подключённых серверах |
| **Scheduled Tasks** | Настройка повторяющихся задач через `/loop` и cron-инструменты | Используйте `/loop 5m /command` или инструмент CronCreate |
| **Chrome Integration** | Автоматизация браузера с headless Chromium | Используйте флаг `--chrome` или команду `/chrome` |
| **Keyboard Customization** | Настройка горячих клавиш, включая поддержку chord | Используйте `/keybindings` или отредактируйте `~/.claude/keybindings.json` |
| **Auto Mode** | Полностью автономная работа без запросов разрешений (Research Preview) | Используйте `--mode auto` или `/permissions auto`; март 2026 |
| **Channels** | Многоканальная коммуникация (Telegram, Slack и т. д.) (Research Preview) | Настройте channel-плагины; март 2026 |
| **Voice Dictation** | Голосовой ввод запросов | Используйте значок микрофона или голосовую привязку клавиш |
| **Agent Hook Type** | Hooks, которые запускают subagent вместо shell-команды | Укажите `"type": "agent"` в конфигурации hook |
| **Prompt Hook Type** | Hooks, которые внедряют текст запроса в беседу | Укажите `"type": "prompt"` в конфигурации hook |
| **MCP Elicitation** | MCP-серверы могут запрашивать ввод пользователя во время выполнения инструмента | Обрабатывается через события hooks `Elicitation` и `ElicitationResult` |
| **WebSocket MCP Transport** | WebSocket-транспорт для подключений к MCP-серверам | Используйте `"transport": "websocket"` в конфиге MCP-сервера |
| **Plugin LSP Support** | Интеграция Language Server Protocol через плагины | Настройте LSP-серверы в `plugin.json` для возможностей редактора |
| **Managed Drop-ins** | Drop-in-конфигурации под управлением организации (v2.1.83) | Настраиваются администраторами через managed policies; автоматически применяются ко всем пользователям |

---

## Матрица быстрой справки

### Руководство по выбору возможностей

| Что нужно | Рекомендуемая возможность | Почему |
|------|---------------------|-----|
| Быстрый ярлык | Slash Command | Вручную, сразу |
| Постоянный контекст | Memory | Загружается автоматически |
| Сложная автоматизация | Skill | Вызывается автоматически |
| Специализированная задача | Subagent | Изолированный контекст |
| Внешние данные | MCP Server | Доступ в реальном времени |
| Автоматизация по событиям | Hook | Запускается по событию |
| Полное решение | Plugin | Пакет "всё в одном" |

### Приоритет установки

| Приоритет | Возможность | Команда |
|----------|---------|---------|
| 1. Essential | Memory | `cp 02-memory/project-CLAUDE.md ./CLAUDE.md` |
| 2. Ежедневное использование | Slash-команды | `cp 01-slash-commands/*.md .claude/commands/` |
| 3. Quality | Subagents | `cp 04-subagents/*.md .claude/agents/` |
| 4. Automation | Hooks | `cp 06-hooks/*.sh ~/.claude/hooks/ && chmod +x ~/.claude/hooks/*.sh` |
| 5. External | MCP | `claude mcp add github -- npx -y @modelcontextprotocol/server-github` |
| 6. Advanced | Skills | `cp -r 03-skills/* ~/.claude/skills/` |
| 7. Полный набор | Plugins | `/plugin install pr-review` |

---

## Полная установка одной командой

Установите все примеры из этого репозитория:

```bash
# Создать каталоги
mkdir -p .claude/{commands,agents,skills} ~/.claude/{hooks,skills}

# Установить все возможности
cp 01-slash-commands/*.md .claude/commands/ && \
cp 02-memory/project-CLAUDE.md ./CLAUDE.md && \
cp -r 03-skills/* ~/.claude/skills/ && \
cp 04-subagents/*.md .claude/agents/ && \
cp 06-hooks/*.sh ~/.claude/hooks/ && \
chmod +x ~/.claude/hooks/*.sh
```

---

## Дополнительные ресурсы

- [Официальная документация Claude Code](https://code.claude.com/docs/en/overview)
- [Спецификация протокола MCP](https://modelcontextprotocol.io)
- [Учебная дорожная карта](LEARNING-ROADMAP.md)
- [Основной README](README.md)

---

**Последнее обновление**: март 2026
