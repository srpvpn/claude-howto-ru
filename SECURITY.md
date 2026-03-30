# Политика безопасности

## Обзор

Безопасность проекта Claude How To важна для нас. Этот документ описывает наши практики безопасности и объясняет, как ответственно сообщать об уязвимостях.

## Поддерживаемые версии

Мы предоставляем обновления безопасности для следующих версий:

| Версия | Статус | Поддержка до |
|---------|--------|---------------|
| Latest (main) | ✅ Active | Current + 6 months |
| 1.x releases | ✅ Active | Until next major version |

**Примечание**: поскольку это образовательный проект-гид, мы сосредоточены на поддержании актуальных best practices и безопасности документации, а не на традиционной версии-поддержке. Обновления применяются напрямую в main branch.

## Практики безопасности

### Безопасность кода

1. **Управление зависимостями**
   - Все Python-зависимости зафиксированы в `requirements.txt`
   - Регулярные обновления через dependabot и ручную проверку
   - Проверка безопасности Bandit на каждом коммите
   - Pre-commit hooks для проверок безопасности

2. **Качество кода**
   - Линтинг с Ruff помогает ловить типовые проблемы
   - Проверка типов с mypy предотвращает уязвимости, связанные с типами
   - Pre-commit hooks обеспечивают соблюдение стандартов
   - Все изменения проверяются перед merge

3. **Контроль доступа**
   - Branch protection на `main` branch
   - Обязательные ревью перед merge
   - Status checks должны проходить перед merge
   - Ограниченный write access к репозиторию

### Безопасность документации

1. **Никаких секретов в примерах**
   - Все API keys в примерах являются заглушками
   - Credentials никогда не хардкодятся
   - Файлы `.env.example` показывают необходимые переменные
   - Есть явные предупреждения про управление секретами

2. **Лучшие практики безопасности**
   - Примеры демонстрируют безопасные паттерны
   - Предупреждения по безопасности выделены в документации
   - Есть ссылки на официальные security guides
   - Обращение с credentials обсуждается в релевантных разделах

3. **Проверка контента**
   - Вся документация проверяется на security issues
   - В contributing guidelines есть security considerations
   - Проверяются внешние ссылки и references

### Безопасность зависимостей

1. **Сканирование**
   - Bandit проверяет весь Python-код на уязвимости
   - Проверки уязвимостей зависимостей через GitHub security alerts
   - Регулярные ручные security audits

2. **Обновления**
   - Security patches применяются оперативно
   - Major versions оцениваются осторожно
   - Changelog включает security-related updates

3. **Прозрачность**
   - Security updates документируются в коммитах
   - Vulnerability disclosures обрабатываются ответственно
   - При необходимости публикуются public security advisories

## Сообщение об уязвимости

### Какие проблемы нас интересуют

Мы благодарны за отчёты о:
- **Уязвимостях кода** в скриптах или примерах
- **Уязвимостях зависимостей** в Python-пакетах
- **Проблемах криптографии** в любых примерах кода
- **Ошибках аутентификации и авторизации** в документации
- **Рисках утечки данных** в примерах конфигурации
- **Уязвимостях инъекций** (SQL, command и т. д.)
- **SSRF/XXE/Path traversal** проблемах

### Что вне зоны ответственности

Вне scope этого проекта:
- Уязвимости в самом Claude Code (сообщайте в Anthropic)
- Проблемы внешних сервисов или библиотек (сообщайте upstream)
- Social engineering или user education
- Теоретические уязвимости без proof of concept
- Уязвимости в зависимостях, уже отправленные по официальным каналам

## Как сообщить

### Private Reporting (предпочтительно)

**Для чувствительных security issues используйте приватное vulnerability reporting GitHub:**

1. Перейдите по ссылке: https://github.com/luongnv89/claude-howto/security/advisories
2. Нажмите "Report a vulnerability"
3. Заполните детали уязвимости
4. Укажите:
   - Чёткое описание уязвимости
   - Затронутый компонент (файл, раздел, пример)
   - Потенциальный impact
   - Шаги воспроизведения, если они есть
   - Предлагаемое исправление, если оно у вас есть

**Что происходит дальше:**
- Мы подтвердим получение в течение 48 часов
- Проведём расследование и оценим severity
- Будем работать с вами над исправлением
- Согласуем сроки disclosure
- Укажем вас в security advisory, если вы не против анонимности

### Public Reporting

Для не чувствительных или уже публичных вопросов:

1. **Откройте GitHub Issue** с label `security`
2. Укажите:
   - Заголовок: `[SECURITY]` и краткое описание
   - Подробное описание
   - Затронутый файл или раздел
   - Потенциальный impact
   - Предлагаемое исправление

## Процесс реагирования на уязвимость

### Assessment (24 hours)

