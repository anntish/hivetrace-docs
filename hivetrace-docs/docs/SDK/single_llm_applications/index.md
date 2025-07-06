---
sidebar_position: 1
title: "Подключение приложений с одной LLM (без агентов)"
---

# Hivetrace SDK

## Обзор

Hivetrace SDK предназначен для интеграции с сервисом Hivetrace, обеспечивая мониторинг пользовательских запросов и ответов LLM.

## Установка

Установите SDK через pip:

```bash
pip install hivetrace[base]
```

## Использование

```python
from hivetrace import HivetraceSDK
```

## Синхронный и асинхронный режимы

Hivetrace SDK поддерживает как синхронный, так и асинхронный режимы выполнения. По умолчанию он работает в асинхронном режиме. Вы можете явно указать режим при инициализации.

### Синхронный режим

#### Инициализация синхронного режима

```python
# Синхронный режим
hivetrace = HivetraceSDK(async_mode=False)
```

#### Отправка пользовательского запроса

```python
# Синхронный режим
response = hivetrace.input(
    application_id="your-application-id",  # получен после регистрации приложения в UI
    message="Пользовательский запрос здесь"
)
```

#### Отправка ответа LLM

```python
# Синхронный режим
response = hivetrace.output(
    application_id="your-application-id",  # получен после регистрации приложения в UI
    message="Ответ LLM здесь"
)
```

### Асинхронный режим

#### Инициализация асинхронного режима

```python
# Асинхронный режим (по умолчанию)
hivetrace = HivetraceSDK(async_mode=True)
```

#### Отправка пользовательского запроса

```python
# Асинхронный режим
response = await hivetrace.input_async(
    application_id="your-application-id",  # получен после регистрации приложения в UI
    message="Пользовательский запрос здесь"
)
```

#### Отправка ответа LLM

```python
# Асинхронный режим
response = await hivetrace.output_async(
    application_id="your-application-id",  # получен после регистрации приложения в UI
    message="Ответ LLM здесь"
)
```

## Пример с дополнительными параметрами

```python
response = hivetrace.input(
    application_id="your-application-id", 
    message="Пользовательский запрос здесь",
    additional_parameters={
        "session_id": "your-session-id",
        "user_id": "your-user-id",
        "agents": {
            "agent-1-id": {"name": "Агент 1", "description": "Описание агента"},
            "agent-2-id": {"name": "Агент 2"},
            "agent-3-id": {}
        }
    }
)
```

> **Примечание:** `session_id`, `user_id` и `agent_id` должны быть действительными UUID.

## API

### `input`

```python
def input(application_id: str, message: str, additional_parameters: dict = None) -> dict:
    ...
```

Отправляет пользовательский запрос в Hivetrace.

* `application_id`: Идентификатор приложения (должен быть действительным UUID, созданным в UI)
* `message`: Пользовательский запрос
* `additional_parameters`: Словарь дополнительных параметров (необязательно)

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

### `output`

```python
def output(application_id: str, message: str, additional_parameters: dict = None) -> dict:
    ...
```

Отправляет ответ LLM в Hivetrace.

* `application_id`: Идентификатор приложения (должен быть действительным UUID, созданным в UI)
* `message`: Ответ LLM
* `additional_parameters`: Словарь дополнительных параметров (необязательно)

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

## Отправка запросов в синхронном режиме

```python
def main():
    hivetrace = HivetraceSDK(async_mode=False)
    response = hivetrace.input(
        application_id="your-application-id",
        message="Пользовательский запрос здесь"
    )

main()
```

## Отправка запросов в асинхронном режиме

```python
import asyncio

async def main():
    hivetrace = HivetraceSDK(async_mode=True)
    response = await hivetrace.input_async(
        application_id="your-application-id",
        message="Пользовательский запрос здесь"
    )
    await hivetrace.close()

asyncio.run(main())
```

### Закрытие асинхронного клиента

```python
await hivetrace.close()
```

## Конфигурация

SDK загружает конфигурацию из переменных окружения. Разрешенный домен (`HIVETRACE_URL`) и API токен (`HIVETRACE_ACCESS_TOKEN`) автоматически извлекаются из окружения.

### Источники конфигурации

Hivetrace SDK может получать конфигурацию из следующих источников:

**Файл .env:**

```bash
HIVETRACE_URL=https://your-hivetrace-instance.com
HIVETRACE_ACCESS_TOKEN=your-access-token  # получен в UI (страница API Tokens)
```

SDK автоматически загрузит эти настройки.

Вы также можете использовать конфигурацию при инициализации экземпляра класса hivetrace sdk для загрузки настроек.
```bash
trace = HivetraceSDK(
        config={
            "HIVETRACE_URL": HIVETRACE_URL,
            "HIVETRACE_ACCESS_TOKEN": HIVETRACE_ACCESS_TOKEN,
        },
        async_mode=False,
    )
```