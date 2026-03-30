<picture>
  <source media="(prefers-color-scheme: dark)" srcset="../resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="../resources/logos/claude-howto-logo.svg">
</picture>

# MCP (Model Context Protocol)

Эта папка содержит полную документацию и примеры конфигурации MCP servers и их использования с Claude Code.

<a id="overview"></a>
## Обзор

MCP (Model Context Protocol) — это стандартизированный способ, с помощью которого Claude получает доступ к внешним инструментам, API и источникам данных в реальном времени. В отличие от Memory, MCP даёт живой доступ к изменяющимся данным.

Ключевые характеристики:
- Доступ к внешним сервисам в реальном времени
- Синхронизация живых данных
- Расширяемая архитектура
- Безопасная аутентификация
- Взаимодействие через инструменты

## Архитектура MCP

```mermaid
graph TB
    A["Claude"]
    B["MCP Server"]
    C["External Service"]

    A -->|Request: list_issues| B
    B -->|Query| C
    C -->|Data| B
    B -->|Response| A

    A -->|Request: create_issue| B
    B -->|Action| C
    C -->|Result| B
    B -->|Response| A

    style A fill:#e1f5fe,stroke:#333,color:#333
    style B fill:#f3e5f5,stroke:#333,color:#333
    style C fill:#e8f5e9,stroke:#333,color:#333
```

## Экосистема MCP

```mermaid
graph TB
    A["Claude"] -->|MCP| B["Filesystem<br/>MCP Server"]
    A -->|MCP| C["GitHub<br/>MCP Server"]
    A -->|MCP| D["Database<br/>MCP Server"]
    A -->|MCP| E["Slack<br/>MCP Server"]
    A -->|MCP| F["Google Docs<br/>MCP Server"]

    B -->|File I/O| G["Local Files"]
    C -->|API| H["GitHub Repos"]
    D -->|Query| I["PostgreSQL/MySQL"]
    E -->|Messages| J["Slack Workspace"]
    F -->|Docs| K["Google Drive"]

    style A fill:#e1f5fe,stroke:#333,color:#333
    style B fill:#f3e5f5,stroke:#333,color:#333
    style C fill:#f3e5f5,stroke:#333,color:#333
    style D fill:#f3e5f5,stroke:#333,color:#333
    style E fill:#f3e5f5,stroke:#333,color:#333
    style F fill:#f3e5f5,stroke:#333,color:#333
    style G fill:#e8f5e9,stroke:#333,color:#333
    style H fill:#e8f5e9,stroke:#333,color:#333
    style I fill:#e8f5e9,stroke:#333,color:#333
    style J fill:#e8f5e9,stroke:#333,color:#333
    style K fill:#e8f5e9,stroke:#333,color:#333
```

## Способы установки MCP

Claude Code поддерживает несколько транспортных протоколов для подключения к MCP server:

### HTTP-транспорт (рекомендуется)

```bash
# Basic HTTP connection
claude mcp add --transport http notion https://mcp.notion.com/mcp

# HTTP with authentication header
claude mcp add --transport http secure-api https://api.example.com/mcp \
  --header "Authorization: Bearer your-token"
```

### Stdio-транспорт (локально)

Для локально запущенных MCP servers:

```bash
# Local Node.js server
claude mcp add --transport stdio myserver -- npx @myorg/mcp-server

# With environment variables
claude mcp add --transport stdio myserver --env KEY=value -- npx server
```

### SSE-транспорт (устарел)

Транспорт Server-Sent Events устарел в пользу `http`, но по-прежнему поддерживается:

```bash
claude mcp add --transport sse legacy-server https://example.com/sse
```

### WebSocket-транспорт

WebSocket-транспорт для постоянных двунаправленных соединений:

```bash
claude mcp add --transport ws realtime-server wss://example.com/mcp
```

### Примечание для Windows

На нативном Windows (не WSL) используйте `cmd /c` для команд `npx`:

```bash
claude mcp add --transport stdio my-server -- cmd /c npx -y @some/package
```

### Аутентификация OAuth 2.0

Claude Code поддерживает OAuth 2.0 для MCP servers, которым это требуется. При подключении к OAuth-enabled серверу Claude Code обрабатывает весь поток аутентификации:

```bash
# Connect to an OAuth-enabled MCP server (interactive flow)
claude mcp add --transport http my-service https://my-service.example.com/mcp

# Pre-configure OAuth credentials for non-interactive setup
claude mcp add --transport http my-service https://my-service.example.com/mcp \
  --client-id "your-client-id" \
  --client-secret "your-client-secret" \
  --callback-port 8080
```

