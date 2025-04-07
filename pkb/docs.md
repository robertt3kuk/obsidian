
# Инструкция по Сохранению Моков для PKB Bridge (pkb-pr.zaman.redmadrobot.com)

## Введение

Этот документ описывает, как сохранять мок-ответы для имитации сервиса `pkb-bridge`, используя мок-сервис `pkb-pr.zaman.redmadrobot.com`. Сохраненные ответы будут возвращаться клиентам (мобильным приложениям, QA-скриптам), когда они обращаются к соответствующим эндпоинтам мок-сервиса с правильным `user_id`.

## Определение Необходимых Моков

В зависимости от тестируемого функционала шлюза, вам может потребоваться сохранить моки для следующих методов `pkb-bridge`:

**1. Сервис `users` (инициатор: `ConfirmLogin`)**
   * [`SendJurSearchByIin`](#save-sendjursearchbyiin)
   * [`GetPersonalInfoByIin`](#save-getpersonalinfobyiin)
   * [`GetPermitDocumentsByIin`](#save-getpermitdocumentsbyiin)
   * [`CreateReport`](#save-createreport)
   * [`GetReportStatus`](#save-getreportstatus)
   * [`GetReport`](#save-getreport)

**2. Сервис `loans` (инициатор: `sendLoanApplicationDataToPkbAndGkb`)**
   * [`SendLoanApplicationInfo` (PKB/GKB)](#save-sendloanapplicationinfo)

**3. Внутренние проверки
   * `susnCheck` -> [`GetSusnStatus` (GetSusnSubject)](#save-getsusnstatus)
   * `militCheck` -> [`GetArmyStatus`](#save-getarmystatus)

## Общая Инструкция по Сохранению Мока (`/save`)

Для сохранения мока используйте `POST` запрос на эндпоинт `/save`.

* **URL:** `https://pkb-pr.zaman.redmadrobot.com/save`
* **Аутентификация:** Basic Auth (используйте логин и пароль, предоставленные для мок-сервиса).
* **Параметры Запроса (Query Parameters):**
    * `user_id` (Обязательный): Уникальный идентификатор (например, ID пользователя, название тестового сценария).
    * `endpoint` (Обязательный): Относительный путь реального эндпоинта PKB. **Внимание:** Если эндпоинт содержит параметры пути (например, `{iin}`), укажите *конкретное значение* (например, `/gbdflinfo/v1/clients/111222333444`). Query параметры (`?param=value`) в `endpoint` указывать *не нужно*.
    * `method` (Обязательный): HTTP метод (`GET`, `POST`).
    * `http_status_code` (Опциональный): HTTP статус ответа (по умолчанию `200`).
* **Тело Запроса:** JSON или XML данные, которые мок-сервис должен вернуть.

---

## Примеры Сохранения Моков для Конкретных Методов

### <a name="save-sendjursearchbyiin"></a>1. Сохранение Мока для `SendJurSearchByIin`

* **Endpoint:** `/jursearch/v1/external/send-request`
* **Method:** `POST`

```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-jursearch&endpoint=/jursearch/v1/external/send-request&method=POST&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-jursearch&endpoint=/jursearch/v1/external/send-request&method=POST&http_status_code=200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "requestNumber": "MOCK-AAIY-131131", "code": "OK", "message": "Mock Success (JurSearch)", "data": { "binIin": "050101500090", "name": "Mock ИП Қуанышұлы", "oked": { "code": "96090", "name": "Mock Предоставление прочих услуг" }, "krp": { "code": "105", "name": "Mock Малые предприятия" }, "kse": { "code": "1122", "name": "Mock Самостоятельно занятые" }, "katoCode": "434035100", "katoId": "260483", "katoAddress": "Mock Address", "fio": "Mock ФИО", "ip": "true", "krpBf": { "code": "105", "name": "Mock Малые предприятия" } } }'
````

### <a name="save-getpersonalinfobyiin"></a>2. Сохранение Мока для `GetPersonalInfoByIin`

- **Endpoint:** `/gbdflinfo/v1/clients/{iin}`
- **Method:** `GET`
- **Пример для IIN:** `111222333444`

Bash

```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-personalinfo&endpoint=/gbdflinfo/v1/clients/111222333444&method=GET&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-personalinfo&endpoint=/gbdflinfo/v1/clients/111222333444&method=GET&http_status_code=200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "code": "OK", "message": "Mock Success (PersonalInfo)", "data": { "request_id": "mock-17060", "status_code": "VALID", "person_data": { "iin": "111222333444", "surname": "MOCK_SURNAME", "name": "MOCK_NAME", "patronymic": "MOCK_PATRONYMIC", "dob": "1990-01-01", "gender": { "code": "1", "name_kz": "Ер", "name_ru": "Мужской" }, "nationality": { "code": "005", "name_kz": "ҚАЗАҚ", "name_ru": "КАЗАХ" } }, "person_photo_data": { "response": { "responseData": { "data": { "message_result": { "code": "00000", "name_ru": "Сообщение успешно обработано" } } } } } } }'
```

### <a name="save-getpermitdocumentsbyiin"></a>3. Сохранение Мока для `GetPermitDocumentsByIin`

- **Endpoint:** `/gbdel-universal/v1/external/send-request`
- **Method:** `POST`

Bash

```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-permitdocs&endpoint=/gbdel-universal/v1/external/send-request&method=POST&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-permitdocs&endpoint=/gbdel-universal/v1/external/send-request&method=POST&http_status_code=200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "code": "200", "message": "Mock OK (PermitDocs)", "data": { "response": { "responseData": { "data": { "reqNum": "MOCK-IHEI-805067", "licenses": { "taxpayerLicense": [ { "globalUniqueNumber": "MOCK-R16000289", "activityType": { "code": "33", "nameRu": "Mock Разрешение на радиочастоты" }, "licensiar": { "code": "D1", "nameRu": "Mock Комитет телекоммуникаций" }, "validityStartDate": "2020-01-01T10:00:00", "validityEndDate": "2025-01-01T10:00:00", "documentId": "mock-doc-id-123" } ] }, "request": { "iinBin": "sender_iin", "pageIndex": "1", "pageSize": "10" }, "systemInfo": { "messageId": "mock-msg-id", "responseCode": "00000" } } } } } }'
```

### <a name="save-createreport"></a>4. Сохранение Мока для `CreateReport`

- **Endpoint:** `/xdata/v1/v1/report/create`
- **Method:** `POST`

Bash

```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-createreport&endpoint=/xdata/v1/v1/report/create&method=POST&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-createreport&endpoint=/xdata/v1/v1/report/create&method=POST&http_status_code=200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "status": "OK", "report_id": "mock-report-uuid-12345", "ready_percentage": 0 }'
```

### <a name="save-getreportstatus"></a>5. Сохранение Мока для `GetReportStatus`

- **Endpoint:** `/xdata/v1/v1/report/status`
- **Method:** `GET`

Bash

```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-reportstatus&endpoint=/xdata/v1/v1/report/status&method=GET&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-reportstatus&endpoint=/xdata/v1/v1/report/status&method=GET&http_status_code=200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "status": "COMPLETED", "report_id": "mock-report-uuid-12345", "ready_percentage": 100 }'
```

### <a name="save-getreport"></a>6. Сохранение Мока для `GetReport`

- **Endpoint:** `/xdata/v1/v1/report/get`
- **Method:** `GET`

Bash

```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-getreport&endpoint=/xdata/v1/v1/report/get&method=GET&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-getreport&endpoint=/xdata/v1/v1/report/get&method=GET&http_status_code=200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "sources": [ { "name": { "ru": "Mock Источник 1", "kk": "Mock Дереккөз 1" }, "status": 1, "code": "MOCK01", "url": "[http://mock.url](http://mock.url)", "date_updated": "2025-01-01T12:00:00Z", "date_actual": "2025-01-01T12:00:00Z", "infos": [ { "ru": [ { "title": "Mock Title", "value": "Mock Value" } ] } ] } ], "reportID": "mock-report-uuid-12345" }'
```

### <a name="save-sendloanapplicationinfo"></a>7. Сохранение Мока для `SendLoanApplicationInfo`

- **Endpoint:** `/credit-app-v2/credit/loan`
- **Method:** `POST`

Bash

```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-sendloaninfo&endpoint=/credit-app-v2/credit/loan&method=POST&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-sendloaninfo&endpoint=/credit-app-v2/credit/loan&method=POST&http_status_code=200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "id": "mock-loan-app-id-678", "iin": "mock-iin-uuid-format", "loan_app_date": "2025-04-08 01:00:00+05", "loan_app_date_unix": 1744000800, "current_date": "2025-04-08 01:00:00+05", "current_date_unix": 1744000800 }'
```

### <a name="save-getsusnstatus"></a>8. Сохранение Мока для `GetSusnStatus` (GetSusnSubject)

- **Endpoint:** `/susn-status-v2/v1/subject`
- **Method:** `POST`

Bash

```bash
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

Bash

```bash
curl -X POST \
  '[https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-armystatus&endpoint=/recruit/v1/555666777888&method=GET&http_status_code=200](https://pkb-pr.zaman.redmadrobot.com/save?user_id=test-user-armystatus&endpoint=/recruit/v1/555666777888&method=GET&http_status_code=200)' \
  -u 'YOUR_USERNAME:YOUR_PASSWORD' \
  -H 'Content-Type: application/json' \
  -d '{ "iin": "555666777888", "status": true, "date": "2024-10-01" }'
```

---
