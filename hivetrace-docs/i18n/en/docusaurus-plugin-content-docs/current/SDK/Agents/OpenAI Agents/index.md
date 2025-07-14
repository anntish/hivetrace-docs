# OpenAI Agents Integration

## Overview

HiveTrace SDK provides seamless integration with the [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/) for monitoring agent interactions, tool usage, handoffs, and task executions in your OpenAI Agents-based applications.

## Quick Start

### Prerequisites

- HiveTrace SDK installed: `pip install hivetrace[openai_agents]`
- OpenAI Agents SDK installed: `pip install openai-agents`
- Valid HiveTrace application ID and access token

### Basic Setup

**Step 1: Initialize the SDK (optional)**

```python
from hivetrace import HivetraceSDK
from hivetrace.adapters.openai_agents import HivetraceOpenAIAgentProcessor

# Initialize SDK
hivetrace = HivetraceSDK(
    config={
        "HIVETRACE_URL": "https://your-hivetrace-instance.com",
        "HIVETRACE_ACCESS_TOKEN": "your-access-token",
    },
    async_mode=False,
)
```

**Step 2: Set Up the Tracing Processor**

The `HivetraceOpenAIAgentProcessor` hooks into the OpenAI Agents SDK tracing system and automatically logs all agent and tool activity to HiveTrace.

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

or setup environment variables HIVETRACE_URL, HIVETRACE_ACCESS_TOKEN, HIVETRACE_APPLICATION_ID for easier configuration:

```python
from agents import Agent, Runner, function_tool, set_trace_processors
from hivetrace.adapters.openai_agents import HivetraceOpenAIAgentProcessor


set_trace_processors([HivetraceOpenAIAgentProcessor()])
```

**Step 3: Call your agents**


```python
from agents.tracing.create import trace

# Define your agent and tools as usual
@function_tool
def get_weather(city: str):
    return f"The weather in {city} is sunny."

agent = Agent(
    name="WeatherAgent",
    instructions="You provide weather updates.",
    tools=[get_weather],
)

# Run the agent
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