# Claude How To - Дизайн-система

## Визуальная идентичность

### Концепция иконки: Compass с Code Bracket

Иконка Claude How To использует **compass с кодовой скобкой `>`**, чтобы показать навигацию через код:

```
     N (green)
     ▲
     │
W ───>─── E     Compass = Guidance/Direction
     │          > Bracket = Code/Terminal/CLI
     ▼
     S (black)
```

Это даёт:
- **Визуальную ясность**: сразу понятно, что это гид по навигации в коде
- **Смысловую нагрузку**: compass = поиск пути; `>` = code/terminal
- **Масштабируемость**: работает от 16px до 512px
- **Соответствие бренду**: минималистичная палитра в духе developer tools

---

## Цветовая система

### Палитра

| Цвет | Hex | RGB | Использование |
|-------|-----|-----|-------|
| Black (Primary) | `#000000` | 0, 0, 0 | Основные штрихи, текст, южная стрелка |
| White (Background) | `#FFFFFF` | 255, 255, 255 | Светлый фон |
| Gray (Secondary) | `#6B7280` | 107, 114, 128 | Малые деления, вторичный текст |
| Bright Green (Accent) | `#22C55E` | 34, 197, 94 | Северная стрелка, центральная точка, акценты |
| Near Black (Dark BG) | `#0A0A0A` | 10, 10, 10 | Фоны тёмного режима |

### Контрастность (WCAG)

- Black on White: **21:1** AAA
- Gray on White: **4.6:1** AA
- Green on White: **3.2:1** (только декоративно, не для текста)
- White on Dark: **19.5:1** AAA

### Правило для акцентного цвета

**Bright Green (#22C55E) зарезервирован только для акцентов:**
- Северная стрелка компаса
- Центральная точка
- Акцентные подчеркивания/границы
- Никогда не использовать как background
- Никогда не использовать для основного текста

---

## Типографика

### Шрифт логотипа
- **Семейство**: Inter, SF Pro Display, -apple-system, Segoe UI, sans-serif
- **"Claude"**: 42px, weight 700 (bold), Black
- **"How-To"**: 32px, weight 500 (medium), Gray (#6B7280)
- **Subtitle**: 10px, weight 500, Gray, letter-spacing 1.5px, uppercase

### Интерфейсный шрифт
- **Семейство**: Inter, SF Pro, system fonts (sans-serif)
- **Weight**: 400-600
- **Стиль**: чистый, читаемый

---

## Детали иконки

### Спецификация компаса

Марк компаса собран из следующих геометрических элементов:

```
Element             | Stroke/Fill    | Color
--------------------|----------------|------------------
Outer ring          | 3px stroke     | Black / White (dark mode)
North tick          | 2.5px stroke   | Black / White (dark mode)
Other cardinal ticks| 2px stroke     | Gray / White 50% (dark mode)
Intercardinal ticks | 1.5px stroke   | Gray / White 40% (dark mode)
North needle        | filled polygon | #22C55E (always green)
South needle        | filled polygon | Black / White (dark mode)
> bracket           | 3px stroke     | Black / White (dark mode)
Center dot          | filled circle  | #22C55E (always green)
```

### Прогрессия размеров

```
16px  → Ring + needles + chevron only (minimal)
32px  → Adds cardinal tick marks
64px  → Adds intercardinal tick marks
128px → Full detail, all elements crisp
256px → Maximum detail, thick strokes
```

---

## Рекомендации по размерам

### Размер логотипа

- **Минимум**: 200px width (для web)
- **Рекомендуемый**: 520px (native size)
- **Максимум**: без ограничений (vector format)
- **Соотношение сторон**: ~4.3:1 (width:height)

### Размер иконки

- **Минимум**: 16px (favicon)
- **Рекомендуемый**: 64-256px (apps, avatars)
- **Максимум**: без ограничений (vector format)
- **Соотношение сторон**: 1:1 (square)

---

## Отступы и выравнивание

### Отступы логотипа

```
┌─────────────────────────────────────┐
│                                     │
│        Clear Space Minimum          │
│         (logo height / 2)           │
│                                     │
│    [COMPASS]  Claude                │
│               How-To                │
│                                     │
└─────────────────────────────────────┘
```

### Центральная точка иконки

Все иконки центрируются в середине canvas:
- 128×128 для canvas 256px
- 64×64 для canvas 128px
- Это обеспечивает выравнивание с другими UI-элементами

---

## Доступность

### Контрастность
- Весь текст соответствует WCAG AA (минимум 4.5:1)
- Зелёный акцент декоративный, не информационный
- Нет зависимости от красно-зелёной палитры

### Масштабируемость
- Векторный формат обеспечивает чёткость на любом размере
- Геометрические формы остаются узнаваемыми на 16px
- Детализация растёт по мере увеличения размера

---

## Примеры применения

### Web Header
- Size: 520×120px logo
- File: `logos/claude-howto-logo.svg`
- Background: White or dark (#0A0A0A)
- Padding: минимум 20px

### App Icon
- Size: 256×256px
- File: `icons/claude-howto-icon.svg`
- Background: White or dark
- Use: ярлыки приложений, аватары

### Browser Favicon
- Size: 32px (primary), 16px (fallback)
- File: `favicons/favicon-32.svg`
- Format: SVG for crisp display

### Social Media
- Profile: 256×256px icon
- Banner: 520×120px logo (centered)

### Documentation
- Chapter Headers: Logo scaled to fit
- Section Icons: 64×64px favicon
- Inline: 32×32px favicon

---

## Детали формата файлов

### SVG Structure

Все SVG файлы выполнены в flat design:
- Без градиентов (только solid colors)
- Без filter effects (никаких blur, glow или shadow)
- Чистая stroke и fill geometry
- ViewBox для responsive scaling
- Читаемый, прокомментированный код

### Совместимость с браузерами

- Chrome/Edge: Full support
- Firefox: Full support
- Safari: Full support
- iOS Safari: Full support
- Все современные браузеры: Full support

---

## Кастомизация

### Изменение акцентного цвета

Чтобы создать вариант с другим accent color:

1. Замените все вхождения `#22C55E` на ваш акцентный цвет
2. Убедитесь, что контрастность остаётся выше 3:1 для декоративных элементов
3. Оставьте структуру black/white/gray без изменений
