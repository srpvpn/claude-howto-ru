<picture>
  <source media="(prefers-color-scheme: dark)" srcset="resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="resources/logos/claude-howto-logo.svg">
</picture>

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-2.2.0-brightgreen)](CHANGELOG.md)
[![Claude Code](https://img.shields.io/badge/Claude_Code-2.1+-purple)](https://code.claude.com)

# Освойте Claude Code за выходные

От простого ввода `claude` до оркестрации агентов, hooks, skills и MCP-серверов — с наглядными туториалами, шаблонами для копирования и пошаговым учебным маршрутом.

> **Примечание о переводе:** это любительский перевод оригинального проекта [luongnv89/claude-howto](https://github.com/luongnv89/claude-howto). Спасибо автору оригинала за основу. Я открыт к вашим улучшениям перевода и правкам формулировок.

**[Начать за 15 минут](#-get-started-in-15-minutes)** | **[Определить свой уровень](#-not-sure-where-to-start)** | **[Открыть каталог возможностей](CATALOG.md)**

---

## Содержание

- [Проблема](#the-problem)
- [Как Claude How To это исправляет](#how-claude-how-to-fixes-this)
- [Как это работает](#how-it-works)
- [Не знаете, с чего начать?](#-not-sure-where-to-start)
- [Начать за 15 минут](#-get-started-in-15-minutes)
- [Что можно собрать на этой базе?](#what-can-you-build-with-this)
- [FAQ](#faq)
- [Вклад](#contributing)
- [Лицензия](#license)

---

<a id="the-problem"></a>
## Проблема

Вы установили Claude Code. Запустили несколько промптов. Что дальше?

- **Официальная документация описывает возможности, но не показывает, как собирать их вместе.** Вы знаете, что существуют slash-команды, но не понимаете, как связать их с hooks, memory и subagents в рабочий процесс, который реально экономит часы.
- **Нет понятного маршрута обучения.** Что учить раньше: MCP или hooks? skills или subagents? В итоге приходится пролистывать всё подряд и не осваивать по-настоящему ничего.
- **Примеры слишком простые.** Slash-команда в стиле `hello world` не помогает построить production-пайплайн code review, который использует memory, делегирует задачи специализированным агентам и автоматически запускает проверки безопасности.

Вы оставляете без дела 90% возможностей Claude Code и даже не знаете, чего именно вам не хватает.

---

<a id="how-claude-how-to-fixes-this"></a>
## Как Claude How To это исправляет

Это не ещё один справочник по возможностям. Это **структурированный, наглядный и ориентированный на примеры гид**, который учит использовать каждую функцию Claude Code с реальными шаблонами, которые можно сразу копировать в свой проект.

| | Official Docs | Этот гид |
|--|---------------|------------|
| **Формат** | Справочная документация | Визуальные туториалы с диаграммами Mermaid |
| **Глубина** | Описание возможностей | Как это работает изнутри |
| **Примеры** | Базовые фрагменты | Production-ready шаблоны, которые можно использовать сразу |
| **Структура** | По функциям | Пошаговый путь обучения от новичка до продвинутого уровня |
| **Онбординг** | Самостоятельный | Направляемая дорожная карта с оценкой времени |
| **Самооценка** | Нет | Интерактивные квизы, которые помогают найти пробелы и собрать персональный маршрут |

### Что вы получите:

- **10 учебных модулей**, покрывающих все возможности Claude Code — от slash-команд до собственных командных команд агентов
- **Конфиги для копирования** — slash-команды, шаблоны CLAUDE.md, hook-скрипты, MCP-конфиги, определения subagents и готовые наборы плагинов
- **Диаграммы Mermaid**, показывающие внутреннюю механику каждой функции, чтобы понимать не только *как*, но и *почему*
- **Направляемый учебный маршрут**, который проведёт вас от новичка до power user за 11-13 часов
- **Встроенная самооценка** — запустите `/self-assessment` или `/lesson-quiz hooks` прямо в Claude Code, чтобы найти пробелы

**[Начать учебный маршрут ->](LEARNING-ROADMAP.md)**

---

<a id="how-it-works"></a>
## Как это работает

### 1. Определите свой уровень

Пройдите [квиз самооценки](LEARNING-ROADMAP.md#-find-your-level) или запустите `/self-assessment` в Claude Code. На основе того, что вы уже знаете, получите персональную дорожную карту.

### 2. Следуйте по направляемому маршруту

Проходите 10 модулей по порядку — каждый опирается на предыдущий. По ходу обучения копируйте шаблоны прямо в свой проект.

### 3. Собирайте возможности в рабочие процессы

Сила этого подхода в комбинации возможностей. Вы научитесь связывать slash-команды + memory + subagents + hooks в автоматизированные пайплайны для code review, деплоя и генерации документации.

### 4. Проверьте понимание

После каждого модуля запускайте `/lesson-quiz [topic]`. Квиз точно показывает, что вы упустили, чтобы можно было быстро закрыть пробелы.

**[Начать за 15 минут](#-get-started-in-15-minutes)**

---

## О проекте перевода

- Это русскоязычная версия оригинального гайда `claude-howto`
- Основа и автор оригинального проекта: [luongnv89/claude-howto](https://github.com/luongnv89/claude-howto)
- Если заметите неточный перевод, корявую формулировку или смысловую ошибку, улучшения приветствуются

---

<a id="-not-sure-where-to-start"></a>
## Не знаете, с чего начать?

Пройдите самооценку или выберите свой уровень:

| Уровень | Вы умеете... | Начните здесь | Время |
|-------|-----------|------------|------|
| **Beginner** | Запускать Claude Code и общаться с ним | [Slash Commands](01-slash-commands/) | ~2.5 часа |
| **Intermediate** | Использовать CLAUDE.md и собственные команды | [Skills](03-skills/) | ~3.5 часа |
| **Advanced** | Настраивать MCP-серверы и hooks | [Advanced Features](09-advanced-features/) | ~5 часов |

**Полный маршрут обучения из 10 модулей:**

| Порядок | Модуль | Уровень | Время |
|-------|--------|-------|------|
| 1 | [Slash Commands](01-slash-commands/) | Beginner | 30 мин |
| 2 | [Memory](02-memory/) | Beginner+ | 45 мин |
| 3 | [Checkpoints](08-checkpoints/) | Intermediate | 45 мин |
| 4 | [CLI Basics](10-cli/) | Beginner+ | 30 мин |
| 5 | [Skills](03-skills/) | Intermediate | 1 час |
| 6 | [Hooks](06-hooks/) | Intermediate | 1 час |
| 7 | [MCP](05-mcp/) | Intermediate+ | 1 час |
| 8 | [Subagents](04-subagents/) | Intermediate+ | 1.5 часа |
| 9 | [Advanced Features](09-advanced-features/) | Advanced | 2-3 часа |
| 10 | [Plugins](07-plugins/) | Advanced | 2 часа |

**[Полная дорожная карта обучения ->](LEARNING-ROADMAP.md)**

---

<a id="-get-started-in-15-minutes"></a>
## Начать за 15 минут

```bash
# 1. Clone the guide
git clone https://github.com/luongnv89/claude-howto.git
cd claude-howto

# 2. Copy your first slash command
mkdir -p /path/to/your-project/.claude/commands
cp 01-slash-commands/optimize.md /path/to/your-project/.claude/commands/

# 3. Try it — in Claude Code, type:
# /optimize

# 4. Ready for more? Set up project memory:
cp 02-memory/project-CLAUDE.md /path/to/your-project/CLAUDE.md

# 5. Install a skill:
cp -r 03-skills/code-review ~/.claude/skills/
```

Нужна полная настройка? Вот **базовая настройка за 1 час**:

```bash
# Slash commands (15 min)
cp 01-slash-commands/*.md .claude/commands/

# Project memory (15 min)
cp 02-memory/project-CLAUDE.md ./CLAUDE.md

# Install a skill (15 min)
cp -r 03-skills/code-review ~/.claude/skills/

# Weekend goal: add hooks, subagents, MCP, and plugins
# Follow the learning path for guided setup
```

<a id="installation-quick-reference"></a>
**[Посмотреть полную справку по установке](#installation-quick-reference)**

---

<a id="what-can-you-build-with-this"></a>
## Что можно собрать на этой базе?

| Сценарий | Какие возможности вы объедините |
|----------|------------------------|
| **Автоматизированный code review** | Slash Commands + Subagents + Memory + MCP |
| **Онбординг команды** | Memory + Slash Commands + Plugins |
| **Автоматизация CI/CD** | CLI Reference + Hooks + Background Tasks |
| **Генерация документации** | Skills + Subagents + Plugins |
| **Security Audits** | Subagents + Skills + Hooks (режим только для чтения) |
| **DevOps-пайплайны** | Plugins + MCP + Hooks + Background Tasks |
| **Сложный рефакторинг** | Checkpoints + Planning Mode + Hooks |

---

<a id="faq"></a>
## FAQ

**Это бесплатно?**
Да. Лицензия MIT, бесплатно навсегда. Используйте в личных проектах, на работе, в команде — ограничений нет, кроме необходимости сохранять уведомление о лицензии.

**Гид поддерживается?**
Да, активно. Он синхронизируется с каждым релизом Claude Code. Текущая версия: v2.2.0 (март 2026), совместима с Claude Code 2.1+.

**Чем это отличается от официальной документации?**
Официальная документация — это справочник по функциям. Этот гид — туториал с диаграммами, готовыми к production шаблонами и последовательным маршрутом обучения. Они дополняют друг друга: начинайте здесь, чтобы учиться, и обращайтесь к docs за точными деталями.

**Сколько времени займёт весь путь?**
11-13 часов на полный маршрут. Но пользу вы получите уже за 15 минут — достаточно скопировать шаблон slash-команды и попробовать его.

**Можно использовать с Claude Sonnet / Haiku / Opus?**
Да. Все шаблоны работают с Claude Sonnet 4.6, Claude Opus 4.6 и Claude Haiku 4.5.

**Можно внести вклад?**
Да, конечно. См. [CONTRIBUTING.md](CONTRIBUTING.md) для правил. Мы рады новым примерам, исправлениям ошибок, улучшениям документации и шаблонам от сообщества.

**Можно читать это офлайн?**
Да. Запустите `uv run scripts/build_epub.py`, чтобы сгенерировать EPUB-книгу со всем содержимым и отрисованными диаграммами.

---

<a id="license"></a>
## Начните осваивать Claude Code уже сегодня

Claude Code у вас уже установлен. Между вами и ростом продуктивности в 10 раз стоит только понимание того, как им пользоваться. Этот гид даёт вам структурированный путь, наглядные объяснения и шаблоны, которые можно копировать и сразу применять.

Лицензия MIT. Бесплатно навсегда. Клонируйте, форкайте, адаптируйте под себя.

**[Начать учебный маршрут ->](LEARNING-ROADMAP.md)** | **[Открыть каталог возможностей](CATALOG.md)** | **[Начать за 15 минут](#-get-started-in-15-minutes)**
