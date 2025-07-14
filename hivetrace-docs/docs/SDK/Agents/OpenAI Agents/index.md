# Интеграция с OpenAI Agents

## Обзор

HiveTrace SDK обеспечивает бесшовную интеграцию с [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/) для мониторинга взаимодействий агентов, использования инструментов, передач управления и выполнения задач в ваших приложениях на основе OpenAI Agents.

## Быстрый старт

### Предварительные требования

- Установленный HiveTrace SDK: `pip install hivetrace[openai_agents]`
- Установленный OpenAI Agents SDK: `pip install openai-agents`
- Действительный ID приложения HiveTrace и токен доступа

### Базовая настройка

**Шаг 1: Инициализация SDK (опционально)**

```python
from hivetrace import HivetraceSDK
from hivetrace.adapters.openai_agents import HivetraceOpenAIAgentProcessor

# Инициализация SDK
hivetrace = HivetraceSDK(
    config={
        "HIVETRACE_URL": "https://your-hivetrace-instance.com",
        "HIVETRACE_ACCESS_TOKEN": "your-access-token",
    },
    async_mode=False,
)
```

**Шаг 2: Настройка трассировки**

`HivetraceOpenAIAgentProcessor` подключается к системе трассировки OpenAI Agents SDK и автоматически логирует всю активность агентов и инструментов в HiveTrace.

```python
from agents import Agent, Runner, function_tool, set_trace_processors
from hivetrace.adapters.openai_agents import HivetraceOpenAIAgentProcessor


set_trace_processors(
    HivetraceOpenAIAgentProcessor(
        hivetrace_instance=hivetrace,
        application_id="your-hivetrace-app-id",
    )
)
```

или настройте переменные окружения HIVETRACE_URL, HIVETRACE_ACCESS_TOKEN, HIVETRACE_APPLICATION_ID для упрощения конфигурации:

```python
from agents import Agent, Runner, function_tool, set_trace_processors
from hivetrace.adapters.openai_agents import HivetraceOpenAIAgentProcessor


set_trace_processors([HivetraceOpenAIAgentProcessor()])
```

**Шаг 3: Вызов ваших агентов**


```python
from agents.tracing.create import trace

# Определите вашего агента и инструменты как обычно
@function_tool
def get_weather(city: str):
    return f"The weather in {city} is sunny."

agent = Agent(
    name="WeatherAgent",
    instructions="You provide weather updates.",
    tools=[get_weather],
)

# Запуск агента
import asyncio
async def main():
    with trace(
        workflow_name="Agent workflow",
        metadata={
            "session_id": "your-session-id",
            "user_id": "your-user-id",
        },
    ):
        result = await Runner.run(agent, "What's the weather in Paris?")
        print(result.final_output)

asyncio.run(main())
```