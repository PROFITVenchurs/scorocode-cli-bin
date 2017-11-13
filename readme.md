
Сборка:

- **sha1** bb479e6d5938e713b1dbaa778ddf74727cc945a7 - [scorocode-cli_amd64.deb](https://github.com/PROFITVenchurs/scorocode-cli-bin/blob/master/scorocode-cli_1.2.3_amd64.deb)
- **sha1** 1f0a1a64c286d20ddf268f8af0947f13c094f72d - [scorocode-cli.pkg](https://github.com/PROFITVenchurs/scorocode-cli-bin/blob/master/scorocode-cli-1.2.3.pkg)
- **sha1** bd438552206c0da8d730e398241707ab2308c581 - [scorocode-cli_x86.msi](https://github.com/PROFITVenchurs/scorocode-cli-bin/blob/master/scorocode-cli_1.2.3_x86.msi)


# Инструкция по использованию *scorocode-cli*

scorocode-cli - консольная утилита для работы с приложениями на https://scorocode.ru

## Оглавление:
- [Краткое описание работы](#Краткое-описание-работы)
- [Прокси-сервер](#Прокси-сервер)
- [Команда: создать приложение](#Создать-приложение)
- [Команда: получить приложение с https://scorocode.ru](#Получить-приложение-с-httpsscorocoderu)
- [scorocode.json](#scorocodejson)
- [Команды изменения структуры и метаданных приложения](#Команды-изменения-структуры-и-метаданных-приложения)
- [Загрузить статический контент](#Загрузить-статический-контент)
- [Коллекции](#Коллекции)
- [Скрипты](#Скрипты)
- [Создать скрипт](#Создать-скрипт)
- [Получить скрипт](#Получить-скрипт)
- [Отправить скрипт](#Отправить-скрипт)
- [Удалить скрипт](#Удалить-скрипт)
- [Запустить скрипт](#Запустить-скрипт)


### Краткое описание работы

С помощью scorocode-cli скачиваем приложение с https://scorocode.ru, и далее путем выполнения
необходимых команд переносим изменения из локальной копии приложения на https://scorocode.ru.

Справку о доступных командах и их параметрах можно получить путем выполнения команд:
- scorocode-cli -help
- scorocode-cli <команда> -help

### Прокси-сервер:

Параметры прокси-сервера могут быть заданы одним из следующих шаблонов:

- "http://\<*user*\>:\<*password*\>@\<*proxy host*\>:\<*proxy port*\>" - прокси-сервер **требует** аутентификации по логину и паролю
- "http://\<*proxy host*\>:\<*proxy port*\>" - прокси-сервер **не требует** аутентификации по логину и паролю

##### Windows

Создайте переменную окружения **"HTTP_PROXY"** вручную или выполните скрипт:

```bash
Set wshShell = CreateObject( "WScript.Shell" )
Set wshSystemEnv = wshShell.Environment( "SYSTEM" )
wshSystemEnv( "HTTP_PROXY" ) = <template>
```

##### Linux

```bash
sudo echo 'HTTP_PROXY=<template>' >> ~/.bashrc
```

##### OS X

```bash
sudo echo 'HTTP_PROXY=<template>' >> ~/.bash_profile
```

### Создать приложение

Создает новое приложение на https://scorocode.ru и сохраняет его метаданные в текущий каталог

```bash
./scorocode-cli create -name <app name>
```

### Получить приложение с https://scorocode.ru

```bash
scorocode-cli fetch -app <ключ приложения> \
                    -cli <клиентский ключ, всегда -'javascript'> \
                    -acc <ключ приложения, всегда - 'masterKey'> \
                    [-dir путь к новому каталогу, в который будет клонировано приложение. Если не задан, то в текущей директории будет создан каталог, с именем соответствующим имени приложения]
```

После выполнения данной команды, в каталог будет клонирована информация:
- параметры проекта - *scorocode.json*
- описание приложения - *application.json*
- описание коллекций - *collections*
- серверные скрипты - *scripts*
- шаблоны писем - *mail_templates*


#### scorocode.json

_**ВНИМАНИЕ!**_ **Не рекомендуется выкладывать этот файл в общий доступ, а также хранить в системе контроля версий. Т.к. в нем хранится информация о секретных ключах приложения!**

Данный файл необходим для хранения параметров подключения к приложения на https://scorocode.ru

```javascript
{
    "branches": [
        {
            // Наименование ветки Git. По умолчанию "master"
            // Если git не используется, то использовать "master"
            "name": "master",

            // Путь к статическим файлам приложения. Пример:
            // 1. путь к архиву zip, который будет распакован на сервере (пример: /mydir/static.zip)
            // 2. путь к файлу (пример: /index.html)
            // 3. путь к каталогу (пример: /mydir).
            //
            // Путь к файлу может быть как относительным (по отношению к каталогу в котором находится файл scorocode.json), так и полным
            "static": "index.html",

            // Ключи приложения:
            "keys": {
                "appId": "<ключ приложения>",
                "master": "ключ приложения, всегда - 'masterKey'",
                "client": "клиентский ключ, всегда -'javascript'"
            }
        }
    ]
}
```


## Команды изменения структуры и метаданных приложения

### Загрузить статический контент

Статический контент - это набор файлов (html, картинки и т.д.), которые будут находится в публичном доступе по адресу: https://<доменное имя>.scorocode.io

Перед загрузкой статического контента необходимо:
- установить доменное имя в настройках приложения
- в файле [scorocode.json](#scorocodejson) установить путь к файлу со статическим контентом в локальной файловой системе. Параметр *"static"*

```bash
scorocode-cli static [-dir <путь к каталогу, в котором находится файл scorocode.json. По умолчанию - это текущий каталог>]
```
### Коллекции

Все коллекции находятся в каталоге проекта /collections. Каждая коллекция представлена несколькими файлами:
- */collections/**<имя коллекции>**/beforeInsert.js* - код триггера перед добавлением
- */collections/**<имя коллекции>**/afterInsert.js* - код триггера после добавления
- */collections/**<имя коллекции>**/beforeRemove.js* - код триггера перед удалением
- */collections/**<имя коллекции>**/afterRemove.js* - код триггера после удаления
- */collections/**<имя коллекции>**/beforeUpdate.js* - код триггера перед обновлением
- */collections/**<имя коллекции>**/afterUpdate.js* - код триггера после обновления
- */collections/**<имя коллекции>**/collection.json* - схема коллекции, её параметры и индексы

```javascript
{
    // Идентификат коллекции на https://scorocode.ru (НЕ ИЗМЕНЯТЬ)
    "id": "59fb33574c27e848b6781d7c",

    // Использовать WebSocket уведомления
    "notify": false,

    // Использовать список контроля доступа документов
    "useDocsACL": false,

    // Список контроля доступа (идентификаторы ролей или идентификаторы пользователей)
    "ACL": {

        // Список контроля доступа операции создания нового документа
        "create": ["*"],

        // Список контроля доступа операции чтения документа
        "read": ["*"],

        // Список контроля доступа операции удаления документа
        "remove": ["*"],

        // Список контроля доступа операции обновления документа
        "update": ["*"]
    },

    // Список полей коллекции
    "fields": [
        {
            // Идентификатор поля коллекции на https://scorocode.ru (НЕ ИЗМЕНЯТЬ)
            "id": "59fb33574c27e848b6781d7d",

            // Наименование поля коллекции
            "name": "readACL",

            // Описание поле (произвольный текст)
            "description":"",


            // Тип поля коллекции. Возможные варианты:
            // "Date" - дата в формате RFC3339 (Пример: "2006-01-02T15:04:05Z07:00")
            // "Boolean" - булево значение, возможные варианты (Пример: true/false)
            // "String" - Строка
            // "File" - Файл
            // "Number" - Числовой тип (целочисленный или с плавающей точкой)
            // "Object" - Объект (Пример: {"property": 1})
            // "Pointer" - Идентификаторов из таблицы, которая указана в параметре "target" (Пример: "M4v3NpJEM4")
            // "Relation" - Список идентификаторов из таблицы, которая указана в параметре "target" (Пример: ["aaaa", "bbb"])
            // "ACL" - Список идентификаторов ролей (Пример: ["aaaa", "bbb"])
            // "Array" - Массив произвольных данных (Пример: [1, true, "text"])
            // "Password" - Строка. Значение храниться в виде хэша
            "type": "ACL",

            // Наименование таблицы, с которой связано поле.
            // Используется только для полей с типами:
            // - "Pointer"
            // - "Relation"
            "target":"users",

            // Признак, что поле является системным.
            // Изменение параметров системных полей
            // игнорируется при обработке на стороне
            // сервера
            "system": true
        }
    ],

    // Признак, что триггер является активным
    "triggers": {
        "beforeInsert": false,
        "afterInsert": false,
        "afterUpdate": false,
        "beforeUpdate": false,
        "beforeRemove": false,
        "afterRemove": false
    },

    // Список индексов коллекции
    "indexes": [
        {
            // Наименование индекса
            "name": "my_index",

            // Список полей индекса
            "fields": [
                {
                    // Наименование поля в коллекции по которому строиться индекс
                    "name": "deviceId",

                    // Порядок сортировки в индексе. Возможные значения:
                    //  1 - сортировать по возрастанию
                    // -1 - сортировать по убыванию
                    "order": 1
                }
            ]
        }
    ]
}
```

### Скрипты

Все скрипты находятся в каталоге проекта /scripts.

Скрипт может содержать настройки, которые могут быть размещены в начале файла в виде группы комментариев отделенных от кода двумя пустыми строками.

```javascript
// @ Идентификатор скрипта на https://scorocode.ru (НЕ ИЗМЕНЯТЬ).
// @ Если это копия другого файла, то удалите этот параметр.
//
// _id: "585be98222a5483cbcfe2ad6"

// @ Краткое описание скрипта
//
// description: ""

// @ Признак автоматического запуска скрипта на сервере по расписанию или единожды
//
// isActiveJob": false

// @ Наименование используемого таймера для запуска по расписанию.
// @ Возможные варианты:
// @ "once" - единожды
// @ "custom" - произвольно
// @ "daily" - ежедневно
// @ "monthly" - ежемесячно
//
// jobType: "daily"

// @ Список контроля доступа (идентификаторы ролей или идентификаторы пользователей)
//
// ACL: []

// @ Время первого запуска скрипта по расписанию в формате RFC3339
//
// jobStartAt: "2017-07-03T15:30:19.751+03:00"

// @ ПАРАМЕТРЫ РАСПИСАНИЯ:

// @ Параметры запуска для произвольного периода выполнения. Где:
// @ days - количество дней, через которые выполнять скрипт
// @ hours - количество часов, через которые выполнять скрипт
// @ minutes - количество минут, через которые выполнять скрипт
//
// custom: {"days": 0, "hours": 0, "minutes": 0}

// @ Параметры запуска для ежедневного периода выполнения. Где:
// @ on - массив номеров дней от 1 до 7, в которые должен выполняться скрипт
// @ hours - час, в который должен быть выполнен скрипт
// @ minutes - минута часа, в который должен быть выполнен скрипт
//
// daily: {"on": [1],"hours": 1,"minutes": 0}

// @ Параметры запуска для ежемесячного периода выполнения. Где:
// @ on - массив месяцев от 1 до 12, в которые должен выполняться скрипт
// @ days - массив дней от 1 до 31, в которые должен выполняться скрипт
// @ hours - час, в который должен быть выполнен скрипт
// @ minutes - минута часа, в который должен быть выполнен скрипт
// @ lastDate - признак запуска в последний день месяца
//
// monthly: {"on": [],"days": [],"lastDate": false,"hours": 0,"minutes": 0}


MY CODE...
```

#### Создать скрипт

Создает новый скрипт в директории проекта и на https://scorocode.ru
Если каталог не существует, то будет создан новый (включая подкаталоги)

```bash
scorocode-cli script -cmd create \
                     -path <путь к файлу с расширением '.js' с/без упоминания каталога '/scripts'> \
                     [-dir <путь к каталогу, в котором находится файл scorocode.json. По умолчанию - это текущий каталог>]

# Example:
# 1.
scorocode-cli script -cmd create -path my_script.js
# или (идентично предыдущему)
scorocode-cli script -cmd create -path /scripts/my_script.js

# 2.
scorocode-cli script -cmd create -path /scripts/my_dir/my_script.js
# или (идентично предыдущему)
scorocode-cli script -cmd create -path /my_dir/my_script.js

```

#### Получить скрипт

Загружает скрипт или каталог со скриптами с https://scorocode.ru в каталог проекта

Алгоритм загрузки скрипта:
1. Если скрипт отсутствует, то в локальной директории проекта создаются новые файлы с данными скрипта
2. Если скрипт уже существует в локальной директории, то его данные перезаписываются данными полученными с сервера.

Алгоритм загрузки каталога:
1. Если каталог отсутствует, то в локальной директории проекта создаются новые подкаталоги и скрипты, на основании структуры файлов полученных с сервера.
2. Если каталог существует, то перед загрузкой данных с сервера в каталоге будут:
- удалены подкаталоги и скрипты, которые отсутствуют на сервере
- существующие скрипты обновлены данными с сервера
- созданы новые подкаталоги и скрипты на основе данных с сервера

```bash
scorocode-cli script -cmd pull \
                     -path <путь к файлу с расширением '.js' или каталогу с/без упоминания каталога '/scripts'> \
                     [-dir <путь к каталогу, в котором находится файл scorocode.json. По умолчанию - это текущий каталог>]
```

Для обновления всех скриптов в параметре **-path** указать значение **/**

#### Отправить скрипт

Сохраняет новую версию скрипта или каталога на https://scorocode.ru из локальных данных

Алгоритм отправки скрипта:
1. Если скрипт существует только в локальной директории проекта, в приложении на https://scorocode.ru будет создан новый скрипт
2. Если скрипт уже существует в приложении на https://scorocode.ru, то его данные перезаписываются данными полученными из локальный директории.

Алгоритм отправки каталога:
1. Если каталог существует только в локальной директории проекта, то в приложении на https://scorocode.ru будут созданы новые подкаталоги и скрипты.
2. Если каталог уже существует, то перед загрузкой данных из локальной директории проекта в каталог приложения на https://scorocode.ru будут:
- удалены подкаталоги и скрипты, которые отсутствуют в локальной версии
- существующие скрипты обновлены данными из локальной версии
- созданы новые подкаталоги и скрипты на основе данных из локальной версии


```bash
scorocode-cli script -cmd push \
                     -path <путь к файлу с расширением '.js' или каталогу с/без упоминания каталога '/scripts'> \
                     [-dir <путь к каталогу, в котором находится файл scorocode.json. По умолчанию - это текущий каталог>]
```

Для обновления всех скриптов в параметре **-path** указать значение **/**

#### Удалить скрипт

Удалить скрипт или каталог

```bash
scorocode-cli script -cmd delete \
                     -path <путь к файлу с расширением '.js' или каталогу с/без упоминания каталога '/scripts'> \
                     [-dir <путь к каталогу, в котором находится файл scorocode.json. По умолчанию - это текущий каталог>]
```

#### Запустить скрипт

Сохраняет новую версию скрипта на https://scorocode.ru из локальных данных и запускает на исполнение
в https://scorocode.ru

```bash
scorocode-cli script -cmd run \
                     -path <путь к файлу с расширением '.js' с/без упоминания каталога '/scripts'> \
                     [-pool <дополнительные параметры скрипта. Путь к файлу '.json' или строка в формате json>] \
                     [-dir <путь к каталогу, в котором находится файл scorocode.json. По умолчанию - это текущий каталог>]
```
