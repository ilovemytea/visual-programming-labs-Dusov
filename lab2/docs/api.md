# Документация API (Лабораторная работа 2.8)

**Студент:** Дусов Алексей  
**Группа:** СПД ИИ-241  

Базовый адрес: `http://localhost:1880`. Все эндпоинты — `GET`, поток `flows/flow-08-endpoints.json`.
Каждый маршрут собран из своей пары `http in` → `function` → `http response`.

| Метод и путь | Что возвращает | Content-Type |
| --- | --- | --- |
| `GET /api/text` | простой текст | `text/plain` |
| `GET /api/info` | JSON с двумя полями | `application/json` |
| `GET /api/items` | список строительных материалов, query-параметры | `application/json` |
| `GET /api/items/:id` | один материал по id, path-параметр | `application/json` |

---

## 1. GET /api/text

Параметров нет.

```
GET http://localhost:1880/api/text
```

Ответ `200 OK`:

```
Привет! Это лабораторная №2 по Node-RED. Студент: Дусов, группа СПД ИИ-241.
```

## 2. GET /api/info

Параметров нет. Возвращает два поля: `student` и `serverTime` (время сервера в ISO 8601, UTC).

```
GET http://localhost:1880/api/info
```

Ответ `200 OK`:

```json
{ "student": "Дусов", "serverTime": "2026-10-21T17:42:44.111Z" }
```

## 3. GET /api/items и GET /api/items/:id

Данные — 5 строительных материалов, зашитых в function-ноде.

### Параметры

| Параметр | Где | Тип | Правило | Ошибка |
| --- | --- | --- | --- | --- |
| `id` | path (`/api/items/:id`) | целое > 0 | только цифры | `400`, если не число; `404`, если такого id нет |
| `category` | query | строка | `brick`, `cement`, `gypsum`, `insulation` | `404`, если в категории нет материалов |
| `limit` | query | целое | от 1 до 10 | `400`, если вне диапазона или не число |

### Успешные запросы

```
GET http://localhost:1880/api/items
```

`200 OK`:

```json
{
  "student": "Дусов",
  "count": 5,
  "items": [
    { "id": 1, "name": "Кирпич керамический М150", "category": "brick", "price": 420 },
    { "id": 2, "name": "Цемент М500 Д0", "category": "cement", "price": 18 },
    { "id": 3, "name": "ГКЛ Стандарт 12.5 мм", "category": "gypsum", "price": 9 },
    { "id": 4, "name": "Минеральная вата Лайт", "category": "insulation", "price": 35 },
    { "id": 5, "name": "Кирпич силикатный М100", "category": "brick", "price": 310 }
  ]
}
```

```
GET http://localhost:1880/api/items?category=brick&limit=1
```

`200 OK`:

```json
{ "student": "Дусов", "count": 1,
  "items": [ { "id": 1, "name": "Кирпич керамический М150", "category": "brick", "price": 420 } ] }
```

```
GET http://localhost:1880/api/items/2
```

`200 OK`:

```json
{ "id": 2, "name": "Цемент М500 Д0", "category": "cement", "price": 18 }
```

### Запросы с ошибками

| Запрос | Статус | Тело ответа |
| --- | --- | --- |
| `GET /api/items/99` | `404 Not Found` | `{"error":"Материал не найден","id":99}` |
| `GET /api/items/abc` | `400 Bad Request` | `{"error":"id должен быть положительным целым числом","got":"abc"}` |
| `GET /api/items?limit=50` | `400 Bad Request` | `{"error":"limit должен быть целым числом от 1 до 10","got":"50"}` |
| `GET /api/items?category=glass` | `404 Not Found` | `{"error":"Нет материалов в категории","category":"glass"}` |

### Проверка через curl

```bash
curl -i http://localhost:1880/api/text
curl -i http://localhost:1880/api/info
curl -i "http://localhost:1880/api/items?category=brick&limit=1"
curl -i http://localhost:1880/api/items/2
curl -i http://localhost:1880/api/items/99
curl -i http://localhost:1880/api/items/abc
```
