<picture>
  <source media="(prefers-color-scheme: dark)" srcset="../resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="../resources/logos/claude-howto-logo.svg">
</picture>

# Checkpoints и Rewind

Checkpoints позволяют сохранять состояние разговора и откатываться к предыдущим точкам в сессии Claude Code. Это незаменимо, когда вы исследуете разные подходы, восстанавливаетесь после ошибок или сравниваете альтернативные решения.

## Overview

Checkpoints позволяют сохранять состояние разговора и возвращаться к предыдущим точкам, что даёт безопасное экспериментирование и возможность исследовать несколько подходов. Это снимки состояния вашей сессии, включая:
- All messages exchanged
- File modifications made
- Tool usage history
- Session context

Checkpoints особенно полезны, когда вы исследуете разные подходы, исправляете ошибки или сравниваете альтернативные решения.

## Key Concepts

| Понятие | Описание |
|---------|-------------|
| **Checkpoint** | Снимок состояния разговора, включая сообщения, файлы и контекст |
| **Rewind** | Возврат к предыдущему checkpoint с отбрасыванием последующих изменений |
| **Branch Point** | Checkpoint, с которого исследуются несколько подходов |

## Accessing Checkpoints

Вы можете открывать и управлять checkpoints двумя основными способами:

### Using Keyboard Shortcut
Нажмите `Esc` дважды (`Esc` + `Esc`), чтобы открыть интерфейс checkpoints и просмотреть сохранённые точки.

### Using Slash Command
Используйте команду `/rewind` (alias: `/checkpoint`) для быстрого доступа:

```bash
# Open rewind interface
/rewind

# Or use the alias
/checkpoint
```

## Варианты Rewind

При откате вам предлагается меню из пяти вариантов:

1. **Restore code and conversation** -- Вернуть и файлы, и сообщения к этому checkpoint
2. **Restore conversation** -- Откатить только сообщения, оставив текущий код без изменений
3. **Restore code** -- Откатить только изменения файлов, сохранив всю историю разговора
4. **Summarize from here** -- Сжать разговор от этой точки в AI-сводку вместо удаления. Исходные сообщения сохраняются в транскрипте. При желании можно задать инструкции, чтобы сфокусировать сводку на конкретных темах.
5. **Never mind** -- Отменить действие и вернуться к текущему состоянию

## Автоматические Checkpoints

Claude Code автоматически создаёт checkpoints для вас:

- **Каждый запрос пользователя** - Новый checkpoint создаётся при каждом вводе пользователя
- **Постоянные** - Checkpoints сохраняются между сессиями
- **Автоочистка** - Checkpoints автоматически удаляются через 30 дней

Это значит, что вы всегда можете откатиться к любой предыдущей точке разговора, будь то несколько минут назад или несколько дней назад.

## Use Cases

| Сценарий | Рабочий процесс |
|----------|----------|
| **Исследование подходов** | Save → Try A → Save → Rewind → Try B → Compare |
| **Безопасный рефакторинг** | Save → Refactor → Test → If fail: Rewind |
| **A/B-тестирование** | Save → Design A → Save → Rewind → Design B → Compare |
| **Восстановление после ошибки** | Замечаете проблему → откат к последнему рабочему состоянию |

## Using Checkpoints

### Просмотр и откат

Нажмите `Esc` дважды или используйте `/rewind`, чтобы открыть браузер checkpoints. Вы увидите список всех доступных checkpoint с временными метками. Выберите любой checkpoint, чтобы откатиться к этому состоянию.

### Сведения о checkpoint

У каждого checkpoint показываются:
- Время создания
- Изменённые файлы
- Количество сообщений в разговоре
- Использованные инструменты

## Практические примеры

### Пример 1: Исследование разных подходов