| Возможность | Описание |
|---------|-------------|
| **Interactive OAuth** | Используйте `/mcp`, чтобы запустить OAuth flow в браузере |
| **Pre-configured OAuth clients** | Встроенные OAuth clients для популярных сервисов вроде Notion, Stripe и других (v2.1.30+) |
| **Pre-configured credentials** | Флаги `--client-id`, `--client-secret`, `--callback-port` для автоматической настройки |
| **Token storage** | Tokens безопасно хранятся в системном keychain |
| **Step-up auth** | Поддерживает step-up authentication для привилегированных операций |
| **Discovery caching** | OAuth discovery metadata кэшируются для более быстрых повторных подключений |
| **Metadata override** | `oauth.authServerMetadataUrl` в `.mcp.json` для переопределения стандартного OAuth metadata discovery |

#### Переопределение OAuth metadata discovery

Если ваш MCP server возвращает ошибки на стандартном OAuth metadata endpoint (`/.well-known/oauth-authorization-server`), но публикует рабочий OIDC endpoint, можно указать Claude Code получать OAuth metadata с другого URL. Установите `authServerMetadataUrl` в объекте `oauth` вашей конфигурации server:

```json
{
  "mcpServers": {
    "my-server": {
      "type": "http",
      "url": "https://mcp.example.com/mcp",
      "oauth": {
        "authServerMetadataUrl": "https://auth.example.com/.well-known/openid-configuration"
      }
    }
  }
}
```

URL должен использовать `https://`. Эта опция требует Claude Code v2.1.64 или новее.

### Claude.ai MCP Connectors

MCP servers, настроенные в вашем аккаунте Claude.ai, автоматически доступны в Claude Code. Это значит, что любые MCP connections, созданные через веб-интерфейс Claude.ai, будут доступны без дополнительной настройки.

Claude.ai MCP connectors также доступны в `--print` mode (v2.1.83+), что позволяет использовать их неинтерактивно и в скриптах.

Чтобы отключить Claude.ai MCP servers в Claude Code, установите переменную окружения `ENABLE_CLAUDEAI_MCP_SERVERS` в значение `false`:

```bash
ENABLE_CLAUDEAI_MCP_SERVERS=false claude
```

> **Примечание:** эта функция доступна только пользователям, вошедшим через аккаунты Claude.ai.

## Процесс настройки MCP

```mermaid
sequenceDiagram
    participant User
    participant Claude as Claude Code
    participant Config as Config File
    participant Service as External Service

    User->>Claude: Type /mcp
    Claude->>Claude: List available MCP servers
    Claude->>User: Show options
    User->>Claude: Select GitHub MCP
    Claude->>Config: Update configuration
    Config->>Claude: Activate connection
    Claude->>Service: Test connection
    Service-->>Claude: Authentication successful
    Claude->>User: ✅ MCP connected!
```

## Поиск MCP tools

Когда описания MCP tools превышают 10% окна контекста, Claude Code автоматически включает tool search, чтобы эффективно выбирать нужные tools, не перегружая контекст модели.

| Параметр | Значение | Описание |
|---------|-------|-------------|
| `ENABLE_TOOL_SEARCH` | `auto` (default) | Автоматически включается, когда описания tools превышают 10% контекста |
| `ENABLE_TOOL_SEARCH` | `auto:<N>` | Автоматически включается на настраиваемом пороге в `N` tools |
| `ENABLE_TOOL_SEARCH` | `true` | Всегда включено независимо от количества tools |
| `ENABLE_TOOL_SEARCH` | `false` | Отключено; все описания tools отправляются полностью |

> **Примечание:** tool search требует Sonnet 4 или новее, либо Opus 4 или новее. Модели Haiku не поддерживаются для tool search.

## Динамические обновления tools

Claude Code поддерживает MCP `list_changed` notifications. Когда MCP server динамически добавляет, удаляет или изменяет доступные tools, Claude Code получает обновление и автоматически корректирует список tools — без переподключения и без перезапуска.

## MCP Elicitation

MCP servers могут запрашивать у пользователя структурированный ввод через интерактивные диалоги (v2.1.49+). Это позволяет MCP server запросить дополнительную информацию прямо в середине workflow — например, подтверждение, выбор из списка вариантов или заполнение обязательных полей — и добавить интерактивность в взаимодействие с MCP server.

## Лимит на описания и инструкции tools

Начиная с v2.1.84, Claude Code вводит **лимит 2 KB** на описания и инструкции tools для каждого MCP server. Это не позволяет отдельным servers слишком сильно раздувать контекст чрезмерно многословными определениями tools, уменьшая context bloat и сохраняя эффективность взаимодействий.

## MCP prompts как slash commands

MCP servers могут публиковать prompts, которые отображаются в Claude Code как slash commands. Prompts доступны по соглашению об именовании:

