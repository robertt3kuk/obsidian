

# Инструкция по Сохранению Моков для PKB Bridge (pkb-pr.zaman.redmadrobot.com)

## Введение

Этот документ описывает, как сохранять мок-ответы для имитации сервиса `pkb-bridge`, используя мок-сервис `pkb-pr.zaman.redmadrobot.com`. Сохраненные ответы будут возвращаться клиентам (мобильным приложениям, QA-скриптам), когда они обращаются к соответствующим эндпоинтам мок-сервиса с правильным `user_id`.

## Определение Необходимых Моков

В зависимости от тестируемого функционала шлюза, вам может потребоваться сохранить моки для следующих методов `pkb-bridge`:

**1. Сервис `users` (инициатор: `ConfirmLogin`)**
   * [`SendJurSearchByIin`](#save-sendjursearchbyiin) (Успешный / Неуспешный - Ограничен)
   * [`GetPersonalInfoByIin`](#save-getpersonalinfobyiin)
   * [`GetPermitDocumentsByIin`](#save-getpermitdocumentsbyiin) (Успешный / Неуспешный - Ограничен)
   * [`CreateReport`](#save-createreport)
   * [`GetReportStatus`](#save-getreportstatus)
   * [`GetReport`](#save-getreport) (Пример без долгов / Пример с долгами)

**2. Сервис `loans` (инициатор: `sendLoanApplicationDataToPkbAndGkb`)**
   * [`SendLoanApplicationInfo` (PKB/GKB)](#save-sendloanapplicationinfo)

**3. Internal Check Chain**
   * `susnCheck` -> [`GetSusnStatus` (GetSusnSubject)](#save-getsusnstatus)
   * `militCheck` -> [`GetArmyStatus`](#save-getarmystatus)

## Общая Инструкция по Сохранению Мока (`/save`)

Для сохранения мока используйте `POST` запрос на эндпоинт `/save`.

* **URL:** `https://pkb-pr.zaman.redmadrobot.com/save`
* **Аутентификация:** Basic Auth (используйте логин и пароль, предоставленные для мок-сервиса).
* **Параметры Запроса (Query Parameters):**
    * `user_id` (Обязательный): Уникальный идентификатор (например, `test_user_scenario1`, `user1_kgd22_debts`).
    * `endpoint` (Обязательный): Относительный путь реального эндпоинта PKB. **Внимание:** Если эндпоинт содержит параметры пути (например, `{iin}`), укажите *конкретное значение* (например, `/gbdflinfo/v1/clients/111222333444`). Query параметры (`?param=value`) в `endpoint` указывать *не нужно*.
    * `method` (Обязательный): HTTP метод (`GET`, `POST`).
    * `http_status_code` (Опциональный): HTTP статус ответа (по умолчанию `200`).
* **Тело Запроса:** JSON или XML данные, которые мок-сервис должен вернуть.

---

## Примеры Сохранения Моков для Конкретных Методов

### <a name="save-sendjursearchbyiin"></a>1. Сохранение Мока для `SendJurSearchByIin`

* **Endpoint:** `/jursearch/v1/external/send-request`
* **Method:** `POST`

**Пример: Успешный ответ (NotRestricted)**

```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=jursearch_success&endpoint=/jursearch/v1/external/send-request&method=POST&http_status_code=200](https://www.google.com/search?q=https://pkb-pr.zaman.redmadrobot.com/save%3Fuser_id%3Djursearch_success%26endpoint%3D/jursearch/v1/external/send-request%26method%3DPOST%26http_status_code%3D200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "Iin": "111222333444", "OkedName": "Test Activity", "OkedCode": "NotRestricted" }'
````

**Пример: Неуспешный ответ (Restricted)**

Bash

```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=jursearch_restricted&endpoint=/jursearch/v1/external/send-request&method=POST&http_status_code=200](https://www.google.com/search?q=https://pkb-pr.zaman.redmadrobot.com/save%3Fuser_id%3Djursearch_restricted%26endpoint%3D/jursearch/v1/external/send-request%26method%3DPOST%26http_status_code%3D200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "Iin": "111222333444", "OkedName": "Restricted Activity", "OkedCode": "Restricted" }'
```

### <a name="save-getpersonalinfobyiin"></a>2. Сохранение Мока для `GetPersonalInfoByIin`

- **Endpoint:** `/gbdflinfo/v1/clients/{iin}`
- **Method:** `GET`
- **Пример для IIN:** `111222333444`

Bash

```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=personalinfo_111222333444&endpoint=/gbdflinfo/v1/clients/111222333444&method=GET&http_status_code=200](https://www.google.com/search?q=https://pkb-pr.zaman.redmadrobot.com/save%3Fuser_id%3Dpersonalinfo_111222333444%26endpoint%3D/gbdflinfo/v1/clients/111222333444%26method%3DGET%26http_status_code%3D200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "Iin": "111222333444", "Name": "Иван", "Surname": "Иванов", "Patronymic": "Иванович", "EngSurname": "Ivanov", "EngName": "Ivan", "Dob": "1990-01-01", "Gender": { "Code": "1", "NameKZ": "Ер", "NameRU": "Мужчина" }, "Nationality": { "Code": "2", "NameKZ": "Қазақ", "NameRU": "Казах" }, "Citizenship": { "Code": "3", "NameKZ": "Қазақстан", "NameRU": "Казахстан" }, "LifeStatus": { "Code": "4", "NameKZ": "Тірі", "NameRU": "Жив" }, "BirthPlace": { "City": "Алматы", "Country": { "Code": "3", "NameKZ": "Қазақстан", "NameRU": "Казахстан" }, "District": { "Code": "5", "NameKZ": "Бостандық", "NameRU": "Бостандыкский" }, "Region": { "Code": "6", "NameKZ": "Алматы", "NameRU": "Алматы" } }, "Address": { "Street": "Абая", "Building": "10", "Flat": "25", "BeginDate": "2010-05-15", "Country": { "Code": "3", "NameKZ": "Қазақстан", "NameRU": "Казахстан" }, "District": { "Code": "5", "NameKZ": "Бостандық", "NameRU": "Бостандыкский" }, "Region": { "Code": "6", "NameKZ": "Алматы", "NameRU": "Алматы" } }, "AddressTemp": [ { "Type": { "Code": "1", "NameKZ": "Уақытша", "NameRU": "Временный" }, "City": "Нур-Султан", "Street": "Кунаева", "Building": "20", "Flat": "5", "BeginDate": "2022-01-01", "EndDate": "2023-01-01" } ], "Documents": [ { "Number": "123456789", "BeginDate": "2015-06-01", "EndDate": "2025-06-01", "Surname": "Иванов", "Name": "Иван", "Patronymic": "Иванович", "BirthDate": "1990-01-01", "Type": { "Code": "10", "NameKZ": "ЖСН", "NameRU": "Паспорт" }, "IssueOrg": { "Code": "20", "NameKZ": "ІІМ", "NameRU": "МВД" }, "Status": { "Code": "30", "NameKZ": "Жарамды", "NameRU": "Действителен" } } ], "PersonPhotoDates": [ { "Iin": "111222333444", "CodeTypeDock": "10", "NumDock": "123456789", "Photo": "base64encodedstring" } ] }'
```

### <a name="save-getpermitdocumentsbyiin"></a>3. Сохранение Мока для `GetPermitDocumentsByIin`

- **Endpoint:** `/gbdel-universal/v1/external/send-request`
- **Method:** `POST`

**Пример: Успешный ответ (NotRestricted)**

Bash

```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=permitdocs_success&endpoint=/gbdel-universal/v1/external/send-request&method=POST&http_status_code=200](https://www.google.com/search?q=https://pkb-pr.zaman.redmadrobot.com/save%3Fuser_id%3Dpermitdocs_success%26endpoint%3D/gbdel-universal/v1/external/send-request%26method%3DPOST%26http_status_code%3D200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "Iin": "111222333444", "ActivityTypes": [ { "Code": "NotRestricted", "NameRu": "Деятельность разрешена", "NameKz": "Қызметке рұқсат етілген" } ] }'
```

**Пример: Неуспешный ответ (Restricted)**

Bash

```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=permitdocs_restricted&endpoint=/gbdel-universal/v1/external/send-request&method=POST&http_status_code=200](https://www.google.com/search?q=https://pkb-pr.zaman.redmadrobot.com/save%3Fuser_id%3Dpermitdocs_restricted%26endpoint%3D/gbdel-universal/v1/external/send-request%26method%3DPOST%26http_status_code%3D200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "Iin": "111222333444", "ActivityTypes": [ { "Code": "Restricted", "NameRu": "Деятельность ограничена", "NameKz": "Қызмет шектелген" } ] }'
```

### <a name="save-createreport"></a>4. Сохранение Мока для `CreateReport`

- **Endpoint:** `/xdata/v1/v1/report/create`
- **Method:** `POST`


```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=createreport_success&endpoint=/xdata/v1/v1/report/create&method=POST&http_status_code=200](https://www.google.com/search?q=https://pkb-pr.zaman.redmadrobot.com/save%3Fuser_id%3Dcreatereport_success%26endpoint%3D/xdata/v1/v1/report/create%26method%3DPOST%26http_status_code%3D200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "ReportID": "mock-report-id-123", "ReadyPercentage": 80, "Status": "OK" }'
```

### <a name="save-getreportstatus"></a>5. Сохранение Мока для `GetReportStatus`

- **Endpoint:** `/xdata/v1/v1/report/status`
- **Method:** `GET`


```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=reportstatus_completed&endpoint=/xdata/v1/v1/report/status&method=GET&http_status_code=200](https://www.google.com/search?q=https://pkb-pr.zaman.redmadrobot.com/save%3Fuser_id%3Dreportstatus_completed%26endpoint%3D/xdata/v1/v1/report/status%26method%3DGET%26http_status_code%3D200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "ReportID": "mock-report-id-123", "ReadyPercentage": 100, "Status": "OK" }'
```

### <a name="save-getreport"></a>6. Сохранение Мока для `GetReport`

- **Endpoint:** `/xdata/v1/v1/report/get`
- **Method:** `GET`

**Пример: Ответ с мобильным источником (KGD22) без долгов**


```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=getreport_mobile_no_debt&endpoint=/xdata/v1/v1/report/get&method=GET&http_status_code=200](https://www.google.com/search?q=https://pkb-pr.zaman.redmadrobot.com/save%3Fuser_id%3Dgetreport_mobile_no_debt%26endpoint%3D/xdata/v1/v1/report/get%26method%3DGET%26http_status_code%3D200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "ReportId": "mock-report-id-456", "Sources": [ { "Code": "KGD29" }, { "Code": "KGD22", "Status": 1, "Infos": [ { "DetailsKk": [ { "Title": "Барлық берешек, оның ішінде (теңге)", "Value": "0" } ], "DetailsRu": [ { "Title": "Всего задолженности (тенге)", "Value": "0" } ] } ] } ] }'
```

**Пример: Ответ с источником KGD22 с долгами**


```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=getreport_kgd22_with_debt&endpoint=/xdata/v1/v1/report/get&method=GET&http_status_code=200](https://www.google.com/search?q=https://pkb-pr.zaman.redmadrobot.com/save%3Fuser_id%3Dgetreport_kgd22_with_debt%26endpoint%3D/xdata/v1/v1/report/get%26method%3DGET%26http_status_code%3D200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "ReportId": "mock-report-id-789", "Sources": [ { "Code": "KGD22", "Status": 1, "Infos": [ { "DetailsKk": [ { "Title": "Барлық берешек, оның ішінде (теңге)", "Value": "123456" } ], "DetailsRu": [ { "Title": "Всего задолженности (тенге)", "Value": "123456" } ] } ] } ] }'
```

### <a name="save-sendloanapplicationinfo"></a>7. Сохранение Мока для `SendLoanApplicationInfo`

- **Endpoint:** `/credit-app-v2/credit/loan`
- **Method:** `POST`


```bash
# Используем пример из предыдущего ответа, т.к. новая структура не предоставлена
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-sendloaninfo&endpoint=/credit-app-v2/credit/loan&method=POST&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-sendloaninfo&endpoint=/credit-app-v2/credit/loan&method=POST&http_status_code=200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "id": "mock-loan-app-id-678", "iin": "mock-iin-uuid-format", "loan_app_date": "2025-04-08 01:00:00+05", "loan_app_date_unix": 1744000800, "current_date": "2025-04-08 01:00:00+05", "current_date_unix": 1744000800 }'
```

### <a name="save-getsusnstatus"></a>8. Сохранение Мока для `GetSusnStatus` (GetSusnSubject)

- **Endpoint:** `/susn-status-v2/v1/subject`
- **Method:** `POST`


```bash
# Используем пример из предыдущего ответа, т.к. новая структура не предоставлена
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-susnstatus&endpoint=/susn-status-v2/v1/subject&method=POST&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-susnstatus&endpoint=/susn-status-v2/v1/subject&method=POST&http_status_code=200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "result": { "birthDate": "1985-05-10", "firstName": "MOCK", "iin": "sender_iin", "secondName": "MOCK", "surname": "MOCK", "signature": { "signatureValue": "mock_signature" }, "status": [ { "statusCode": 1, "statusNameRu": "Mock Статус Активен", "statusDstart": "2020-01-01", "statusDend": "2025-12-31" } ] } }'
```

### <a name="save-getarmystatus"></a>9. Сохранение Мока для `GetArmyStatus`

- **Endpoint:** `/recruit/v1/{iin}`
- **Method:** `GET`
- **Пример для IIN:** `555666777888`


```bash
# Используем пример из предыдущего ответа, т.к. новая структура не предоставлена
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=armystatus_555666777888&endpoint=/recruit/v1/555666777888&method=GET&http_status_code=200](https://www.google.com/search?q=https://pkb-pr.zaman.redmadrobot.com/save%3Fuser_id%3Darmystatus_555666777888%26endpoint%3D/recruit/v1/555666777888%26method%3DGET%26http_status_code%3D200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "iin": "555666777888", "status": true, "date": "2024-10-01" }'
```