1. Мы подтверждаем получение отчёта
2. Оцениваем severity с помощью [CVSS v3.1](https://www.first.org/cvss/v3.1/specification-document)
3. Определяем, входит ли проблема в scope
4. Связываемся с вами с первичной оценкой

### Development (1-7 days)

1. Мы разрабатываем исправление
2. Проверяем и тестируем исправление
3. Создаём security advisory
4. Готовим release notes

### Disclosure (зависит от severity)

**Critical (CVSS 9.0-10.0)**
- Исправление выпускается немедленно
- Публикуется advisory
- Репортерам отправляется предупреждение за 24 часа

**High (CVSS 7.0-8.9)**
- Исправление выпускается в течение 48-72 часов
- Репортерам отправляется предупреждение за 5 дней
- Advisory публикуется вместе с релизом

**Medium (CVSS 4.0-6.9)**
- Исправление включается в следующий обычный update
- Advisory публикуется вместе с релизом

**Low (CVSS 0.1-3.9)**
- Исправление включается в следующий обычный update
- Advisory публикуется вместе с релизом

### Публикация

Мы публикуем security advisories, которые включают:
- Описание уязвимости
- Затронутые компоненты
- Оценку severity (CVSS score)
- Версию исправления
- Workarounds, если применимо
- Credit reporter'у с его согласия

## Лучшие практики для репортов

### Перед отправкой

- **Проверьте проблему**: можно ли воспроизвести её стабильно?
- **Поищите существующие issues**: не сообщал ли кто-то уже об этом?
- **Проверьте документацию**: есть ли рекомендации по безопасному использованию?
- **Проверьте исправление**: работает ли ваш вариант?

### При отправке

- **Будьте конкретны**: указывайте точные пути к файлам и номера строк
- **Дайте контекст**: почему это security issue?
- **Покажите impact**: что может сделать атакующий?
- **Опишите шаги**: как воспроизвести проблему?
- **Предложите fixes**: как бы вы это исправили?

### После отправки

- **Наберитесь терпения**: ресурсы ограничены
- **Будьте на связи**: быстро отвечайте на уточняющие вопросы
- **Сохраняйте конфиденциальность**: не публикуйте до исправления
- **Соблюдайте координацию**: следуйте нашему графику disclosure

## Security Headers and Configuration

### Repository Security

- **Branch protection**: Main branch requires 2 approvals for changes
- **Status checks**: All CI/CD checks must pass
- **CODEOWNERS**: Designated reviewers for key files
- **Signed commits**: Recommended for contributors

### Development Security

```bash
# Install pre-commit hooks
pre-commit install

# Run security scans locally
bandit -c pyproject.toml -r scripts/
mypy scripts/ --ignore-missing-imports
ruff check scripts/
```

### Dependency Security

```bash
# Check for known vulnerabilities
pip install safety
safety check

# Or use pip-audit
pip install pip-audit
pip-audit
```

## Security Guidelines for Contributors

### When Writing Examples

1. **Never hardcode secrets**
   ```python
   # ❌ Bad
   api_key = "sk-1234567890"

   # ✅ Good
   api_key = os.getenv("API_KEY")
   ```

2. **Warn about security implications**
   ```markdown
   ⚠️ **Security Note**: Never commit `.env` files to git.
   Add to `.gitignore` immediately.
   ```

3. **Use secure defaults**
   - Enable authentication by default
   - Use HTTPS where applicable
   - Validate and sanitize inputs
   - Use parameterized queries

4. **Document security considerations**
   - Explain why security matters
   - Show secure vs. insecure patterns
   - Link to authoritative sources
   - Include warnings prominently

### When Reviewing Contributions

1. **Check for exposed secrets**
   - Scan for common patterns (api_key=, password=)
   - Review configuration files
   - Check environment variables

2. **Verify secure coding practices**
   - No hardcoded credentials
   - Proper input validation
   - Secure authentication/authorization
   - Safe file handling

3. **Test security implications**
   - Can this be misused?
   - What is the worst case?
   - Are there edge cases?

## Security Resources

### Official Standards
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [CWE Top 25](https://cwe.mitre.org/top25/)
- [CVSS Calculator](https://www.first.org/cvss/calculator/3.1)

### Python Security
- [Python Security Advisories](https://www.python.org/dev/security/)
- [PyPI Security](https://pypi.org/help/#security)
- [Bandit Documentation](https://bandit.readthedocs.io/)

### Dependency Management
- [OWASP Dependency Check](https://owasp.org/www-project-dependency-check/)
- [GitHub Security Alerts](https://docs.github.com/en/code-security/dependabot/dependabot-alerts/about-dependabot-alerts)

### General Security
- [Anthropic Security](https://www.anthropic.com/)
- [GitHub Security Best Practices](https://docs.github.com/en/code-security)

## Security Advisories Archive

Previous security advisories are available in the [GitHub Security Advisories](https://github.com/luongnv89/claude-howto/security/advisories) tab.

## Контакт

По вопросам безопасности или для обсуждения security practices:

1. **Private Security Report**: используйте приватное vulnerability reporting GitHub
2. **General Security Questions**: откройте discussion с тегом `[SECURITY]`
3. **Security Policy Feedback**: создайте issue с label `security`

## Благодарности

Мы ценим security researchers и участников сообщества, которые помогают сохранять проект безопасным. Контрибьюторы, сообщающие об уязвимостях ответственно, будут отмечены в наших security advisories, если они не захотят остаться анонимными.

## Обновления политики

Эта политика безопасности пересматривается и обновляется:
- Когда обнаруживаются новые уязвимости
- Когда меняются best practices по безопасности
- Когда меняется scope проекта
- Не реже одного раза в год

**Last Updated**: January 2026
**Next Review**: January 2027

---

Спасибо, что помогаете сохранять Claude How To безопасным! 🔒