```
/mcp__<server>__<prompt>
```

Например, если server с именем `github` публикует prompt `review`, его можно вызвать как `/mcp__github__review`.

## Дедупликация servers

Когда один и тот же MCP server определён в нескольких scopes (local, project, user), приоритет у локальной конфигурации. Это позволяет переопределять project-level или user-level MCP settings локальными настройками без конфликтов.

Это позволяет переопределять project-level или user-level MCP settings локальными настройками без конфликтов.

## MCP Resources через @-упоминания

Вы можете ссылаться на MCP resources прямо в prompts, используя синтаксис `@`-mention:

```
@server-name:protocol://resource/path
```

Например, чтобы сослаться на конкретный resource базы данных:

```
@database:postgres://mydb/users
```

Это позволяет Claude подхватывать и включать содержимое MCP resource прямо в контекст разговора.

## Области MCP

MCP configurations можно хранить в разных scopes с различным уровнем совместного доступа:

| Область | Расположение | Описание | Доступно | Требует одобрения |
|-------|----------|-------------|-------------|------------------|
| **Local** (default) | `~/.claude.json` (under project path) | Private to current user, только текущий проект (в старых версиях называлось `project`) | Только вы | Нет |
| **Project** | `.mcp.json` | Закоммичен в git repository | Члены команды | Да (при первом использовании) |
| **User** | `~/.claude.json` | Доступно во всех проектах (в старых версиях называлось `global`) | Только вы | Нет |

### Использование project scope

Храните project-specific MCP configurations в `.mcp.json`:

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.github.com/mcp"
    }
  }
}
```

Участники команды увидят prompt на одобрение при первом использовании project MCPs.

## Управление MCP configuration

### Добавление MCP servers

```bash
# Добавить HTTP-based server
claude mcp add --transport http github https://api.github.com/mcp

# Добавить локальный stdio server
claude mcp add --transport stdio database -- npx @company/db-server

# Показать все MCP servers
claude mcp list

# Получить детали конкретного server
claude mcp get github

# Удалить MCP server
claude mcp remove github

# Сбросить project-specific approval choices
claude mcp reset-project-choices

# Импортировать из Claude Desktop
claude mcp add-from-claude-desktop
```

## Таблица доступных MCP servers

| MCP Server | Назначение | Частые tools | Auth | Real-time |
|------------|---------|--------------|------|-----------|
| **Filesystem** | Операции с файлами | read, write, delete | OS permissions | ✅ Да |
| **GitHub** | Управление repository | list_prs, create_issue, push | OAuth | ✅ Да |
| **Slack** | Коммуникация команды | send_message, list_channels | Token | ✅ Да |
| **Database** | SQL queries | query, insert, update | Credentials | ✅ Да |
| **Google Docs** | Доступ к документам | read, write, share | OAuth | ✅ Да |
| **Asana** | Управление проектами | create_task, update_status | API Key | ✅ Да |
| **Stripe** | Платёжные данные | list_charges, create_invoice | API Key | ✅ Да |
| **Memory** | Постоянная память | store, retrieve, delete | Local | ❌ Нет |

## Практические примеры

### Пример 1: конфигурация GitHub MCP

**File:** `.mcp.json` (project root)

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

**Доступные GitHub MCP tools:**

#### Управление Pull Request
- `list_prs` - List all PRs in repository
- `get_pr` - Get PR details including diff
- `create_pr` - Create new PR
- `update_pr` - Update PR description/title
- `merge_pr` - Merge PR to main branch
- `review_pr` - Add review comments

**Пример запроса:**
```
/mcp__github__get_pr 456

# Returns:
Title: Add dark mode support
Author: @alice
Description: Implements dark theme using CSS variables
Status: OPEN
Reviewers: @bob, @charlie
```

#### Управление Issue
- `list_issues` - List all issues
- `get_issue` - Get issue details
- `create_issue` - Create new issue
- `close_issue` - Close issue
- `add_comment` - Add comment to issue

#### Информация о repository
- `get_repo_info` - Repository details
- `list_files` - File tree structure
- `get_file_content` - Read file contents
- `search_code` - Search across codebase

#### Операции с commit
- `list_commits` - Commit history
- `get_commit` - Specific commit details
- `create_commit` - Create new commit

**Настройка**:
```bash
export GITHUB_TOKEN="your_github_token"
# Or use the CLI to add directly:
claude mcp add --transport stdio github -- npx @modelcontextprotocol/server-github
```

### Подстановка переменных окружения в configuration

MCP configurations поддерживают подстановку переменных окружения с fallback default-ами. Синтаксис `${VAR}` и `${VAR:-default}` работает в полях `command`, `args`, `env`, `url` и `headers`.

```json
{
  "mcpServers": {
    "api-server": {
      "type": "http",
      "url": "${API_BASE_URL:-https://api.example.com}/mcp",
      "headers": {
        "Authorization": "Bearer ${API_KEY}",
        "X-Custom-Header": "${CUSTOM_HEADER:-default-value}"
      }
    },
    "local-server": {
      "command": "${MCP_BIN_PATH:-npx}",
      "args": ["${MCP_PACKAGE:-@company/mcp-server}"],
      "env": {
        "DB_URL": "${DATABASE_URL:-postgresql://localhost/dev}"
      }
    }
  }
}
```

Переменные подставляются во время выполнения:
- `${VAR}` - использует переменную окружения, ошибка если она не задана
- `${VAR:-default}` - использует переменную окружения, а если она не задана, берёт default

### Пример 2: настройка Database MCP

**Конфигурация:**

```json
{
  "mcpServers": {
    "database": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-database"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@localhost/mydb"
      }
    }
  }
}
```

**Пример использования:**

```markdown
User: Fetch all users with more than 10 orders

