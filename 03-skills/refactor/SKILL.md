---
name: code-refactor
description: Systematic code refactoring based on Martin Fowler's methodology. Use when users ask to refactor code, improve code structure, reduce technical debt, clean up legacy code, eliminate code smells, or improve code maintainability. This skill guides through a phased approach with research, planning, and safe incremental implementation.
---

# Навык code refactoring

Системный подход к рефакторингу кода на основе *Refactoring: Improving the Design of Existing Code* Мартина Фаулера (2-е издание). Навык делает упор на безопасные, небольшие изменения, подтверждённые тестами.

> "Рефакторинг — это процесс изменения программной системы таким образом, чтобы внешнее поведение кода не менялось, а внутренняя структура улучшалась." — Martin Fowler

## Основные принципы

1. **Сохранение поведения**: внешнее поведение не должно меняться
2. **Маленькие шаги**: только небольшие и проверяемые изменения
3. **Тесты прежде всего**: тесты — это страховка
4. **Непрерывность**: рефакторинг — это постоянная практика, а не разовая акция
5. **Совместная работа**: на каждом этапе нужно согласование с пользователем

## Обзор рабочего процесса

```
Phase 1: Research & Analysis
    ↓
Phase 2: Test Coverage Assessment
    ↓
Phase 3: Code Smell Identification
    ↓
Phase 4: Refactoring Plan Creation
    ↓
Phase 5: Incremental Implementation
    ↓
Phase 6: Review & Iteration
```

---

## Phase 1: Research & Analysis

### Цели
- Понять структуру и назначение кодовой базы
- Определить границы рефакторинга
- Собрать контекст о бизнес-требованиях

### Вопросы пользователю
Перед началом уточните:

1. **Объём**: какие файлы, модули или функции нужно рефакторить?
2. **Цели**: какую проблему нужно решить? (читаемость, производительность, поддерживаемость)
3. **Ограничения**: есть ли области, которые трогать нельзя?
4. **Срочность**: мешает ли это другой работе?
5. **Тесты**: существуют ли тесты и проходят ли они?

### Действия
- [ ] Изучить целевой код
- [ ] Определить зависимости и интеграции
- [ ] Зафиксировать текущую архитектуру
- [ ] Отметить технический долг (TODO, FIXME и т. п.)

### Результат
Покажите пользователю:
- Сводку структуры кода
- Выявленные проблемные области
- Первичные рекомендации
- **Запросите разрешение продолжить**

---

## Phase 2: Test Coverage Assessment

### Зачем нужны тесты
> "Рефакторинг без тестов — это как вождение без ремня безопасности." — Martin Fowler

Тесты — это **ключевой фактор**, который делает безопасный рефакторинг возможным. Без них легко внести баги.

### Шаги оценки

1. **Проверить наличие тестов**
   ```bash
   # Search for test files
   find . -name "*test*" -o -name "*spec*" | head -20
   ```

2. **Запустить существующие тесты**
   ```bash
   # JavaScript/TypeScript
   npm test

   # Python
   pytest -v

   # Java
   mvn test
   ```

3. **Проверить покрытие (если доступно)**
   ```bash
   # JavaScript
   npm run test:coverage

   # Python
   pytest --cov=.
   ```

### Точка принятия решения

**Если тесты есть и они проходят:**
- Переходите к Phase 3

**Если тестов нет или их мало:**
Предложите варианты:
1. Написать тесты сначала (рекомендуется)
2. Добавлять тесты постепенно во время рефакторинга
3. Продолжить без тестов (рискованно, требует согласия пользователя)

**Если тесты падают:**
- Остановитесь. Сначала исправьте падающие тесты
- Спросите пользователя: исправляем тесты сначала?

### Если нужно писать тесты

Для каждой рефакторимой функции убедитесь, что тесты покрывают:
- Happy path
- Edge cases (пустые значения, null, границы)
- Сценарии ошибок (невалидный ввод, исключения)

Используйте цикл red-green-refactor:
1. Написать падающий тест (red)
2. Добиться его прохождения (green)
3. Рефакторить

---

## Phase 3: Code Smell Identification

### Что такое code smells?
Это симптомы более глубоких проблем в коде. Это не баги, но признаки того, что код можно улучшить.

### Что проверять

См. [references/code-smells.md](references/code-smells.md) для полного каталога.

#### Краткая памятка

| Code smell | Признаки | Влияние |
|-------|-------|--------|
| **Long Method** | Методы > 30-50 строк | Сложно понимать, тестировать и сопровождать |
| **Duplicated Code** | Одинаковая логика в нескольких местах | Исправления нужно вносить в нескольких местах |
| **Large Class** | Класс с большим количеством обязанностей | Нарушает Single Responsibility |
| **Feature Envy** | Метод чаще использует данные другого класса | Слабая инкапсуляция |
| **Primitive Obsession** | Слишком много примитивов вместо объектов | Потеря доменных концепций |
| **Long Parameter List** | Методы с 4+ параметрами | Сложно вызывать правильно |
| **Data Clumps** | Одни и те же наборы данных встречаются вместе | Не хватает абстракции |
| **Switch Statements** | Сложные switch/if-else цепочки | Трудно расширять |
| **Speculative Generality** | Код "на всякий случай" | Лишняя сложность |
| **Dead Code** | Неиспользуемый код | Путаница и издержки на поддержку |

### Шаги анализа

1. **Автоматический анализ** (если есть скрипты)
   ```bash
   python scripts/detect-smells.py <file>
   ```

2. **Ручная проверка**
   - Последовательно пройти по коду
   - Зафиксировать каждый smell с местом и серьёзностью
   - Классифицировать по влиянию (Critical/High/Medium/Low)

3. **Приоритизация**
   Сосредоточьтесь на smells, которые:
   - Блокируют текущую разработку
   - Вызывают баги или путаницу
   - Затрагивают наиболее часто меняемые участки

### Результат: отчёт о smells

Покажите пользователю:
- Список обнаруженных smells с местами
- Оценку серьёзности для каждого
- Рекомендуемый порядок приоритетов
- **Запросите согласование приоритетов**

---

## Phase 4: Refactoring Plan Creation

### Выбор рефакторинга

Для каждого smell выберите подходящий рефакторинг из каталога.

См. [references/refactoring-catalog.md](references/refactoring-catalog.md) для полного списка.

#### Соответствие smell -> refactoring

| Code Smell | Recommended Refactoring(s) |
|------------|---------------------------|
| Long Method | Extract Method, Replace Temp with Query |
| Duplicated Code | Extract Method, Pull Up Method, Form Template Method |
| Large Class | Extract Class, Extract Subclass |
| Feature Envy | Move Method, Move Field |
| Primitive Obsession | Replace Primitive with Object, Replace Type Code with Class |
| Long Parameter List | Introduce Parameter Object, Preserve Whole Object |
| Data Clumps | Extract Class, Introduce Parameter Object |
| Switch Statements | Replace Conditional with Polymorphism |
| Speculative Generality | Collapse Hierarchy, Inline Class, Remove Dead Code |
| Dead Code | Remove Dead Code |

### Структура плана

Используйте шаблон из [templates/refactoring-plan.md](templates/refactoring-plan.md).

Для каждого рефакторинга укажите:
1. **Цель**: какой код меняется
2. **Smell**: какую проблему он решает
3. **Refactoring**: какой приём применяется
4. **Шаги**: подробные микро-шаги
5. **Риски**: что может пойти не так
6. **Rollback**: как откатить изменения при необходимости
