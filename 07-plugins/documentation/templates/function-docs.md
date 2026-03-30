# Функция: `functionName`

## Description
Краткое описание того, что делает функция.

## Signature
```typescript
function functionName(param1: Type1, param2: Type2): ReturnType
```

## Parameters

| Параметр | Тип | Обязателен | Описание |
|-----------|------|----------|-------------|
| param1 | Type1 | Да | Описание param1 |
| param2 | Type2 | Нет | Описание param2 |

## Returns
**Type**: `ReturnType`

Описание того, что возвращается.

## Throws
- `Error`: Когда передан некорректный ввод
- `TypeError`: Когда передан неверный тип

## Examples

### Базовое использование
```typescript
const result = functionName('value1', 'value2');
console.log(result);
```

### Продвинутое использование
```typescript
const result = functionName(
  complexParam1,
  { option: true }
);
```

## Notes
- Дополнительные замечания или предупреждения
- Соображения по производительности
- Лучшие практики

## See Also
- [Связанная функция](#)
- [Документация API](#)
