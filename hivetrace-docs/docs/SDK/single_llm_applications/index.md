---
sidebar_position: 1
title: "Подключение приложений с одной LLM (без агентов)"
---

## Обзор

Hivetrace SDK позволяет интегрироваться с сервисом Hivetrace для мониторинга пользовательских запросов и ответов LLM. Поддерживаются синхронные и асинхронные режимы работы, а конфигурация может выполняться через переменные окружения.

---

## Установка

Установите из PyPI:

```bash
pip install hivetrace[base]
```

---

## Быстрый старт

```python
from hivetrace import SyncHivetraceSDK, AsyncHivetraceSDK
```

Можно использовать синхронный клиент (`SyncHivetraceSDK`) или асинхронный клиент (`AsyncHivetraceSDK`). Выберите тот, который подходит вашему окружению.

---

## Синхронный клиент

### Инициализация (Sync)

```python
# Синхронный клиент читает конфигурацию из переменных окружения или принимает явную конфигурацию
client = SyncHivetraceSDK()
```

### Отправка пользовательского запроса (input)

```python
response = client.input(
    application_id="your-application-id",  # Получается после регистрации приложения в UI
    message="Текст пользовательского запроса",
)
```

### Отправка ответа LLM (output)

```python
response = client.output(
    application_id="your-application-id",
    message="Текст ответа LLM",
)
```

---

## Асинхронный клиент

### Инициализация (Async)

```python
# Асинхронный клиент можно использовать как контекстный менеджер
client = AsyncHivetraceSDK()
```

### Отправка пользовательского запроса (input)

```python
response = await client.input(
    application_id="your-application-id",
    message="Текст пользовательского запроса",
)
```

### Отправка ответа LLM (output)

```python
response = await client.output(
    application_id="your-application-id",
    message="Текст ответа LLM",
)
```

---

## Пример с дополнительными параметрами

```python
response = client.input(
    application_id="your-application-id",
    message="Текст пользовательского запроса",
    additional_parameters={
        "session_id": "your-session-id",
        "user_id": "your-user-id",
        "agents": {
            "agent-1-id": {"name": "Agent 1", "description": "Описание агента"},
            "agent-2-id": {"name": "Agent 2"},
            "agent-3-id": {}
        }
    }
)
```

> **Примечание:** `session_id`, `user_id` и все идентификаторы агентов должны быть корректными UUID.

---

## API

### `input`

```python
# Sync
def input(application_id: str, message: str, additional_parameters: dict | None = None) -> dict: ...

# Async
async def input(application_id: str, message: str, additional_parameters: dict | None = None) -> dict: ...
```

Отправляет **пользовательский запрос** в Hivetrace.

* `application_id` — Идентификатор приложения (UUID, создается в UI)
* `message` — Текст запроса
* `additional_parameters` — Необязательный словарь с дополнительным контекстом (сессия, пользователь, агенты и т. д.)

**Пример ответа:**

```json
{
  "status": "processed",
  "monitoring_result": {
    "is_toxic": false,
    "type_of_violation": "benign",
    "token_count": 9,
    "token_usage_warning": false,
    "token_usage_unbounded": false
  }
}
```

---

### `output`

```python
# Sync
def output(application_id: str, message: str, additional_parameters: dict | None = None) -> dict: ...

# Async
async def output(application_id: str, message: str, additional_parameters: dict | None = None) -> dict: ...
```

Отправляет **ответ LLM** в Hivetrace.

* `application_id` — Идентификатор приложения (UUID, создается в UI)
* `message` — Текст ответа LLM
* `additional_parameters` — Необязательный словарь с дополнительным контекстом (сессия, пользователь, агенты и т. д.)

**Пример ответа:**

```json
{
  "status": "processed",
  "monitoring_result": {
    "is_toxic": false,
    "type_of_violation": "safe",
    "token_count": 21,
    "token_usage_warning": false,
    "token_usage_unbounded": false
  }
}
```

---

## Отправка запросов в синхронном режиме

```python
def main():
    # вариант 1: контекстный менеджер
    with SyncHivetraceSDK() as client:
        response = client.input(
            application_id="your-application-id",
            message="Текст пользовательского запроса",
        )

    # вариант 2: ручное закрытие
    client = SyncHivetraceSDK()
    try:
        response = client.input(
            application_id="your-application-id",
            message="Текст пользовательского запроса",
        )
    finally:
        client.close()

main()
```

---

## Отправка запросов в асинхронном режиме

```python
import asyncio

async def main():
    # вариант 1: контекстный менеджер
    async with AsyncHivetraceSDK() as client:
        response = await client.input(
            application_id="your-application-id",
            message="Текст пользовательского запроса",
        )

    # вариант 2: ручное закрытие
    client = AsyncHivetraceSDK()
    try:
        response = await client.input(
            application_id="your-application-id",
            message="Текст пользовательского запроса",
        )
    finally:
        await client.close()

asyncio.run(main())
```

### Закрытие асинхронного клиента

```python
await client.close()
```

---

## Конфигурация

SDK считывает конфигурацию из переменных окружения:

* `HIVETRACE_URL` — базовый URL, доступный для вызовов.
* `HIVETRACE_ACCESS_TOKEN` — API-токен для аутентификации.

Эти переменные загружаются автоматически при создании клиента.

### Источники конфигурации

Hivetrace SDK может получать конфигурацию из следующих источников:

**Файл .env:**

```bash
HIVETRACE_URL=https://your-hivetrace-instance.com
HIVETRACE_ACCESS_TOKEN=your-access-token  # получен в UI (страница API Tokens)
```

SDK загрузит эти настройки автоматически.

Также можно передать конфигурацию явно при создании клиента:

```python
client = SyncHivetraceSDK(
    config={
        "HIVETRACE_URL": HIVETRACE_URL,
        "HIVETRACE_ACCESS_TOKEN": HIVETRACE_ACCESS_TOKEN,
    },
)
```

## Переменные окружения

Для удобства можно задать переменные окружения:

```bash
# .env файл
HIVETRACE_URL=https://your-hivetrace-instance.com
HIVETRACE_ACCESS_TOKEN=your-access-token
HIVETRACE_APP_ID=your-application-id
```


Лицензия
========

Этот проект лицензирован под Apache License 2.0.