Claude: I'll query your database to find that information.

# Using MCP database tool:
SELECT u.*, COUNT(o.id) as order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id
HAVING COUNT(o.id) > 10
ORDER BY order_count DESC;

# Results:
- Alice: 15 orders
- Bob: 12 orders
- Charlie: 11 orders
```

**Setup**:
```bash
export DATABASE_URL="postgresql://user:pass@localhost/mydb"
# Or use the CLI to add directly:
claude mcp add --transport stdio database -- npx @modelcontextprotocol/server-database
```

### Пример 3: Multi-MCP workflow

**Сценарий: генерация daily report**

```markdown
# Daily Report workflow с использованием нескольких MCPs

## Настройка
1. GitHub MCP - получить PR metrics
2. Database MCP - запросить sales data
3. Slack MCP - опубликовать report
4. Filesystem MCP - сохранить report

## Workflow

### Шаг 1: получить данные GitHub
/mcp__github__list_prs completed:true last:7days

Output:
- Total PRs: 42
- Average merge time: 2.3 hours
- Review turnaround: 1.1 hours

### Шаг 2: запросить database
SELECT COUNT(*) as sales, SUM(amount) as revenue
FROM orders
WHERE created_at > NOW() - INTERVAL '1 day'

Output:
- Sales: 247
- Revenue: $12,450

### Шаг 3: сгенерировать report
Собрать данные в HTML report

### Шаг 4: сохранить в Filesystem
Записать report.html в `/reports/`

### Шаг 5: отправить в Slack
Отправить summary в канал `#daily-reports`

Итоговый вывод:
✅ Report generated and posted
📊 47 PRs merged this week
💰 $12,450 in daily sales
```

**Настройка**:
```bash
export GITHUB_TOKEN="your_github_token"
export DATABASE_URL="postgresql://user:pass@localhost/mydb"
export SLACK_TOKEN="your_slack_token"
# Add each MCP server via the CLI or configure them in .mcp.json
```

### Пример 4: операции Filesystem MCP

**Configuration:**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-filesystem", "/home/user/projects"]
    }
  }
}
```

**Доступные операции:**

| Операция | Команда | Назначение |
|-----------|---------|------------|
| List files | `ls ~/projects` | Показать содержимое каталога |
| Read file | `cat src/main.ts` | Прочитать содержимое файла |
| Write file | `create docs/api.md` | Создать новый файл |
| Edit file | `edit src/app.ts` | Изменить файл |
| Search | `grep "async function"` | Искать в файлах |
| Delete | `rm old-file.js` | Удалить файл |

**Setup**:
```bash
# Use the CLI to add directly:
claude mcp add --transport stdio filesystem -- npx @modelcontextprotocol/server-filesystem /home/user/projects
```

## MCP против Memory: матрица принятия решения

```mermaid
graph TD
    A["Нужны внешние данные?"]
    A -->|Нет| B["Использовать Memory"]
    A -->|Да| C["Они часто меняются?"]
    C -->|Нет/редко| B
    C -->|Да/часто| D["Использовать MCP"]

    B -->|Хранит| E["Preferences<br/>Context<br/>History"]
    D -->|Получает доступ| F["Live APIs<br/>Databases<br/>Services"]

    style A fill:#fff3e0,stroke:#333,color:#333
    style B fill:#e1f5fe,stroke:#333,color:#333
    style C fill:#fff3e0,stroke:#333,color:#333
    style D fill:#f3e5f5,stroke:#333,color:#333
    style E fill:#e8f5e9,stroke:#333,color:#333
    style F fill:#e8f5e9,stroke:#333,color:#333
```

