<picture>
  <source media="(prefers-color-scheme: dark)" srcset="resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="resources/logos/claude-howto-logo.svg">
</picture>

# Полный гайд по концепциям Claude

Полный справочный гайд по Slash Commands, Subagents, Memory, протоколу MCP и Agent Skills с таблицами, диаграммами и практическими примерами.

---

## Содержание

1. [Slash Commands](#slash-commands)
2. [Subagents](#subagents)
3. [Memory](#memory)
4. [MCP Protocol](#mcp-protocol)
5. [Agent Skills](#agent-skills)
6. [Plugins](#plugins)
7. [Hooks](#hooks)
8. [Checkpoints and Rewind](#checkpoints-and-rewind)
9. [Advanced Features](#advanced-features)
10. [Comparison & Integration](#comparison--integration)

---

## Slash Commands

### Обзор

Slash commands — это ярлыки, запускаемые пользователем и хранящиеся как Markdown-файлы, которые Claude Code может выполнять. Они позволяют командам стандартизировать часто используемые запросы и workflows.

### Архитектура

```mermaid
graph TD
    A["Ввод пользователя: /command-name"] -->|Запускает| B["Поиск в .claude/commands/"]
    B -->|Находит| C["command-name.md"]
    C -->|Загружает| D["Содержимое Markdown"]
    D -->|Выполняет| E["Claude обрабатывает запрос"]
    E -->|Возвращает| F["Результат в контексте"]
```

### Структура файлов

```mermaid
graph LR
    A["Корень проекта"] -->|содержит| B[".claude/commands/"]
    B -->|содержит| C["optimize.md"]
    B -->|содержит| D["test.md"]
    B -->|содержит| E["docs/"]
    E -->|содержит| F["generate-api-docs.md"]
    E -->|содержит| G["generate-readme.md"]
```

### Таблица организации команд

| Расположение | Область | Доступность | Сценарий использования | Отслеживается Git |
|----------|-------|--------------|----------|-------------|
| `.claude/commands/` | Для проекта | Участники команды | Командные workflows, общие стандарты | ✅ Да |
| `~/.claude/commands/` | Личное | Отдельный пользователь | Личные ярлыки между проектами | ❌ Нет |
| Подкаталоги | С пространством имён | Зависит от родителя | Организация по категориям | ✅ Да |

### Возможности

| Возможность | Пример | Поддерживается |
|---------|---------|-----------|
| Выполнение shell-скриптов | `bash scripts/deploy.sh` | ✅ Да |
| Ссылки на файлы | `@path/to/file.js` | ✅ Да |
| Интеграция Bash | `$(git log --oneline)` | ✅ Да |
| Аргументы | `/pr --verbose` | ✅ Да |
| Команды MCP | `/mcp__github__list_prs` | ✅ Да |

### Практические примеры

#### Пример 1: Команда оптимизации кода

**Файл:** `.claude/commands/optimize.md`

```markdown
---
name: Code Optimization
description: Анализировать код на проблемы производительности и предлагать оптимизации
tags: performance, analysis
---

# Оптимизация кода

Проверьте предоставленный код на следующие проблемы в порядке приоритета:

1. **Узкие места производительности** - найдите операции O(n²), неэффективные циклы
2. **Утечки памяти** - найдите неосвобождённые ресурсы, циклические ссылки
3. **Улучшения алгоритмов** - предложите лучшие алгоритмы или структуры данных
4. **Возможности кеширования** - найдите повторяющиеся вычисления
5. **Проблемы конкурентности** - найдите race condition или проблемы потоков

Оформите ответ так:
- Критичность проблемы (Critical/High/Medium/Low)
- Расположение в коде
- Объяснение
- Рекомендуемое исправление с примером кода
```

**Использование:**
```bash
# Пользователь вводит в Claude Code
/optimize

# Claude загружает запрос и ждёт ввода кода
```

#### Пример 2: Команда-помощник для Pull Request

**Файл:** `.claude/commands/pr.md`

```markdown
---
name: Prepare Pull Request
description: Очистить код, подготовить изменения и оформить pull request
tags: git, workflow
---

# Чеклист подготовки Pull Request

Перед созданием PR выполните следующие шаги:

1. Запустите linting: `prettier --write .`
2. Запустите тесты: `npm test`
3. Проверьте git diff: `git diff HEAD`
4. Проиндексируйте изменения: `git add .`
5. Создайте сообщение коммита по Conventional Commits:
   - `fix:` для исправлений ошибок
   - `feat:` для новых возможностей
   - `docs:` для документации
   - `refactor:` для перестройки кода
   - `test:` для добавления тестов
   - `chore:` для обслуживания

6. Сгенерируйте сводку PR, включая:
   - Что изменилось
   - Почему это изменилось
   - Какие тесты выполнены
   - Возможные последствия
```

**Использование:**
```bash
/pr

# Claude проходит по чеклисту и готовит PR
```

#### Пример 3: Иерархический генератор документации

**Файл:** `.claude/commands/docs/generate-api-docs.md`

```markdown
---
name: Generate API Documentation
description: Создать полную API-документацию из исходного кода
tags: documentation, api
---

# Генератор API-документации

Сгенерируйте API-документацию так:

1. Просканируйте все файлы в `/src/api/`
2. Извлеките сигнатуры функций и комментарии JSDoc
3. Организуйте по endpoint/module
4. Создайте markdown с примерами
5. Включите схемы запросов и ответов
6. Добавьте документацию по ошибкам

Формат вывода:
- Markdown-файл в `/docs/api.md`
- Добавьте примеры curl для всех endpoint
- Добавьте TypeScript-типизацию
```

### Диаграмма жизненного цикла команды

```mermaid
sequenceDiagram
    participant Пользователь
    participant Claude as Claude Code
    participant FS as File System
    participant CLI as Shell/Bash

    Пользователь->>Claude: Types /optimize
    Claude->>FS: Searches .claude/commands/
    FS-->>Claude: Returns optimize.md
    Claude->>Claude: Loads Markdown content
    Claude->>Пользователь: Displays prompt context
    Пользователь->>Claude: Provides code to analyze
    Claude->>CLI: (May execute scripts)
    CLI-->>Claude: Results
    Claude->>Пользователь: Returns analysis
```

### Лучшие практики

| ✅ Делайте | ❌ Не делайте |
|------|---------|
| Используйте понятные, ориентированные на действие имена | Создавайте команды для одноразовых задач |
| Указывайте слова-триггеры в описании | Встраивайте сложную логику в команды |
| Делайте команды сфокусированными на одной задаче | Создавайте дублирующие команды |
| Версионируйте проектные команды | Хардкодьте чувствительную информацию |
| Организуйте по подкаталогам | Создавайте длинные списки команд |
| Используйте простые и читаемые запросы | Используйте сокращённые или криптичные формулировки |

---

## Subagents

### Обзор

Subagents — это специализированные AI-помощники с изолированными окнами контекста и настраиваемыми системными промптами. Они позволяют делегировать задачи, сохраняя чёткое разделение ответственности.

### Диаграмма архитектуры

```mermaid
graph TB
    Пользователь["👤 Пользователь"]
    Main["🎯 Main Agent<br/>(Coordinator)"]
    Reviewer["🔍 Code Reviewer<br/>Subagent"]
    Tester["✅ Test Engineer<br/>Subagent"]
    Docs["📝 Documentation<br/>Subagent"]

    Пользователь -->|asks| Main
    Main -->|delegates| Reviewer
    Main -->|delegates| Tester
    Main -->|delegates| Docs
    Reviewer -->|returns result| Main
    Tester -->|returns result| Main
    Docs -->|returns result| Main
    Main -->|synthesizes| Пользователь
```

### Жизненный цикл subagent

```mermaid
sequenceDiagram
    participant Пользователь
    participant MainAgent as Main Agent
    participant CodeReviewer as Code Reviewer<br/>Subagent
    participant Context as Separate<br/>Context Window

    Пользователь->>MainAgent: "Build new auth feature"
    MainAgent->>MainAgent: Analyze task
    MainAgent->>CodeReviewer: "Review this code"
    CodeReviewer->>Context: Initialize clean context
    Context->>CodeReviewer: Load reviewer instructions
    CodeReviewer->>CodeReviewer: Perform review
    CodeReviewer-->>MainAgent: Return findings
    MainAgent->>MainAgent: Incorporate results
    MainAgent-->>Пользователь: Provide synthesis
```

### Таблица конфигурации subagent

| Параметр | Тип | Назначение | Пример |
|---------------|------|---------|---------|
| `name` | String | Идентификатор агента | `code-reviewer` |
| `description` | String | Назначение и триггерные фразы | `Comprehensive code quality analysis` |
| `tools` | List/String | Разрешённые возможности | `read, grep, diff, lint_runner` |
| `system_prompt` | Markdown | Поведенческие инструкции | Пользовательские правила |

### Иерархия доступа к инструментам

```mermaid
graph TD
    A["Конфигурация subagent"] -->|Вариант 1| B["Наследовать все инструменты<br/>из основного потока"]
    A -->|Вариант 2| C["Указать отдельные инструменты"]
    B -->|Включает| B1["Операции с файлами"]
    B -->|Включает| B2["Shell-команды"]
    B -->|Включает| B3["Инструменты MCP"]
    C -->|Явный список| C1["read, grep, diff"]
    C -->|Явный список| C2["Bash(npm:*), Bash(test:*)"]
```

### Практические примеры

#### Пример 1: Полная настройка subagent

**Файл:** `.claude/agents/code-reviewer.md`

```yaml
---
name: code-reviewer
description: Полный анализ качества кода и поддерживаемости
tools: read, grep, diff, lint_runner
---

# Агент code reviewer

Вы эксперт по code review, специализирующийся на:
- Оптимизации производительности
- Уязвимостях безопасности
- Поддерживаемости кода
- Покрытии тестами
- Паттернах проектирования

## Приоритеты ревью (по порядку)

1. **Проблемы безопасности** - аутентификация, авторизация, утечка данных
2. **Проблемы производительности** - операции O(n²), утечки памяти, неэффективные запросы
3. **Качество кода** - читаемость, именование, документация
4. **Покрытие тестами** - пропущенные тесты, граничные случаи
5. **Паттерны проектирования** - принципы SOLID, архитектура

## Формат вывода ревью

Для каждой проблемы:
- **Severity**: Critical / High / Medium / Low
- **Category**: Security / Performance / Quality / Testing / Design
- **Location**: путь к файлу и номер строки
- **Issue Description**: что не так и почему
- **Suggested Fix**: пример исправления
- **Impact**: как это влияет на систему

## Пример ревью

### Проблема: N+1 Query Problem
- **Severity**: High
- **Category**: Performance
- **Location**: src/user-service.ts:45
- **Issue**: цикл выполняет запрос к базе в каждой итерации
- **Fix**: используйте JOIN или пакетный запрос
```

**Файл:** `.claude/agents/test-engineer.md`

```yaml
---
name: test-engineer
description: Стратегия тестирования, анализ покрытия и автоматизированное тестирование
tools: read, write, bash, grep
---

# Агент test engineer

Вы эксперт в:
- Написании полных тестовых наборов
- Обеспечении высокого покрытия кода (>80%)
- Проверке граничных случаев и сценариев ошибок
- Бенчмаркинге производительности
- Интеграционном тестировании

## Стратегия тестирования

1. **Модульные тесты** - отдельные функции/методы
2. **Интеграционные тесты** - взаимодействие компонентов
3. **End-to-End тесты** - полные workflows
4. **Граничные случаи** - граничные условия
5. **Сценарии ошибок** - обработка сбоев

## Требования к результату тестов

- Используйте Jest для JavaScript/TypeScript
- Добавляйте setup/teardown для каждого теста
- Мокайте внешние зависимости
- Документируйте назначение теста
- Добавляйте проверки производительности, когда это уместно

## Требования к покрытию

- Минимум 80% покрытия кода
- 100% для критических путей
- Отчёт о зонах с недостающим покрытием
```

**Файл:** `.claude/agents/documentation-writer.md`

```yaml
---
name: documentation-writer
description: Техническая документация, API docs и руководства пользователя
tools: read, write, grep
---

# Агент документации

Вы создаёте:
- документацию API с примерами
- руководства пользователя и туториалы
- архитектурную документацию
- записи changelog
- улучшения комментариев в коде

## Стандарты документации

1. **Ясность** - используйте простой и понятный язык
2. **Примеры** - добавляйте практические примеры кода
3. **Полнота** - покрывайте все параметры и возвращаемые значения
4. **Структура** - используйте единообразное форматирование
5. **Точность** - сверяйтесь с реальным кодом

## Разделы документации

### Для API
- Описание
- Параметры (с типами)
- Возвращаемые значения (с типами)
- Исключения (возможные ошибки)
- Примеры (curl, JavaScript, Python)
- Связанные endpoint

### Для возможностей
- Обзор
- Требования
- Пошаговые инструкции
- Ожидаемые результаты
- Устранение неполадок
- Связанные темы
```

#### Пример 2: Делегирование subagent в действии

```markdown
# Сценарий: Создание платёжной функции

## Запрос пользователя
"Создай безопасную функцию обработки платежей, интегрированную со Stripe"

## Поток основного агента

1. **Этап планирования**
   - Понимает требования
   - Определяет необходимые задачи
   - Планирует архитектуру

2. **Делегирует subagent code reviewer**
   - Задача: "Проверь реализацию обработки платежей на безопасность"
   - Контекст: auth, API-ключи, обработка токенов
   - Проверяет: SQL-инъекции, утечку ключей, принудительный HTTPS

3. **Делегирует subagent test engineer**
   - Задача: "Создай полные тесты для платёжных сценариев"
   - Контекст: успешные сценарии, сбои, граничные случаи
   - Создаёт тесты для: успешных платежей, отклонённых карт, сетевых сбоев, webhook

4. **Делегирует subagent documentation writer**
   - Задача: "Документируй endpoint платёжного API"
   - Контекст: схемы запросов и ответов
   - Результат: API-документация с примерами curl и кодами ошибок

5. **Синтез**
   - Основной агент собирает все результаты
   - Объединяет выводы
   - Возвращает пользователю готовое решение
```

#### Пример 3: Область разрешений инструментов

**Ограниченная настройка - только конкретные команды**

```yaml
---
name: secure-reviewer
description: Ревью кода с упором на безопасность и минимальными разрешениями
tools: read, grep
---

# Безопасный ревьюер кода

Проверяет код только на уязвимости безопасности.

Этот агент:
- ✅ Читает файлы для анализа
- ✅ Ищет паттерны
- ❌ Не может выполнять код
- ❌ Не может изменять файлы
- ❌ Не может запускать тесты

Это гарантирует, что ревьюер случайно ничего не сломает.
```

**Расширенная настройка - все инструменты для реализации**

```yaml
---
name: implementation-agent
description: Полные возможности реализации для разработки функций
tools: read, write, bash, grep, edit, glob
---

# Агент реализации

Создаёт функции по спецификациям.

This agent:
- ✅ Reads specifications
- ✅ Writes new code files
- ✅ Runs build commands
- ✅ Searches codebase
- ✅ Edits existing files
- ✅ Finds files matching patterns

Полный набор возможностей для самостоятельной разработки функций.
```

### Управление контекстом subagent

```mermaid
graph TB
    A["Контекст основного агента<br/>50,000 токенов"]
    B["Контекст subagent 1<br/>20,000 токенов"]
    C["Контекст subagent 2<br/>20,000 токенов"]
    D["Контекст subagent 3<br/>20,000 токенов"]

    A -->|Чистый лист| B
    A -->|Чистый лист| C
    A -->|Чистый лист| D

    B -->|Только результаты| A
    C -->|Только результаты| A
    D -->|Только результаты| A

    style A fill:#e1f5ff
    style B fill:#fff9c4
    style C fill:#fff9c4
    style D fill:#fff9c4
```

### Когда использовать subagent

| Сценарий | Использовать subagent | Почему |
|----------|--------------|-----|
| Сложная функция с множеством шагов | ✅ Да | Разделение ответственности, защита от загрязнения контекста |
| Быстрое code review | ❌ Нет | Лишние накладные расходы |
| Параллельное выполнение задач | ✅ Да | У каждого subagent свой контекст |
| Нужна узкая экспертиза | ✅ Да | Пользовательские системные промпты |
| Долгий анализ | ✅ Да | Предотвращает переполнение основного контекста |
| Одна задача | ❌ Нет | Лишняя задержка |

### Команды агентов

Команды агентов координируют несколько агентов, работающих над связанными задачами. Вместо делегирования по одному subagent за раз, Agent Teams позволяют основному агенту координировать группу агентов, которые сотрудничают, обмениваются промежуточными результатами и движутся к общей цели. Это полезно для больших задач, например полной разработки функции, где frontend-агент, backend-агент и тестовый агент работают параллельно.

---

## Memory

### Обзор

Memory позволяет Claude сохранять контекст между сессиями и разговорами. Она существует в двух формах: автоматическая синтезация в claude.ai и файловая `CLAUDE.md` в Claude Code.

### Архитектура памяти

```mermaid
graph TB
    A["Claude Session"]
    B["Пользователь Input"]
    C["Memory System"]
    D["Memory Storage"]

    B -->|Пользователь provides info| C
    C -->|Synthesizes every 24h| D
    D -->|Loads automatically| A
    A -->|Uses context| C
```

### Иерархия памяти в Claude Code (7 уровней)

Claude Code загружает память из 7 уровней, перечисленных от высшего к низшему приоритету:

```mermaid
graph TD
    A["1. Managed Policy<br/>Enterprise admin policies"] --> B["2. Project Memory<br/>./CLAUDE.md"]
    B --> C["3. Project Rules<br/>.claude/rules/*.md"]
    C --> D["4. Пользователь Memory<br/>~/.claude/CLAUDE.md"]
    D --> E["5. Пользователь Rules<br/>~/.claude/rules/*.md"]
    E --> F["6. Local Memory<br/>.claude/local/CLAUDE.md"]
    F --> G["7. Auto Memory<br/>Automatically captured preferences"]

    style A fill:#fce4ec,stroke:#333,color:#333
    style B fill:#e1f5fe,stroke:#333,color:#333
    style C fill:#e1f5fe,stroke:#333,color:#333
    style D fill:#f3e5f5,stroke:#333,color:#333
    style E fill:#f3e5f5,stroke:#333,color:#333
    style F fill:#e8f5e9,stroke:#333,color:#333
    style G fill:#fff3e0,stroke:#333,color:#333
```

### Таблица расположения памяти

| Уровень | Расположение | Область | Приоритет | Общий доступ | Лучше всего для |
|------|----------|-------|----------|--------|----------|
| 1. Managed Policy | Админская политика предприятия | Organization | Highest | Все пользователи организации | Соответствие требованиям, политики безопасности |
| 2. Project | `./CLAUDE.md` | Project | High | Команда (Git) | Стандарты команды, архитектура |
| 3. Project Rules | `.claude/rules/*.md` | Project | High | Команда (Git) | Модульные соглашения проекта |
| 4. Пользователь | `~/.claude/CLAUDE.md` | Personal | Medium | Индивидуально | Личные предпочтения |
| 5. Пользователь Rules | `~/.claude/rules/*.md` | Personal | Medium | Индивидуально | Модульные личные правила |
| 6. Local | `.claude/local/CLAUDE.md` | Local | Low | Не делится | Настройки для конкретной машины |
| 7. Auto Memory | Automatic | Session | Lowest | Индивидуально | Изученные предпочтения, шаблоны |

### Автопамять

Автопамять автоматически фиксирует предпочтения пользователя и шаблоны, замеченные во время сессий. Claude учится на ваших взаимодействиях и запоминает:

- предпочтения по стилю кода
- типичные исправления, которые вы вносите
- выбор фреймворков и инструментов
- предпочтения по стилю общения

Автопамять работает в фоне и не требует ручной настройки.

### Жизненный цикл обновления памяти

```mermaid
sequenceDiagram
    participant Пользователь
    participant Claude as Claude Code
    participant Editor as File System
    participant Memory as CLAUDE.md

    Пользователь->>Claude: "Запомни: используй async/await"
    Claude->>Пользователь: "В какой файл памяти?"
    Пользователь->>Claude: "Память проекта"
    Claude->>Editor: Открыть ~/.claude/settings.json
    Claude->>Memory: Записать в ./CLAUDE.md
    Memory-->>Claude: Файл сохранён
    Claude->>Claude: Загрузить обновлённую память
    Claude-->>Пользователь: "Память сохранена!"
```

### Практические примеры

#### Пример 1: Структура памяти проекта

**File:** `./CLAUDE.md`

```markdown
# Конфигурация проекта

## Обзор проекта
- **Имя**: E-commerce Platform
- **Стек**: Node.js, PostgreSQL, React 18, Docker
- **Размер команды**: 5 разработчиков
- **Дедлайн**: Q4 2025

## Архитектура
@docs/architecture.md
@docs/api-standards.md
@docs/database-schema.md

## Стандарты разработки

### Стиль кода
- Используйте Prettier для форматирования
- Используйте ESLint с конфигурацией airbnb
- Максимальная длина строки: 100 символов
- Используйте отступ в 2 пробела

### Соглашения по именованию
- **Файлы**: kebab-case (user-controller.js)
- **Классы**: PascalCase (ПользовательService)
- **Функции/переменные**: camelCase (getПользовательById)
- **Константы**: UPPER_SNAKE_CASE (API_BASE_URL)
- **Таблицы базы данных**: snake_case (user_accounts)

### Git-workflow
- Имена веток: `feature/description` или `fix/description`
- Сообщения коммитов: следуйте Conventional Commits
- Перед merge нужен PR
- Все проверки CI/CD должны пройти
- Нужен минимум 1 апрув

### Требования к тестам
- Минимум 80% покрытия кода
- У всех критических путей должны быть тесты
- Используйте Jest для модульных тестов
- Используйте Cypress для E2E тестов
- Имена файлов тестов: `*.test.ts` или `*.spec.ts`

### Стандарты API
- Только RESTful endpoint
- JSON для запросов и ответов
- Корректно используйте HTTP-коды статуса
- Версионируйте endpoint API: `/api/v1/`
- Документируйте все endpoint с примерами

### База данных
- Используйте миграции для изменений схемы
- Никогда не хардкодьте учётные данные
- Используйте connection pooling
- Включайте логирование запросов в разработке
- Нужны регулярные бэкапы

### Развёртывание
- Развёртывание на основе Docker
- Оркестрация через Kubernetes
- Стратегия blue-green deployment
- Автоматический rollback при сбое
- Миграции базы запускаются до deploy

## Частые команды

| Команда | Назначение |
|---------|---------|
| `npm run dev` | Запустить сервер разработки |
| `npm test` | Запустить набор тестов |
| `npm run lint` | Проверить стиль кода |
| `npm run build` | Собрать для production |
| `npm run migrate` | Запустить миграции базы данных |

## Контакты команды
- Tech Lead: Sarah Chen (@sarah.chen)
- Product Manager: Mike Johnson (@mike.j)
- DevOps: Alex Kim (@alex.k)

## Известные проблемы и обходные пути
- Connection pooling PostgreSQL ограничен 20 во время пиковых нагрузок
- Обход: реализовать очередь запросов
- Проблемы совместимости Safari 14 с async generators
- Обход: использовать transpiler Babel

## Связанные проекты
- Analytics Dashboard: `/projects/analytics`
- Mobile App: `/projects/mobile`
- Admin Panel: `/projects/admin`
```

#### Пример 2: Память для конкретного каталога

**File:** `./src/api/CLAUDE.md`

~~~~markdown
# Стандарты модуля API

Этот файл переопределяет корневой `CLAUDE.md` для всего в `/src/api/`

## Специфические стандарты API

### Проверка запросов
- Используйте Zod для валидации схем
- Всегда проверяйте входные данные
- Возвращайте 400 с ошибками валидации
- Добавляйте подробности по полям

### Аутентификация
- Все endpoint требуют JWT token
- Token передаётся в заголовке Authorization
- Token истекает через 24 часа
- Реализуйте механизм refresh token

### Формат ответа

Все ответы должны соответствовать этой структуре:

```json
{
  "success": true,
  "data": { /* actual data */ },
  "timestamp": "2025-11-06T10:30:00Z",
  "version": "1.0"
}
```

### Ошибки ответа:
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Пользователь message",
    "details": { /* field errors */ }
  },
  "timestamp": "2025-11-06T10:30:00Z"
}
```

### Пагинация
- Используйте cursor-based pagination, а не offset
- Добавляйте boolean `hasMore`
- Ограничьте максимальный размер страницы до 100
- Размер страницы по умолчанию: 20

### Rate limiting
- 1000 запросов в час для аутентифицированных пользователей
- 100 запросов в час для публичных endpoint
- Возвращайте 429 при превышении
- Добавляйте заголовок retry-after

### Кеширование
- Используйте Redis для кеша сессий
- Длительность кеша по умолчанию: 5 минут
- Инвалидация при операциях записи
- Помечайте ключи кеша типом ресурса
~~~~

#### Example 3: Personal Memory

**File:** `~/.claude/CLAUDE.md`

~~~~markdown
# Мои предпочтения в разработке

## Обо мне
- **Уровень опыта**: 8 лет full-stack разработки
- **Предпочитаемые языки**: TypeScript, Python
- **Стиль общения**: Прямой, с примерами
- **Стиль обучения**: Визуальные диаграммы с кодом

## Предпочтения по коду

### Обработка ошибок
Я предпочитаю явную обработку ошибок через try-catch и осмысленные сообщения.
Избегайте общих ошибок. Всегда логируйте ошибки для отладки.

### Комментарии
Используйте комментарии для WHY, а не WHAT. Код должен быть самодокументируемым.
Комментарии должны объяснять бизнес-логику или неочевидные решения.

### Тестирование
Я предпочитаю TDD (test-driven development).
Сначала пишите тесты, потом реализацию.
Сосредоточьтесь на поведении, а не на деталях реализации.

### Архитектура
Я предпочитаю модульный, слабосвязанный дизайн.
Используйте dependency injection ради тестируемости.
Разделяйте ответственность (Controllers, Services, Repositories).

## Предпочтения по отладке
- Используйте `console.log` с префиксом `[DEBUG]`
- Добавляйте контекст: имя функции, релевантные переменные
- Используйте stack trace, когда он доступен
- Всегда добавляйте timestamps в логи

## Коммуникация
- Объясняйте сложные концепции с помощью диаграмм
- Показывайте конкретные примеры до теории
- Добавляйте примеры кода до/после
- Суммируйте ключевые моменты в конце

## Организация проекта
Я организую проекты так:
```
project/
  ├── src/
  │   ├── api/
  │   ├── services/
  │   ├── models/
  │   └── utils/
  ├── tests/
  ├── docs/
  └── docker/
```

## Инструменты
- **IDE**: VS Code с vim keybindings
- **Терминал**: Zsh с Oh-My-Zsh
- **Форматирование**: Prettier (100 символов в строке)
- **Linter**: ESLint с конфигурацией airbnb
- **Фреймворк тестов**: Jest с React Testing Library
~~~~

#### Example 4: Memory Обновить During Session

**Session Interaction:**

```markdown
Пользователь: Remember that I prefer using React hooks instead of class components
     for all new components.

Claude: Я добавляю это в память. В какой файл памяти это записать?
        1. Память проекта (./CLAUDE.md)
        2. Личная память (~/.claude/CLAUDE.md)

Пользователь: Project memory

Claude: ✅ Память сохранена!

Added to ./CLAUDE.md:
---

### Разработка компонентов
- Используйте функциональные компоненты с React Hooks
- Предпочитайте hooks класс-компонентам
- Используйте custom hooks для переиспользуемой логики
- Используйте useCallback для обработчиков событий
- Используйте useMemo для дорогих вычислений
```

### Memory в Claude Web/Desktop

#### Таймлайн синтеза Memory

```mermaid
graph LR
A["День 1: разговоры<br/>пользователя"] -->|24 hours| B["День 2: синтез<br/>Memory"]
    B -->|Automatic| C["Memory обновлена<br/>и кратко изложена"]
    C -->|Loaded in| D["День 2-N:<br/>новые разговоры"]
    D -->|Add to| E["Memory"]
    E -->|24 hours later| F["Memory Refreshed"]
```

**Пример сводки памяти:**

```markdown
## Память Claude о пользователе

### Профессиональный фон
- Senior full-stack developer с 8 годами опыта
- Фокус на TypeScript/Node.js backend и React frontend
- Активный contributor open source
- Интерес к AI и machine learning

### Контекст проекта
- Сейчас строю e-commerce платформу
- Стек: Node.js, PostgreSQL, React 18, Docker
- Работаю с командой из 5 разработчиков
- Использую CI/CD и blue-green deployments

### Предпочтения по общению
- Предпочитает прямые и краткие объяснения
- Любит визуальные диаграммы и примеры
- Ценит фрагменты кода
- Объясняет бизнес-логику в комментариях

### Текущие цели
- Улучшить производительность API
- Повысить покрытие тестами до 90%
- Реализовать стратегию кеширования
- Документировать архитектуру
```

### Сравнение возможностей Memory

| Возможность | Claude Web/Desktop | Claude Code (CLAUDE.md) |
|---------|-------------------|------------------------|
| Автосинтез | ✅ Каждые 24 часа | ❌ Вручную |
| Межпроектность | ✅ Общая | ❌ Только проект |
| Доступ команды | ✅ Общие проекты | ✅ Отслеживается Git |
| Поиск | ✅ Встроенный | ✅ Через `/memory` |
| Редактирование | ✅ В чате | ✅ Прямое редактирование файла |
| Импорт/экспорт | ✅ Да | ✅ Копирование/вставка |
| Постоянство | ✅ 24ч+ | ✅ Бессрочно |

---

## Протокол MCP

### Обзор

MCP (Model Context Protocol) — это стандартизированный способ для Claude получать доступ к внешним инструментам, API и источникам данных в реальном времени. В отличие от Memory, MCP даёт живой доступ к изменяющимся данным.

### Архитектура MCP

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
```

### Экосистема MCP

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
```

### Процесс настройки MCP

```mermaid
sequenceDiagram
    participant Пользователь
    participant Claude as Claude Code
    participant Config as Config File
    participant Service as External Service

    Пользователь->>Claude: Type /mcp
    Claude->>Claude: List available MCP servers
    Claude->>Пользователь: Show options
    Пользователь->>Claude: Выбрать GitHub MCP
    Claude->>Config: Обновить configuration
    Config->>Claude: Активировать connection
    Claude->>Service: Test connection
    Service-->>Claude: Authentication successful
    Claude->>Пользователь: ✅ MCP connected!
```

### Таблица доступных MCP-серверов

| MCP-сервер | Назначение | Распространённые инструменты | Аутентификация | Реальное время |
|------------|---------|--------------|------|-----------|
| **Filesystem** | Операции с файлами | read, write, delete | Разрешения ОС | ✅ Да |
| **GitHub** | Управление репозиторием | list_prs, create_issue, push | OAuth | ✅ Да |
| **Slack** | Командная коммуникация | send_message, list_channels | Token | ✅ Да |
| **Database** | SQL-запросы | query, insert, update | Учётные данные | ✅ Да |
| **Google Docs** | Доступ к документам | read, write, share | OAuth | ✅ Да |
| **Asana** | Управление проектами | create_task, update_status | API Key | ✅ Да |
| **Stripe** | Платёжные данные | list_charges, create_invoice | API Key | ✅ Да |
| **Memory** | Постоянная память | store, retrieve, delete | Local | ❌ Нет |

### Практические примеры

#### Пример 1: Конфигурация GitHub MCP

**Файл:** `.mcp.json` (область проекта) или `~/.claude.json` (область пользователя)

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

**Доступные инструменты GitHub MCP:**

~~~~markdown
# Инструменты GitHub MCP

## Управление Pull Request
- `list_prs` - Показать все PR в репозитории
- `get_pr` - Получить детали PR, включая diff
- `create_pr` - Создать новый PR
- `update_pr` - Обновить описание/заголовок PR
- `merge_pr` - Слить PR в main branch
- `review_pr` - Добавить комментарии ревью

Пример запроса:
```
/mcp__github__get_pr 456

# Возвращает:
Title: Add dark mode support
Author: @alice
Description: Implements dark theme using CSS variables
Status: OPEN
Reviewers: @bob, @charlie
```

## Управление issues
- `list_issues` - Показать все issues
- `get_issue` - Получить детали issue
- `create_issue` - Создать новый issue
- `close_issue` - Закрыть issue
- `add_comment` - Добавить комментарий к issue

## Информация о репозитории
- `get_repo_info` - Детали репозитория
- `list_files` - Структура дерева файлов
- `get_file_content` - Читать содержимое файлов
- `search_code` - Поиск по codebase

## Операции с коммитами
- `list_commits` - История коммитов
- `get_commit` - Детали конкретного коммита
- `create_commit` - Создать новый коммит
~~~~

#### Пример 2: Настройка Database MCP

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
Пользователь: Получи всех пользователей с более чем 10 заказами

Claude: Я выполню запрос к вашей базе данных, чтобы найти эту информацию.

# Использование инструмента MCP для базы данных:
SELECT u.*, COUNT(o.id) as order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id
HAVING COUNT(o.id) > 10
ORDER BY order_count DESC;

# Результаты:
- Alice: 15 заказов
- Bob: 12 заказов
- Charlie: 11 заказов
```

#### Пример 3: Рабочий процесс Multi-MCP

**Сценарий: Генерация ежедневного отчёта**

```markdown
# Workflow ежедневного отчёта с использованием нескольких MCP

## Настройка
1. GitHub MCP - собрать метрики PR
2. Database MCP - запросить данные о продажах
3. Slack MCP - отправить отчёт
4. Filesystem MCP - сохранить отчёт

## Workflow

### Шаг 1: Получить данные из GitHub
/mcp__github__list_prs completed:true last:7days

Вывод:
- Total PRs: 42
- Average merge time: 2.3 hours
- Review turnaround: 1.1 hours

### Шаг 2: Запрос к базе данных
SELECT COUNT(*) as sales, SUM(amount) as revenue
FROM orders
WHERE created_at > NOW() - INTERVAL '1 day'

Вывод:
- Продажи: 247
- Выручка: $12,450

### Шаг 3: Сгенерировать отчёт
Объединить данные в HTML-отчёт

### Шаг 4: Сохранить в файловую систему
Записать `report.html` в `/reports/`

### Шаг 5: Отправить в Slack
Отправить сводку в канал `#daily-reports`

Итог:
✅ Отчёт сгенерирован и отправлен
📊 За эту неделю слито 47 PR
💰 $12,450 ежедневных продаж
```

#### Пример 4: Операции Filesystem MCP

**Конфигурация:**

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
|-----------|---------|---------|
| Список файлов | `ls ~/projects` | Показать содержимое каталога |
| Читать файл | `cat src/main.ts` | Читать содержимое файла |
| Записать файл | `create docs/api.md` | Создать новый файл |
| Редактировать файл | `edit src/app.ts` | Изменить файл |
| Поиск | `grep "async function"` | Искать в файлах |
| Удалить | `rm old-file.js` | Удалить файл |

### MCP против Memory: матрица решений

```mermaid
graph TD
    A["Нужны внешние данные?"]
    A -->|Нет| B["Использовать Memory"]
    A -->|Да| C["Они часто меняются?"]
    C -->|Нет/редко| B
    C -->|Да/часто| D["Использовать MCP"]

    B -->|Хранит| E["Предпочтения<br/>Контекст<br/>История"]
    D -->|Получает доступ к| F["Живые API<br/>Базы данных<br/>Сервисы"]

    style B fill:#e1f5ff
    style D fill:#fff9c4
```

### Паттерн запрос/ответ

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

    Note over MCP,DB: Доступ в реальном времени<br/>Без кеширования
```

---

## Agent Skills

### Обзор

Agent Skills — это переиспользуемые возможности, вызываемые моделью и упакованные как папки с инструкциями, скриптами и ресурсами. Claude автоматически определяет и использует подходящие навыки.

### Архитектура skill

```mermaid
graph TB
    A["Каталог skill"]
    B["SKILL.md"]
    C["YAML-метаданные"]
    D["Инструкции"]
    E["Скрипты"]
    F["Шаблоны"]

    A --> B
    B --> C
    B --> D
    E --> A
    F --> A
```

### Процесс загрузки skill

```mermaid
sequenceDiagram
    participant Пользователь
    participant Claude as Claude
    participant System as System
    participant Skill as Skill

    Пользователь->>Claude: "Создай Excel-отчёт"
    Claude->>System: Сканировать доступные skills
    System->>System: Загрузить метаданные skill
    Claude->>Claude: Сопоставить запрос пользователя со skills
    Claude->>Skill: Загрузить SKILL.md для xlsx skill
    Skill-->>Claude: Вернуть инструкции + инструменты
    Claude->>Claude: Выполнить skill
    Claude->>Пользователь: Сгенерировать Excel-файл
```

### Таблица типов и расположений skill

| Тип | Расположение | Область | Общий доступ | Синхронизация | Лучше всего для |
|------|----------|-------|--------|------|----------|
| Pre-built | Встроено | Глобально | Все пользователи | Авто | Создание документов |
| Personal | `~/.claude/skills/` | Индивидуально | Нет | Вручную | Личная автоматизация |
| Project | `.claude/skills/` | Команда | Да | Git | Стандарты команды |
| Plugin | Через установку плагина | Зависит | Зависит | Авто | Интегрированные возможности |

### Предустановленные skills

```mermaid
graph TB
    A["Предустановленные skills"]
    B["PowerPoint (pptx)"]
    C["Excel (xlsx)"]
    D["Word (docx)"]
    E["PDF"]

    A --> B
    A --> C
    A --> D
    A --> E

    B --> B1["Создавать презентации"]
    B --> B2["Редактировать слайды"]
    C --> C1["Создавать таблицы"]
    C --> C2["Анализировать данные"]
    D --> D1["Создавать документы"]
    D --> D2["Форматировать текст"]
    E --> E1["Генерировать PDF"]
    E --> E2["Заполнять формы"]
```

### Встроенные skills

Claude Code теперь включает 5 встроенных skills, доступных сразу:

| Skill | Команда | Назначение |
|-------|---------|---------|
| **Simplify** | `/simplify` | Упростить сложный код или объяснения |
| **Batch** | `/batch` | Выполнять операции по множеству файлов или элементов |
| **Debug** | `/debug` | Систематическая отладка с поиском первопричины |
| **Loop** | `/loop` | Планировать повторяющиеся задачи по таймеру |
| **Claude API** | `/claude-api` | Работать с Anthropic API напрямую |

Эти встроенные skills всегда доступны и не требуют установки или настройки.

### Практические примеры

#### Пример 1: Пользовательский skill для code review

**Структура каталога:**

```
~/.claude/skills/code-review/
├── SKILL.md
├── templates/
│   ├── review-checklist.md
│   └── finding-template.md
└── scripts/
    ├── analyze-metrics.py
    └── compare-complexity.py
```

**Файл:** `~/.claude/skills/code-review/SKILL.md`

```yaml
---
name: Code Review Specialist
description: Полный code review с анализом безопасности, производительности и качества
version: "1.0.0"
tags:
  - code-review
  - quality
  - security
when_to_use: When users ask to review code, analyze code quality, or evaluate pull requests
effort: high
shell: bash
---

# Skill code review

Этот skill даёт расширенные возможности code review с фокусом на:

1. **Анализ безопасности**
   - Проблемы аутентификации/авторизации
   - Риски утечки данных
   - Уязвимости инъекций
   - Слабые места криптографии
   - Логирование чувствительных данных

2. **Анализ производительности**
   - Эффективность алгоритмов (Big O)
   - Оптимизация памяти
   - Оптимизация запросов к базе
   - Возможности кеширования
   - Проблемы конкурентности

3. **Качество кода**
   - Принципы SOLID
   - Паттерны проектирования
   - Соглашения по именованию
   - Документация
   - Покрытие тестами

4. **Поддерживаемость**
   - Читаемость кода
   - Размер функций (должен быть < 50 строк)
   - Цикломатическая сложность
   - Управление зависимостями
   - Типобезопасность

## Шаблон ревью

Для каждого проверенного фрагмента кода указывайте:

### Сводка
- Общая оценка качества (1-5)
- Количество ключевых находок
- Рекомендуемые приоритетные области

### Критические проблемы (если есть)
- **Issue**: ясное описание
- **Location**: файл и номер строки
- **Impact**: почему это важно
- **Severity**: Critical/High/Medium
- **Fix**: пример исправления

### Находки по категориям

#### Безопасность (если есть проблемы)
Перечислите уязвимости безопасности с примерами

#### Производительность (если есть проблемы)
Перечислите проблемы производительности с анализом сложности

#### Качество (если есть проблемы)
Перечислите проблемы качества кода с предложениями по рефакторингу

#### Поддерживаемость (если есть проблемы)
Перечислите проблемы поддерживаемости с улучшениями
```
## Python-скрипт: analyze-metrics.py

```python
#!/usr/bin/env python3
import re
import sys

def analyze_code_metrics(code):
    """Анализ кода по общим метрикам."""

    # Подсчёт функций
    functions = len(re.findall(r'^def\s+\w+', code, re.MULTILINE))

    # Подсчёт классов
    classes = len(re.findall(r'^class\s+\w+', code, re.MULTILINE))

    # Средняя длина строки
    lines = code.split('\n')
    avg_length = sum(len(l) for l in lines) / len(lines) if lines else 0

    # Оценка сложности
    complexity = len(re.findall(r'\b(if|elif|else|for|while|and|or)\b', code))

    return {
        'functions': functions,
        'classes': classes,
        'avg_line_length': avg_length,
        'complexity_score': complexity
    }

if __name__ == '__main__':
    with open(sys.argv[1], 'r') as f:
        code = f.read()
    metrics = analyze_code_metrics(code)
    for key, value in metrics.items():
        print(f"{key}: {value:.2f}")
```

## Python-скрипт: compare-complexity.py

```python
#!/usr/bin/env python3
"""
Сравнение цикломатической сложности кода до и после изменений.
Помогает понять, действительно ли рефакторинг упрощает структуру кода.
"""

import re
import sys
from typing import Dict, Tuple

class ComplexityAnalyzer:
    """Анализ метрик сложности кода."""

    def __init__(self, code: str):
        self.code = code
        self.lines = code.split('\n')

    def calculate_cyclomatic_complexity(self) -> int:
        """
        Рассчитать цикломатическую сложность по методу Маккейба.
        Учитываются точки ветвления: if, elif, else, for, while, except, and, or
        """
        complexity = 1  # Базовая сложность

        # Подсчёт точек ветвления
        decision_patterns = [
            r'\bif\b',
            r'\belif\b',
            r'\bfor\b',
            r'\bwhile\b',
            r'\bexcept\b',
            r'\band\b(?!$)',
            r'\bor\b(?!$)'
        ]

        for pattern in decision_patterns:
            matches = re.findall(pattern, self.code)
            complexity += len(matches)

        return complexity

    def calculate_cognitive_complexity(self) -> int:
        """
        Рассчитать когнитивную сложность - насколько код трудно понимать?
        Основано на глубине вложенности и потоке управления.
        """
        cognitive = 0
        nesting_depth = 0

        for line in self.lines:
            # Отслеживание глубины вложенности
            if re.search(r'^\s*(if|for|while|def|class|try)\b', line):
                nesting_depth += 1
                cognitive += nesting_depth
            elif re.search(r'^\s*(elif|else|except|finally)\b', line):
                cognitive += nesting_depth

            # Сбрасывать вложенность при выходе из отступа
            if line and not line[0].isspace():
                nesting_depth = 0

        return cognitive

    def calculate_maintainability_index(self) -> float:
        """
        Индекс поддерживаемости находится в диапазоне 0-100.
        > 85: Отлично
        > 65: Хорошо
        > 50: Средне
        < 50: Плохо
        """
        lines = len(self.lines)
        cyclomatic = self.calculate_cyclomatic_complexity()
        cognitive = self.calculate_cognitive_complexity()

        # Упрощённый расчёт MI
        mi = 171 - 5.2 * (cyclomatic / lines) - 0.23 * (cognitive) - 16.2 * (lines / 1000)

        return max(0, min(100, mi))

    def get_complexity_report(self) -> Dict:
    """Сформировать полный отчёт по сложности."""
        return {
            'cyclomatic_complexity': self.calculate_cyclomatic_complexity(),
            'cognitive_complexity': self.calculate_cognitive_complexity(),
            'maintainability_index': round(self.calculate_maintainability_index(), 2),
            'lines_of_code': len(self.lines),
            'avg_line_length': round(sum(len(l) for l in self.lines) / len(self.lines), 2) if self.lines else 0
        }


def compare_files(before_file: str, after_file: str) -> None:
    """Сравнить метрики сложности между двумя версиями кода."""

    with open(before_file, 'r') as f:
        before_code = f.read()

    with open(after_file, 'r') as f:
        after_code = f.read()

    before_analyzer = ComplexityAnalyzer(before_code)
    after_analyzer = ComplexityAnalyzer(after_code)

    before_metrics = before_analyzer.get_complexity_report()
    after_metrics = after_analyzer.get_complexity_report()

    print("=" * 60)
    print("СРАВНЕНИЕ СЛОЖНОСТИ КОДА")
    print("=" * 60)

    print("\nДО:")
    print(f"  Цикломатическая сложность: {before_metrics['cyclomatic_complexity']}")
    print(f"  Когнитивная сложность:     {before_metrics['cognitive_complexity']}")
    print(f"  Индекс поддерживаемости:   {before_metrics['maintainability_index']}")
    print(f"  Строк кода:                {before_metrics['lines_of_code']}")
    print(f"  Средняя длина строки:      {before_metrics['avg_line_length']}")

    print("\nПОСЛЕ:")
    print(f"  Цикломатическая сложность: {after_metrics['cyclomatic_complexity']}")
    print(f"  Когнитивная сложность:     {after_metrics['cognitive_complexity']}")
    print(f"  Индекс поддерживаемости:   {after_metrics['maintainability_index']}")
    print(f"  Строк кода:                {after_metrics['lines_of_code']}")
    print(f"  Средняя длина строки:      {after_metrics['avg_line_length']}")

    print("\nИЗМЕНЕНИЯ:")
    cyclomatic_change = after_metrics['cyclomatic_complexity'] - before_metrics['cyclomatic_complexity']
    cognitive_change = after_metrics['cognitive_complexity'] - before_metrics['cognitive_complexity']
    mi_change = after_metrics['maintainability_index'] - before_metrics['maintainability_index']
    loc_change = after_metrics['lines_of_code'] - before_metrics['lines_of_code']

    print(f"  Цикломатическая сложность: {cyclomatic_change:+d}")
    print(f"  Когнитивная сложность:     {cognitive_change:+d}")
    print(f"  Индекс поддерживаемости:   {mi_change:+.2f}")
    print(f"  Строк кода:                {loc_change:+d}")

    print("\nОЦЕНКА:")
    if mi_change > 0:
        print("  ✅ Код СТАЛ более поддерживаемым")
    elif mi_change < 0:
        print("  ⚠️  Код СТАЛ менее поддерживаемым")
    else:
        print("  ➡️  Поддерживаемость не изменилась")

    if cyclomatic_change < 0:
        print("  ✅ Сложность СНИЗИЛАСЬ")
    elif cyclomatic_change > 0:
        print("  ⚠️  Сложность ВОЗРОСЛА")
    else:
        print("  ➡️  Сложность не изменилась")

    print("=" * 60)


if __name__ == '__main__':
    if len(sys.argv) != 3:
        print("Использование: python compare-complexity.py <before_file> <after_file>")
        sys.exit(1)

    compare_files(sys.argv[1], sys.argv[2])
```

## Шаблон: review-checklist.md

```markdown
# Чеклист code review

## Чеклист безопасности
- [ ] Нет хардкода учётных данных и секретов
- [ ] Валидация всех пользовательских входов
- [ ] Защита от SQL-инъекций (параметризованные запросы)
- [ ] Защита CSRF на операциях, меняющих состояние
- [ ] Защита от XSS с корректным экранированием
- [ ] Проверки аутентификации на защищённых endpoint
- [ ] Проверки авторизации на ресурсах
- [ ] Безопасное хеширование паролей (bcrypt, argon2)
- [ ] Нет чувствительных данных в логах
- [ ] HTTPS принудительно

## Чеклист производительности
- [ ] Нет N+1 запросов
- [ ] Индексы используются уместно
- [ ] Кеширование реализовано там, где это полезно
- [ ] Нет блокирующих операций в главном потоке
- [ ] Async/await используется корректно
- [ ] Большие наборы данных пагинируются
- [ ] Соединения с базой объединены в пул
- [ ] Регулярные выражения оптимизированы
- [ ] Нет лишнего создания объектов
- [ ] Утечки памяти предотвращены

## Чеклист качества
- [ ] Функции < 50 строк
- [ ] Понятные имена переменных
- [ ] Нет дублирования кода
- [ ] Корректная обработка ошибок
- [ ] Комментарии объясняют WHY, а не WHAT
- [ ] Нет `console.log` в production
- [ ] Проверка типов (TypeScript/JSDoc)
- [ ] Соблюдаются принципы SOLID
- [ ] Паттерны проектирования применены корректно
- [ ] Самодокументируемый код

## Чеклист тестирования
- [ ] Написаны модульные тесты
- [ ] Покрыты граничные случаи
- [ ] Проверены сценарии ошибок
- [ ] Есть интеграционные тесты
- [ ] Покрытие > 80%
- [ ] Нет flaky-тестов
- [ ] Замоканы внешние зависимости
- [ ] Понятные имена тестов
```

## Шаблон: finding-template.md

~~~~markdown
# Шаблон находки code review

Используйте этот шаблон для документирования каждой проблемы, найденной во время code review.

---

## Issue: [TITLE]

### Критичность
- [ ] Critical (блокирует deployment)
- [ ] High (нужно исправить до merge)
- [ ] Medium (желательно исправить скоро)
- [ ] Low (необязательно, но полезно)

### Категория
- [ ] Security
- [ ] Performance
- [ ] Code Quality
- [ ] Maintainability
- [ ] Testing
- [ ] Design Pattern
- [ ] Documentation

### Location
**Файл:** `src/components/ПользовательCard.tsx`

**Строки:** 45-52

**Функция/метод:** `renderПользовательDetails()`

### Описание проблемы

**Что:** Опишите, в чём проблема.

**Почему это важно:** Объясните влияние и почему это нужно исправить.

**Текущее поведение:** Покажите проблемный код или поведение.

**Ожидаемое поведение:** Опишите, что должно происходить вместо этого.

### Code Example

#### Текущее (проблемное)

```typescript
// Показывает проблему N+1 запросов
const users = fetchПользовательs();
users.forEach(user => {
  const posts = fetchПользовательPosts(user.id); // Запрос на пользователя!
  renderПользовательPosts(posts);
});
```

#### Рекомендуемое исправление

```typescript
// Оптимизировано с помощью JOIN-запроса
const usersWithPosts = fetchПользовательsWithPosts();
usersWithPosts.forEach(({ user, posts }) => {
  renderПользовательPosts(posts);
});
```

### Анализ влияния

| Aspect | Impact | Severity |
|--------|--------|----------|
| Performance | 100+ queries for 20 users | High |
| Пользователь Experience | Slow page load | High |
| Масштабируемость | Breaks at scale | Critical |
| Maintainability | Hard to debug | Medium |

### Связанные проблемы

- Similar issue in `AdminПользовательList.tsx` line 120
- Related PR: #456
- Related issue: #789

### Дополнительные ресурсы

- [N+1 Query Problem](https://en.wikipedia.org/wiki/N%2B1_problem)
- [Database Join Documentation](https://docs.example.com/joins)
- [Performance Optimization Guide](./docs/performance.md)

### Заметки ревьюера

- This is a common pattern in this codebase
- Consider adding this to the code style guide
- Might be worth creating a helper function

### Ответ автора (для обратной связи)

*Заполняется автором кода:*

- [ ] Fix implemented in commit: `abc123`
- [ ] Fix status: Complete / In Progress / Needs Discussion
- [ ] Questions or concerns: (describe)

---

## Статистика находок (для ревьюера)

При ревью нескольких находок отслеживайте:

- **Total Issues Found:** X
- **Critical:** X
- **High:** X
- **Medium:** X
- **Low:** X

**Рекомендация:** ✅ Approve / ⚠️ Request Changes / 🔄 Needs Discussion

**Общее качество кода:** 1-5 звёзд
~~~~

## Пример использования

~~~~markdown
Пользователь: Review this React component code

Claude: Я проведу полное code review с помощью своего skill code review.

[Загружает SKILL.md]
[Анализирует код по чеклисту]
[Запускает Python-скрипт метрик]
[Применяет шаблоны]

## Результаты code review

### Сводка
Оценка качества: 3.5/5
Ключевые находки: 7 пунктов
Приоритетные области: оптимизация производительности, обработка ошибок

### Критические проблемы: 1

**Issue**: Проблема N+1 запросов
**Location**: ПользовательComponent.tsx, строки 45-52
**Impact**: Выполняет 100+ запросов к базе для списка из 20 пользователей
**Severity**: High
**Fix**:
```javascript
// Before: N+1 queries
const users = fetchПользовательs();
users.forEach(user => fetchПользовательPosts(user.id)); // 20+ queries

// After: Single query with JOIN
const users = fetchПользовательsWithPosts(); // 1 query
```

### Находки по производительности
- Отсутствует пагинация на больших списках
- Рекомендация: использовать React.memo() для элементов
- Запрос к базе: можно оптимизировать индексом

### Находки по качеству
- Функция на строке 20 занимает 127 строк (максимум: 50)
- Отсутствует error boundary
- У props должны быть TypeScript-типы
~~~~

#### Пример 2: Skill brand voice

**Структура каталога:**

```
.claude/skills/brand-voice/
├── SKILL.md
├── brand-guidelines.md
├── tone-examples.md
└── templates/
    ├── email-template.txt
    ├── social-post-template.txt
    └── blog-post-template.md
```

**Файл:** `.claude/skills/brand-voice/SKILL.md`

```yaml
---
name: Brand Voice Consistency
description: Обеспечивать соответствие всей коммуникации правилам brand voice и tone
tags:
  - brand
  - writing
  - consistency
when_to_use: When creating marketing copy, customer communications, or public-facing content
---

# Skill brand voice

## Обзор
Этот skill обеспечивает единообразный brand voice, tone и messaging во всех коммуникациях.

## Идентичность бренда

### Миссия
Помогать командам автоматизировать рабочие процессы разработки с помощью AI

### Ценности
- **Простота**: делать сложные вещи простыми
- **Надёжность**: безотказное выполнение
- **Расширение возможностей**: усиливать человеческую креативность

### Тон общения
- **Дружелюбный, но профессиональный** - доступный, но не фамильярный
- **Ясный и краткий** - без жаргона, технические концепции объяснять просто
- **Уверенный** - мы понимаем, что делаем
- **Эмпатичный** - понимаем потребности и боли пользователя

## Правила написания

### Делайте ✅
- Используйте "you" при обращении к читателю
- Используйте активный залог: "Claude generates reports", а не "Reports are generated by Claude"
- Начинайте с ценности
- Используйте конкретные примеры
- Держите предложения короче 20 слов
- Используйте списки для ясности
- Добавляйте призывы к действию

### Не делайте ❌
- Не используйте корпоративный жаргон
- Не поучайте и не упрощайте до примитивности
- Не используйте "we believe" или "we think"
- Не используйте ALL CAPS, кроме как для акцента
- Не создавайте стены текста
- Не предполагаете технических знаний

## Словарь

### ✅ Предпочтительные термины
- Claude (не "the Claude AI")
- Генерация кода (не "auto-coding")
- Agent (не "bot")
- Streamline (не "revolutionize")
- Integrate (не "synergize")

### ❌ Избегайте терминов
- "Cutting-edge" (заезженное)
- "Game-changer" (размытое)
- "Leverage" (корпоративный жаргон)
- "Utilize" (используйте "use")
- "Paradigm shift" (неясное)
```
## Примеры

### ✅ Хороший пример
"Claude automates your code review process. Instead of manually checking each PR, Claude reviews security, performance, and quality—saving your team hours every week."

Почему это работает: понятная ценность, конкретные выгоды, ориентация на действие

### ❌ Плохой пример
"Claude leverages cutting-edge AI to provide comprehensive software development solutions."

Почему это не работает: расплывчато, корпоративный жаргон, нет конкретной ценности

## Шаблон: Email

```
Subject: [Clear, benefit-driven subject]

Hi [Name],

[Opening: What's the value for them]

[Body: How it works / What they'll get]

[Specific example or benefit]

[Call to action: Clear next step]

Best regards,
[Name]
```

## Шаблон: Social Media

```
[Hook: Grab attention in first line]
[2-3 lines: Value or interesting fact]
[Call to action: Link, question, or engagement]
[Emoji: 1-2 max for visual interest]
```

## Файл: tone-examples.md
```
Exciting announcement:
"Save 8 hours per week on code reviews. Claude reviews your PRs automatically."

Empathetic support:
"We know deployments can be stressful. Claude handles testing so you don't have to worry."

Confident product feature:
"Claude doesn't just suggest code. It understands your architecture and maintains consistency."

Educational blog post:
"Let's explore how agents improve code review workflows. Here's what we learned..."
```

#### Пример 3: Skill генератора документации

**Файл:** `.claude/skills/doc-generator/SKILL.md`

~~~~yaml
---
name: API Documentation Generator
description: Generate comprehensive, accurate API documentation from source code
version: "1.0.0"
tags:
  - documentation
  - api
  - automation
when_to_use: When creating or updating API documentation
---

# API Documentation Generator Skill

## Что генерирует

- спецификации OpenAPI/Swagger
- документацию API endpoint
- примеры использования SDK
- гайды по интеграции
- справочник кодов ошибок
- гайды по аутентификации

## Структура документации

### Для каждого endpoint

```markdown
## GET /api/v1/users/:id

### Описание
Краткое объяснение, что делает этот endpoint

### Параметры

| Имя | Тип | Обязателен | Описание |
|------|------|----------|-------------|
| id | string | Да | ID пользователя |

### Ответ

**200 Успех**
```json
{
  "id": "usr_123",
  "name": "John Doe",
  "email": "john@example.com",
  "created_at": "2025-01-15T10:30:00Z"
}
```

**404 Не найдено**
```json
{
  "error": "USER_NOT_FOUND",
  "message": "Пользователь does not exist"
}
```

### Примеры

**cURL**
```bash
curl -X GET "https://api.example.com/api/v1/users/usr_123" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

**JavaScript**
```javascript
const user = await fetch('/api/v1/users/usr_123', {
  headers: { 'Authorization': 'Bearer token' }
}).then(r => r.json());
```

**Python**
```python
response = requests.get(
    'https://api.example.com/api/v1/users/usr_123',
    headers={'Authorization': 'Bearer token'}
)
user = response.json()
```

## Python-скрипт: generate-docs.py

```python
#!/usr/bin/env python3
import ast
import json
from typing import Dict, List

class APIDocExtractor(ast.NodeVisitor):
    """Извлечь API-документацию из Python-исходника."""

    def __init__(self):
        self.endpoints = []

    def visit_FunctionDef(self, node):
        """Извлечь документацию функции."""
        if node.name.startswith('get_') or node.name.startswith('post_'):
            doc = ast.get_docstring(node)
            endpoint = {
                'name': node.name,
                'docstring': doc,
                'params': [arg.arg for arg in node.args.args],
                'returns': self._extract_return_type(node)
            }
            self.endpoints.append(endpoint)
        self.generic_visit(node)

    def _extract_return_type(self, node):
        """Извлечь тип возвращаемого значения из аннотации функции."""
        if node.returns:
            return ast.unparse(node.returns)
        return "Any"

def generate_markdown_docs(endpoints: List[Dict]) -> str:
    """Сгенерировать markdown-документацию из endpoint."""
    docs = "# API Documentation\n\n"

    for endpoint in endpoints:
        docs += f"## {endpoint['name']}\n\n"
        docs += f"{endpoint['docstring']}\n\n"
        docs += f"**Parameters**: {', '.join(endpoint['params'])}\n\n"
        docs += f"**Returns**: {endpoint['returns']}\n\n"
        docs += "---\n\n"

    return docs

if __name__ == '__main__':
    import sys
    with open(sys.argv[1], 'r') as f:
        tree = ast.parse(f.read())

    extractor = APIDocExtractor()
    extractor.visit(tree)

    markdown = generate_markdown_docs(extractor.endpoints)
    print(markdown)
~~~~
### Обнаружение и вызов skill

```mermaid
graph TD
    A["Пользователь Request"] --> B["Claude Analyzes"]
    B -->|Scans| C["Available Skills"]
    C -->|Metadata check| D["Skill Description Match?"]
    D -->|Yes| E["Load SKILL.md"]
    D -->|No| F["Try next skill"]
    F -->|More skills?| D
    F -->|No more| G["Использовать general knowledge"]
    E --> H["Extract Instructions"]
    H --> I["Execute Skill"]
    I --> J["Return Results"]
```

### Skill против других возможностей

```mermaid
graph TB
    A["Extending Claude"]
    B["Slash Commands"]
    C["Subagents"]
    D["Memory"]
    E["MCP"]
    F["Skills"]

    A --> B
    A --> C
    A --> D
    A --> E
    A --> F

    B -->|Пользователь-invoked| G["Quick shortcuts"]
    C -->|Auto-delegated| H["Isolated contexts"]
    D -->|Persistent| I["Cross-session context"]
    E -->|Real-time| J["External data access"]
    F -->|Auto-invoked| K["Autonomous execution"]
```

---

## Плагины Claude Code

### Обзор

Плагины Claude Code — это собранные вместе наборы кастомизаций (slash commands, subagents, MCP servers и hooks), которые устанавливаются одной командой. Это самый высокий уровень расширения, объединяющий несколько возможностей в цельные, разделяемые пакеты.

### Архитектура

```mermaid
graph TB
    A["Плагин"]
    B["Slash Commands"]
    C["Subagents"]
    D["MCP Servers"]
    E["Hooks"]
    F["Конфигурация"]

    A -->|объединяет| B
    A -->|объединяет| C
    A -->|объединяет| D
    A -->|объединяет| E
    A -->|объединяет| F
```

### Процесс загрузки плагина

```mermaid
sequenceDiagram
    participant Пользователь
    participant Claude as Claude Code
    participant Plugin as Marketplace плагинов
    participant Установить as Установка
    participant SlashCmds as Slash Commands
    participant Subagents
    participant MCPServers as MCP Servers
    participant Hooks
    participant Tools as Настроитьd Tools

    Пользователь->>Claude: /plugin install pr-review
    Claude->>Plugin: Скачать манифест плагина
    Plugin-->>Claude: Вернуть определение плагина
    Claude->>Установить: Извлечь компоненты
    Установить->>SlashCmds: Настроить
    Установить->>Subagents: Настроить
    Установить->>MCPServers: Настроить
    Установить->>Hooks: Настроить
    SlashCmds-->>Tools: Готово к использованию
    Subagents-->>Tools: Готово к использованию
    MCPServers-->>Tools: Готово к использованию
    Hooks-->>Tools: Готово к использованию
    Tools-->>Claude: Плагин установлен ✅
```

### Типы и распространение плагинов

| Тип | Область | Общий доступ | Авторитет | Примеры |
|------|-------|--------|-----------|----------|
| Official | Глобально | Все пользователи | Anthropic | PR Review, Security Guidance |
| Community | Публично | Все пользователи | Сообщество | DevOps, Data Science |
| Organization | Внутренне | Участники команды | Компания | Внутренние стандарты, инструменты |
| Personal | Индивидуально | Один пользователь | Разработчик | Пользовательские workflows |

### Структура определения плагина

```yaml
---
name: plugin-name
version: "1.0.0"
description: "Что делает этот плагин"
author: "Ваше имя"
license: MIT

# Метаданные плагина
tags:
  - category
  - use-case

# Требования
requires:
  - claude-code: ">=1.0.0"

# Собранные компоненты
components:
  - type: commands
    path: commands/
  - type: agents
    path: agents/
  - type: mcp
    path: mcp/
  - type: hooks
    path: hooks/

# Конфигурация
config:
  auto_load: true
  enabled_by_default: true
---
```

### Структура плагина

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   ├── task-1.md
│   ├── task-2.md
│   └── workflows/
├── agents/
│   ├── specialist-1.md
│   ├── specialist-2.md
│   └── configs/
├── skills/
│   ├── skill-1.md
│   └── skill-2.md
├── hooks/
│   └── hooks.json
├── .mcp.json
├── .lsp.json
├── settings.json
├── templates/
│   └── issue-template.md
├── scripts/
│   ├── helper-1.sh
│   └── helper-2.py
├── docs/
│   ├── README.md
│   └── USAGE.md
└── tests/
    └── plugin.test.js
```

### Практические примеры

#### Пример 1: Плагин PR review

**Файл:** `.claude-plugin/plugin.json`

```json
{
  "name": "pr-review",
  "version": "1.0.0",
  "description": "Complete PR review workflow with security, testing, and docs",
  "author": {
    "name": "Anthropic"
  },
  "license": "MIT"
}
```

**Файл:** `commands/review-pr.md`

```markdown
---
name: Review PR
description: Начать полное ревью PR с проверками безопасности и тестов
---

# Ревью PR

Эта команда запускает полное ревью pull request, включая:

1. Анализ безопасности
2. Проверку покрытия тестами
3. Обновление документации
4. Проверки качества кода
5. Оценку влияния на производительность
```

**Файл:** `agents/security-reviewer.md`

```yaml
---
name: security-reviewer
description: Ревью кода с фокусом на безопасность
tools: read, grep, diff
---

# Ревьюер безопасности

Специализируется на поиске уязвимостей безопасности:
- Проблемы аутентификации/авторизации
- Утечка данных
- Инъекционные атаки
- Безопасная конфигурация
```

**Установка:**

```bash
/plugin install pr-review

# Результат:
# ✅ 3 slash-команды установлены
# ✅ 3 subagents настроены
# ✅ 2 MCP-сервера подключены
# ✅ 4 hooks зарегистрированы
# ✅ Готово к использованию!
```

#### Пример 2: Плагин DevOps

**Компоненты:**

```
devops-automation/
├── commands/
│   ├── deploy.md
│   ├── rollback.md
│   ├── status.md
│   └── incident.md
├── agents/
│   ├── deployment-specialist.md
│   ├── incident-commander.md
│   └── alert-analyzer.md
├── mcp/
│   ├── github-config.json
│   ├── kubernetes-config.json
│   └── prometheus-config.json
├── hooks/
│   ├── pre-deploy.js
│   ├── post-deploy.js
│   └── on-error.js
└── scripts/
    ├── deploy.sh
    ├── rollback.sh
    └── health-check.sh
```

#### Пример 3: Плагин документации

**Собранные компоненты:**

```
documentation/
├── commands/
│   ├── generate-api-docs.md
│   ├── generate-readme.md
│   ├── sync-docs.md
│   └── validate-docs.md
├── agents/
│   ├── api-documenter.md
│   ├── code-commentator.md
│   └── example-generator.md
├── mcp/
│   ├── github-docs-config.json
│   └── slack-announce-config.json
└── templates/
    ├── api-endpoint.md
    ├── function-docs.md
    └── adr-template.md
```

### Маркетплейс плагинов

```mermaid
graph TB
    A["Маркетплейс плагинов"]
    B["Официальный<br/>Anthropic"]
    C["Сообщество<br/>Marketplace"]
    D["Корпоративный<br/>Registry"]

    A --> B
    A --> C
    A --> D

    B -->|Категории| B1["Разработка"]
    B -->|Категории| B2["DevOps"]
    B -->|Категории| B3["Документация"]

    C -->|Поиск| C1["Автоматизация DevOps"]
    C -->|Поиск| C2["Мобильная разработка"]
    C -->|Поиск| C3["Data Science"]

    D -->|Внутренние| D1["Стандарты компании"]
    D -->|Внутренние| D2["Наследованные системы"]
    D -->|Внутренние| D3["Соответствие требованиям"]
```

### Установка и жизненный цикл плагина

```mermaid
graph LR
    A["Найти"] -->|Просмотр| B["Marketplace"]
    B -->|Выбрать| C["Страница плагина"]
    C -->|Посмотреть| D["Компоненты"]
    D -->|Установить| E["/plugin install"]
    E -->|Извлечь| F["Настроить"]
    F -->|Активировать| G["Использовать"]
    G -->|Проверить| H["Обновить"]
    H -->|Available| G
    G -->|Done| I["Disable"]
    I -->|Later| J["Enable"]
    J -->|Back| G
```

### Сравнение возможностей плагинов

| Возможность | Slash Command | Skill | Subagent | Plugin |
|---------|---------------|-------|----------|--------|
| **Установка** | Ручное копирование | Ручное копирование | Ручная настройка | Одна команда |
| **Время настройки** | 5 минут | 10 минут | 15 минут | 2 минуты |
| **Упаковка** | Один файл | Один файл | Один файл | Несколько |
| **Версионирование** | Вручную | Вручную | Вручную | Автоматически |
| **Деление в команде** | Копирование файла | Копирование файла | Копирование файла | ID установки |
| **Обновления** | Вручную | Вручную | Вручную | Доступны автоматически |
| **Зависимости** | Нет | Нет | Нет | Могут быть |
| **Marketplace** | Нет | Нет | Нет | Да |
| **Распространение** | Репозиторий | Репозиторий | Репозиторий | Marketplace |

### Сценарии использования плагинов

| Сценарий | Рекомендация | Почему |
|----------|-----------------|-----|
| **Онбординг команды** | ✅ Используйте плагин | Мгновенная настройка, все конфигурации |
| **Настройка фреймворка** | ✅ Используйте плагин | Упаковывает команды для конкретного фреймворка |
| **Корпоративные стандарты** | ✅ Используйте плагин | Централизованное распространение, версионирование |
| **Быстрая автоматизация задач** | ❌ Используйте команду | Слишком сложно для этого |
| **Экспертиза в одной области** | ❌ Используйте skill | Слишком тяжело, лучше skill |
| **Специализированный анализ** | ❌ Используйте subagent | Создавайте вручную или используйте skill |
| **Доступ к живым данным** | ❌ Используйте MCP | Отдельно, не нужно упаковывать |

### Когда создавать плагин

```mermaid
graph TD
    A["Нужно ли создавать плагин?"]
    A -->|Нужно несколько компонентов| B{"Несколько команд<br/>или subagents<br/>или MCP?"}
    B -->|Да| C["✅ Создать плагин"]
    B -->|Нет| D["Использовать отдельную возможность"]
    A -->|Командный workflow| E{"Делиться с<br/>командой?"}
    E -->|Да| C
    E -->|Нет| F["Оставить как локальную настройку"]
    A -->|Сложная настройка| G{"Нужна авто<br/>конфигурация?"}
    G -->|Да| C
    G -->|Нет| D
```

### Публикация плагина

**Шаги публикации:**

1. Создайте структуру плагина со всеми компонентами
2. Напишите манифест `.claude-plugin/plugin.json`
3. Создайте `README.md` с документацией
4. Протестируйте локально через `/plugin install ./my-plugin`
5. Отправьте в marketplace плагинов
6. Пройдите ревью и одобрение
7. Опубликуйте в marketplace
8. Пользователи смогут установить одной командой

**Пример заявки:**

~~~~markdown
# PR Review Plugin

## Описание
Полный workflow PR review с проверками безопасности, тестов и документации.

## Что включено
- 3 slash-команды для разных типов review
- 3 специализированных subagents
- Интеграция GitHub и CodeQL MCP
- Автоматические hooks для проверки безопасности

## Установка
```bash
/plugin install pr-review
```

## Возможности
✅ Анализ безопасности
✅ Проверка покрытия тестами
✅ Проверка документации
✅ Code quality assessment
✅ Performance impact analysis

## Использование
```bash
/review-pr
/check-security
/check-tests
```

## Требования
- Claude Code 1.0+
- Доступ к GitHub
- CodeQL (опционально)
~~~~

### Плагин против ручной конфигурации

**Ручная настройка (2+ часа):**
- Устанавливать slash commands по одному
- Создавать subagents по отдельности
- Настраивать MCP отдельно
- Настраивать hooks вручную
- Документировать всё
- Делиться с командой (и надеяться, что они всё настроят правильно)

**С плагином (2 минуты):**
```bash
/plugin install pr-review
# ✅ Всё установлено и настроено
# ✅ Готово к немедленному использованию
# ✅ Команда может воспроизвести точную настройку
```

---

## Сравнение и интеграция

### Матрица сравнения возможностей

| Возможность | Вызов | Постоянство | Область | Сценарий использования |
|---------|-----------|------------|-------|----------|
| **Slash Commands** | Вручную (`/cmd`) | Только сессия | Одна команда | Быстрые ярлыки |
| **Subagents** | Авто-делегирование | Изолированный контекст | Специализированная задача | Распределение задач |
| **Memory** | Загружается автоматически | Межсессионно | Контекст пользователя/команды | Долгосрочное обучение |
| **MCP Protocol** | Запрашивается автоматически | Внешние данные в реальном времени | Доступ к живым данным | Динамическая информация |
| **Skills** | Вызываются автоматически | На основе файловой системы | Переиспользуемая экспертиза | Автоматизированные workflows |

### Таймлайн взаимодействия

```mermaid
graph LR
    A["Старт сессии"] -->|Загрузка| B["Memory (CLAUDE.md)"]
    B -->|Обнаружить| C["Доступные skills"]
    C -->|Зарегистрировать| D["Slash Commands"]
    D -->|Подключить| E["MCP Servers"]
    E -->|Готово| F["Взаимодействие с пользователем"]

    F -->|Ввести /cmd| G["Slash Command"]
    F -->|Запрос| H["Автовызов skill"]
    F -->|Запросить| I["Данные MCP"]
    F -->|Сложная задача| J["Делегировать subagent"]

    G -->|Uses| B
    H -->|Uses| B
    I -->|Uses| B
    J -->|Uses| B
```

### Практический пример интеграции: автоматизация поддержки клиентов

#### Архитектура

```mermaid
graph TB
    Пользователь["Customer Email"] -->|Receives| Router["Support Router"]

    Router -->|Analyze| Memory["Memory<br/>Customer history"]
    Router -->|Lookup| MCP1["MCP: Customer DB<br/>Previous tickets"]
    Router -->|Проверить| MCP2["MCP: Slack<br/>Team status"]

    Router -->|Сложное| Sub1["Subagent: Tech Support<br/>Контекст: технические проблемы"]
    Router -->|Простое| Sub2["Subagent: Billing<br/>Контекст: платежные вопросы"]
    Router -->|Срочное| Sub3["Subagent: Escalation<br/>Контекст: обработка приоритета"]

    Sub1 -->|Форматировать| Skill1["Skill: Response Generator<br/>Сохранён брендовый стиль"]
    Sub2 -->|Форматировать| Skill2["Skill: Response Generator"]
    Sub3 -->|Форматировать| Skill3["Skill: Response Generator"]

    Skill1 -->|Сгенерировать| Output["Отформатированный ответ"]
    Skill2 -->|Generate| Output
    Skill3 -->|Generate| Output

    Output -->|Опубликовать| MCP3["MCP: Slack<br/>Уведомить команду"]
    Output -->|Отправить| Reply["Ответ клиенту"]
```

#### Поток запроса

```markdown
## Поток запроса поддержки клиентов

### 1. Входящее письмо
"При попытке загрузить файлы получаю ошибку 500. Это блокирует мой workflow!"

### 2. Поиск в Memory
- Загружает CLAUDE.md со стандартами поддержки
- Проверяет историю клиента: VIP-клиент, 3-й инцидент за этот месяц

### 3. Запросы MCP
- GitHub MCP: Список открытых issues (находит связанный bug report)
- Database MCP: Проверка статуса системы (сбоев не обнаружено)
- Slack MCP: Проверяет, в курсе ли engineering

### 4. Обнаружение и загрузка skill
- Запрос соответствует skill "Technical Support"
- Загружает шаблон ответа поддержки из Skill

### 5. Делегирование subagent
- Направляет в Tech Support Subagent
- Передаёт контекст: история клиента, детали ошибки, известные проблемы
- Subagent имеет полный доступ к инструментам read, bash, grep

### 6. Обработка subagent
Tech Support Subagent:
- Ищет в codebase причину ошибки 500 при загрузке файлов
- Находит недавнее изменение в commit 8f4a2c
- Создаёт документацию с обходным решением

### 7. Выполнение skill
Response Generator Skill:
- Использует guidelines Brand Voice
- Форматирует ответ с эмпатией
- Включает шаги обходного решения
- Ссылается на связанную документацию

### 8. Вывод MCP
- Публикует обновление в канал Slack #support
- Отмечает engineering team
- Обновляет ticket в Jira MCP

### 9. Ответ
Клиент получает:
- Сочувственное подтверждение
- Объяснение причины
- Немедленное обходное решение
- Сроки постоянного исправления
- Ссылку на связанные issues
```

### Полная оркестровка возможностей

```mermaid
sequenceDiagram
    participant Пользователь
    participant Claude as Claude Code
    participant Memory as Memory<br/>CLAUDE.md
    participant MCP as MCP Servers
    participant Skills as Skills
    participant SubAgent as Subagents

    Пользователь->>Claude: Request: "Build auth system"
    Claude->>Memory: Load project standards
    Memory-->>Claude: Auth standards, team practices
    Claude->>MCP: Query GitHub for similar implementations
    MCP-->>Claude: Code examples, best practices
    Claude->>Skills: Detect matching Skills
    Skills-->>Claude: Security Review Skill + Testing Skill
    Claude->>SubAgent: Delegate implementation
    SubAgent->>SubAgent: Build feature
    Claude->>Skills: Apply Security Review Skill
    Skills-->>Claude: Security checklist results
    Claude->>SubAgent: Delegate testing
    SubAgent-->>Claude: Test results
    Claude->>Пользователь: Complete system delivered
```

### Когда использовать каждую возможность

```mermaid
graph TD
    A["Новая задача"] --> B{Тип задачи?}

    B -->|Повторяющийся workflow| C["Slash Command"]
    B -->|Нужны данные в реальном времени| D["MCP Protocol"]
    B -->|Запомнить на будущее| E["Memory"]
    B -->|Специализированная подзадача| F["Subagent"]
    B -->|Предметная работа| G["Skill"]

    C --> C1["✅ Командный ярлык"]
    D --> D1["✅ Доступ к живому API"]
    E --> E1["✅ Постоянный контекст"]
    F --> F1["✅ Параллельное выполнение"]
    G --> G1["✅ Автовызванная экспертиза"]
```

### Дерево выбора

```mermaid
graph TD
    Start["Нужно расширить Claude?"]

    Start -->|Быстрая повторяющаяся задача| A{"Вручную или автоматически?"}
    A -->|Вручную| B["Slash Command"]
    A -->|Автоматически| C["Skill"]

    Start -->|Нужны внешние данные| D{"В реальном времени?"}
    D -->|Да| E["MCP Protocol"]
    D -->|Нет/межсессионно| F["Memory"]

    Start -->|Сложный проект| G{"Несколько ролей?"}
    G -->|Да| H["Subagents"]
    G -->|Нет| I["Skills + Memory"]

    Start -->|Долгосрочный контекст| J["Memory"]
    Start -->|Командный workflow| K["Slash Command +<br/>Memory"]
    Start -->|Полная автоматизация| L["Skills +<br/>Subagents +<br/>MCP"]
```

---

## Сводная таблица

| Аспект | Slash Commands | Subagents | Memory | MCP | Skills | Plugins |
|--------|---|---|---|---|---|---|
| **Сложность настройки** | Easy | Medium | Easy | Medium | Medium | Easy |
| **Кривая обучения** | Low | Medium | Low | Medium | Medium | Low |
| **Польза для команды** | High | High | Medium | High | High | Very High |
| **Уровень автоматизации** | Low | High | Medium | High | High | Very High |
| **Управление контекстом** | Single-session | Isolated | Persistent | Real-time | Persistent | All features |
| **Нагрузка на поддержку** | Low | Medium | Low | Medium | Medium | Low |
| **Масштабируемость** | Good | Excellent | Good | Excellent | Excellent | Excellent |
| **Совместимость для обмена** | Fair | Fair | Good | Good | Good | Excellent |
| **Версионирование** | Manual | Manual | Manual | Manual | Manual | Automatic |
| **Установка** | Manual copy | Manual config | N/A | Manual config | Manual copy | One command |

---

## Руководство по быстрому старту

### Неделя 1: Начните просто
- Создайте 2-3 slash command для типовых задач
- Включите Memory в настройках
- Опишите стандарты команды в `CLAUDE.md`

### Неделя 2: Добавьте доступ к данным в реальном времени
- Настройте 1 MCP (GitHub или Database)
- Используйте `/mcp` для настройки
- Запрашивайте живые данные в своих workflows

### Неделя 3: Распределяйте работу
- Создайте первый Subagent для конкретной роли
- Используйте команду `/agents`
- Проверьте делегирование на простой задаче

### Неделя 4: Автоматизируйте всё
- Создайте первый Skill для повторяющейся автоматизации
- Используйте marketplace навыков или создайте свой
- Объедините все возможности в единый workflow

### Постоянно
- Ежемесячно пересматривайте и обновляйте Memory
- Добавляйте новые Skills по мере появления шаблонов
- Оптимизируйте MCP-запросы
- Улучшайте промпты Subagent

---

## Hooks

### Обзор

Hooks — это shell-команды, запускаемые по событиям и автоматически выполняющиеся в ответ на события Claude Code. Они дают автоматизацию, валидацию и собственные workflows без ручного вмешательства.

### События hooks

Claude Code поддерживает **25 событий hooks** в четырёх типах hooks (command, http, prompt, agent):

| Событие hook | Триггер | Сценарии использования |
|------------|---------|-----------|
| **SessionStart** | Сессия начинается/возобновляется/очищается/compact | Настройка окружения, инициализация |
| **InstructionsLoaded** | Загружен `CLAUDE.md` или файл правил | Валидация, преобразование, дополнение |
| **ПользовательPromptSubmit** | Пользователь отправляет запрос | Проверка ввода, фильтрация запроса |
| **PreToolUse** | Перед запуском любого инструмента | Валидация, контроль одобрения, логирование |
| **PermissionRequest** | Показан диалог разрешения | Сценарии авто-одобрения/отклонения |
| **PostToolUse** | Инструмент успешно завершён | Автоформатирование, уведомления, очистка |
| **PostToolUseFailure** | Сбой выполнения инструмента | Обработка ошибок, логирование |
| **Notification** | Отправлено уведомление | Оповещения, внешние интеграции |
| **SubagentStart** | Subagent запущен | Внедрение контекста, инициализация |
| **SubagentStop** | Subagent завершён | Валидация результата, логирование |
| **Stop** | Claude завершает ответ | Генерация сводки, задачи очистки |
| **StopFailure** | Ошибка API завершает ход | Восстановление после ошибки, логирование |
| **TeammateIdle** | Напарник в команде агентов простаивает | Распределение работы, координация |
| **TaskCompleted** | Задача отмечена как завершённая | Постобработка задачи |
| **TaskCreated** | Задача создана через TaskCreate | Отслеживание, логирование |
| **ConfigChange** | Изменён конфиг-файл | Валидация, распространение |
| **CwdChanged** | Изменился рабочий каталог | Настройка под каталог |
| **FileChanged** | Изменился отслеживаемый файл | Мониторинг файлов, триггер перестройки |
| **PreCompact** | Перед сжатием контекста | Сохранение состояния |
| **PostCompact** | После завершения compact | Действия после compact |
| **WorktreeCreate** | Создаётся worktree | Настройка окружения, установка зависимостей |
| **WorktreeRemove** | Worktree удаляется | Очистка, освобождение ресурсов |
| **Elicitation** | MCP-сервер запрашивает ввод пользователя | Проверка ввода |
| **ElicitationResult** | Пользователь отвечает на elicitation | Обработка ответа |
| **SessionEnd** | Сессия завершается | Очистка, финальное логирование |

### Распространённые hooks

Hooks настраиваются в `~/.claude/settings.json` (уровень пользователя) или `.claude/settings.json` (уровень проекта):

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "prettier --write $CLAUDE_FILE_PATH"
          }
        ]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Edit",
        "hooks": [
          {
            "type": "command",
            "command": "eslint $CLAUDE_FILE_PATH"
          }
        ]
      }
    ]
  }
}
```

### Переменные окружения hooks

- `$CLAUDE_FILE_PATH` - Путь к редактируемому/записываемому файлу
- `$CLAUDE_TOOL_NAME` - Имя используемого инструмента
- `$CLAUDE_SESSION_ID` - Идентификатор текущей сессии
- `$CLAUDE_PROJECT_DIR` - Путь к каталогу проекта

### Лучшие практики

✅ **Делайте:**
- Держите hooks быстрыми (< 1 секунды)
- Используйте hooks для валидации и автоматизации
- Обрабатывайте ошибки корректно
- Используйте абсолютные пути

❌ **Не делайте:**
- Не делайте hooks интерактивными
- Не используйте hooks для долгих задач
- Не хардкодьте учётные данные

**Смотрите**: [06-hooks/](06-hooks/) для подробных примеров

---

## Checkpoints и откат

### Обзор

Checkpoints позволяют сохранять состояние беседы и откатываться к предыдущим точкам, что даёт безопасные эксперименты и исследование нескольких подходов.

### Основные понятия

| Понятие | Описание |
|---------|-------------|
| **Checkpoint** | Снимок состояния беседы, включая сообщения, файлы и контекст |
| **Rewind** | Возврат к предыдущему checkpoint с откатом последующих изменений |
| **Branch Point** | Checkpoint, от которого исследуются разные подходы |

### Доступ к checkpoints

Checkpoints создаются автоматически при каждом запросе пользователя. Чтобы откатиться:

```bash
# Нажмите Esc дважды, чтобы открыть браузер checkpoints
Esc + Esc

# Или используйте команду /rewind
/rewind
```

Когда вы выбираете checkpoint, доступны пять вариантов:
1. **Restore code and conversation** -- Откатить и код, и разговор к этой точке
2. **Restore conversation** -- Откатить сообщения, оставить текущий код
3. **Restore code** -- Откатить файлы, оставить разговор
4. **Summarize from here** -- Сжать разговор в сводку
5. **Never mind** -- Отмена

### Сценарии использования

| Сценарий | Workflow |
|----------|----------|
| **Исследование подходов** | Сохранить → Попробовать A → Сохранить → Откат → Попробовать B → Сравнить |
| **Безопасный рефакторинг** | Сохранить → Рефакторинг → Тест → Если ошибка: Откат |
| **A/B-тестирование** | Сохранить → Дизайн A → Сохранить → Откат → Дизайн B → Сравнить |
| **Восстановление после ошибки** | Замечена проблема → Откат к последнему рабочему состоянию |

### Конфигурация

```json
{
  "autoCheckpoint": true
}
```

**Смотрите**: [08-checkpoints/](08-checkpoints/) для подробных примеров

---

## Продвинутые возможности

### Режим планирования

Создавайте детальные планы реализации до написания кода.

**Активация:**
```bash
/plan Implement user authentication system
```

**Преимущества:**
- Понятный план с оценкой времени
- Оценка рисков
- Систематическое разбиение задач
- Возможность ревью и изменения плана

### Расширенное мышление

Глубокое рассуждение для сложных задач.

**Активация:**
- Переключение `Alt+T` (или `Option+T` на macOS) во время сессии
- Установите переменную `MAX_THINKING_TOKENS` для программного управления

```bash
# Включить расширенное мышление через переменную окружения
export MAX_THINKING_TOKENS=50000
claude -p "Should we use microservices or monolith?"
```

**Преимущества:**
- Тщательный анализ компромиссов
- Лучшие архитектурные решения
- Учет граничных случаев
- Систематическая оценка

### Фоновые задачи

Запускайте долгие операции, не блокируя беседу.

**Использование:**
```bash
Пользователь: Run tests in background

Claude: Started task bg-1234

/task list           # Show all tasks
/task status bg-1234 # Проверить progress
/task show bg-1234   # View output
/task cancel bg-1234 # Cancel task
```

### Режимы разрешений

Управляйте тем, что может делать Claude.

| Режим | Описание | Сценарий использования |
|------|-------------|----------|
| **default** | Стандартные разрешения с запросами для чувствительных действий | Общая разработка |
| **acceptEdits** | Автоматически принимать правки файлов без подтверждения | Доверенные workflows редактирования |
| **plan** | Только анализ и планирование, без изменения файлов | Code review, планирование архитектуры |
| **auto** | Автоматически одобрять безопасные действия, спрашивать только для рискованных | Баланс автономности и безопасности |
| **dontAsk** | Выполнять все действия без запросов подтверждения | Опытные пользователи, автоматизация |
| **bypassPermissions** | Полный неограниченный доступ, без проверок безопасности | CI/CD pipeline, доверенные скрипты |

**Использование:**
```bash
claude --permission-mode plan          # Read-only analysis
claude --permission-mode acceptEdits   # Auto-accept edits
claude --permission-mode auto          # Auto-approve safe actions
claude --permission-mode dontAsk       # No confirmation prompts
```

### Headless Mode (режим вывода)

Запускайте Claude Code без интерактивного ввода для автоматизации и CI/CD, используя флаг `-p` (print).

**Использование:**
```bash
# Запустить конкретную задачу
claude -p "Run all tests"

# Передать ввод через pipe для анализа
cat error.log | claude -p "explain this error"

# Интеграция CI/CD (GitHub Actions)
- name: AI Code Review
  run: claude -p "Review PR changes and report issues"

# JSON-вывод для скриптов
claude -p --output-format json "list all functions in src/"
```

### Запланированные задачи

Запускайте задачи по повторяющемуся расписанию с помощью команды `/loop`.

**Использование:**
```bash
/loop every 30m "Запускать тесты и сообщать о сбоях"
/loop every 2h "Проверять обновления зависимостей"
/loop every 1d "Генерировать ежедневную сводку изменений кода"
```

Запланированные задачи выполняются в фоне и сообщают результат после завершения. Они полезны для непрерывного мониторинга, периодических проверок и автоматизированного обслуживания.

### Интеграция с Chrome

Claude Code может интегрироваться с браузером Chrome для задач web-автоматизации. Это даёт такие возможности, как навигация по страницам, заполнение форм, создание скриншотов и извлечение данных с сайтов прямо в рабочем процессе разработки.

### Управление сессиями

Управляйте несколькими рабочими сессиями.

**Команды:**
```bash
/resume                # Возобновить предыдущий разговор
/rename "Feature"      # Назвать текущую сессию
/fork                  # Разветвить в новую сессию
claude -c              # Продолжить последнюю беседу
claude -r "Feature"    # Возобновить сессию по имени/ID
```

### Интерактивные возможности

**Горячие клавиши:**
- `Ctrl + R` - Поиск по истории команд
- `Tab` - Автодополнение
- `↑ / ↓` - История команд
- `Ctrl + L` - Очистить экран

**Многострочный ввод:**
```bash
Пользователь: \
> Длинный сложный запрос
> на несколько строк
> \end
```

### Конфигурация

Полный пример конфигурации:

```json
{
  "planning": {
    "autoEnter": true,
    "requireApproval": true
  },
  "extendedThinking": {
    "enabled": true,
    "showThinkingProcess": true
  },
  "backgroundTasks": {
    "enabled": true,
    "maxConcurrentTasks": 5
  },
  "permissions": {
    "mode": "default"
  }
}
```

**Смотрите**: [09-advanced-features/](09-advanced-features/) для полного гайда

---

## Ресурсы

- [Документация Claude Code](https://code.claude.com/docs/en/overview)
- [Документация Anthropic](https://docs.anthropic.com)
- [MCP GitHub Servers](https://github.com/modelcontextprotocol/servers)
- [Anthropic Cookbook](https://github.com/anthropics/anthropic-cookbook)

---

*Последнее обновление: март 2026*
*Для Claude Haiku 4.5, Sonnet 4.6 и Opus 4.6*
*Теперь включает: Hooks, Checkpoints, Planning Mode, Extended Thinking, Background Tasks, Permission Modes (6 modes), Headless Mode, Session Management, Auto Memory, Agent Teams, Scheduled Tasks, Chrome Integration, Channels, Voice Dictation и Bundled Skills*
