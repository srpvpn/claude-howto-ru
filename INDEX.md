<picture>
  <source media="(prefers-color-scheme: dark)" srcset="resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="resources/logos/claude-howto-logo.svg">
</picture>

# Claude Code Examples - Полный индекс

Этот документ даёт полный индекс всех файлов с примерами, сгруппированных по типам функций.

## Сводка

- **Всего файлов**: 100+
- **Категорий**: 10
- **Plugins**: 3 полноценных plugin-пакета
- **Skills**: 6 полноценных skills
- **Hooks**: 8 примеров hooks
- **Готово к использованию**: все примеры

---

## 01. Slash Commands (10 файлов)

Ярлыки для запуска частых рабочих процессов.

| Файл | Описание | Сценарий использования |
|------|-------------|------------------------|
| `optimize.md` | Анализатор оптимизации кода | Поиск проблем с производительностью |
| `pr.md` | Подготовка pull request | Автоматизация PR-процесса |
| `generate-api-docs.md` | Генератор документации API | Создание API docs |
| `commit.md` | Помощник по сообщениям коммитов | Стандартизированные коммиты |
| `setup-ci-cd.md` | Настройка CI/CD pipeline | Автоматизация DevOps |
| `push-all.md` | Отправка всех изменений | Быстрый push-workflow |
| `unit-test-expand.md` | Расширение покрытия unit-тестами | Автоматизация тестов |
| `doc-refactor.md` | Рефакторинг документации | Улучшение docs |
| `pr-slash-command.png` | Пример скриншота | Визуальная справка |
| `README.md` | Документация | Руководство по установке и использованию |

**Путь установки**: `.claude/commands/`

**Использование**: `/optimize`, `/pr`, `/generate-api-docs`, `/commit`, `/setup-ci-cd`, `/push-all`, `/unit-test-expand`, `/doc-refactor`

---

## 02. Memory (6 файлов)

Постоянный контекст и стандарты проекта.

| Файл | Описание | Область | Расположение |
|------|-------------|---------|--------------|
| `project-CLAUDE.md` | Стандарты командного проекта | Весь проект | `./CLAUDE.md` |
| `directory-api-CLAUDE.md` | Правила для API-части | Каталог | `./src/api/CLAUDE.md` |
| `personal-CLAUDE.md` | Личные предпочтения | Пользователь | `~/.claude/CLAUDE.md` |
| `memory-saved.png` | Скриншот: memory saved | - | Визуальная справка |
| `memory-ask-claude.png` | Скриншот: ask Claude | - | Визуальная справка |
| `README.md` | Документация | - | Справка |

**Установка**: копируйте в нужное место

**Использование**: загружается Claude автоматически

---

## 03. Skills (28 файлов)

Возможности, которые запускаются автоматически, со скриптами и шаблонами.

### Code Review Skill (5 файлов)
```
code-review/
├── SKILL.md                          # Описание skill
├── scripts/
│   ├── analyze-metrics.py            # Анализатор метрик кода
│   └── compare-complexity.py         # Сравнение сложности
└── templates/
    ├── review-checklist.md           # Чеклист ревью
    └── finding-template.md           # Шаблон найденной проблемы
```

**Назначение**: комплексный code review с анализом security, производительности и качества

**Автозапуск**: при ревью кода

### Brand Voice Skill (4 файла)
```
brand-voice/
├── SKILL.md                          # Описание skill
├── templates/
│   ├── email-template.txt            # Формат письма
│   └── social-post-template.txt      # Формат поста для соцсетей
└── tone-examples.md                  # Примеры сообщений
```

**Назначение**: поддержание единого tone of voice в коммуникациях

**Автозапуск**: при создании маркетинговых текстов

### Documentation Generator Skill (2 файла)
```
doc-generator/
├── SKILL.md                          # Описание skill
└── generate-docs.py                  # Python-генератор docs
```

**Назначение**: генерация подробной API-документации из исходного кода