## Паттерн request/response

```mermaid
sequenceDiagram
    participant App as Claude
    participant MCP as MCP Server
    participant DB as Database

    App->>MCP: Request: "SELECT * FROM users WHERE id=1"
    MCP->>DB: Execute query
    DB-->>MCP: Result set
    MCP-->>App: Return parsed data
    App->>App: Process result
    App->>App: Continue task

    Note over MCP,DB: Real-time access<br/>No caching
```

## Переменные окружения

Храните чувствительные credentials в переменных окружения:

```bash
# ~/.bashrc or ~/.zshrc
export GITHUB_TOKEN="ghp_xxxxxxxxxxxxx"
export DATABASE_URL="postgresql://user:pass@localhost/mydb"
export SLACK_TOKEN="xoxb-xxxxxxxxxxxxx"
```

Затем ссылайтесь на них в MCP config:

```json
{
  "env": {
    "GITHUB_TOKEN": "${GITHUB_TOKEN}"
  }
}
```

## Claude как MCP Server (`claude mcp serve`)

Сам Claude Code может выступать как MCP server для других приложений. Это позволяет внешним tools, редакторам и системам автоматизации использовать возможности Claude через стандартный MCP protocol.

```bash
# Start Claude Code as an MCP server on stdio
claude mcp serve
```

После этого другие applications могут подключаться к этому server как к любому stdio-based MCP server. Например, чтобы добавить Claude Code как MCP server в другом экземпляре Claude Code:

```bash
claude mcp add --transport stdio claude-agent -- claude mcp serve
```

Это полезно для построения multi-agent workflows, где один экземпляр Claude orchestrates другой.

## Управляемая конфигурация MCP (Enterprise)

Для корпоративных развёртываний IT-администраторы могут принудительно задавать политики MCP server через файл `managed-mcp.json`. Этот файл даёт эксклюзивный контроль над тем, какие MCP servers разрешены или заблокированы на уровне всей организации.

**Расположение:**
- macOS: `/Library/Application Support/ClaudeCode/managed-mcp.json`
- Linux: `~/.config/ClaudeCode/managed-mcp.json`
- Windows: `%APPDATA%\ClaudeCode\managed-mcp.json`

**Возможности:**
- `allowedMcpServers` -- whitelist разрешённых servers
- `deniedMcpServers` -- blocklist запрещённых servers
- Поддерживает сопоставление по имени server, команде и URL patterns
- Организационные MCP policies применяются до пользовательской конфигурации
- Предотвращает неавторизованные подключения к servers

**Пример конфигурации:**

```json
{
  "allowedMcpServers": [
    {
      "serverName": "github",
      "serverUrl": "https://api.github.com/mcp"
    },
    {
      "serverName": "company-internal",
      "serverCommand": "company-mcp-server"
    }
  ],
  "deniedMcpServers": [
    {
      "serverName": "untrusted-*"
    },
    {
      "serverUrl": "http://*"
    }
  ]
}
```

> **Примечание:** если server совпадает и с `allowedMcpServers`, и с `deniedMcpServers`, приоритет у deny rule.

## MCP Servers, предоставляемые Plugins

Plugins могут упаковывать собственные MCP servers, делая их автоматически доступными после установки plugin. Такие MCP servers можно определить двумя способами:

1. **Standalone `.mcp.json`** -- Поместите файл `.mcp.json` в корневой каталог plugin
2. **Inline in `plugin.json`** -- Определите MCP servers прямо в manifest plugin

Используйте переменную `${CLAUDE_PLUGIN_ROOT}`, чтобы ссылаться на пути относительно каталога установки plugin:

```json
{
  "mcpServers": {
    "plugin-tools": {
      "command": "node",
      "args": ["${CLAUDE_PLUGIN_ROOT}/dist/mcp-server.js"],
      "env": {
        "CONFIG_PATH": "${CLAUDE_PLUGIN_ROOT}/config.json"
      }
    }
  }
}
```

## MCP в рамках Subagent

MCP servers можно определять прямо во frontmatter agent с помощью ключа `mcpServers:`, ограничивая их конкретным subagent вместо всего проекта. Это полезно, когда agent нужен доступ к определённому MCP server, который не требуется другим agents в workflow.

```yaml
---
mcpServers:
  my-tool:
    type: http
    url: https://my-tool.example.com/mcp
---

Вы - agent с доступом к `my-tool` для специализированных операций.
```

MCP servers, ограниченные subagent, доступны только в контексте выполнения этого agent и не разделяются с parent или sibling agents.

## Ограничения на MCP output

Claude Code вводит ограничения на output MCP tools, чтобы предотвратить переполнение контекста:

