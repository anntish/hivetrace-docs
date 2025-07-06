---
sidebar_position: 3
title: "Team Setup"
description: "Learn how to set up a developer team and delegate tasks"
---

# Team setup

In this section you will learn how to set up a developer team in HiveTrace for effective task delegation and collaboration.

### Navigating to user management

1. Log in to the HiveTrace dashboard
2. Go to **System users** in the main menu
3. Here you can manage all system users

![System users management](/img/system_users.png)

### Adding new developers

#### Step 1: Create a user

1. Click the **Add** button
2. Fill out the form with the developer's data:

#### Required fields
```yaml
email: "ivan.petrov@company.com"
username: "Ivan Ivanov"
password: "xxx-xxx-xx"
```

:::note
The password must be at least 8 characters long
:::

### Managing existing users

#### Activation/deactivation

- **Deactivated users** lose access to the system until reactivated
- **Active users** can work in the system according to their permissions

#### Deleting users

> **⚠️ Warning**: Deleting a user is an irreversible operation!

### Access rights

#### Restrictions for developers

:::warning Access restrictions
Developers will receive **limited access** to the system:

- ❌ **Managing system users is unavailable**
- ❌ **Viewing user messages is unavailable**
- ❌ **LLM responses are unavailable**
- ✅ **Core development and integration functions are available**
- ✅ **Viewing alerts and metrics**
- ✅ **Working with API and agents**
::: 