**Автозапуск**: при создании или обновлении API docs

### Refactor Skill (5 файлов)
```
refactor/
├── SKILL.md                          # Описание skill
├── scripts/
│   ├── analyze-complexity.py         # Анализатор сложности
│   └── detect-smells.py              # Поиск code smells
├── references/
│   ├── code-smells.md                # Каталог code smells
│   └── refactoring-catalog.md        # Паттерны рефакторинга
└── templates/
    └── refactoring-plan.md           # Шаблон плана рефакторинга
```

**Назначение**: системный рефакторинг кода с анализом сложности

**Автозапуск**: при рефакторинге

### Claude MD Skill (1 файл)
```
claude-md/
└── SKILL.md                          # Описание skill
```

**Назначение**: управление и оптимизация файлов CLAUDE.md

### Blog Draft Skill (3 файла)
```
blog-draft/
├── SKILL.md                          # Описание skill
└── templates/
    ├── draft-template.md             # Шаблон черновика статьи
    └── outline-template.md           # Шаблон структуры статьи
```

**Назначение**: подготовка блог-постов со стабильной структурой

**Плюс**: `README.md` - обзор skills и руководство по использованию

**Путь установки**: `~/.claude/skills/` или `.claude/skills/`

---

## 04. Subagents (9 файлов)

Специализированные AI-ассистенты с собственными возможностями.

| Файл | Описание | Инструменты | Сценарий использования |
|------|-------------|-------------|------------------------|
| `code-reviewer.md` | Анализ качества кода | read, grep, diff, lint_runner | Полноценные ревью |
| `test-engineer.md` | Анализ покрытия тестами | read, write, bash, grep | Автоматизация тестов |
| `documentation-writer.md` | Создание документации | read, write, grep | Генерация docs |
| `secure-reviewer.md` | Security review (только чтение) | read, grep | Security-аудит |
| `implementation-agent.md` | Полная реализация | read, write, bash, grep, edit, glob | Разработка функций |
| `debugger.md` | Специалист по отладке | read, bash, grep | Поиск причин багов |
| `data-scientist.md` | Специалист по анализу данных | read, write, bash | Data workflows |
| `clean-code-reviewer.md` | Стандарты clean code | read, grep | Качество кода |
| `README.md` | Документация | - | Руководство по установке и использованию |

**Путь установки**: `.claude/agents/`

**Использование**: автоматически делегируются главным агентом

---

## 05. MCP Protocol (5 файлов)

Интеграции с внешними инструментами и API.

| Файл | Описание | Интеграция с | Сценарий использования |
|------|-------------|--------------|------------------------|
| `github-mcp.json` | Интеграция с GitHub | GitHub API | Управление PR/issue |
| `database-mcp.json` | Запросы к базе данных | PostgreSQL/MySQL | Живые запросы к данным |
| `filesystem-mcp.json` | Операции с файлами | Локальная файловая система | Управление файлами |
| `multi-mcp.json` | Несколько серверов | GitHub + DB + Slack | Комплексная интеграция |
| `README.md` | Документация | - | Руководство по установке и использованию |

**Путь установки**: `.mcp.json` (project scope) или `~/.claude.json` (user scope)

**Использование**: `/mcp__github__list_prs` и т. п.

---

## 06. Hooks (9 файлов)

Скрипты автоматизации, которые запускаются по событиям.

| Файл | Описание | Событие | Сценарий использования |
|------|-------------|---------|------------------------|
| `format-code.sh` | Автоформатирование кода | PreToolUse:Write | Форматирование |
| `pre-commit.sh` | Запуск тестов перед коммитом | PreToolUse:Bash | Автоматизация тестов |
| `security-scan.sh` | Проверка безопасности | PostToolUse:Write | Security checks |
| `log-bash.sh` | Логирование bash-команд | PostToolUse:Bash | Учёт команд |
| `validate-prompt.sh` | Проверка prompt'ов | PreToolUse | Валидация ввода |
| `notify-team.sh` | Отправка уведомлений | Notification | Уведомления команде |
| `context-tracker.py` | Учёт использования окна контекста | PostToolUse | Мониторинг контекста |
| `context-tracker-tiktoken.py` | Точный учёт токенов | PostToolUse | Подсчёт токенов |
| `README.md` | Документация | - | Руководство по установке и использованию |