```
User: Let's add a caching layer to the API

Claude: I'll add Redis caching to your API endpoints...
[Makes changes at checkpoint A]

User: Actually, let's try in-memory caching instead

Claude: I'll rewind to explore a different approach...
[User presses Esc+Esc and rewinds to checkpoint A]
[Implements in-memory caching at checkpoint B]

User: Now I can compare both approaches
```

### Пример 2: Восстановление после ошибки

```
User: Refactor the authentication module to use JWT

Claude: I'll refactor the authentication module...
[Makes extensive changes]

User: Wait, that broke the OAuth integration. Let's go back.

Claude: I'll help you rewind to before the refactoring...
[User presses Esc+Esc and selects the checkpoint before the refactor]

User: Let's try a more conservative approach this time
```

### Пример 3: Безопасные эксперименты

```
User: Let's try rewriting this in a functional style
[Creates checkpoint before experiment]

Claude: [Makes experimental changes]

User: The tests are failing. Let's rewind.
[User presses Esc+Esc and rewinds to the checkpoint]

Claude: I've rewound the changes. Let's try a different approach.
```

### Пример 4: Ветвление подходов

```
User: I want to compare two database designs
[Takes note of checkpoint - call it "Start"]

Claude: I'll create the first design...
[Implements Schema A]

User: Now let me go back and try the second approach
[User presses Esc+Esc and rewinds to "Start"]

Claude: Now I'll implement Schema B...
[Implements Schema B]

User: Great! Now I have both schemas to choose from
```

## Хранение checkpoint

Claude Code автоматически управляет вашими checkpoints:

- Checkpoints создаются автоматически при каждом запросе пользователя
- Старые checkpoints хранятся до 30 дней
- Checkpoints автоматически очищаются, чтобы не росло бесконечное хранилище

## Шаблоны рабочих процессов

### Стратегия ветвления для исследования

When exploring multiple approaches:

```
1. Start with initial implementation → Checkpoint A
2. Try Approach 1 → Checkpoint B
3. Rewind to Checkpoint A
4. Try Approach 2 → Checkpoint C
5. Compare results from B and C
6. Choose best approach and continue
```

### Шаблон безопасного рефакторинга

When making significant changes:

```
1. Current state → Checkpoint (auto)
2. Start refactoring
3. Run tests
4. If tests pass → Continue working
5. If tests fail → Rewind and try different approach
```

## Лучшие практики

Поскольку checkpoints создаются автоматически, вы можете сосредоточиться на работе, не думая о ручном сохранении состояния. Но держите в уме следующие практики:

### Эффективное использование checkpoints

✅ **Делайте:**
- Просматривайте доступные checkpoints перед откатом
- Используйте rewind, когда хотите исследовать разные направления
- Сохраняйте checkpoints, чтобы сравнивать подходы
- Понимайте, что делает каждый вариант rewind (restore code and conversation, restore conversation, restore code или summarize)

❌ **Не делайте:**
- Не полагайтесь только на checkpoints для сохранности кода
- Не ожидайте, что checkpoints отслеживают внешние изменения файловой системы
- Не используйте checkpoints как замену git commit

## Configuration

Вы можете включать и отключать автоматические checkpoints в настройках:

```json
{
  "autoCheckpoint": true
}
```

- `autoCheckpoint`: Включает или отключает автоматическое создание checkpoint при каждом запросе пользователя (по умолчанию: `true`)

## Limitations

У checkpoints есть следующие ограничения:

- **Изменения через Bash-команды НЕ отслеживаются** - Операции вроде `rm`, `mv`, `cp` в файловой системе не попадают в checkpoints
- **Внешние изменения НЕ отслеживаются** - Изменения, сделанные вне Claude Code (в редакторе, терминале и т. д.), не фиксируются
- **Это не замена системе контроля версий** - Используйте git для постоянных и проверяемых изменений в кодовой базе

## Troubleshooting

### Отсутствуют checkpoints

**Проблема**: Ожидаемый checkpoint не найден

**Решение**:
- Проверьте, не были ли checkpoints очищены
- Убедитесь, что `autoCheckpoint` включён в настройках
- Проверьте свободное место на диске

