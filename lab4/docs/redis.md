# Redis в n8n: создание и удаление ключа

**Студент:** Дусов Алексей
**Группа:** СПД ИИ-241

## 1. Запуск Redis

Redis запущен в Docker. Лабораторная предлагает команду

```bash
docker run -d --name redis -p 6379:6379 redis:7
```

У меня Redis описан в том же [docker-compose.yml](../docker-compose.yml), что и n8n: образ `redis:7`, контейнер `redis`, порт `6379:6379`. Проверка, что Redis отвечает:

```bash
docker exec redis redis-cli ping
```

Ответ: `PONG`.

## 2. Credentials в n8n

| Параметр | Значение |
| --- | --- |
| Название | `Redis Dusov (local)` |
| Тип | Redis |
| Host | `host.docker.internal` |
| Port | `6379` |
| Database | `0` |
| Password | не задан (локальный Redis без пароля) |

Почему `host.docker.internal`, а не `localhost`: n8n работает в своём контейнере, и `localhost` внутри него — это сам контейнер n8n, а не компьютер. `host.docker.internal` — адрес хост-машины, где проброшен порт Redis `6379`. Чтобы это имя работало в Docker, в `docker-compose.yml` для n8n добавлено `extra_hosts: host.docker.internal:host-gateway`.

## 3. Workflow 1 — создание и чтение ключа

Файл: [flows/flow-09-redis.json](../flows/flow-09-redis.json)

```text
Ручной запуск → Redis: Set dusov → Redis: Get dusov
```

| Нода | Операция | Параметры |
| --- | --- | --- |
| Redis: Set dusov | **Set** | Key = `dusov`, Value = `Дусов, СПД ИИ-241, lab4 — ключ создан из n8n`, Key Type = String |
| Redis: Get dusov | **Get** | Key = `dusov`, Name (Property Name) = `value` |

Результат Get:

```json
{ "value": "Дусов, СПД ИИ-241, lab4 — ключ создан из n8n" }
```

Проверка из консоли Redis:

```bash
docker exec redis redis-cli GET dusov
```

## 4. Workflow 2 — удаление и проверка отсутствия

Файл: [flows/flow-10-redis.json](../flows/flow-10-redis.json)

```text
Ручной запуск → Redis: Delete dusov → Redis: Keys dusov (exists?) → Code: ключа нет?
```

| Нода | Операция | Что делает |
| --- | --- | --- |
| Redis: Delete dusov | **Delete** | удаляет ключ `dusov` |
| Redis: Keys dusov (exists?) | **Keys** | ищет ключи по шаблону `dusov`; если ключа нет, возвращает `{ "keys": [] }` |
| Code: ключа нет? | JavaScript | превращает список ключей в `exists: true/false` и понятное сообщение |

**Почему не Exists.** В Redis-ноде n8n (версия 2.44.6) нет операции `Exists`: есть Delete, Get, Increment, Info, Keys, List Length, Pop, Publish, Push и Set. Поэтому отсутствие ключа проверяется операцией **Keys** — это аналог команды `EXISTS`. У ноды Keys включено **Always Output Data**, чтобы workflow продолжился и при пустом результате.

Результат после удаления:

```json
{ "key": "dusov", "exists": false, "message": "Ключ удалён — его больше нет в Redis" }
```

Проверено и обратное: пока ключ существует, Keys возвращает `{ "keys": ["dusov"] }`, а проверка — `exists: true`.

## 5. Как работает Redis (кратко)

* Redis — хранилище «ключ → значение» в оперативной памяти: очень быстрое, используется для кэша, сессий, счётчиков и очередей.
* `SET key value` — записать, `GET key` — прочитать, `DEL key` — удалить, `EXISTS key` или `KEYS pattern` — проверить наличие.
* Данные живут, пока работает контейнер Redis. Для лабораторной этого достаточно: volume для Redis не подключался, после пересоздания контейнера ключи пропадут.

## 6. Скриншоты

| Шаг | Скриншот |
| :--- | :--- |
| Создание ключа (Set) | ![set](../screenshots/09-redis-set.png) |
| Чтение значения (Get) | ![get](../screenshots/09-redis-get.png) |
| Удаление (Delete) | ![delete](../screenshots/10-redis-delete.png) |
| Проверка отсутствия (Keys + Code) | ![check](../screenshots/10-redis-check.png) |
