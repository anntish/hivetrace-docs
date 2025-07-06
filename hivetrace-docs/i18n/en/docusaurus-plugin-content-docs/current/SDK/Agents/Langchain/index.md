# Integration with LangChain

## Overview

The HiveTrace SDK offers seamless integration with LangChain, allowing you to monitor agent interactions, tool usage, and task execution in your LangChain-based applications.

---

## Quick Start

### Prerequisites

- HiveTrace SDK installed: `pip install hivetrace[langchain]`
- A valid HiveTrace **Application ID** and **Access Token**

### Basic Setup

#### Step&nbsp;1: Initialize the SDK

```python
from hivetrace import HivetraceSDK
from hivetrace import LangChainAdapter

# SDK initialisation
hivetrace = HivetraceSDK(
    config={
        "HIVETRACE_URL": "https://your-hivetrace-instance.com",  # required
        "HIVETRACE_ACCESS_TOKEN": "your-access-token",          # required
    },
    async_mode=False,  # optional, defaults to True
)
```

#### Step&nbsp;2: Define agent IDs

```python
import uuid

# Generate agent IDs once and persist them
PREDEFINED_AGENT_IDS = {
    "MainHub": str(uuid.uuid4()),
    "agent1": str(uuid.uuid4()),
    "agent2": str(uuid.uuid4()),
}
```

#### Step&nbsp;3: Configure the LangChain adapter

```python
from hivetrace.adapters.langchain import AgentLoggingCallback

# Create a callback for logging
logging_callback = AgentLoggingCallback(
    default_root_name="MainHub",
    predefined_agent_ids=PREDEFINED_AGENT_IDS,
)

# Initialise the adapter
adapter = LangChainAdapter(
    hivetrace=hivetrace,
    application_id="your-hivetrace-app-id",
    user_id="optional-user-id",
    session_id="optional-session-id",
)
```

#### Step&nbsp;4: Use in your LangChain application

```python
from langchain.agents import AgentExecutor, create_openai_tools_agent
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_openai import ChatOpenAI

# Initialise your LLM
llm = ChatOpenAI(model="gpt-4", temperature=0)

# Create your tools with the callback
tools = [
    YourCustomTool(callback_handler=logging_callback),
    AnotherTool(callback_handler=logging_callback),
]

# Build the agent
agent = create_openai_tools_agent(
    llm=llm,
    tools=tools,
    prompt=your_prompt_template,
)

# Create the executor with the callback
agent_executor = AgentExecutor(
    agent=agent,
    tools=tools,
    callbacks=[logging_callback],
    verbose=True,
)

# Handle a user request
conversation_id = str(uuid.uuid4())
agents_mapping = {
    agent_uuid: {
        "name": agent_name,
        "description": "",
    }
    for agent_name, agent_uuid in PREDEFINED_AGENT_IDS.items()
}

# Log the input
adapter.input(
    message="Your user query here",
    additional_params={
        "agents": agents_mapping,
        "agent_conversation_id": conversation_id,
    }
)

# Execute the agent
result = agent_executor.invoke({"input": "Your user query here"})

# The adapter automatically logs all agent interactions and tool usage
```

---

## Using the `@trace` Decorator

You can automatically monitor your LangChain orchestrator with the `@trace` decorator:

```python
from hivetrace import langchain_trace as trace

@trace(
    hivetrace=hivetrace,
    application_id="your-hivetrace-app-id",
    user_id="optional-user-id",
    session_id="optional-session-id",
)
class YourOrchestrator:
    def __init__(self, llm):
        self.llm = llm
        self.logging_callback = AgentLoggingCallback(
            default_root_name="MainHub",
            predefined_agent_ids=PREDEFINED_AGENT_IDS,
        )
        # Initialise your agents and tools here

    def run(self, query: str):
        # Orchestration logic
        self.logging_callback.reset()  # use reset() before each run
        ...

result = YourOrchestrator(llm).run("Your query")
print(result)
```

> **Important**: If you reuse the same instance of `YourOrchestrator`, the internal `AgentLoggingCallback` remains the same. Call `reset()` to clear accumulated data so that only the events of the current request are sent to HiveTrace.

---

## Reference

### Required Parameters

| Parameter | Description |
|-----------|-------------|
| `hivetrace` | An initialised SDK instance |
| `application_id` | The HiveTrace Application ID from the UI |

### Optional Parameters

| Parameter | Description |
|-----------|-------------|
| `user_id` | User identifier |
| `session_id` | Session identifier |

### Environment Variables

```bash
# .env file
HIVETRACE_URL=https://your-hivetrace-instance.com
HIVETRACE_ACCESS_TOKEN=your-access-token
```

License
========

This project is licensed under Apache License 2.0.