### Не удалось выполнить rewind

**Проблема**: Невозможно откатиться к checkpoint

**Решение**:
- Убедитесь, что нет конфликтующих незакоммиченных изменений
- Проверьте, не повреждён ли checkpoint
- Попробуйте откатиться к другому checkpoint

## Integration with Git

Checkpoints дополняют git, но не заменяют его:

| Возможность | Git | Checkpoints |
|---------|-----|-------------|
| Область | Файловая система | Разговор + файлы |
| Постоянство | Постоянное | Привязанное к сессии |
| Гранулярность | Коммиты | Любая точка |
| Скорость | Медленнее | Мгновенно |
| Совместное использование | Да | Ограничено |

Используйте оба инструмента вместе:
1. Используйте checkpoints для быстрого экспериментирования
2. Используйте git commit для окончательно принятых изменений
3. Создавайте checkpoint перед git-операциями
4. Фиксируйте в git успешные состояния после checkpoint

## Quick Start Guide

### Базовый рабочий процесс

1. **Работайте как обычно** - Claude Code создаёт checkpoints автоматически
2. **Хотите вернуться назад?** - Нажмите `Esc` дважды или используйте `/rewind`
3. **Выберите checkpoint** - Выберите его из списка для отката
4. **Выберите, что восстановить** - Выберите restore code and conversation, restore conversation, restore code, summarize from here или отмену
5. **Продолжайте работу** - Вы снова оказались в той точке

### Горячие клавиши

- **`Esc` + `Esc`** - Открыть браузер checkpoint
- **`/rewind`** - Альтернативный способ доступа к checkpoints
- **`/checkpoint`** - Alias для `/rewind`

## Как понять, когда пора откатываться: мониторинг контекста

Checkpoints позволяют откатываться назад, но как понять, *когда* это нужно? По мере роста разговора окно контекста Claude заполняется, и качество модели незаметно падает. Можно продолжать писать код, по сути опираясь на «полуслепую» модель, даже не замечая этого.

**[cc-context-stats](https://github.com/luongnv89/cc-context-stats)** решает эту задачу, добавляя в строку состояния Claude Code зоны контекста в реальном времени. Он показывает, где вы находитесь в окне контекста — от **Plan** (зелёная зона, можно спокойно планировать и кодировать) через **Code** (жёлтая зона, не стоит начинать новые планы) до **Dump** (оранжевая зона, пора завершать и откатываться). Когда зона меняется, вы понимаете, что пора сделать checkpoint и начать заново, а не продолжать работу на деградирующем качестве.

## Связанные концепции

- **[Advanced Features](../09-advanced-features/)** - Режим планирования и другие продвинутые возможности
- **[Memory Management](../02-memory/)** - Управление историей разговора и контекстом
- **[Slash Commands](../01-slash-commands/)** - Команды, запускаемые пользователем
- **[Hooks](../06-hooks/)** - Автоматизация, основанная на событиях
- **[Plugins](../07-plugins/)** - Встроенные пакеты расширений

## Дополнительные ресурсы

- [Официальная документация по checkpointing](https://code.claude.com/docs/en/checkpointing)
- [Руководство по Advanced Features](../09-advanced-features/) - Extended thinking и другие возможности

## Итог

Checkpoints — это автоматическая функция Claude Code, которая позволяет безопасно исследовать разные подходы, не боясь потерять работу. Каждый запрос пользователя автоматически создаёт новый checkpoint, поэтому вы можете откатиться к любой предыдущей точке сессии.

Ключевые преимущества:
- Без страха экспериментировать с несколькими подходами
- Быстро восстанавливаться после ошибок
- Сравнивать разные решения бок о бок
- Безопасно сочетать checkpoints с системами контроля версий

Помните: checkpoints не заменяют git. Используйте checkpoints для быстрого экспериментирования, а git — для постоянных изменений в коде.
