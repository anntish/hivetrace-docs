---
sidebar_position: 1
title: "Connecting Applications with a Single LLM (no agents)"
---

# HiveTrace SDK

## Overview

The Hivetrace SDK lets you integrate with the Hivetrace service to monitor user prompts and LLM responses. It supports both synchronous and asynchronous workflows and can be configured via environment variables.

---

## Installation

Install from PyPI:

```bash
pip install hivetrace[base]
```

---

## Quick Start

```python
from hivetrace import SyncHivetraceSDK, AsyncHivetraceSDK
```

You can use either the synchronous client (`SyncHivetraceSDK`) or the asynchronous client (`AsyncHivetraceSDK`). Choose the one that fits your runtime.

---

## Synchronous Client

### Initialize (Sync)

```python
# The sync client reads configuration from environment variables or accepts an explicit config
client = SyncHivetraceSDK()
```

### Send a user prompt (input)

```python
response = client.input(
    application_id="your-application-id",  # Obtained after registering the application in the UI
    message="User prompt here",
)

# Optionally attach files (filename, bytes, mime_type)
files = [
    ("doc1.txt", open("doc1.txt", "rb"), "text/plain"),
]
response_with_files = client.input(
    application_id="your-application-id",
    message="User prompt with files",
    files=files,
)
```

### Send an LLM response (output)

```python
response = client.output(
    application_id="your-application-id",
    message="LLM response here",
)

# With files
files = [
    ("doc1.txt", open("doc1.txt", "rb"), "text/plain"),
]
response_with_files = client.output(
    application_id="your-application-id",
    message="LLM response with files",
    files=files,
)
```

> **Note:** the file is attached to analyze what users have sent to LLM. At the moment, the contents of the files are not checked by the hivetrace censor.

---

## Asynchronous Client

### Initialize (Async)

```python
# The async client can be used as a context manager
client = AsyncHivetraceSDK()
```

### Send a user prompt (input)

```python
response = await client.input(
    application_id="your-application-id",
    message="User prompt here",
)

# With files (filename, bytes, mime_type)
files = [
    ("doc1.txt", open("doc1.txt", "rb"), "text/plain"),
]
response_with_files = await client.input(
    application_id="your-application-id",
    message="User prompt with files",
    files=files,
)
```

### Send an LLM response (output)

```python
response = await client.output(
    application_id="your-application-id",
    message="LLM response here",
)

# With files
files = [
    ("doc1.txt", open("doc1.txt", "rb"), "text/plain"),
]
response_with_files = await client.output(
    application_id="your-application-id",
    message="LLM response with files",
    files=files,
)
```

> **Note:** the file is attached to analyze what users have sent to LLM. At the moment, the contents of the files are not checked by the hivetrace censor.

---

## Example with Additional Parameters

```python
response = client.input(
    application_id="your-application-id",
    message="User prompt here",
    additional_parameters={
        "session_id": "your-session-id",
        "user_id": "your-user-id",
        "agents": {
            "agent-1-id": {"name": "Agent 1", "description": "Agent description"},
            "agent-2-id": {"name": "Agent 2"},
            "agent-3-id": {}
        },
        # If you want to send only to censor and avoid DB persistence on backend
        "censor_only": True,
    }
)
```

---

## API

### `input`

```python
# Sync
def input(application_id: str, message: str, additional_parameters: dict | None = None, files: list[tuple[str, bytes, str]] | None = None,) -> dict: ...

# Async
async def input(application_id: str, message: str, additional_parameters: dict | None = None, files: list[tuple[str, bytes, str]] | None = None,) -> dict: ...
```

Sends a **user prompt** to Hivetrace.

* `application_id` — Application identifier (must be a valid UUID, created in the UI)
* `message` — The user prompt
* `additional_parameters` — Optional dictionary with extra context (session, user, agents, etc.)

**Response example:**

```json
{'status': 'processed',
 'monitoring_result': {'id': '8dc9fdd0-9029-4370-836b-eaa15f9c3f0e',
  'flagged': False,
  'type_of_violation': 'safe',
  'custom_flagged': None,
  'refusal_flagged': None,
  'token_count': 466,
  'token_usage_severity': 'high'},
 'blocked': False
}
```

---

### `output`

```python
# Sync
def output(application_id: str, message: str, additional_parameters: dict | None = None, files: list[tuple[str, bytes, str]] | None = None,) -> dict: ...

# Async
async def output(application_id: str, message: str, additional_parameters: dict | None = None, files: list[tuple[str, bytes, str]] | None = None,) -> dict: ...
```

Sends an **LLM response** to Hivetrace.

* `application_id` — Application identifier (must be a valid UUID, created in the UI)
* `message` — The LLM response
* `additional_parameters` — Optional dictionary with extra context (session, user, agents, etc.)

**Response example:**

```json
{'status': 'processed',
 'monitoring_result': {'id': '8dc9fdd0-9029-4370-836b-eaa15f9c3f0e',
  'flagged': False,
  'type_of_violation': 'safe',
  'custom_flagged': None,
  'refusal_flagged': None,
  'token_count': 466,
  'token_usage_severity': 'high'},
 'blocked': False
}
```

---

## Sending Requests in Sync Mode

```python
def main():
    # option 1: context manager
    with SyncHivetraceSDK() as client:
        response = client.input(
            application_id="your-application-id",
            message="User prompt here",
        )

    # option 2: manual close
    client = SyncHivetraceSDK()
    try:
        response = client.input(
            application_id="your-application-id",
            message="User prompt here",
        )
    finally:
        client.close()

main()
```

---

## Sending Requests in Async Mode

```python
import asyncio

async def main():
    # option 1: context manager
    async with AsyncHivetraceSDK() as client:
        response = await client.input(
            application_id="your-application-id",
            message="User prompt here",
        )

    # option 2: manual close
    client = AsyncHivetraceSDK()
    try:
        response = await client.input(
            application_id="your-application-id",
            message="User prompt here",
        )
    finally:
        await client.close()

asyncio.run(main())
```

### Closing the Async Client

```python
await client.close()
```

---

## Configuration

The SDK reads configuration from environment variables:

* `HIVETRACE_URL` — Base URL allowed to call.
* `HIVETRACE_ACCESS_TOKEN` — API token used for authentication.

These are loaded automatically when you create a client.


### Configuration Sources

Hivetrace SDK can retrieve configuration from the following sources:

**.env File:**

```bash
HIVETRACE_URL=https://your-hivetrace-instance.com
HIVETRACE_ACCESS_TOKEN=your-access-token  # obtained in the UI (API Tokens page)
```

The SDK will automatically load these settings.

You can also pass a config dict explicitly when creating a client instance.
```bash
client = SyncHivetraceSDK(
    config={
        "HIVETRACE_URL": HIVETRACE_URL,
        "HIVETRACE_ACCESS_TOKEN": HIVETRACE_ACCESS_TOKEN,
    },
)
```

## Environment Variables

Set up your environment variables for easier configuration:

```bash
# .env file
HIVETRACE_URL=https://your-hivetrace-instance.com
HIVETRACE_ACCESS_TOKEN=your-access-token
HIVETRACE_APP_ID=your-application-id
```

License
========

This project is licensed under Apache License 2.0.