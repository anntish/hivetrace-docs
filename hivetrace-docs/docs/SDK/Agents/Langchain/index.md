# Интеграция с LangChain

## Обзор

HiveTrace SDK обеспечивает бесшовную интеграцию с LangChain для мониторинга взаимодействий агентов, использования инструментов и выполнения задач в ваших приложениях на основе LangChain.

## Быстрый старт

### Предварительные требования

- Установленный HiveTrace SDK: `pip install hivetrace[langchain]`
- Действительный ID приложения HiveTrace и токен доступа

### Базовая настройка

**Шаг 1: Инициализация SDK**

```python
from hivetrace import HivetraceSDK
from hivetrace import LangChainAdapter

# Инициализация SDK
hivetrace = HivetraceSDK(
    config={
        "HIVETRACE_URL": "https://your-hivetrace-instance.com",      # обязательно
        "HIVETRACE_ACCESS_TOKEN": "your-access-token",              # обязательно
    },
    async_mode=False,  # опционально, по умолчанию=True
)
```

**Шаг 2: Настройка ID агентов**

```python
import uuid

# Определите ID ваших агентов (сгенерируйте один раз и сохраните)
PREDEFINED_AGENT_IDS = {
    "MainHub": str(uuid.uuid4()),
    "agent1": str(uuid.uuid4()),
    "agent2": str(uuid.uuid4()),
}
```

**Шаг 3: Настройка адаптера LangChain**

```python
from hivetrace.adapters.langchain import AgentLoggingCallback

# Создание callback для логирования
logging_callback = AgentLoggingCallback(
    default_root_name="MainHub",
    predefined_agent_ids=PREDEFINED_AGENT_IDS,
)

# Инициализация адаптера
adapter = LangChainAdapter(
    hivetrace=hivetrace,
    application_id="your-hivetrace-app-id",
    user_id="optional-user-id",
    session_id="optional-session-id",
)
```

**Шаг 4: Использование в вашем приложении LangChain**

```python
from langchain.agents import AgentExecutor, create_openai_tools_agent
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_openai import ChatOpenAI

# Инициализация вашей LLM
llm = ChatOpenAI(model="gpt-4", temperature=0)

# Создание ваших инструментов с callback
tools = [
    YourCustomTool(callback_handler=logging_callback),
    AnotherTool(callback_handler=logging_callback),
]

# Создание вашего агента
agent = create_openai_tools_agent(
    llm=llm,
    tools=tools,
    prompt=your_prompt_template,
)

# Создание исполнителя с callback
agent_executor = AgentExecutor(
    agent=agent,
    tools=tools,
    callbacks=[logging_callback],
    verbose=True,
)

# Обработка запроса
conversation_id = str(uuid.uuid4())
agents_mapping = {
    agent_uuid: {
        "name": agent_name,
        "description": "",
    }
    for agent_name, agent_uuid in PREDEFINED_AGENT_IDS.items()
}

# Логирование ввода
adapter.input(
    message="Ваш запрос здесь",
    additional_params={
        "agents": agents_mapping,
        "agent_conversation_id": conversation_id,
    }
)

# Выполнение агента
result = agent_executor.invoke({"input": "Ваш запрос здесь"})

# Адаптер автоматически залогирует все взаимодействия агентов и использование инструментов
```

### Использование декоратора Trace

Вы можете использовать декоратор `@trace` для автоматического мониторинга вашего оркестратора LangChain:

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
        # Инициализируйте ваших агентов и инструменты здесь

    def run(self, query: str):
        # Ваша логика оркестрации здесь
        self.logging_callback.reset() # используйте reset()
        pass

    result = orchestrator.run("ваш запрос")
    print(result)
    
```

 > **Важно**: если вы повторно используете тот же экземпляр `OrchestratorAgent`,
 > внутренний `AgentLoggingCallback` остается тем же. Вызывая
 > `reset()`, все накопленные данные очищаются и только события
 > текущего запроса отправляются в HiveTrace.
