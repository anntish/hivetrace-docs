---
sidebar_position: 5
title: "Censorship Policies Setup"
---

## Censorship Policies

HiveTrace provides built-in and custom policies to secure your application.

![HiveTrace censorship policies](/img/policy.png)

### Built-in policies

Use the default safety settings that cover the main threats.

#### Policies for censoring user requests

| Policy | Description |
|--------|-------------|
| `prompt_injection` | Detects attempts to inject malicious instructions into the user's prompt to bypass model restrictions |
| `jailbreak` | Identifies jailbreak techniques aimed at forcing the model to produce unwanted or prohibited responses |
| `prompt_to_extract_data` | Detects requests intended to extract confidential data or information |
| `harmful_prompt` | Filters requests containing malicious content and profanity |

#### Policies for censoring model responses

| Policy | Description |
|--------|-------------|
| `harmful_response` | Checks model responses for malicious or dangerous content |
| `refusal_label` | Tags responses in which the model refused to answer the user's request |

### Custom policies

Configure policies to match the requirements of your application

:::note
**Important**: Custom policies censor only user requests.
:::

Custom policies are specified as a system prompt to extend the censorship rules.

### Examples of custom policies

##### 1. For a retail bot

```
Only topics related to orders, delivery, payment, returns, warranties and product consultation are allowed, including characteristics, availability, promotions and compatibility.

Any conversation outside these topics – personal questions, philosophical reflections, flirting, discussions about politics, medicine, technology, education, social issues and other topics not related to shopping – is prohibited and considered a policy violation.
```

##### 2. For a banking bot

```
Only topics directly related to banking products are allowed: loans, debit and credit cards, installment plans, transfers, payments, account information, limits, fees, rules for using bank services and security of banking operations.

Topics outside the banking sphere — medicine, education, IT, technology, entertainment, politics, philosophy and personal questions — are prohibited.
``` 