**Путь установки**: настройка в `~/.claude/settings.json`

**Использование**: настраиваются в settings и запускаются автоматически

**Типы hooks** (4 типа, 25 событий):
- Tool Hooks: PreToolUse, PostToolUse, PostToolUseFailure, PermissionRequest
- Session Hooks: SessionStart, SessionEnd, Stop, StopFailure, SubagentStart, SubagentStop
- Task Hooks: UserPromptSubmit, TaskCompleted, TaskCreated, TeammateIdle
- Lifecycle Hooks: ConfigChange, CwdChanged, FileChanged, PreCompact, PostCompact, WorktreeCreate, WorktreeRemove, Notification, InstructionsLoaded, Elicitation, ElicitationResult

---

## 07. Plugins (3 complete plugins, 40 файлов)

Поставляемые наборы возможностей.

### PR Review Plugin (10 файлов)
```
pr-review/
├── .claude-plugin/
│   └── plugin.json                   # Манифест plugin
├── commands/
│   ├── review-pr.md                  # Полноценное ревью
│   ├── check-security.md             # Проверка безопасности
│   └── check-tests.md                # Проверка покрытия тестами
├── agents/
│   ├── security-reviewer.md          # Специалист по безопасности
│   ├── test-checker.md               # Специалист по тестам
│   └── performance-analyzer.md       # Специалист по производительности
├── mcp/
│   └── github-config.json            # Интеграция с GitHub
├── hooks/
│   └── pre-review.js                 # Валидация перед ревью
└── README.md                         # Документация plugin
```

**Возможности**: анализ безопасности, покрытие тестами, влияние на производительность

**Команды**: `/review-pr`, `/check-security`, `/check-tests`

**Установка**: `/plugin install pr-review`

### DevOps Automation Plugin (15 файлов)
```
devops-automation/
├── .claude-plugin/
│   └── plugin.json                   # Манифест plugin
├── commands/
│   ├── deploy.md                     # Деплой
│   ├── rollback.md                   # Откат
│   ├── status.md                     # Статус системы
│   └── incident.md                   # Реагирование на инцидент
├── agents/
│   ├── deployment-specialist.md      # Эксперт по деплою
│   ├── incident-commander.md         # Координатор инцидента
│   └── alert-analyzer.md             # Анализатор алертов
├── mcp/
│   └── kubernetes-config.json        # Интеграция с Kubernetes
├── hooks/
│   ├── pre-deploy.js                 # Проверки перед деплоем
│   └── post-deploy.js                # Задачи после деплоя
├── scripts/
│   ├── deploy.sh                     # Автоматизация деплоя
│   ├── rollback.sh                   # Автоматизация отката
│   └── health-check.sh               # Проверки здоровья
└── README.md                         # Документация plugin
```

**Возможности**: деплой в Kubernetes, rollback, мониторинг, реагирование на инциденты

**Команды**: `/deploy`, `/rollback`, `/status`, `/incident`

**Установка**: `/plugin install devops-automation`

### Documentation Plugin (14 файлов)
```
documentation/
├── .claude-plugin/
│   └── plugin.json                   # Манифест plugin
├── commands/
│   ├── generate-api-docs.md          # Генерация API docs
│   ├── generate-readme.md            # Создание README
│   ├── sync-docs.md                  # Синхронизация docs
│   └── validate-docs.md              # Проверка docs
├── agents/
│   ├── api-documenter.md             # Специалист по API docs
│   ├── code-commentator.md           # Специалист по комментариям в коде
│   └── example-generator.md          # Генератор примеров
├── mcp/
│   └── github-docs-config.json       # Интеграция с GitHub
├── templates/
│   ├── api-endpoint.md               # Шаблон API endpoint
│   ├── function-docs.md              # Шаблон docs для функций
│   └── adr-template.md               # Шаблон ADR
└── README.md                         # Документация plugin
```

