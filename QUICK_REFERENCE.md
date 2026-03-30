<picture>
  <source media="(prefers-color-scheme: dark)" srcset="resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="resources/logos/claude-howto-logo.svg">
</picture>

# Claude Code Examples - Краткая справка

## 🚀 Быстрые команды установки

### Slash-команды
```bash
# Установить всё
cp 01-slash-commands/*.md .claude/commands/

# Установить конкретную команду
cp 01-slash-commands/optimize.md .claude/commands/
```

### Memory
```bash
# Память проекта
cp 02-memory/project-CLAUDE.md ./CLAUDE.md

# Личная память
cp 02-memory/personal-CLAUDE.md ~/.claude/CLAUDE.md
```

### Skills
```bash
# Личные skills
cp -r 03-skills/code-review ~/.claude/skills/

# Skills проекта
cp -r 03-skills/code-review .claude/skills/
```

### Subagents
```bash
# Установить всё
cp 04-subagents/*.md .claude/agents/

# Установить конкретного подагента
cp 04-subagents/code-reviewer.md .claude/agents/
```

### MCP
```bash
# Задать учётные данные
export GITHUB_TOKEN="your_token"
export DATABASE_URL="postgresql://..."

# Установить конфиг (область проекта)
cp 05-mcp/github-mcp.json .mcp.json

# Или в пользовательской области: добавьте в ~/.claude.json
```

### Hooks
```bash
# Установить hooks
mkdir -p ~/.claude/hooks
cp 06-hooks/*.sh ~/.claude/hooks/
chmod +x ~/.claude/hooks/*.sh

# Настроить в settings (~/.claude/settings.json)
```

### Plugins
```bash
# Установить из примеров (если опубликованы)
/plugin install pr-review
/plugin install devops-automation
/plugin install documentation
```

### Checkpoints
```bash
# Checkpoints создаются автоматически для каждого пользовательского запроса
# Чтобы откатиться, нажмите Esc дважды или используйте:
/rewind

# Затем выберите: восстановить код и разговор, восстановить разговор,
# восстановить код, суммировать отсюда или не обращать внимания
```

### Продвинутые возможности
```bash
# Настройка в settings (.claude/settings.json)
# См. 09-advanced-features/config-examples.json

# Режим планирования
/plan Описание задачи

# Режимы разрешений (используйте флаг `--permission-mode`)
# default          - Запрашивает подтверждение для рискованных действий
# acceptEdits      - Автоматически принимает правки файлов, спрашивает для остальных
# plan             - Только чтение, без изменений
# dontAsk          - Принимает все действия, кроме рискованных
# auto             - Фоновый классификатор сам определяет разрешения
# bypassPermissions - Принимает все действия (требует `--dangerously-skip-permissions`)

# Управление сессиями
/resume                # Возобновить предыдущий разговор
/rename "name"         # Назвать текущую сессию
/fork                  # Разветвить текущую сессию
claude -c              # Продолжить самый последний разговор
claude -r "session"    # Возобновить сессию по имени/ID
```

---

## 📋 Сводка по возможностям

| Возможность | Путь установки | Использование |
|---------|-------------|-------|
| **Slash-команды (55+)** | `.claude/commands/*.md` | `/command-name` |
| **Memory** | `./CLAUDE.md` | Загружается автоматически |
| **Skills** | `.claude/skills/*/SKILL.md` | Запускаются автоматически |
| **Subagents** | `.claude/agents/*.md` | Делегируются автоматически |
| **MCP** | `.mcp.json` (project) или `~/.claude.json` (user) | `/mcp__server__action` |
| **Hooks (25 событий)** | `~/.claude/hooks/*.sh` | Срабатывают по событиям (4 типа) |
| **Plugins** | Через `/plugin install` | Собирают всё вместе |
| **Checkpoints** | Встроено | `Esc+Esc` или `/rewind` |
| **Режим планирования** | Встроено | `/plan <task>` |
| **Режимы разрешений (6)** | Встроено | `--allowedTools`, `--permission-mode` |
| **Сессии** | Встроено | `/session <command>` |
| **Background Tasks** | Встроено | Выполняются в фоне |
| **Удалённое управление** | Встроено | WebSocket API |
| **Веб-сессии** | Встроено | `claude web` |
| **Git Worktrees** | Встроено | `/worktree` |
| **Автопамять** | Встроено | Автосохраняется в CLAUDE.md |
| **Список задач** | Встроено | `/task list` |
| **Встроенные skills (5)** | Встроено | `/simplify`, `/loop`, `/claude-api`, `/voice`, `/browse` |

