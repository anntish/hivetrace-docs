# Интеграция CrewAI

Мониторьте ваши многоагентные системы CrewAI с автоматическим отслеживанием всех взаимодействий агентов, использования инструментов и выполнения задач.

> **Важно**: Эта интеграция предназначена для **линейных рабочих процессов агентов**, где агенты выполняют задачи последовательно. Скоро: Поддержка сложных моделей взаимодействия агентов (параллельное выполнение, динамическое делегирование).
> 
> **Примечание**: Агенты, показанные в примерах (Исследователь, Писатель, Рецензент), предназначены только для демонстрации. Вы можете использовать любых агентов с любыми ролями, целями и инструментами, которые подходят для вашего конкретного случая использования.

---

## Быстрый старт

### Предварительные требования

- Установленный HiveTrace SDK: `pip install hivetrace[crewai]`
- Действительный ID приложения HiveTrace и токен доступа

### Базовая настройка

**Шаг 1: Инициализация SDK (обязательно) - используйте .env или конфигурацию**

```python
from hivetrace import HivetraceSDK

# Инициализация SDK (обязательно)
hivetrace = HivetraceSDK(
    config={
        "HIVETRACE_URL": "https://your-hivetrace-instance.com",      # обязательно
        "HIVETRACE_ACCESS_TOKEN": "your-access-token",              # обязательно
    },
    async_mode=False,  # опционально, по умолчанию=True
)
```

**Шаг 2: Настройка мониторинга агентов**

```python
import uuid
from crewai import Agent, Crew, Task
from hivetrace import crewai_trace as trace

# Создайте уникальные UUID для ваших агентов (сгенерируйте один раз и сохраните)
RESEARCHER_ID = str(uuid.uuid4())
WRITER_ID = str(uuid.uuid4())
REVIEWER_ID = str(uuid.uuid4())

# Определите идентификаторы агентов (обязательно для мониторинга)
AGENT_IDS = {
    "researcher": RESEARCHER_ID,
    "writer": WRITER_ID,
    "reviewer": REVIEWER_ID,
}

# Создайте сопоставление агентов для мониторинга (обязательно)
agent_id_mapping = {
    "Researcher": {
        "id": AGENT_IDS["researcher"], 
        "description": "Исследует темы и собирает информацию"
    },
    "Writer": {
        "id": AGENT_IDS["writer"], 
        "description": "Создает качественный письменный контент"
    },
    "Reviewer": {
        "id": AGENT_IDS["reviewer"], 
        "description": "Рецензирует и улучшает качество контента"
    },
}
```

**Шаг 3: Настройка агентов с отслеживанием инструментов**

```python
from crewai_tools import WebSearchTool

# Создайте инструменты и назначьте ID агентов для отслеживания
research_tools = [WebSearchTool()]
for tool in research_tools:
    tool.agent_id = AGENT_IDS["researcher"]  # обязательно для отслеживания инструментов

# Определите агентов
researcher = Agent(
    role="Researcher",
    goal="Тщательно исследовать заданную тему",
    backstory="Эксперт-исследователь с доступом к веб-поиску",
    tools=research_tools,
    verbose=True,
)

writer = Agent(
    role="Writer", 
    goal="Писать всесторонний контент",
    backstory="Профессиональный писатель контента",
    verbose=True,
)

reviewer = Agent(
    role="Reviewer",
    goal="Рецензировать и улучшать качество контента", 
    backstory="Редакционный эксперт, сосредоточенный на качестве",
    verbose=True,
)
```

**Шаг 4: Применение декоратора мониторинга**

