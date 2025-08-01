---
sidebar_position: 8
title: "Мониторинг и контроль"
---

## Отслеживание сессий

Следите за мониторингом сообщений в реальном времени через страницу **Сессии пользователей**.

На этой странице отображаются диаграммы и сводная таблица по результатам мониторинга всех сообщений пользователей и ответов моделей.

### Структура таблицы мониторинга

В таблице представлены следующие столбцы:

#### Основная информация
- **ID сессии** - уникальный идентификатор сессии пользователя в чате вашего приложения
- **Пользователь** - пользователь, к которому относится запись (отправитель сообщения или получатель ответа модели)
- **Приложение** - приложение, которое вы создали на странице "Приложения"
- **Дата** - дата записи
- **Время** - время записи

#### Сообщения и агенты
- **Msg** - текст сообщения пользователя или ответа модели
- **Агенты** - агенты, которые обрабатывали текущее сообщение
- **Направление** - направление сообщения:
  - `In` - сообщение пользователя
  - `Out` - ответ модели

#### Политики безопасности
- **Default policy** - результат проверки встроенной политики цензора
- **Custom policy** - результат проверки пользовательской политики цензора (только для входящих сообщений)
- **Refusal policy** - результат проверки политики отказа

> **Примечание:** Для всех политик результат записывается как:
> - `Okay` - политика не нарушена
> - `Bad` - политика нарушена

#### Дополнительные функции
- **Алерты** - кнопка для просмотра нарушений в сессии, за которыми последовало оповещение
- **Трассировка** - кнопка для просмотра графа взаимодействия агентов и вызовов инструментов (доступно только если в записи работали агенты)
- **Обратная связь** - возможность оценить корректность работы цензора с помощью реакций (палец вверх/вниз)