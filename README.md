# EasyMoRules
> Описание правил автоматизации для EasyMo

## Разделы

- [Условия (Conditions)](#условия)
- [Действия (Actions)](#действия)
- [Синтаксис](#синтаксис)

## Условия

- [notificationChannel](#notificationchannel)
- [notifyChannel](#notifychannel)

### notificationChannel
#### `type` newMessage
Срабатывает при получении сообщения от клиента
```javascript
"conditions": [                               
        {
          "category"    :   "notificationChannel",  
          "type"        :   "newMessage",           
          "descr"       :   {                       
            "type"            :   "equal",          
            "messageText"     :   "Привет"
          }
        }
      ],
```
Поля для `descr`:
- type

    > может принимать следующие значения:
    - equal 
    - included messageText
- messageType

    > может принимать следующие значения:
    - quick_phrase (в связке с textid)
    - textid (если quick_phrase)
    - text (просто текст если не используется готовая быстрая фраза)
    
### notifyChannel
#### `type` sendMessage
Срабатывает при отправке сообщения клиенту
```javascript
"actions": [                               
        {
          "category"    :   "notifyChannel",  
          "type"        :   "sendMessage",           
          "descr"       :   {                       
            "type"            :   "equal",          
            "messageText"     :   "Привет"
          }
        }
      ],
```
Поля для `descr`:
- type

    > может принимать следующие значения:
    - equal 
    - included messageText
- messageType

    > может принимать следующие значения:
    - quick_phrase (в связке с textid)
    - textid (если quick_phrase)
    - text (просто текст если не используется готовая быстрая фраза)

## Действия

### setField
#### `type` customers, orders, delivery
Меняет поле в информации о клиенте, заказе или доставке
```javascript
"actions": [                               
        {
          "category"    :   "setField",  
          "type"        :   "customers",  //  Может принимать разные значения, описано чуть ниже          
          "descr"       :   {                       
            "name"          :   "equal",          
            "value"         :   "Привет",
            "type"          :
          }
        }
      ],
```
Значния для `type`:
- customers (информация о клиенте)
- orders  (информация о заказе)
- delivery  (информация о доставке)

Поля для `descr`:
- name  (Название поля)
- value (Новое значение)
- type (для мультиселект поля)
    > может принимать следующие значения:
    - add (Добавить)
    - delete (Удалить)
    
### notifyChannel
#### `type` sendMessage
Отправляет сообщение клиенту
```javascript
"conditions": [                               
        {
          "category"    :   "notifyChannel",  
          "type"        :   "sendMessage",  //  Может принимать разные значения, описано чуть ниже          
          "descr"       :   {                       
            "messageType"   :   "quick_phrase",          
            "phraseId"      :   "1",
            "text"          :   "Привет, тестовое сообщение"
          }
        }
      ],
```

Поля для `descr`:
- messageType  (Тип сообщения)
- phraseId (Если messageType = "quick_phrase" то id быстрой фразы)
- text (голое сообщение, просто текст, если не указана быстрая фраза)

## Дополнительно

Дополнительные поля для [действий]

### delay
К каждому действию можно добавить поле `delay`, например:

```javascript
actions: [
        {
          "delay"           :   10,                 // Задает задержку для выполнения действия в [Секундах]
          "category"        :   "notifyChannel",
          "type"            :   "sendMessage",
          "descr"           :   {
            "messageType"             : "quick_phrase",
            "id"                      : 10
          }
        }
      ]

```

### waitAnswer
К каждому действию можно добавить объект( обернутое в { } ) `waitAnswer`, например:
waitAnswer выполняет действие после того как получил сообщение от клиента
```javascript
actions: [
        {
          "delay"           :   10,                           // Задает задержку для выполнения действия в [Секундах]
          "category"        :   "notifyChannel",
          "type"            :   "sendMessage",
          "descr"           :   {
            "messageType"             : "quick_phrase",
            "id"                      : 10
          }
        },
        "waitAnswer"      :   [                               //  Действия после ответа клиента на сообщение
            {
              "delay"           :   120,
              "waitType"        :   "ifCustomerNotAnswer",    //  если в течение delay не дождались ответа
              "category"        :   "setField",
              "type"            :   "customers",
              "descr"           :   {
                "name"                    : "Тэги",
                "value"                   : "Пинг",
                "type"                    : "add"
              }
            },
            {
              "waitType"        :   "ifCustomerAnswer",       //  если дождались, в этом случае delay указывать не нужно
              "category"        :   "setField",
              "type"            :   "customers",
              "descr"           :   {
                "name"                    : "Тэги",
                "value"                   : "Пинг",
                "type"                    : "delete"
              }
            }
          ]
      ]

```

Значния для `waitType`:
- ifCustomerNotAnswer (для этого типа требуется [delay](#delay), если в течение delay не дождались ответа, то выполнить)
- ifCustomerAnswer  (если дождались, в этом случае delay указывать не нужно)


## Синтаксис

> Фигурные скобки(.{ _ }.) значат, что блок может содердать в себе некоторые свойства, например:

```javascript
{
  "item": 0
}
```

> Квадратные скобки([  ]) значат, что блок может содержать несколько элементов, например:
```javascript
[
  {
    "item": 0
  },
  {
    "item": 1
  }
]
```

> Базовая структура файла автоматизации

```javascript
{
  "automationRules": [
    {
      "conditions": [                               //  Условия
        {
          "category"    :   "notificationChannel",  //  Категория условия
          "type"        :   "newMessage",           //  Тип условия
          "descr"       :   {                       //  В descr содержатся данные, необходимые для условия
            "type"            :   "equal",          //  все необходимые данные и их описание приведены в разделе [Условия]
            "messageText"     :   "Привет"
          }
        }
      ],
      "actions": [                                  //  Действия
        {
          "category"        :   "notifyChannel",    //  Категория действия
          "type"            :   "sendMessage",      //  Тип действия
          "descr"           :   {                   //  В descr содержатся данные, необходимые для условия
            "messageType"         : "quick_phrase", //  все необходимые данные и их описание приведены в разделе [Действия]
            "id"                  : 4
          }
        }
      ]
    }
  ]
}
```