```python
@trace(
    hivetrace=hivetrace,                           # обязательно
    application_id="your-hivetrace-app-id",       # обязательно
    agent_id_mapping=agent_id_mapping,            # обязательно
)
def create_monitored_crew():
    """Создать и вернуть мониторимую команду CrewAI."""
    
    # Определите задачи
    research_task = Task(
        description="Исследовать {topic} и собрать ключевую информацию",
        agent=researcher,
        expected_output="Подробный отчет об исследовании"
    )
    
    writing_task = Task(
        description="Написать статью о {topic} на основе исследования",
        agent=writer,
        expected_output="Хорошо написанная статья"
    )
    
    review_task = Task(
        description="Рецензировать и улучшить статью",
        agent=reviewer, 
        expected_output="Отполированная финальная статья"
    )
    
    return Crew(
        agents=[researcher, writer, reviewer],
        tasks=[research_task, writing_task, review_task],
        verbose=True,
    )
```

**Шаг 5: Выполнение с мониторингом**

    # Примечание: Все параметры в additional_parameters опциональны, 
    # но параметр "agents" обязателен, если вы хотите мониторить деятельность агентов

```python
def run_monitored_workflow(topic: str, user_id: str = None, session_id: str = None):
    """Выполнить рабочий процесс агентов с полным мониторингом."""
    
    # Сгенерируйте ID разговора для этого выполнения
    conversation_id = str(uuid.uuid4())
    
    # Зарегистрируйте начальный ввод пользователя (рекомендуется)
    hivetrace.input(
        application_id="your-hivetrace-app-id",
        message=f"Пользователь запросил создание контента для темы: {topic}",
        additional_parameters={
            "agent_conversation_id": conversation_id,
            "user_id": user_id,                    # опционально
            "session_id": session_id,              # опционально
            "agents": {
                agent_data["id"]: {
                    "name": agent_name,
                    "description": agent_data["description"]
                }
                for agent_name, agent_data in agent_id_mapping.items()
            }
        },
    )
    
    # Создайте и выполните команду
    crew = create_monitored_crew()
    
    # Выполните с параметрами времени выполнения
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

## Справочник по конфигурации

### Обязательные параметры

| Параметр | Описание |
|----------|----------|
| `hivetrace` | Инициализированный экземпляр SDK |
| `application_id` | ID вашего приложения HiveTrace из UI |
| `agent_id_mapping` | Сопоставление ролей агентов с ID и описаниями |

### Опциональные параметры

| Параметр | Описание |
|----------|----------|
| `user_id` | Идентификатор пользователя |
| `session_id` | Идентификатор сессии |

### Формат сопоставления ID агентов

```python
agent_id_mapping = {
    "Имя роли агента": {
        "id": "уникальная-uuid-строка",        # обязательно
        "description": "Описание агента"       # обязательно
    }
}
```

---

## Переменные окружения

Настройте переменные окружения для более легкой конфигурации:

```bash
# файл .env
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

## Расширенное использование

### Асинхронный режим

```python
import asyncio

# Инициализация в асинхронном режиме
hivetrace = HivetraceSDK(async_mode=True)

@trace(hivetrace=hivetrace, application_id="app-id", agent_id_mapping=mapping)
def create_crew():
    return Crew(agents=[...], tasks=[...])

async def run_async_workflow():
    # Зарегистрируйте ввод пользователя
    await hivetrace.input_async(
        application_id="app-id",
        message="Ввод пользователя",
    )
    
    # Выполните команду (требует CrewAI с поддержкой асинхронности)
    crew = create_crew()
    result = await crew.kickoff_async(inputs={"topic": "AI"})
    
    # Не забудьте закрыть асинхронный клиент
    await hivetrace.close()

# Запустите асинхронный рабочий процесс
asyncio.run(run_async_workflow())
```

### Отслеживание инструментов

Для комплексного мониторинга назначьте ID агентов всем инструментам:

```python
from crewai_tools import FileReadTool, WebSearchTool

# Создайте инструменты
search_tool = WebSearchTool()
file_tool = FileReadTool()

# Назначьте ID агента для отслеживания (обязательно)
search_tool.agent_id = "agent-uuid-here"
file_tool.agent_id = "agent-uuid-here"

# Добавьте к агенту
agent = Agent(
    role="Researcher",
    tools=[search_tool, file_tool],
    # ... другие параметры
)
```

---

Теперь у вас есть полный мониторинг вашей системы агентов CrewAI, интегрированной с HiveTrace!