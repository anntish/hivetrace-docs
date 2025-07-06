---
sidebar_position: 1
title: "Connecting Applications with a Single LLM (no agents)"
---

# HiveTrace SDK

## Overview

The HiveTrace SDK is designed to integrate your application with the HiveTrace service, providing monitoring of user requests and LLM responses.

## Installation

Install the SDK via **pip**:

```bash
pip install hivetrace[base]
```

## Usage

```python
from hivetrace import HivetraceSDK
```

## Synchronous vs. Asynchronous modes

The SDK supports both synchronous and asynchronous execution. By default, it works **asynchronously**. You can explicitly set the mode during initialisation.

### Synchronous mode

#### Initialise in synchronous mode

```python
# Synchronous mode
hivetrace = HivetraceSDK(async_mode=False)
```

#### Send a user request

```python
response = hivetrace.input(
    application_id="your-application-id",  # obtained after registering the application in the UI
    message="User request here",
)
```

#### Send an LLM response

```python
response = hivetrace.output(
    application_id="your-application-id",  # obtained after registering the application in the UI
    message="LLM response here",
)
```

### Asynchronous mode (default)

#### Initialise in asynchronous mode

```python
hivetrace = HivetraceSDK(async_mode=True)
```

#### Send a user request asynchronously

```python
response = await hivetrace.input_async(
    application_id="your-application-id",
    message="User request here",
)
```

#### Send an LLM response asynchronously

```python
response = await hivetrace.output_async(
    application_id="your-application-id",
    message="LLM response here",
)
```

## Example with additional parameters

```python
response = hivetrace.input(
    application_id="your-application-id",
    message="User request here",
    additional_parameters={
        "session_id": "your-session-id",
        "user_id": "your-user-id",
        "agents": {
            "agent-1-id": {"name": "Agent 1", "description": "Agent description"},
            "agent-2-id": {"name": "Agent 2"},
            "agent-3-id": {},
        },
    },
)
```

> **Note:** `session_id`, `user_id`, and every `agent_id` must be valid UUIDs.

## API Reference

### `input`

```python
def input(application_id: str, message: str, additional_parameters: dict | None = None) -> dict:
    ...
```

Sends a **user request** to HiveTrace.

* **application_id** — Application identifier (must be a valid UUID created in the UI)
* **message** — User message
* **additional_parameters** — Optional dictionary with extra data

**Sample response**

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
def output(application_id: str, message: str, additional_parameters: dict | None = None) -> dict:
    ...
```

Sends an **LLM response** to HiveTrace.

**Sample response**

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

## Sending requests in synchronous mode

```python
def main():
    hivetrace = HivetraceSDK(async_mode=False)
    response = hivetrace.input(
        application_id="your-application-id",
        message="User request here",
    )

main()
```

## Sending requests in asynchronous mode

```python
import asyncio

async def main():
    hivetrace = HivetraceSDK(async_mode=True)
    response = await hivetrace.input_async(
        application_id="your-application-id",
        message="User request here",
    )
    await hivetrace.close()

asyncio.run(main())
```

### Closing the asynchronous client

```python
await hivetrace.close()
```

## Configuration

The SDK loads configuration from **environment variables**. The base URL (`HIVETRACE_URL`) and API token (`HIVETRACE_ACCESS_TOKEN`) are automatically pulled from the environment.

### Configuration sources

HiveTrace SDK can retrieve configuration from the following sources:

**.env file**

```bash
HIVETRACE_URL=https://your-hivetrace-instance.com
HIVETRACE_ACCESS_TOKEN=your-access-token  # generated on the UI (API Tokens page)
```

The SDK will automatically load these settings.

You can also pass the configuration explicitly during SDK initialisation:

```python
trace = HivetraceSDK(
    config={
        "HIVETRACE_URL": HIVETRACE_URL,
        "HIVETRACE_ACCESS_TOKEN": HIVETRACE_ACCESS_TOKEN,
    },
    async_mode=False,
)
``` 

License
========

This project is licensed under Apache License 2.0.