---
name: blog-draft
description: Draft a blog post from ideas and resources. Use when users want to write a blog post, create content from research, or draft articles. Guides through research, brainstorming, outlining, and iterative drafting with version control.
---

## User Input

```text
$ARGUMENTS
```

Вы **должны** учитывать ввод пользователя перед продолжением. Пользователь должен указать:
- **Idea/Topic**: основная тема или идея поста
- **Resources**: URL, файлы или ссылки на исследования (опционально, но желательно)
- **Target audience**: для кого предназначен пост (опционально)
- **Tone/Style**: формальный, разговорный, технический и т. п. (опционально)

**ВАЖНО**: если пользователь просит изменить **уже существующий** пост, пропустите шаги 0-8 и сразу переходите к **Step 9**. Сначала прочитайте существующие черновики, затем начинайте итерации.

## Поток выполнения

Следуйте шагам последовательно. **Не пропускайте шаги и не идите дальше без согласования там, где оно требуется.**

### Step 0: Создать папку проекта

1. Сгенерируйте имя папки по формату: `YYYY-MM-DD-short-topic-name`
   - Используйте сегодняшнюю дату
   - Создайте короткий slug, удобный для URL (lowercase, hyphens, максимум 5 слов)

2. Создайте структуру:
   ```
   blog-posts/
   └── YYYY-MM-DD-short-topic-name/
       └── resources/
   ```

3. Подтвердите создание папки с пользователем перед продолжением.

### Step 1: Исследование и сбор ресурсов

1. Создайте подпапку `resources/` в директории поста

2. Для каждого предоставленного ресурса:
   - **URLs**: извлеките и сохраните ключевую информацию в `resources/` как markdown-файлы
   - **Files**: прочитайте и кратко изложите в `resources/`
   - **Topics**: используйте web search для сбора актуальной информации

3. Для каждого ресурса создайте summary-файл в `resources/`:
   - `resources/source-1-[short-name].md`
   - `resources/source-2-[short-name].md`
   - и т. д.

4. Каждый summary должен содержать:
   ```markdown
   # Source: [Title/URL]

   ## Key Points
   - Point 1
   - Point 2

   ## Relevant Quotes/Data
   - Quote or statistic 1
   - Quote or statistic 2

   ## How This Relates to Topic
   Brief explanation of relevance
   ```

5. Представьте пользователю summary исследований.

### Step 2: Brainstorm & Clarify

1. На основе темы и собранных ресурсов покажите:
   - **Main themes** из исследования
   - **Potential angles** для поста
   - **Key points**, которые стоит раскрыть
   - **Gaps** в информации, которые нужно уточнить

2. Задайте уточняющие вопросы:
   - Какой главный вывод вы хотите донести до читателя?
   - Какие пункты из исследования нужно подчеркнуть?
   - Какой нужен объём? (short: 500-800 words, medium: 1000-1500, long: 2000+)
   - Что нужно исключить?

3. **Ждите ответа пользователя, прежде чем продолжать.**

### Step 3: Предложить outline

1. Создайте структурированный outline:

   ```markdown
   # Blog Post Outline: [Title]

   ## Meta Information
   - **Target Audience**: [who]
   - **Tone**: [style]
   - **Target Length**: [word count]
   - **Main Takeaway**: [key message]

   ## Proposed Structure

   ### Hook/Introduction
   - Opening hook idea
   - Context setting
   - Thesis statement

   ### Section 1: [Title]
   - Key point A
   - Key point B
   - Supporting evidence from [source]

   ### Section 2: [Title]
   - Key point A
   - Key point B

   [Continue for all sections...]

   ### Conclusion
   - Summary of key points
   - Call to action or final thought

   ## Sources to Cite
   - Source 1
   - Source 2
   ```

2. Представьте outline пользователю и **попросите утвердить его или внести правки**.

### Step 4: Сохранить утверждённый outline

1. После утверждения сохраните его в `OUTLINE.md` в папке поста.

2. Подтвердите, что outline сохранён.

### Step 5: Зафиксировать outline в git (если репозиторий git)

1. Проверьте, является ли текущая директория git-репозиторием.

2. Если да:
   - Добавьте новые файлы в stage: папку поста, ресурсы и `OUTLINE.md`
   - Создайте commit с сообщением: `docs: Add outline for blog post - [topic-name]`
   - Отправьте изменения в remote

3. Если это не git-репозиторий, пропустите шаг и сообщите пользователю.

### Step 6: Написать черновик

1. На основе утверждённого outline напишите полный черновик поста.

2. Строго следуйте структуре из `OUTLINE.md`.

3. Включите:
   - Захватывающее вступление
   - Чёткие заголовки секций
   - Подтверждения и примеры из исследования
   - Плавные переходы между секциями
   - Сильное заключение с основным выводом
   - **Citations**: все сравнения, статистика, данные и факты должны иметь ссылку на источник

4. Сохраните черновик как `draft-v0.1.md` в папке поста.

5. Формат:
   ```markdown
   # [Blog Post Title]

   *[Optional: subtitle or tagline]*

   [Full content with inline citations...]

   ---

   ## References
   - [1] Source 1 Title - URL or Citation
   - [2] Source 2 Title - URL or Citation
   - [3] Source 3 Title - URL or Citation
   ```

6. **Требования к цитированию**:
   - Каждый факт, статистика и сравнение должны иметь inline citation
   - Используйте нумерованные ссылки [1], [2] и т. п. или именованные цитаты [Source Name]
   - Связывайте цитаты с разделом References в конце
   - Пример: "Исследования показывают, что 65% разработчиков предпочитают TypeScript [1]"
   - Пример: "React превосходит Vue по скорости рендеринга на 20% [React Benchmarks 2024]"

### Step 7: Зафиксировать черновик в git (если репозиторий git)

1. Проверьте, находитесь ли вы в git-репозитории.

2. Если да:
   - Добавьте файл черновика в stage
   - Создайте commit с сообщением: `docs: Add draft v0.1 for blog post - [topic-name]`
   - Отправьте изменения в remote

3. Если нет, пропустите шаг и сообщите пользователю.

### Step 8: Показать черновик на ревью

1. Покажите пользователю содержимое черновика.

2. Попросите обратную связь:
   - Общее впечатление?
   - Какие секции нужно расширить или сократить?
   - Нужны ли изменения тона?
   - Чего не хватает?
   - Какие правки или переписывание нужны?

3. **Ждите ответа пользователя.**

### Step 9: Итерация или финализация

**Если пользователь просит изменения:**
1. Зафиксируйте все изменения
2. Вернитесь к Step 6 со следующими корректировками:
   - Увеличьте номер версии (v0.2, v0.3 и т. д.)
   - Учтите всю обратную связь
   - Сохраняйте как `draft-v[X.Y].md`
   - Повторяйте Steps 7-8

**Если пользователь одобряет:**
1. Подтвердите финальную версию черновика
