---
sidebar_position: 1
slug: /
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# HiveTrace documentation

[v2.0.0] - 2025-07-07

<div style={{textAlign: 'left', marginBottom: '2rem'}}>

**HiveTrace** is a platform for monitoring and analyzing LLM applications with support for agents and multi-agent systems.

</div>

## 🚀 Quick Start

Get started with HiveTrace in a few simple steps:

<div style={{display: 'grid', gridTemplateColumns: 'repeat(auto-fit, minmax(300px, 1fr))', gap: '1rem', marginBottom: '2rem'}}>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '8px', padding: '1.5rem', backgroundColor: 'var(--ifm-background-color)'}}>

### 📋 Preparation
- [Authorization](getting_started/auth-setup)
- [Command setup (optional)](getting_started/team-setup)
- [API tokens](getting_started/integration)

</div>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '8px', padding: '1.5rem', backgroundColor: 'var(--ifm-background-color)'}}>

### ⚙️ Settings
- [Application Setup](getting_started/app-setup)
- [User Management](getting_started/user-management)

</div>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '8px', padding: '1.5rem', backgroundColor: 'var(--ifm-background-color)'}}>

### 🔗 Analytics
- [Monitoring and control](getting_started/monitoring)

</div>

</div>

# SDK: integration tools

### Connecting applications

<Tabs>
<TabItem value="single-llm" label="Single LLM" default>

Easily connect applications with a single language model without using agents.

[📖 Connecting applications with one LLM](SDK/single_llm_applications)

</TabItem>
<TabItem value="agents" label="Agents">

Powerful tools for working with agents and multi-agent systems.

<div style={{display: 'grid', gridTemplateColumns: 'repeat(auto-fit, minmax(250px, 1fr))', gap: '1rem', marginTop: '1rem'}}>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '6px', padding: '1rem', textAlign: 'center'}}>

#### 🤖 CrewAI
[CrewAI Documentation](SDK/Agents/CrewAI/)

</div>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '6px', padding: '1rem', textAlign: 'center'}}>

#### 🔗 Langchain
[Langchain Documentation](SDK/Agents/Langchain/)

</div>

</div>

</TabItem>
</Tabs>

## 🎯 Main features

<div style={{display: 'grid', gridTemplateColumns: 'repeat(auto-fit, minmax(280px, 1fr))', gap: '1.5rem', marginTop: '2rem'}}>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '8px', padding: '1.5rem', backgroundColor: 'var(--ifm-background-color)'}}>

### 📊 Real-time monitoring
Monitor the security of your LLM applications:
- Censor user messages
- Censor model responses

</div>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '8px', padding: '1.5rem', backgroundColor: 'var(--ifm-background-color)'}}>

### , Agent support
Integration with popular agency frameworks
- CrewAI
- Langchain

Building agent and trunk tracing graphs

</div>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '8px', padding: '1.5rem', backgroundColor: 'var(--ifm-background-color)'}}>

### 🔓 Security Policy Management
A wide range of built-in protection mechanisms

Customizable user policies for different scenarios

</div>

<div style={{border: '1px solid var(--ifm-color-emphasis-300)', borderRadius: '8px', padding: '1.5rem', backgroundColor: 'var(--ifm-background-color)'}}>

### 🧹 Cleaning of personal data
More than 20 categories for masking/detecting/deleting personal data in user messages and model responses

</div>

</div>