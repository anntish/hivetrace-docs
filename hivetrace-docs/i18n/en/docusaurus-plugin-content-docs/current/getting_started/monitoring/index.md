---
sidebar_position: 8
title: "Monitoring and Control"
---

## Tracking sessions

Monitor messages in real time on the **User sessions** page.

This page displays charts and a summary table with monitoring results for all user messages and model responses.

### Monitoring table structure

The table contains the following columns:

#### Basic information
- **Session ID** - unique identifier of the user's chat session in your application
- **User** - the user to whom the entry relates (the sender of the message or the recipient of the model's response)
- **Application** - the application you created on the "Applications" page
- **Date** - record date
- **Time** - record time

#### Messages and agents
- **Msg** - text of the user's message or the model's response
- **Agents** - agents that processed the current message
- **Direction** - message direction:
  - `In` - user message
  - `Out` - model response

#### Security policies
- **Default policy** - result of the built-in censor policy check
- **Custom policy** - result of the custom censor policy check (only for incoming messages)
- **Refusal policy** - result of the refusal policy check

> **Note:** For all policies the result is recorded as:
> - `Okay` - policy not violated
> - `Bad` - policy violated

#### Additional functions
- **Alerts** - button to view violations in the session that triggered an alert
- **Trace** - button to view the graph of agent interaction and tool calls (available only if agents were executed in the record)
- **Feedback** - ability to evaluate the correctness of the censor using reactions (thumbs up/down) 