| Ограничение | Порог | Поведение |
|-------|-----------|----------|
| **Warning** | 10,000 tokens | Показывается предупреждение, что output большой |
| **Default max** | 25,000 tokens | Output обрезается после этого лимита |
| **Disk persistence** | 50,000 characters | Результаты tools, превышающие 50K символов, сохраняются на диск |

Максимальный лимит output настраивается через переменную окружения `MAX_MCP_OUTPUT_TOKENS`:

```bash
# Increase the max output to 50,000 tokens
export MAX_MCP_OUTPUT_TOKENS=50000
```

## Решение context bloat через code execution

По мере роста использования MCP подключение к десяткам servers с сотнями или тысячами tools создаёт серьёзную проблему: **context bloat**. Пожалуй, это самая большая проблема MCP на масштабе, и engineering team Anthropic предложила элегантное решение — использовать code execution вместо прямых tool calls.

> **Источник**: [Code Execution with MCP: Building More Efficient Agents](https://www.anthropic.com/engineering/code-execution-with-mcp) — Anthropic Engineering Blog

### Проблема: два источника token waste

**1. Определения tools перегружают окно контекста**

Большинство MCP clients загружают все определения tools заранее. При подключении к тысячам tools модель должна обработать сотни тысяч tokens ещё до того, как прочитает запрос пользователя.

**2. Промежуточные результаты потребляют дополнительные tokens**

Каждый промежуточный результат tool проходит через контекст модели. Представьте перенос стенограммы встречи из Google Drive в Salesforce — полная стенограмма проходит через контекст **дважды**: при чтении и при записи в destination. Стенограмма двухчасовой встречи может добавить 50,000+ лишних tokens.

```mermaid
graph LR
    A["Model"] -->|"Tool Call: getDocument"| B["MCP Server"]
    B -->|"Full transcript (50K tokens)"| A
    A -->|"Tool Call: updateRecord<br/>(re-sends full transcript)"| B
    B -->|"Confirmation"| A

    style A fill:#ffcdd2,stroke:#333,color:#333
    style B fill:#f3e5f5,stroke:#333,color:#333
```

### Решение: MCP Tools как Code APIs

Вместо того чтобы проводить определения tools и результаты через окно контекста, agent **пишет code**, который вызывает MCP tools как APIs. Код выполняется в sandboxed execution environment, а модели возвращается только итоговый результат.

```mermaid
graph LR
    A["Model"] -->|"Writes code"| B["Code Execution<br/>Environment"]
    B -->|"Calls tools directly"| C["MCP Servers"]
    C -->|"Data stays in<br/>execution env"| B
    B -->|"Only final result<br/>(minimal tokens)"| A

    style A fill:#c8e6c9,stroke:#333,color:#333
    style B fill:#e1f5fe,stroke:#333,color:#333
    style C fill:#f3e5f5,stroke:#333,color:#333
```

#### Как это работает

MCP tools are presented as a file tree of typed functions:

```
servers/
├── google-drive/
│   ├── getDocument.ts
│   └── index.ts
├── salesforce/
│   ├── updateRecord.ts
│   └── index.ts
└── ...
```

Each tool file contains a typed wrapper:

```typescript
// ./servers/google-drive/getDocument.ts
import { callMCPTool } from "../../../client.js";

interface GetDocumentInput {
  documentId: string;
}

interface GetDocumentResponse {
  content: string;
}

export async function getDocument(
  input: GetDocumentInput
): Promise<GetDocumentResponse> {
  return callMCPTool<GetDocumentResponse>(
    'google_drive__get_document', input
  );
}
```

Затем agent пишет code, который оркестрирует tools:

```typescript
import * as gdrive from './servers/google-drive';
import * as salesforce from './servers/salesforce';

// Data flows directly between tools — never through the model
const transcript = (
  await gdrive.getDocument({ documentId: 'abc123' })
).content;

await salesforce.updateRecord({
  objectType: 'SalesMeeting',
  recordId: '00Q5f000001abcXYZ',
  data: { Notes: transcript }
});
```

**Результат: использование token-ов падает примерно с 150,000 до 2,000 — сокращение на 98.7%.**

### Ключевые преимущества

| Преимущество | Описание |
|---------|-------------|
| **Progressive Disclosure** | Agent просматривает filesystem и загружает только те определения tools, которые ему нужны, а не все сразу |
| **Context-Efficient Results** | Данные фильтруются и преобразуются в execution environment до возврата модели |
| **Powerful Control Flow** | Циклы, условные конструкции и обработка ошибок выполняются в code без лишних round-trips через модель |
| **Privacy Preservation** | Промежуточные данные (PII, чувствительные записи) остаются в execution environment; они не попадают в model context |
| **State Persistence** | Agents могут сохранять промежуточные результаты в файлы и строить переиспользуемые skill functions |

#### Example: Filtering Large Datasets

```typescript
// Without code execution — all 10,000 rows flow through context
// TOOL CALL: gdrive.getSheet(sheetId: 'abc123')
//   -> returns 10,000 rows in context

// With code execution — filter in the execution environment
const allRows = await gdrive.getSheet({ sheetId: 'abc123' });
const pendingOrders = allRows.filter(
  row => row["Status"] === 'pending'
);
console.log(`Found ${pendingOrders.length} pending orders`);
console.log(pendingOrders.slice(0, 5)); // Only 5 rows reach the model
```

#### Example: Loop Without Round-Tripping

```typescript
// Poll for a deployment notification — runs entirely in code
let found = false;
while (!found) {
  const messages = await slack.getChannelHistory({
    channel: 'C123456'
  });
  found = messages.some(
    m => m.text.includes('deployment complete')
  );
  if (!found) await new Promise(r => setTimeout(r, 5000));
}
console.log('Deployment notification received');
```

### Компромиссы, которые стоит учитывать

Code execution добавляет собственную сложность. Для запуска кода, сгенерированного agent, требуется:

- **Безопасная sandboxed execution environment** с подходящими лимитами ресурсов
- **Monitoring и logging** исполняемого кода
- Дополнительные **infrastructure overhead** по сравнению с прямыми tool calls

Преимущества — меньшая стоимость token-ов, меньшая latency, лучшая композиция tools — нужно сопоставлять с этими затратами на реализацию. Для agents с несколькими MCP servers прямые tool calls могут быть проще. Для agents на масштабе (десятки servers, сотни tools) code execution даёт заметное улучшение.

### MCPorter: runtime для композиции MCP tools

[MCPorter](https://github.com/steipete/mcporter) — это TypeScript runtime и CLI toolkit, который делает вызов MCP servers практичным без boilerplate и помогает уменьшить context bloat за счёт выборочной экспозиции tools и typed wrappers.

**Что это решает:** вместо загрузки всех определений tools со всех MCP servers сразу MCPorter позволяет находить, просматривать и вызывать конкретные tools по требованию — сохраняя контекст лёгким.

**Ключевые возможности:**

| Возможность | Описание |
|---------|-------------|
| **Zero-config discovery** | Автоматически находит MCP servers из Cursor, Claude, Codex или локальных configs |
| **Typed tool clients** | `mcporter emit-ts` генерирует `.d.ts` interfaces и готовые к запуску wrappers |
| **Composable API** | `createServerProxy()` открывает tools как методы в camelCase с helper-ами `.text()`, `.json()`, `.markdown()` |
| **CLI generation** | `mcporter generate-cli` превращает любой MCP server в отдельный CLI с фильтрацией `--include-tools` / `--exclude-tools` |
| **Parameter hiding** | Необязательные параметры по умолчанию скрыты, что уменьшает verbosity схемы |

**Установка:**

```bash
npx mcporter list          # No install required — discover servers instantly
pnpm add mcporter          # Add to a project
brew install steipete/tap/mcporter  # macOS via Homebrew
```

**Пример — композиция tools в TypeScript:**

```typescript
import { createRuntime, createServerProxy } from "mcporter";

const runtime = await createRuntime();
const gdrive = createServerProxy(runtime, "google-drive");
const salesforce = createServerProxy(runtime, "salesforce");

// Data flows between tools without passing through the model context
const doc = await gdrive.getDocument({ documentId: "abc123" });
await salesforce.updateRecord({
  objectType: "SalesMeeting",
  recordId: "00Q5f000001abcXYZ",
  data: { Notes: doc.text() }
});
```

**Пример — вызов tool через CLI:**

```bash
# Вызвать конкретный tool напрямую
npx mcporter call linear.create_comment issueId:ENG-123 body:'Looks good!'

# Показать доступные servers и tools
npx mcporter list
```

MCPorter дополняет описанный выше подход code execution, предоставляя runtime infrastructure для вызова MCP tools как typed APIs, что упрощает удержание промежуточных данных вне model context.

## Лучшие практики

### Соображения по безопасности

#### Делайте ✅
- Используйте переменные окружения для всех credentials
- Регулярно обновляйте tokens и API keys (рекомендуется раз в месяц)
- По возможности используйте read-only tokens
- Ограничивайте доступ MCP server минимально необходимой областью
- Следите за использованием MCP server и журналами доступа
- Используйте OAuth для внешних сервисов, когда это возможно
- Вводите rate limiting для MCP requests
- Тестируйте MCP connections перед production use
- Документируйте все активные MCP connections
- Поддерживайте MCP server packages в актуальном состоянии

#### Не делайте ❌
- Не встраивайте credentials прямо в config files
- Не коммитьте tokens или secrets в git
- Не передавайте tokens в командных чатах или письмах
- Не используйте personal tokens для team projects
- Не выдавайте лишние permissions
- Не игнорируйте authentication errors
- Не публикуйте MCP endpoints наружу
- Не запускайте MCP servers с root/admin privileges
- Не кэшируйте sensitive data в logs
- Не отключайте authentication mechanisms

### Лучшие практики конфигурации

1. **Version Control**: Храните `.mcp.json` в git, но для secrets используйте переменные окружения
2. **Least Privilege**: Выдавайте минимум permissions, необходимых каждому MCP server
3. **Isolation**: По возможности запускайте разные MCP servers в отдельных processes
4. **Monitoring**: Логируйте все MCP requests и errors для audit trails
5. **Testing**: Тестируйте все MCP configurations перед deploy в production

### Советы по производительности

- Кэшируйте часто запрашиваемые данные на уровне application
- Используйте более конкретные MCP queries, чтобы уменьшить передачу данных
- Следите за временем ответа MCP operations
- Рассмотрите rate limiting для внешних APIs
- Используйте batching при выполнении нескольких операций

## Инструкции по установке

### Предварительные требования
- Node.js and npm installed
- Claude Code CLI installed
- API tokens/credentials for external services

### Пошаговая настройка

1. **Добавьте ваш первый MCP server** через CLI (пример: GitHub):
```bash
claude mcp add --transport stdio github -- npx @modelcontextprotocol/server-github
```

   Или создайте файл `.mcp.json` в корне проекта:
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

2. **Установите переменные окружения:**
```bash
export GITHUB_TOKEN="your_github_personal_access_token"
```

3. **Проверьте соединение:**
```bash
claude /mcp
```

4. **Используйте MCP tools:**
```bash
/mcp__github__list_prs
/mcp__github__create_issue "Title" "Description"
```

### Установка для конкретных сервисов

**GitHub MCP:**
```bash
npm install -g @modelcontextprotocol/server-github
```

**Database MCP:**
```bash
npm install -g @modelcontextprotocol/server-database
```

**Filesystem MCP:**
```bash
npm install -g @modelcontextprotocol/server-filesystem
```

**Slack MCP:**
```bash
npm install -g @modelcontextprotocol/server-slack
```

## Устранение неполадок

### MCP Server не найден
```bash
# Проверить, что MCP server установлен
npm list -g @modelcontextprotocol/server-github

# Установить, если отсутствует
npm install -g @modelcontextprotocol/server-github
```

### Сбой аутентификации
```bash
# Проверить, что переменная окружения задана
echo $GITHUB_TOKEN

# Повторно экспортировать при необходимости
export GITHUB_TOKEN="your_token"

# Проверить, что token имеет правильные permissions
# Check GitHub token scopes at: https://github.com/settings/tokens
```

### Тайм-аут соединения
- Check network connectivity: `ping api.github.com`
- Verify API endpoint is accessible
- Check rate limits on API
- Try increasing timeout in config
- Check for firewall or proxy issues

### Сбой MCP Server
- Check MCP server logs: `~/.claude/logs/`
- Verify all environment variables are set
- Ensure proper file permissions
- Try reinstalling the MCP server package
- Check for conflicting processes on the same port

## Связанные концепции

### Memory против MCP
- **Memory**: Хранит постоянные, неизменные данные (preferences, context, history)
- **MCP**: Получает доступ к живым, изменяющимся данным (APIs, databases, real-time services)

### Когда что использовать
- **Use Memory** для: user preferences, conversation history, learned context
- **Use MCP** для: current GitHub issues, live database queries, real-time data

### Интеграция с другими возможностями Claude
- Комбинируйте MCP с Memory для richer context
- Используйте MCP tools в prompts для более качественного reasoning
- Применяйте несколько MCP для complex workflows

## Дополнительные ресурсы

- [Официальная документация MCP](https://code.claude.com/docs/en/mcp)
- [Спецификация MCP Protocol](https://modelcontextprotocol.io/specification)
- [GitHub-репозиторий MCP](https://github.com/modelcontextprotocol/servers)
- [Доступные MCP Servers](https://github.com/modelcontextprotocol/servers)
- [MCPorter](https://github.com/steipete/mcporter) — TypeScript runtime и CLI для вызова MCP servers без boilerplate
- [Code Execution with MCP](https://www.anthropic.com/engineering/code-execution-with-mcp) — инженерный блог Anthropic о решении context bloat
- [Справка по Claude Code CLI](https://code.claude.com/docs/en/cli-reference)
- [Документация Claude API](https://docs.anthropic.com)
