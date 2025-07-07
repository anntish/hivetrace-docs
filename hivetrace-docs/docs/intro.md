---
sidebar_position: 1
slug: /
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Документация HiveTrace [v2.0.0] - 2025-07-07

<div style={{textAlign: 'left', marginBottom: '2rem'}}>

**HiveTrace** — это платформа для мониторинга и анализа LLM-приложений с поддержкой агентов и мультиагентных систем.

</div>

## 🚀 Быстрый старт

Начните работу с HiveTrace за несколько простых шагов:

<div style={{display: 'grid', gridTemplateColumns: 'repeat(auto-fit, minmax(300px, 1fr))', gap: '1rem', marginBottom: '2rem'}}>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '8px', padding: '1.5rem', backgroundColor: 'var(--ifm-background-color)'}}>

### 📋 Подготовка
- [Авторизация](getting_started/auth-setup)
- [Настройка команды (опционально)](getting_started/team-setup)
- [API-токены](getting_started/integration)

</div>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '8px', padding: '1.5rem', backgroundColor: 'var(--ifm-background-color)'}}>

### ⚙️ Настройка
- [Настройка приложения](getting_started/app-setup)
- [Управление пользователями](getting_started/user-management)

</div>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '8px', padding: '1.5rem', backgroundColor: 'var(--ifm-background-color)'}}>

### 🔗 Аналитика
- [Мониторинг и контроль](getting_started/monitoring)

</div>

</div>

## 🛠️ SDK: инструменты интеграции

### Подключение приложений

<Tabs>
<TabItem value="single-llm" label="Одиночная LLM" default>

Простое подключение приложений с одной языковой моделью без использования агентов.

[📖 Подключение приложений с одной LLM](SDK/single_llm_applications)

</TabItem>
<TabItem value="agents" label="Агенты">

Мощные инструменты для работы с агентами и мультиагентными системами.

<div style={{display: 'grid', gridTemplateColumns: 'repeat(auto-fit, minmax(250px, 1fr))', gap: '1rem', marginTop: '1rem'}}>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '6px', padding: '1rem', textAlign: 'center'}}>

#### 🤖 CrewAI
[Документация CrewAI](SDK/Agents/CrewAI/)

</div>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '6px', padding: '1rem', textAlign: 'center'}}>

#### 🔗 Langchain
[Документация Langchain](SDK/Agents/Langchain/)

</div>

</div>

</TabItem>
</Tabs>

## 🎯 Основные возможности

<div style={{display: 'grid', gridTemplateColumns: 'repeat(auto-fit, minmax(280px, 1fr))', gap: '1.5rem', marginTop: '2rem'}}>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '8px', padding: '1.5rem', backgroundColor: 'var(--ifm-background-color)'}}>

### 📊 Мониторинг в реальном времени
Отслеживайте безопасность ваших LLM-приложений:
  - цензор на сообщения пользователей
  - цензор на ответы модели

</div>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '8px', padding: '1.5rem', backgroundColor: 'var(--ifm-background-color)'}}>

### 🤖 Поддержка агентов
Интеграция с популярными агентскими фреймворками
  - CrewAI
  - Langchain

Построение графов трассировки агентов и тулов

</div>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '8px', padding: '1.5rem', backgroundColor: 'var(--ifm-background-color)'}}>

### 🔓 Управление политиками безопасности
Широкий список встроенных защитных механизмов

Настраиваемые пользовательские политики под разные сценарии

</div>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '8px', padding: '1.5rem', backgroundColor: 'var(--ifm-background-color)'}}>

### 🧹 Очистка персональных данных
Более 20 категорий для маскирования/обнаружения/удаления персональных данных в сообщениях пользователей и ответах модели

</div>

</div>
