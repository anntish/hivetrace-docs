# CrewAI Integration

Monitor your CrewAI multi-agent systems with automatic tracking of all agent interactions, tool usage, and task execution.

> **Important:** This integration currently supports **linear agent workflows** where agents perform tasks sequentially. Support for more complex interaction models (parallel execution, dynamic delegation, etc.) is coming soon.
> 
> **Note:** The agents shown in the examples (Researcher, Writer, Reviewer) are for demonstration only. You can use any agents, roles, goals, and tools that fit your use-case.

---

## Quick Start

### Prerequisites

- HiveTrace SDK installed: `pip install hivetrace[crewai]`
- A valid HiveTrace **Application ID** and **Access Token**

### Basic Setup

#### Step&nbsp;1: Initialize the SDK (required)

You can use environment variables or pass the configuration explicitly.

```python
from hivetrace import HivetraceSDK

hivetrace = HivetraceSDK(
    config={
        "HIVETRACE_URL": "https://your-hivetrace-instance.com",  # required
        "HIVETRACE_ACCESS_TOKEN": "your-access-token",          # required
    },
    async_mode=False,  # optional, defaults to True
)
```

#### Step&nbsp;2: Configure agent monitoring

```python
import uuid
from crewai import Agent, Crew, Task
from hivetrace import crewai_trace as trace

# Generate unique UUIDs for your agents (generate once and persist)
RESEARCHER_ID = str(uuid.uuid4())
WRITER_ID = str(uuid.uuid4())
REVIEWER_ID = str(uuid.uuid4())

# Map roles to IDs (required for monitoring)
AGENT_IDS = {
    "researcher": RESEARCHER_ID,
    "writer": WRITER_ID,
    "reviewer": REVIEWER_ID,
}

# Create a mapping with descriptions (required by the decorator)
agent_id_mapping = {
    "Researcher": {
        "id": AGENT_IDS["researcher"],
        "description": "Researches topics and gathers information",
    },
    "Writer": {
        "id": AGENT_IDS["writer"],
        "description": "Produces high-quality written content",
    },
    "Reviewer": {
        "id": AGENT_IDS["reviewer"],
        "description": "Reviews and improves content quality",
    },
}
```

#### Step&nbsp;3: Configure agents and tools with tracking

```python
from crewai_tools import WebSearchTool

# Create tools and assign agent IDs for tracking
research_tools = [WebSearchTool()]
for tool in research_tools:
    tool.agent_id = AGENT_IDS["researcher"]  # required for tool tracking

# Define the agents
researcher = Agent(
    role="Researcher",
    goal="Thoroughly research the given topic",
    backstory="Expert researcher with web-search capabilities",
    tools=research_tools,
    verbose=True,
)

writer = Agent(
    role="Writer",
    goal="Produce comprehensive content",
    backstory="Professional content writer",
    verbose=True,
)

reviewer = Agent(
    role="Reviewer",
    goal="Review and improve content quality",
    backstory="Editorial expert focused on quality",
    verbose=True,
)
```

#### Step&nbsp;4: Apply the monitoring decorator

```python
@trace(
    hivetrace=hivetrace,            # required
    application_id="your-hivetrace-app-id",  # required
    agent_id_mapping=agent_id_mapping,         # required
)
def create_monitored_crew():
    """Create and return a monitored CrewAI team."""

    # Define tasks
    research_task = Task(
        description="Research {topic} and gather key information",
        agent=researcher,
        expected_output="Detailed research report",
    )

    writing_task = Task(
        description="Write an article on {topic} based on the research",
        agent=writer,
        expected_output="Well-written article",
    )

    review_task = Task(
        description="Review and improve the article",
        agent=reviewer,
        expected_output="Polished final article",
    )

    return Crew(
        agents=[researcher, writer, reviewer],
        tasks=[research_task, writing_task, review_task],
        verbose=True,
    )
```

#### Step&nbsp;5: Run with monitoring

```python
def run_monitored_workflow(topic: str, user_id: str | None = None, session_id: str | None = None):
    """Execute the agent workflow with full monitoring."""

    conversation_id = str(uuid.uuid4())

    # Log the initial user input (recommended)
    hivetrace.input(
        application_id="your-hivetrace-app-id",
        message=f"User requested content for topic: {topic}",
        additional_parameters={
            "agent_conversation_id": conversation_id,
            "user_id": user_id,
            "session_id": session_id,
            "agents": {
                data["id"]: {
                    "name": name,
                    "description": data["description"],
                }
                for name, data in agent_id_mapping.items()
            },
        },
    )

    # Create and execute the crew
    crew = create_monitored_crew()

    execution_params = {"inputs": {"topic": topic}}
    if user_id:
        execution_params["user_id"] = user_id
    if session_id:
        execution_params["session_id"] = session_id
    if conversation_id:
        execution_params["agent_conversation_id"] = conversation_id

    result = crew.kickoff(**execution_params)
    return result
```

---

## Configuration Reference

### Required Parameters

| Parameter | Description |
|-----------|-------------|
| `hivetrace` | An initialised SDK instance |
| `application_id` | The HiveTrace Application ID from the UI |
| `agent_id_mapping` | Mapping of agent roles to IDs and descriptions |

### Optional Parameters

| Parameter | Description |
|-----------|-------------|
| `user_id` | User identifier |
| `session_id` | Session identifier |

### Agent ID Mapping Format

```python
agent_id_mapping = {
    "Agent role name": {
        "id": "unique-uuid-string",  # required
        "description": "Agent description",  # required
    }
}
```

---

## Environment Variables

Set environment variables for an easier setup:

```bash
# .env file
HIVETRACE_URL=https://your-hivetrace-instance.com
HIVETRACE_ACCESS_TOKEN=your-access-token
HIVETRACE_APP_ID=your-application-id
```

```python
import os
from dotenv import load_dotenv

load_dotenv()

hivetrace = HivetraceSDK(
    config={
        "HIVETRACE_URL": os.getenv("HIVETRACE_URL"),
        "HIVETRACE_ACCESS_TOKEN": os.getenv("HIVETRACE_ACCESS_TOKEN"),
    },
    async_mode=False,
)
```

---

## Advanced Usage

### Asynchronous Mode

```python
import asyncio

hivetrace = HivetraceSDK(async_mode=True)

@trace(
    hivetrace=hivetrace,
    application_id="app-id",
    agent_id_mapping=agent_id_mapping,
)
def create_crew():
    return Crew(agents=[...], tasks=[...])

async def run_async_workflow():
    await hivetrace.input_async(
        application_id="app-id",
        message="User input",
    )

    crew = create_crew()
    result = await crew.kickoff_async(inputs={"topic": "AI"})

    await hivetrace.close()

asyncio.run(run_async_workflow())
```

### Tool Tracking

Assign an `agent_id` to every tool you want to track:

```python
from crewai_tools import FileReadTool, WebSearchTool

search_tool = WebSearchTool()
file_tool = FileReadTool()

search_tool.agent_id = "agent-uuid-here"
file_tool.agent_id  = "agent-uuid-here"

agent = Agent(
    role="Researcher",
    tools=[search_tool, file_tool],
    ...
)
```

License
========

This project is licensed under Apache License 2.0.
