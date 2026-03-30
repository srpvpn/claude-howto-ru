# Быстрый старт - брендовые материалы

## Скопируйте материалы в свой проект

```bash
# Скопировать все ресурсы в веб-проект
cp -r resources/ /path/to/your/website/

# Или только favicons для web
cp resources/favicons/* /path/to/your/website/public/
```

## Добавить в HTML (copy & paste)

```html
<!-- Favicons -->
<link rel="icon" type="image/svg+xml" href="/resources/favicons/favicon-32.svg" sizes="32x32">
<link rel="icon" type="image/svg+xml" href="/resources/favicons/favicon-16.svg" sizes="16x16">
<link rel="apple-touch-icon" href="/resources/favicons/favicon-128.svg">
<link rel="icon" type="image/svg+xml" href="/resources/favicons/favicon-256.svg" sizes="256x256">
<meta name="theme-color" content="#000000">
```

## Использование в Markdown/документации

```markdown
# Claude How To

![Claude How To Logo](resources/logos/claude-howto-logo.svg)

![Icon](resources/icons/claude-howto-icon.svg)
```

## Рекомендуемые размеры

| Назначение | Размер | Файл |
|---------|------|------|
| Заголовок сайта | 520×120 | `logos/claude-howto-logo.svg` |
| Иконка приложения | 256×256 | `icons/claude-howto-icon.svg` |
| Вкладка браузера | 32×32 | `favicons/favicon-32.svg` |
| Экран смартфона | 128×128 | `favicons/favicon-128.svg` |
| Desktop app | 256×256 | `favicons/favicon-256.svg` |
| Маленький аватар | 64×64 | `favicons/favicon-64.svg` |

## Значения цветов

```css
/* Используйте это в CSS */
--color-primary: #000000;
--color-secondary: #6B7280;
--color-accent: #22C55E;
--color-bg-light: #FFFFFF;
--color-bg-dark: #0A0A0A;
```

## Что означает дизайн иконки

**Компас с кодовым bracket'ом**:
- Кольцо компаса = навигация, структурированный путь обучения
- Зелёная северная стрелка = направление, прогресс, подсказка
- Чёрная южная стрелка = опора, прочная база
- Скобка `>` = терминальный prompt, код, CLI-контекст
- Деления = точность, поэтапность

Это символизирует "поиск пути в коде с понятным сопровождением".

## Что использовать и где

### Website
- **Header**: Logo (`logos/claude-howto-logo.svg`)
- **Favicon**: 32px (`favicons/favicon-32.svg`)
- **Social preview**: Icon (`icons/claude-howto-icon.svg`)

### GitHub
- **README badge**: Icon (`icons/claude-howto-icon.svg`) в размере 64-128px
- **Аватар репозитория**: Icon (`icons/claude-howto-icon.svg`)

### Social Media
- **Профиль**: Icon (`icons/claude-howto-icon.svg`)
- **Баннер**: Logo (`logos/claude-howto-logo.svg`)
- **Thumbnail**: Icon в 256×256px

### Documentation
- **Заголовки глав**: Logo или icon (масштабировать по месту)
- **Иконки разделов**: Favicon (32-64px)

---

См. [README.md](README.md) для полной документации.