---

## 🎯 Частые сценарии использования

### Ревью кода
```bash
# Способ 1: slash-команда
cp 01-slash-commands/optimize.md .claude/commands/
# Использование: /optimize

# Способ 2: subagent
cp 04-subagents/code-reviewer.md .claude/agents/
# Использование: авто-делегирование

# Способ 3: skill
cp -r 03-skills/code-review ~/.claude/skills/
# Использование: автоматический запуск

# Способ 4: plugin (полное решение)
/plugin install pr-review
# Использование: /review-pr
```

### Документация
```bash
# Slash-команда
cp 01-slash-commands/generate-api-docs.md .claude/commands/

# Subagent
cp 04-subagents/documentation-writer.md .claude/agents/

# Skill
cp -r 03-skills/doc-generator ~/.claude/skills/

# Plugin (полное решение)
/plugin install documentation
```

### DevOps
```bash
# Полный plugin
/plugin install devops-automation

# Команды: /deploy, /rollback, /status, /incident
```

### Стандарты команды
```bash
# Память проекта
cp 02-memory/project-CLAUDE.md ./CLAUDE.md

# Отредактируйте под свою команду
vim CLAUDE.md
```

### Автоматизация и hooks
```bash
# Установить hooks (25 событий, 4 типа: command, http, prompt, agent)
mkdir -p ~/.claude/hooks
cp 06-hooks/*.sh ~/.claude/hooks/
chmod +x ~/.claude/hooks/*.sh

# Примеры:
# - Pre-commit tests: pre-commit.sh
# - Auto-format code: format-code.sh
# - Security scanning: security-scan.sh

# Режим auto для полностью автономных workflows
claude --enable-auto-mode -p "Refactor and test the auth module"
# Или переключайте режимы интерактивно через Shift+Tab
```

### Безопасный рефакторинг
```bash
# Checkpoints создаются автоматически перед каждым запросом
# Попробуйте рефакторинг
# Если всё работает: продолжайте
# Если нет: нажмите Esc+Esc или используйте /rewind, чтобы откатиться
```

### Сложная реализация
```bash
# Используйте режим планирования
/plan Implement user authentication system

# Claude создаёт детальный план
# Проверьте и одобрите
# Claude реализует всё пошагово
```

### Интеграция CI/CD
```bash
# Запуск в headless-режиме (без интерактива)
claude -p "Run all tests and generate report"

# С режимом разрешений для CI
claude -p "Run tests" --permission-mode dontAsk

# С режимом auto для полностью автономных CI-задач
claude --enable-auto-mode -p "Run tests and fix failures"

# С hooks для автоматизации
# См. 09-advanced-features/README.md
```

### Обучение и эксперименты
```bash
# Используйте режим plan для безопасного анализа
claude --permission-mode plan

# Экспериментируйте безопасно - checkpoints создаются автоматически
# Если нужно откатиться: нажмите Esc+Esc или используйте /rewind
```

### Команды агентов
```bash
# Включить команды агентов
export CLAUDE_AGENT_TEAMS=1

# Или в settings.json
{ "agentTeams": { "enabled": true } }

# Начните с: "Реализуй функцию X командным подходом"
```

### Запланированные задачи
```bash
# Запускать команду каждые 5 минут
/loop 5m /check-status

# Одноразовое напоминание
/loop 30m "remind me to check the deploy"
```

---

## 📁 Справка по расположению файлов

