---
sidebar_position: 9
title: "Alerts"
---

# Alerts

On the **Alerts** page you can configure alert delivery channels (Telegram bot/email) and view received alerts.

## Viewing alerts

The alerts table displays the following columns:

### Basic information
- **ID** - unique identifier of the alert
- **Source** - the channel to which the alert was sent. If the field is empty, the channels are not configured and the alert was delivered only on the HiveTrace platform
- **User** - the user whose interaction with the model led to the creation of the alert
- **Session** - session identifier
- **Trace** - link to the trace (provided agents were executed in the record)

### Types and direction
- **Violation type** - type of detected violation:
  - `MaliciousMessage` - malicious message
  - `TokenUnbounded` - token limit exceeded
- **Direction**:
  - `In` - alert due to user messages in the current session
  - `Out` - alert due to model responses in the current session

### Threat level marks for each policy
- **Threat level Custom**
- **Threat level Flagged**
- **Threat level Refusal**

### Timestamps
- **Date** - alert creation date
- **Time** - alert creation time 