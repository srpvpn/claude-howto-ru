<picture>
  <source media="(prefers-color-scheme: dark)" srcset="logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="logos/claude-howto-logo.svg">
</picture>

# Claude How To - Брендовые материалы

Полная коллекция логотипов, иконок и favicons для проекта Claude How To. Все материалы используют дизайн V3.0: compass со знаком code bracket (`>`), который символизирует навигацию по коду под руководством подсказок — в палитре Black/White/Gray с акцентом Bright Green (#22C55E).

## Структура каталога

```
resources/
├── logos/
│   ├── claude-howto-logo.svg       # Основной логотип - светлая тема (520×120px)
│   └── claude-howto-logo-dark.svg  # Основной логотип - тёмная тема (520×120px)
├── icons/
│   ├── claude-howto-icon.svg       # Иконка приложения - светлая тема (256×256px)
│   └── claude-howto-icon-dark.svg  # Иконка приложения - тёмная тема (256×256px)
└── favicons/
    ├── favicon-16.svg              # Favicon - 16×16px
    ├── favicon-32.svg              # Favicon - 32×32px (основной)
    ├── favicon-64.svg              # Favicon - 64×64px
    ├── favicon-128.svg             # Favicon - 128×128px
    └── favicon-256.svg             # Favicon - 256×256px
```

Дополнительные материалы в `assets/logo/`:
```
assets/logo/
├── logo-full.svg       # Знак + wordmark (горизонтальный)
├── logo-mark.svg       # Только символ compass (120×120px)
├── logo-wordmark.svg   # Только текст
├── logo-icon.svg       # Иконка приложения (512×512, rounded)
├── favicon.svg         # Оптимизированный 16×16
├── logo-white.svg      # Белая версия для тёмных фонов
└── logo-black.svg     # Чёрная монохромная версия
```

## Обзор материалов

### Концепция дизайна (V3.0)

**Compass with Code Bracket** — навигация встречается с кодом:
- **Compass Ring** = навигация, поиск пути
- **North Needle (Green)** = направление, прогресс на пути обучения
- **South Needle (Black)** = опора, прочная база
- **`>` Bracket** = terminal prompt, code, CLI-контекст
- **Tick Marks** = точность, структурированное обучение

### Логотипы

**Файлы**:
- `logos/claude-howto-logo.svg` (светлая тема)
- `logos/claude-howto-logo-dark.svg` (тёмная тема)

**Спецификации**:
- **Размер**: 520×120 px
- **Назначение**: основной header/branding-логотип с wordmark
- **Использование**:
  - Заголовки сайта
  - README badges
  - Маркетинговые материалы
  - Печатные материалы
- **Формат**: SVG (полностью масштабируемый)
- **Режимы**: светлый (белый фон) и тёмный (#0A0A0A background)

### Иконки

**Файлы**:
- `icons/claude-howto-icon.svg` (светлая тема)
- `icons/claude-howto-icon-dark.svg` (тёмная тема)

**Спецификации**:
- **Размер**: 256×256 px
- **Назначение**: иконка приложения, аватары, thumbnails
- **Использование**:
  - App icons
  - Profile avatars
  - Social media thumbnails
  - Documentation headers
- **Формат**: SVG (полностью масштабируемый)
- **Режимы**: светлый (белый фон) и тёмный (#0A0A0A background)

**Элементы дизайна**:
- Compass ring с cardinal и intercardinal tick marks
- Зелёная северная стрелка (direction/guidance)
- Чёрная южная стрелка (foundation)
- `>` code bracket в центре (terminal/CLI)
- Зелёная центральная точка-акцент

### Favicons

Оптимизированные версии в нескольких размерах для web:

| Файл | Размер | DPI | Использование |
|------|------|-----|-------|
| `favicon-16.svg` | 16×16 px | 1x | Вкладки браузера (старые браузеры) |
| `favicon-32.svg` | 32×32 px | 1x | Стандартный favicon |
| `favicon-64.svg` | 64×64 px | 1x-2x | High-DPI displays |
| `favicon-128.svg` | 128×128 px | 2x | Apple touch icon, bookmarks |
| `favicon-256.svg` | 256×256 px | 4x | Современные браузеры, PWA icons |

**Примечания по оптимизации**:
- 16px: минимальная геометрия — ring, needles, chevron only
- 32px: добавляет cardinal tick marks
- 64px+: полный уровень детализации с intercardinal ticks
- Все варианты сохраняют визуальную согласованность с основной иконкой
- SVG format обеспечивает чёткое отображение на любом размере

## HTML-интеграция

### Базовая настройка favicon

```html
<!-- Browser favicon -->
<link rel="icon" type="image/svg+xml" href="/resources/favicons/favicon-32.svg">
<link rel="icon" type="image/svg+xml" href="/resources/favicons/favicon-16.svg" sizes="16x16">

<!-- Apple touch icon (mobile home screen) -->
<link rel="apple-touch-icon" href="/resources/favicons/favicon-128.svg">

<!-- PWA & modern browsers -->
<link rel="icon" type="image/svg+xml" href="/resources/favicons/favicon-256.svg" sizes="256x256">
```

### Полная настройка

```html
<head>
  <!-- Primary favicon -->
  <link rel="icon" type="image/svg+xml" href="/resources/favicons/favicon-32.svg" sizes="32x32">
  <link rel="icon" type="image/svg+xml" href="/resources/favicons/favicon-16.svg" sizes="16x16">

  <!-- Apple touch icon -->
  <link rel="apple-touch-icon" href="/resources/favicons/favicon-128.svg">

  <!-- PWA icons -->
  <link rel="icon" type="image/svg+xml" href="/resources/favicons/favicon-256.svg" sizes="256x256">

  <!-- Android -->
  <link rel="shortcut icon" href="/resources/favicons/favicon-256.svg">

  <!-- PWA manifest reference (if using manifest.json) -->
  <meta name="theme-color" content="#000000">
</head>
```

## Цветовая палитра

### Основные цвета
- **Black**: `#000000` (основной текст, strokes, south needle)
- **White**: `#FFFFFF` (светлые фоны)
- **Gray**: `#6B7280` (вторичный текст, мелкие деления)

### Акцентный цвет
- **Bright Green**: `#22C55E` (north needle, center dot, accent lines — только для highlights, никогда как background)

### Тёмный режим
- **Background**: `#0A0A0A` (near-black)

### CSS Variables
```css
--color-primary: #000000;
--color-secondary: #6B7280;
--color-accent: #22C55E;
--color-bg-light: #FFFFFF;
--color-bg-dark: #0A0A0A;
```

### Tailwind Config
```js
colors: {
  brand: {
    primary: '#000000',
    secondary: '#6B7280',
    accent: '#22C55E',
  }
}
```

### Рекомендации по использованию
- Используйте black для основного текста и структурных элементов
- Используйте gray для второстепенных элементов
- Используйте green **только** для highlights — стрелка, точки, акцентные линии
- Никогда не используйте green как фон
- Поддерживайте WCAG AA contrast (минимум 4.5:1)

## Рекомендации по дизайну

### Использование логотипа
- Используйте на белом или тёмном (#0A0A0A) фоне
- Масштабируйте пропорционально
- Оставляйте свободное пространство вокруг логотипа (минимум: logo height / 2)
- Используйте светлую или тёмную версию в зависимости от фона

### Использование иконки
- Используйте стандартные размеры: 16, 32, 64, 128, 256px
- Сохраняйте пропорции compass
- Масштабируйте пропорционально

### Использование favicon
- Выбирайте размер по контексту
- 16-32px: browser tabs, bookmarks
- 64px: favicon site icons
- 128px+: Apple/Android home screens

## Оптимизация SVG

Все SVG файлы выполнены в flat design без градиентов и фильтров:
- Чистая stroke-based geometry
- Без встроенных rasters
- Оптимизированные paths
- Responsive viewBox

Для web-оптимизации:
```bash
# Compress SVG while maintaining quality
svgo --config='{
  "js2svg": {
    "indent": 2
  },
  "plugins": [
    "convertStyleToAttrs",
    "removeRasterImages"
  ]
}' input.svg -o output.svg
```

## PNG Conversion

Чтобы конвертировать SVG в PNG для поддержки старых браузеров:

```bash
# Using ImageMagick
convert -density 300 -background none favicon-256.svg favicon-256.png

# Using Inkscape
inkscape -D -z --file=favicon-256.svg --export-png=favicon-256.png
```

## Accessibility

- Высокая контрастность цветов (WCAG AA compliant — минимум 4.5:1)
- Чистые геометрические формы, узнаваемые на любом размере
- Масштабируемый векторный формат
- В иконках нет текста (текст добавляется отдельно в wordmark)
- Нет зависимости от красно-зелёной палитры для смысла

## Attribution

Эти материалы являются частью проекта Claude How To.

**Лицензия**: MIT (см. файл LICENSE в проекте)

## Version History

- **v3.0** (February 2026): Compass-bracket design with Black/White/Gray + Green accent palette
- **v2.0** (January 2026): Claude-inspired 12-ray starburst design with emerald palette
- **v1.0** (January 2026): Original hexagon-based progression icon design