**Возможности**: API docs, генерация README, синхронизация docs, проверка

**Команды**: `/generate-api-docs`, `/generate-readme`, `/sync-docs`, `/validate-docs`

**Установка**: `/plugin install documentation`

**Плюс**: `README.md` — обзор plugins и руководство по использованию

---

## 08. Checkpoints and Rewind (2 файла)

Сохранение состояния разговора и поиск альтернативных подходов.

| Файл | Описание | Содержимое |
|------|-------------|------------|
| `README.md` | Документация | Подробное руководство по checkpoint'ам |
| `checkpoint-examples.md` | Реальные примеры | Миграция базы данных, оптимизация производительности, итерации UI, отладка |
| | | |

**Основные понятия**:
- **Checkpoint**: снимок состояния разговора
- **Rewind**: возврат к предыдущему checkpoint'у
- **Branch Point**: исследование нескольких подходов

**Использование**:
```
# Checkpoints создаются автоматически с каждым пользовательским сообщением
# Чтобы выполнить rewind, нажмите Esc дважды или используйте:
/rewind
# Затем выберите: Restore code and conversation, Restore conversation,
# Restore code, Summarize from here или Never mind
```

**Сценарии использования**:
- Попробовать разные реализации
- Восстановиться после ошибок
- Безопасно экспериментировать
- Сравнивать решения
- A/B testing

---

## 09. Advanced Features (3 файла)

Продвинутые возможности для сложных рабочих процессов.

| Файл | Описание | Возможности |
|------|-------------|------------|
| `README.md` | Полное руководство | Вся документация по advanced features |
| `config-examples.json` | Примеры конфигураций | 10+ конфигураций под разные сценарии |
| `planning-mode-examples.md` | Примеры planning | REST API, миграция БД, рефакторинг |
| Scheduled Tasks | Повторяющиеся задачи с `/loop` и cron tools | Автоматизированные периодические workflows |
| Chrome Integration | Автоматизация браузера через headless Chromium | Web testing и scraping |
| Remote Control (expanded) | Способы подключения, безопасность, таблица сравнения | Управление удалёнными сессиями |
| Keyboard Customization | Кастомные keybindings, поддержка chord, контексты | Персональные shortcuts |
| Desktop App (expanded) | Connectors, launch.json, enterprise-функции | Интеграция с Desktop |
| | | |

**Охватываемые advanced features**:

### Planning Mode
- Создание подробных планов реализации
- Оценка времени и рисков
- Систематическая декомпозиция задач

### Extended Thinking
- Глубокое рассуждение для сложных задач
- Анализ архитектурных решений
- Оценка trade-off'ов

### Background Tasks
- Долгие операции без блокировки
- Параллельные workflows разработки
- Управление и мониторинг задач

### Permission Modes
- **default**: запрашивать подтверждение для рискованных действий
- **acceptEdits**: автоматически принимать правки файлов, спрашивать для остальных
- **plan**: режим только чтения, без изменений
- **auto**: автоматически одобрять безопасные действия, спрашивать для рискованных
- **dontAsk**: принимать всё, кроме рискованных действий
- **bypassPermissions**: принимать всё (требуется `--dangerously-skip-permissions`)

### Headless Mode (`claude -p`)
- Интеграция с CI/CD
- Автоматическое выполнение задач
- Пакетная обработка

### Session Management
- Несколько рабочих сессий
- Переключение и сохранение сессий
- Персистентность сессий

### Interactive Features
- Горячие клавиши
- История команд
- Автодополнение
- Многострочный ввод

### Configuration
- Полное управление настройками
- Конфигурации для разных окружений
