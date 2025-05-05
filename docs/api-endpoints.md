# Эндпоинты для MVP

## 1. Авторизация и управление аккаунтом

```
POST /api/auth/register
```
- Регистрация нового администратора (бизнеса)
- Параметры: email, password, businessName, phoneNumber
- Возвращает: userId, authToken

```
POST /api/auth/login
```
- Вход в систему
- Параметры: email, password
- Возвращает: authToken, userInfo

```
GET /api/auth/me
```
- Получение информации о текущем пользователе
- Заголовки: Authorization
- Возвращает: userInfo, businessDetails

```
PUT /api/auth/profile
```
- Обновление профиля администратора
- Заголовки: Authorization
- Параметры: businessName, description, contactInfo и т.д.
- Возвращает: updatedProfile

## 2. Интеграция с Telegram

```
POST /api/telegram/connect
```
- Подключение существующего Telegram-бота к аккаунту
- Заголовки: Authorization
- Параметры: botToken
- Возвращает: botInfo, connectionStatus

```
POST /api/telegram/webhook
```
- Эндпоинт для приема webhook-сообщений от Telegram
- Параметры: [стандартные данные от Telegram Bot API]
- Внутренняя обработка, без возврата данных клиенту

```
GET /api/telegram/status
```
- Проверка статуса подключения бота
- Заголовки: Authorization
- Возвращает: isActive, lastActivity, stats

## 3. Управление услугами

```
GET /api/services
```
- Получение списка услуг администратора
- Заголовки: Authorization
- Возвращает: массив с услугами

```
POST /api/services
```
- Создание новой услуги
- Заголовки: Authorization
- Параметры: name, description, duration, price
- Возвращает: serviceId, serviceDetails

```
PUT /api/services/{serviceId}
```
- Редактирование существующей услуги
- Заголовки: Authorization
- Параметры: name, description, duration, price
- Возвращает: обновленную информацию об услуге

```
DELETE /api/services/{serviceId}
```
- Удаление услуги
- Заголовки: Authorization
- Возвращает: статус операции

## 4. Управление расписанием

```
GET /api/schedule
```
- Получение расписания (с возможностью фильтрации по датам)
- Заголовки: Authorization
- Параметры: startDate, endDate
- Возвращает: доступные слоты и существующие записи

```
POST /api/schedule/workHours
```
- Настройка рабочих часов
- Заголовки: Authorization
- Параметры: weekday, startTime, endTime, isWorkDay
- Возвращает: обновленное расписание рабочих часов

```
POST /api/schedule/blockTime
```
- Блокировка определенного времени (обед, отпуск и т.д.)
- Заголовки: Authorization
- Параметры: startDateTime, endDateTime, reason
- Возвращает: createdBlockId, blockDetails

```
DELETE /api/schedule/blockTime/{blockId}
```
- Снятие блокировки времени
- Заголовки: Authorization
- Возвращает: статус операции

## 5. Управление записями

```
GET /api/appointments
```
- Получение всех записей (с фильтрацией)
- Заголовки: Authorization
- Параметры: startDate, endDate, status
- Возвращает: массив записей

```
GET /api/appointments/{appointmentId}
```
- Получение детальной информации о конкретной записи
- Заголовки: Authorization
- Возвращает: полную информацию о записи

```
POST /api/appointments
```
- Создание записи администратором (например, по звонку)
- Заголовки: Authorization
- Параметры: clientId/clientInfo, serviceId, dateTime
- Возвращает: appointmentId, appointmentDetails

```
PUT /api/appointments/{appointmentId}/status
```
- Изменение статуса записи (подтверждение, отмена)
- Заголовки: Authorization
- Параметры: status, comment
- Возвращает: обновленную информацию о записи

## 6. Управление клиентами

```
GET /api/clients
```
- Получение списка клиентов
- Заголовки: Authorization
- Параметры: search, limit, offset
- Возвращает: массив с информацией о клиентах

```
GET /api/clients/{clientId}
```
- Получение информации о конкретном клиенте
- Заголовки: Authorization
- Возвращает: информацию о клиенте и историю записей

```
POST /api/clients/notes
```
- Добавление заметки о клиенте
- Заголовки: Authorization
- Параметры: clientId, noteText
- Возвращает: noteId, noteDetails

## 7. Конструктор ботов (базовая версия)

```
GET /api/botbuilder/templates
```
- Получение доступных шаблонов для бота
- Заголовки: Authorization
- Возвращает: массив шаблонов

```
GET /api/botbuilder/settings
```
- Получение текущих настроек бота
- Заголовки: Authorization
- Возвращает: текущую конфигурацию бота

```
PUT /api/botbuilder/settings
```
- Обновление настроек бота
- Заголовки: Authorization
- Параметры: welcomeMessage, confirmationTemplate и т.д.
- Возвращает: обновленные настройки

```
PUT /api/botbuilder/buttons
```
- Настройка кнопок и меню бота
- Заголовки: Authorization
- Параметры: mainMenu, serviceSelection и т.д.
- Возвращает: обновленные настройки кнопок

## 8. Аналитика (базовая)

```
GET /api/analytics/summary
```
- Получение общей статистики использования
- Заголовки: Authorization
- Параметры: period
- Возвращает: количество записей, отмен, новых клиентов и т.д.

## Формат данных для ключевых запросов

### Создание услуги:

```json
// POST /api/services
{
  "name": "Стрижка",
  "description": "Мужская стрижка с мытьем головы",
  "duration": 60,  // в минутах
  "price": 1500,
  "isActive": true
}
```

### Настройка рабочих часов:

```json
// POST /api/schedule/workHours
{
  "weekday": 1,  // 0 - воскресенье, 1 - понедельник и т.д.
  "startTime": "09:00",
  "endTime": "18:00",
  "isWorkDay": true,
  "breakTime": {
    "startTime": "13:00",
    "endTime": "14:00"
  }
}
```

### Создание записи:

```json
// POST /api/appointments
{
  "clientInfo": {
    "telegramId": 123456789,
    "name": "Иван Петров",
    "phone": "+375291234567"
  },
  "serviceId": "5f8e6d4c2a1b3c0d1e2f3g4h",
  "dateTime": "2025-05-10T14:30:00Z"
}
```

### Настройка приветственного сообщения бота:

```json
// PUT /api/botbuilder/settings
{
  "welcomeMessage": "Добро пожаловать в наш сервис! Чем я могу вам помочь?",
  "confirmationTemplate": "Ваша запись на {service} подтверждена на {date} в {time}.",
  "reminderTemplate": "Напоминаем о записи на {service} завтра в {time}."
}
```