```
Ваш проект/
├── .claude/
│   ├── commands/              # Здесь находятся slash-команды
│   ├── agents/                # Здесь находятся subagents
│   ├── skills/                # Здесь находятся project skills
│   └── settings.json          # Настройки проекта (hooks и т. д.)
├── .mcp.json                  # Конфигурация MCP (область проекта)
├── CLAUDE.md                  # Память проекта
└── src/
    └── api/
        └── CLAUDE.md          # Память для конкретного каталога

Домашняя папка пользователя/
├── .claude/
│   ├── commands/              # Личные команды
│   ├── agents/                # Личные агенты
│   ├── skills/                # Личные skills
│   ├── hooks/                 # Hook-скрипты
│   ├── settings.json          # Настройки пользователя
│   ├── managed-settings.d/    # Управляемые настройки (enterprise/org)
│   └── CLAUDE.md              # Личная память
└── .claude.json               # Личная конфигурация MCP (область пользователя)
```

---

## 🔍 Поиск примеров

### По категориям
- **Slash Commands**: `01-slash-commands/`
- **Memory**: `02-memory/`
- **Skills**: `03-skills/`
- **Subagents**: `04-subagents/`
- **MCP**: `05-mcp/`
- **Hooks**: `06-hooks/`
- **Plugins**: `07-plugins/`
- **Checkpoints**: `08-checkpoints/`
- **Advanced Features**: `09-advanced-features/`
- **CLI**: `10-cli/`

### По сценарию использования
- **Performance**: `01-slash-commands/optimize.md`
- **Security**: `04-subagents/secure-reviewer.md`
- **Testing**: `04-subagents/test-engineer.md`
- **Docs**: `03-skills/doc-generator/`
- **DevOps**: `07-plugins/devops-automation/`

### По сложности
- **Простые**: slash-команды
- **Средние**: subagents, memory
- **Продвинутые**: skills, hooks
- **Полные**: plugins

---

## 🎓 Путь обучения

### День 1
```bash
# Прочитать обзор
cat README.md

# Установить команду
cp 01-slash-commands/optimize.md .claude/commands/

# Попробовать
/optimize
```

### День 2-3
```bash
# Настроить memory
cp 02-memory/project-CLAUDE.md ./CLAUDE.md
vim CLAUDE.md

# Установить subagent
cp 04-subagents/code-reviewer.md .claude/agents/
```

### День 4-5
```bash
# Настроить MCP
export GITHUB_TOKEN="your_token"
cp 05-mcp/github-mcp.json .mcp.json

# Попробовать команды MCP
/mcp__github__list_prs
```

### Неделя 2
```bash
# Установить skill
cp -r 03-skills/code-review ~/.claude/skills/

# Пусть он вызывается автоматически
# Просто скажите: "Проверь этот код на проблемы"
```

### Неделя 3+
```bash
# Установить полный plugin
/plugin install pr-review

# Использовать встроенные возможности
/review-pr
/check-security
/check-tests
```

---

## Новые возможности (март 2026)

| Возможность | Описание | Использование |
|---------|-------------|-------|
| **Auto Mode** | Полностью автономная работа с фоновым классификатором | флаг `--enable-auto-mode`, `Shift+Tab` для переключения режимов |
| **Channels** | Интеграция с Discord и Telegram | флаг `--channels`, боты Discord/Telegram |
| **Voice Dictation** | Произносите команды и контекст для Claude | команда `/voice` |
| **Hooks (25 events)** | Расширенная система hooks с 4 типами | типы hooks: command, http, prompt, agent |
| **MCP Elicitation** | MCP-серверы могут запрашивать ввод пользователя во время выполнения | Автоматический запрос, когда серверу нужна уточняющая информация |
| **WebSocket MCP** | WebSocket-транспорт для MCP-подключений | Настраивается в `.mcp.json` с URL `ws://` |
| **Plugin LSP** | Поддержка Language Server Protocol для плагинов | `userConfig`, переменная `${CLAUDE_PLUGIN_DATA}` |
| **Remote Control** | Управление Claude Code через WebSocket API | `claude --remote` для внешних интеграций |
| **Web Sessions** | Браузерный интерфейс Claude Code | запуск через `claude web` |
| **Desktop App** | Нативное desktop-приложение | Скачать с claude.ai/download |
| **Task List** | Управление фоновыми задачами | `/task list`, `/task status <id>` |
| **Auto Memory** | Автоматическое сохранение памяти из бесед | Claude автоматически сохраняет ключевой контекст в `CLAUDE.md` |
| **Git Worktrees** | Изолированные рабочие пространства для параллельной разработки | `/worktree` для создания изолированного workspace |
| **Model Selection** | Переключение между Sonnet 4.6 и Opus 4.6 | `/model` или флаг `--model` |
| **Agent Teams** | Координация нескольких агентов над задачами | Включается переменной окружения `CLAUDE_AGENT_TEAMS=1` |
| **Scheduled Tasks** | Повторяющиеся задачи через `/loop` | `/loop 5m /command` или инструмент CronCreate |
| **Chrome Integration** | Автоматизация браузера | флаг `--chrome` или команда `/chrome` |
| **Keyboard Customization** | Пользовательские горячие клавиши | команда `/keybindings` |

