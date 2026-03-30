# Журнал изменений

## v2.2.0 — 2026-03-26

### Документация

- Синхронизированы все туториалы и справочные материалы с Claude Code v2.1.84 (f78c094) @luongnv89
  - Обновлены slash-команды: 55+ built-in + 5 bundled skills, 3 помечены как deprecated
  - Расширены hook events с 18 до 25, добавлен тип hook `agent` (теперь 4 типа)
  - Добавлены Auto Mode, Channels, Voice Dictation в advanced features
  - Добавлены поля frontmatter `effort`, `shell` для skills; поля `initialPrompt`, `disallowedTools` для agents
  - Добавлен WebSocket transport для MCP, elicitation, лимит 2KB на tool
  - Добавлена поддержка plugin LSP, `userConfig`, `${CLAUDE_PLUGIN_DATA}`
  - Обновлены все справочные документы (CATALOG, QUICK_REFERENCE, LEARNING-ROADMAP, INDEX)
- README переписан в формате landing page guide (32a0776) @luongnv89

### Исправления ошибок

- Добавлены отсутствующие слова в cSpell и разделы README для соответствия CI (93f9d51) @luongnv89
- Добавлено `Sandboxing` в словарь cSpell (b80ce6f) @luongnv89

**Полный журнал изменений**: https://github.com/luongnv89/claude-howto/compare/v2.1.1...v2.2.0

---

## v2.1.1 — 2026-03-13

### Исправления ошибок

- Удалена мёртвая ссылка на marketplace, из-за которой падала CI-проверка ссылок (3fdf0d6) @luongnv89
- Добавлены `sandboxed` и `pycache` в словарь cSpell (dc64618) @luongnv89

**Полный журнал изменений**: https://github.com/luongnv89/claude-howto/compare/v2.1.0...v2.1.1

---

## v2.1.0 — 2026-03-13

### Возможности

- Добавлен адаптивный учебный маршрут с навыками self-assessment и lesson quiz (1ef46cd) @luongnv89
  - `/self-assessment` — интерактивный квиз уровня владения по 10 областям возможностей с персональным учебным маршрутом
  - `/lesson-quiz [lesson]` — проверка знаний по конкретному уроку с 8-10 целевыми вопросами

### Исправления ошибок

- Обновлены битые URL, deprecations и устаревшие ссылки (8fe4520) @luongnv89
- Исправлены битые ссылки в resources и self-assessment skill (7a05863) @luongnv89
- Для вложенных code blocks в concepts guide использованы tilde fences (5f82719) @VikalpP
- Добавлены отсутствующие слова в словарь cSpell (8df7572) @luongnv89

### Документация

- Phase 5 QA — исправлены консистентность, URL и терминология по всей документации (00bbe4c) @luongnv89
- Завершены Phases 3-4 — новое покрытие возможностей и обновление справочных документов (132de29) @luongnv89
- Добавлен runtime MCPorter в раздел про MCP context bloat (ef52705) @luongnv89
- Добавлены недостающие команды, функции и настройки в 6 гидов (4bc8f15) @luongnv89
- Добавлен style guide на основе существующих соглашений репозитория (84141d0) @luongnv89
- Добавлена строка self-assessment в сравнительную таблицу гида (8fe0c96) @luongnv89
- Добавлен @VikalpP в список contributors для PR #7 (d5b4350) @luongnv89
- Добавлены ссылки на self-assessment и lesson-quiz skills в README и roadmap (d5a6106) @luongnv89

### Новые участники

- @VikalpP сделал свой первый вклад в #7

**Полный журнал изменений**: https://github.com/luongnv89/claude-howto/compare/v2.0.0...v2.1.0

---

## v2.0.0 — 2026-02-01

### Возможности

- Синхронизирована вся документация с возможностями Claude Code за февраль 2026 (487c96d)
  - Обновлены 26 файлов во всех 10 учебных директориях и 7 справочных документах
  - Добавлена документация по **Auto Memory** — persistent learnings per project
  - Добавлена документация по **Remote Control**, **Web Sessions** и **Desktop App**
  - Добавлена документация по **Agent Teams** (экспериментальная multi-agent collaboration)
  - Добавлена документация по **MCP OAuth 2.0**, **Tool Search** и **Claude.ai Connectors**
  - Добавлена документация по **Persistent Memory** и **Worktree Isolation** для subagents
  - Добавлена документация по **Background Subagents**, **Task List**, **Prompt Suggestions**
  - Добавлена документация по **Sandboxing** и **Managed Settings** (Enterprise)
  - Добавлена документация по **HTTP Hooks** и 7 новым hook events
  - Добавлена документация по **Plugin Settings**, **LSP Servers** и обновлениям Marketplace
  - Добавлена документация по опции rewind **Summarize from Checkpoint**
  - Описаны 17 новых slash-команд (`/fork`, `/desktop`, `/teleport`, `/tasks`, `/fast` и др.)
  - Описаны новые CLI-флаги (`--worktree`, `--from-pr`, `--remote`, `--teleport`, `--teammate-mode` и др.)
  - Описаны новые переменные окружения для auto memory, уровней effort, agent teams и др.

### Дизайн

- Переработан логотип в знак compass-bracket с минималистичной палитрой (20779db)

### Исправления ошибок / корректировки

- Обновлены названия моделей: Sonnet 4.5 → **Sonnet 4.6**, Opus 4.5 → **Opus 4.6**
- Исправлены названия permission modes: вместо вымышленных "Unrestricted/Confirm/Read-only" используются реальные `default`/`acceptEdits`/`plan`/`dontAsk`/`bypassPermissions`
- Исправлены hook events: убраны вымышленные `PreCommit`/`PostCommit`/`PrePush`, добавлены реальные события (`SubagentStart`, `WorktreeCreate`, `ConfigChange` и т. д.)
- Исправлен CLI-синтаксис: `claude-code --headless` заменён на `claude -p` (print mode)
- Исправлены команды checkpoint: вымышленные `/checkpoint save/list/rewind/diff` заменены на реальный интерфейс `Esc+Esc` / `/rewind`
- Исправлено управление сессиями: вымышленные `/session list/new/switch/save` заменены на реальные `/resume`/`/rename`/`/fork`
- Исправлен формат plugin manifest: `plugin.yaml` → `.claude-plugin/plugin.json`
- Исправлены пути MCP-конфигов: `~/.claude/mcp.json` → `.mcp.json` (project) / `~/.claude.json` (user)
- Исправлены URL документации: `docs.claude.com` → `docs.anthropic.com`; удалён вымышленный `plugins.claude.com`
- Удалены вымышленные поля конфигурации в нескольких файлах
- Обновлены все даты "Last Updated" на февраль 2026

**Полный журнал изменений**: https://github.com/luongnv89/claude-howto/compare/20779db...v2.0.0
