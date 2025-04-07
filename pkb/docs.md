# Инструкция по Сохранению Моков для PKB Bridge (pkb-pr.zaman.redmadrobot.com)

## Введение

Этот документ описывает, как сохранять мок-ответы для имитации сервиса `pkb-bridge`, используя мок-сервис `pkb-pr.zaman.redmadrobot.com`. Используйте `user_id`: **`e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c`** для всех примеров.

## Определение Необходимых Моков

В зависимости от тестируемого функционала шлюза, вам может потребоваться сохранить моки для следующих методов `pkb-bridge`:

**1. Сервис `users` (инициатор: `ConfirmLogin`)**
   * [`SendJurSearchByIin`](#save-sendjursearchbyiin) (Успешный / Ограничен)
   * [`GetPersonalInfoByIin`](#save-getpersonalinfobyiin)
   * [`GetPermitDocumentsByIin`](#save-getpermitdocumentsbyiin) (Успешный / Ограничен)
   * [`CreateReport`](#save-createreport)
   * [`GetReportStatus`](#save-getreportstatus)
   * [`GetReport`](#save-getreport) (Без долгов / С долгами)

**2. Сервис `loans` (инициатор: `sendLoanApplicationDataToPkbAndGkb`)**
   * [`SendLoanApplicationInfo` (PKB/GKB)](#save-sendloanapplicationinfo)

**3. Internal Check Chain**
   * `susnCheck` -> [`GetSusnStatus` (GetSusnSubject)](#save-getsusnstatus)
   * `militCheck` -> [`GetArmyStatus`](#save-getarmystatus)

## Общая Инструкция по Сохранению Мока (`/save`)

Для сохранения мока используйте `POST` запрос на эндпоинт `/save`.

* **URL:** `https://pkb-pr.zaman.redmadrobot.com/save` с обязательными Query параметрами:
    * `user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c`
    * `endpoint=/path/to/real/endpoint` (Путь реального эндпоинта PKB. Если путь содержит параметры, например `{iin}`, укажите *конкретное значение*: `/clients/111222333444`. Query параметры указывать *не нужно*.)
    * `method=METHOD` (`GET` или `POST`)
    * `http_status_code=200` (Опционально, по умолчанию `200`)
* **Аутентификация:** Basic Auth (`-u 'zaman:secret'`).
* **Тело Запроса (`-d '{...}'`):** JSON данные, которые мок-сервис должен вернуть.

**Примечание:** При сохранении нескольких сценариев (например, успешный и неуспешный) для одного и того же `user_id`, `endpoint` и `method`, **последний сохраненный мок перезапишет предыдущий**.

---

## Примеры Сохранения Моков

### <a name="save-sendjursearchbyiin"></a>1. `SendJurSearchByIin`

* **Endpoint:** `/jursearch/v1/external/send-request`
* **Method:** `POST`

**Успешный ответ (NotRestricted):**
```bash
curl '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/jursearch/v1/external/send-request&method=POST&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/jursearch/v1/external/send-request&method=POST&http_status_code=200)' \
  -u 'zaman:secret' \
  -d '{ "Iin": "111222333444", "OkedName": "Test Activity", "OkedCode": "NotRestricted" }'
````

**Ответ с ограничением (Restricted):**


```bash
curl '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/jursearch/v1/external/send-request&method=POST&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/jursearch/v1/external/send-request&method=POST&http_status_code=200)' \
  -u 'zaman:secret' \
  -d '{ "Iin": "111222333444", "OkedName": "Restricted Activity", "OkedCode": "Restricted" }'
```

### <a name="save-getpersonalinfobyiin"></a>2. `GetPersonalInfoByIin`

- **Endpoint:** `/gbdflinfo/v1/clients/{iin}` (Пример для IIN `111222333444`)
- **Method:** `GET`


```bash
curl '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/gbdflinfo/v1/clients/111222333444&method=GET&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/gbdflinfo/v1/clients/111222333444&method=GET&http_status_code=200)' \
  -u 'zaman:secret' \
  -d '{ "Iin": "111222333444", "Name": "Иван", "Surname": "Иванов", "Patronymic": "Иванович", "EngSurname": "Ivanov", "EngName": "Ivan", "Dob": "1990-01-01", "Gender": { "Code": "1", "NameKZ": "Ер", "NameRU": "Мужчина" }, "Nationality": { "Code": "2", "NameKZ": "Қазақ", "NameRU": "Казах" }, "Citizenship": { "Code": "3", "NameKZ": "Қазақстан", "NameRU": "Казахстан" }, "LifeStatus": { "Code": "4", "NameKZ": "Тірі", "NameRU": "Жив" }, "BirthPlace": { "City": "Алматы", "Country": { "Code": "3", "NameKZ": "Қазақстан", "NameRU": "Казахстан" }, "District": { "Code": "5", "NameKZ": "Бостандық", "NameRU": "Бостандыкский" }, "Region": { "Code": "6", "NameKZ": "Алматы", "NameRU": "Алматы" } }, "Address": { "Street": "Абая", "Building": "10", "Flat": "25", "BeginDate": "2010-05-15", "Country": { "Code": "3", "NameKZ": "Қазақстан", "NameRU": "Казахстан" }, "District": { "Code": "5", "NameKZ": "Бостандық", "NameRU": "Бостандыкский" }, "Region": { "Code": "6", "NameKZ": "Алматы", "NameRU": "Алматы" } }, "AddressTemp": [ { "Type": { "Code": "1", "NameKZ": "Уақытша", "NameRU": "Временный" }, "City": "Нур-Султан", "Street": "Кунаева", "Building": "20", "Flat": "5", "BeginDate": "2022-01-01", "EndDate": "2023-01-01" } ], "Documents": [ { "Number": "123456789", "BeginDate": "2015-06-01", "EndDate": "2025-06-01", "Surname": "Иванов", "Name": "Иван", "Patronymic": "Иванович", "BirthDate": "1990-01-01", "Type": { "Code": "10", "NameKZ": "ЖСН", "NameRU": "Паспорт" }, "IssueOrg": { "Code": "20", "NameKZ": "ІІМ", "NameRU": "МВД" }, "Status": { "Code": "30", "NameKZ": "Жарамды", "NameRU": "Действителен" } } ], "PersonPhotoDates": [ { "Iin": "111222333444", "CodeTypeDock": "10", "NumDock": "123456789", "Photo": "base64encodedstring" } ] }'
```

### <a name="save-getpermitdocumentsbyiin"></a>3. `GetPermitDocumentsByIin`

- **Endpoint:** `/gbdel-universal/v1/external/send-request`
- **Method:** `POST`

**Успешный ответ (NotRestricted):**

Bash

```
curl '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/gbdel-universal/v1/external/send-request&method=POST&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/gbdel-universal/v1/external/send-request&method=POST&http_status_code=200)' \
  -u 'zaman:secret' \
  -d '{ "Iin": "111222333444", "ActivityTypes": [ { "Code": "NotRestricted", "NameRu": "Деятельность разрешена", "NameKz": "Қызметке рұқсат етілген" } ] }'
```

**Ответ с ограничением (Restricted):**


```bash
curl '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/gbdel-universal/v1/external/send-request&method=POST&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/gbdel-universal/v1/external/send-request&method=POST&http_status_code=200)' \
  -u 'zaman:secret' \
  -d '{ "Iin": "111222333444", "ActivityTypes": [ { "Code": "Restricted", "NameRu": "Деятельность ограничена", "NameKz": "Қызмет шектелген" } ] }'
```

### <a name="save-createreport"></a>4. `CreateReport`

- **Endpoint:** `/xdata/v1/v1/report/create`
- **Method:** `POST`


```bash
curl '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/xdata/v1/v1/report/create&method=POST&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/xdata/v1/v1/report/create&method=POST&http_status_code=200)' \
  -u 'zaman:secret' \
  -d '{ "ReportID": "mock-report-id-123", "ReadyPercentage": 80, "Status": "OK" }'
```

### <a name="save-getreportstatus"></a>5. `GetReportStatus`

- **Endpoint:** `/xdata/v1/v1/report/status`
- **Method:** `GET`


```bash
curl '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/xdata/v1/v1/report/status&method=GET&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/xdata/v1/v1/report/status&method=GET&http_status_code=200)' \
  -u 'zaman:secret' \
  -d '{ "ReportID": "mock-report-id-123", "ReadyPercentage": 100, "Status": "OK" }'
```

### <a name="save-getreport"></a>6. `GetReport`

- **Endpoint:** `/xdata/v1/v1/report/get`
- **Method:** `GET`

**Пример: Ответ без долгов (KGD22 Value=0)**


```bash
curl '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/xdata/v1/v1/report/get&method=GET&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/xdata/v1/v1/report/get&method=GET&http_status_code=200)' \
  -u 'zaman:secret' \
  -d '{ "ReportId": "mock-report-id-456", "Sources": [ { "Code": "KGD29" }, { "Code": "KGD22", "Status": 1, "Infos": [ { "DetailsKk": [ { "Title": "Барлық берешек, оның ішінде (теңге)", "Value": "0" } ], "DetailsRu": [ { "Title": "Всего задолженности (тенге)", "Value": "0" } ] } ] } ] }'
```

**Пример: Ответ с долгами (KGD22 Value=123456)**


```bash
curl '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/xdata/v1/v1/report/get&method=GET&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/xdata/v1/v1/report/get&method=GET&http_status_code=200)' \
  -u 'zaman:secret' \
  -d '{ "ReportId": "mock-report-id-789", "Sources": [ { "Code": "KGD22", "Status": 1, "Infos": [ { "DetailsKk": [ { "Title": "Барлық берешек, оның ішінде (теңге)", "Value": "123456" } ], "DetailsRu": [ { "Title": "Всего задолженности (тенге)", "Value": "123456" } ] } ] } ] }'
```

### <a name="save-sendloanapplicationinfo"></a>7. `SendLoanApplicationInfo`

- **Endpoint:** `/credit-app-v2/credit/loan`
- **Method:** `POST`


```bash
curl '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/credit-app-v2/credit/loan&method=POST&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/credit-app-v2/credit/loan&method=POST&http_status_code=200)' \
  -u 'zaman:secret' \
  -d '{ "id": "mock-loan-app-id-678", "iin": "mock-iin-uuid-format", "loan_app_date": "2025-04-08 01:00:00+05", "loan_app_date_unix": 1744000800, "current_date": "2025-04-08 01:00:00+05", "current_date_unix": 1744000800 }'
```

### <a name="save-getsusnstatus"></a>8. `GetSusnStatus` (GetSusnSubject)

- **Endpoint:** `/susn-status-v2/v1/subject`
- **Method:** `POST`


```bash
curl '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/susn-status-v2/v1/subject&method=POST&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/susn-status-v2/v1/subject&method=POST&http_status_code=200)' \
  -u 'zaman:secret' \
  -d '{ "result": { "birthDate": "1985-05-10", "firstName": "MOCK", "iin": "sender_iin", "secondName": "MOCK", "surname": "MOCK", "signature": { "signatureValue": "mock_signature" }, "status": [ { "statusCode": 1, "statusNameRu": "Mock Статус Активен", "statusDstart": "2020-01-01", "statusDend": "2025-12-31" } ] } }'
```

### <a name="save-getarmystatus"></a>9. `GetArmyStatus`

- **Endpoint:** `/recruit/v1/{iin}` (Пример для IIN `555666777888`)
- **Method:** `GET`


```bash
curl 'https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/recruit/v1/555666777888&method=GET&http_status_code=200' \
  -u 'zaman:secret' \
  -d '{ "iin": "555666777888", "status": true, "date": "2024-10-01" }'
```
