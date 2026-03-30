---
name: api-documentation-generator
description: Generate comprehensive, accurate API documentation from source code. Use when creating or updating API documentation, generating OpenAPI specs, or when users mention API docs, endpoints, or documentation.
---

# Навык генерации API-документации

## Что генерирует

- Спецификации OpenAPI/Swagger
- Документацию API-эндпоинтов
- Примеры использования SDK
- Руководства по интеграции
- Справочники кодов ошибок
- Руководства по аутентификации

## Структура документации

### Для каждого эндпоинта

```markdown
## GET /api/v1/users/:id

### Description
Краткое объяснение того, что делает этот endpoint

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| id | string | Yes | User ID |

### Response

**200 Success**
```json
{
  "id": "usr_123",
  "name": "John Doe",
  "email": "john@example.com",
  "created_at": "2025-01-15T10:30:00Z"
}
```

**404 Not Found**
```json
{
  "error": "USER_NOT_FOUND",
  "message": "User does not exist"
}
```

### Examples

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
```