---

## Советы и приёмы

### Кастомизация
- Начинайте с примеров как есть
- Подстраивайте под свои потребности
- Проверяйте перед тем, как делиться с командой
- Храните конфигурации в version control

### Лучшие практики
- Используйте memory для стандартов команды
- Используйте plugins для полноценных workflows
- Используйте subagents для сложных задач
- Используйте slash-команды для быстрых задач

### Устранение неполадок
```bash
# Проверить расположение файлов
ls -la .claude/commands/
ls -la .claude/agents/

# Проверить синтаксис YAML
head -20 .claude/agents/code-reviewer.md

# Проверить подключение MCP
echo $GITHUB_TOKEN
```

---

## 📊 Матрица возможностей

| Что нужно | Используйте это | Пример |
|------|----------|---------|
| Быстрый ярлык | Slash Command (55+) | `01-slash-commands/optimize.md` |
| Стандарты команды | Memory | `02-memory/project-CLAUDE.md` |
| Автоматический workflow | Skill | `03-skills/code-review/` |
| Специализированная задача | Subagent | `04-subagents/code-reviewer.md` |
| Внешние данные | MCP (+ Elicitation, WebSocket) | `05-mcp/github-mcp.json` |
| Автоматизация по событиям | Hook (25 событий, 4 типа) | `06-hooks/pre-commit.sh` |
| Полное решение | Plugin (+ поддержка LSP) | `07-plugins/pr-review/` |
| Безопасный эксперимент | Checkpoint | `08-checkpoints/checkpoint-examples.md` |
| Полная автономность | Auto Mode | `--enable-auto-mode` или `Shift+Tab` |
| Чат-интеграции | Channels | `--channels` (Discord, Telegram) |
| CI/CD pipeline | CLI | `10-cli/README.md` |

---

## 🔗 Быстрые ссылки

- **Основной гайд**: `README.md`
- **Полный индекс**: `INDEX.md`
- **Сводка**: `EXAMPLES_SUMMARY.md`
- **Исходный гайд**: `claude_concepts_guide.md`

---

## 📞 Частые вопросы

**Q: Что использовать?**
A: Начните со slash-команд и добавляйте возможности по мере необходимости.

**Q: Можно ли смешивать возможности?**
A: Да. Они работают вместе. Memory + Commands + MCP = мощная комбинация.

**Q: Как делиться с командой?**
A: Закоммитьте каталог `.claude/` в git.

**Q: Что насчёт секретов?**
A: Используйте переменные окружения; никогда не хардкодьте их.

**Q: Можно изменять примеры?**
A: Да. Это шаблоны для настройки.

---

## ✅ Чеклист

Чеклист для начала работы:

- [ ] Прочитать `README.md`
- [ ] Установить 1 slash-команду
- [ ] Попробовать команду
- [ ] Создать проектный `CLAUDE.md`
- [ ] Установить 1 subagent
- [ ] Настроить 1 MCP-интеграцию
- [ ] Установить 1 skill
- [ ] Попробовать полный plugin
- [ ] Настроить под свои нужды
- [ ] Поделиться с командой

---

**Быстрый старт**: `cat README.md`

**Полный индекс**: `cat INDEX.md`

**Эта карточка**: держите под рукой для быстрой справки!
