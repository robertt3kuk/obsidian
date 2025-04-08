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
   *  [`SendLoanApplicationInfoPKB`](#save-sendloanapplicationinfoPKB)
   *  [`SendLoanApplicationInfoGKB`](#save-sendloanapplicationinfoGKB)


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
* **Аутентификация:** Basic Auth (`-u 'admin:secret'`).
* **Тело Запроса (`-d '{...}'`):** JSON данные или любый другие XML, ZIP , которые мок-сервис должен вернуть.

**Примечание:** При сохранении нескольких сценариев (например, успешный и неуспешный) для одного и того же `user_id`, `endpoint` и `method`, **последний сохраненный мок перезапишет предыдущий**.

**Моковые данные**: более точный моковые данные для разных сценариев смотреть в документации


---

## Примеры Сохранения Моков

### <a name="save-sendjursearchbyiin"></a>1. `SendJurSearchByIin`

* **Endpoint:** `/jursearch/v1/external/send-request`
* **Method:** `POST`
* StatusCode: 200

**Успешный ответ (NotRestricted):**

**curl команда:**
```bash
curl 'https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/jursearch/v1/external/send-request&method=POST&http_status_code=200' \
  -u 'admin:secret' \
  -d '{
  "requestNumber": "AAIY-131131",
  "code": "OK",
  "message": "Success",
  "data": {
    "binIin": "050101500090",
    "name": "ИП Қуанышұлы",
    "oked": {
      "code": "96090",
      "name": "Предоставление прочих индивидуальных услуг, не  включенных в другие группировки"
    },
    "krp": {
      "code": "105",
      "name": "Малые предприятия (<= 5)"
    },
    "kse": {
      "code": "1122",
      "name": "Самостоятельно занятые лица"
    },
    "katoCode": "434035100",
    "katoId": 260483,
    "katoAddress": "КЫЗЫЛОРДИНСКАЯ ОБЛАСТЬ, ЖАНАКОРГАНСКИЙ РАЙОН, АККУИКСКИЙ С.О., С.БИРЛИК",
    "fio": "ҚУАНЫШҰЛЫ ӘДІЛБЕК",
    "ip": true,
    "kfs": {},
    "krpBf": {
      "name": "Малые предприятия (<= 5)"
    }
  }
}'
```

**JSON тело:**
```json
{
  "requestNumber": "AAIY-131131",
  "code": "OK",
  "message": "Success",
  "data": {
    "binIin": "050101500090",
    "name": "ИП Қуанышұлы",
    "oked": {
      "code": "96090",
      "name": "Предоставление прочих индивидуальных услуг, не  включенных в другие группировки"
    },
    "krp": {
      "code": "105",
      "name": "Малые предприятия (<= 5)"
    },
    "kse": {
      "code": "1122",
      "name": "Самостоятельно занятые лица"
    },
    "katoCode": "434035100",
    "katoId": 260483,
    "katoAddress": "КЫЗЫЛОРДИНСКАЯ ОБЛАСТЬ, ЖАНАКОРГАНСКИЙ РАЙОН, АККУИКСКИЙ С.О., С.БИРЛИК",
    "fio": "ҚУАНЫШҰЛЫ ӘДІЛБЕК",
    "ip": true,
    "kfs": {},
    "krpBf": {
      "name": "Малые предприятия (<= 5)"
    }
  }
}
```

### <a name="save-getpersonalinfobyiin"></a>2. `GetPersonalInfoByIin`

- **Endpoint:** `/gbdflinfo/v1/clients/{iin}` (Пример для IIN `111222333444`)
- **Method:** `GET`
- StatusCode: 200

**curl команда:**
```bash
curl 'https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/gbdflinfo/v1/clients/111222333444&method=GET&http_status_code=200' \
  -u 'admin:secret' \
  -d '{
  "code": "OK",
  "message": "Success",
  "data": {
    "request_id": "588525",
    "status_code": "VALID",
    "person_data": {
      "iin": "300201301013",
      "surname": "ИЩЕНКО",
      "engSurname": "",
      "name": "КОНСТАНТИН",
      "engFirstName": "",
      "patronymic": "ФЕДОРОВИЧ",
      "dob": "1930-02-01",
      "gender": {
        "code": "1",
        "name_kz": "Ер",
        "name_ru": "Мужской",
        "change_date": "2013-09-19T09:44:33+05:00"
      },
      "nationality": {
        "code": "005",
        "name_kz": "ҚАЗАҚ",
        "name_ru": "КАЗАХ",
        "change_date": "2013-09-19T09:45:37+05:00"
      },
      "citizenship": {
        "code": "398",
        "name_kz": "ҚАЗАҚСТАН",
        "name_ru": "КАЗАХСТАН",
        "change_date": "2013-09-19T09:45:48+05:00"
      },
      "life_status": {
        "code": "2",
        "name_kz": "Қайтыс болған",
        "name_ru": "Умерший",
        "change_date": "2013-09-19T09:45:30+05:00"
      },
      "birth_place": {
        "city": "ЛЕСНОЕ",
        "country": {
          "code": "398",
          "name_kz": "ҚАЗАҚСТАН",
          "name_ru": "КАЗАХСТАН",
          "change_date": "2013-09-19T09:45:48+05:00"
        },
        "district": {
          "code": "1917",
          "name_kz": "ШЫҒ-ҚАЗАҚСТАН",
          "name_ru": "В-КАЗАХСТАНСКАЯ",
          "change_date": "2013-09-19T09:45:41+05:00"
        },
        "region": {
          "code": "1917215",
          "name_kz": "ЗЫРЯНОВ АУДАНЫ",
          "name_ru": "ЗЫРЯНОВСКИЙ РАЙОН",
          "change_date": "2019-07-25T10:20:48+05:00"
        }
      },
      "address": {
        "street": "УЛИЦА Добролюбова",
        "building": "49",
        "flat": "",
        "beginDate": "2017-02-09",
        "country": {
          "code": "398",
          "name_kz": "ҚАЗАҚСТАН",
          "name_ru": "КАЗАХСТАН",
          "change_date": "2013-09-19T09:45:48+05:00"
        },
        "district": {
          "code": "1910",
          "name_kz": "АЛМАТЫ",
          "name_ru": "АЛМАТЫ",
          "change_date": "2013-09-19T09:45:41+05:00"
        },
        "region": {
          "code": "1910278",
          "name_kz": "МЕДЕУ",
          "name_ru": "МЕДЕУСКИЙ",
          "change_date": "2013-09-19T09:45:21+05:00"
        }
      },
      "addressTemp": {
        "Address": null
      },
      "documents": {
        "document": [
          {
            "number": "N1679922",
            "begin_date": "1999-02-24",
            "end_date": "2020-02-01",
            "surname": "ИЩЕНКО",
            "name": "КОНСТАНТИН",
            "patronymic": "ФЕДОРОВИЧ",
            "birth_date": "1930-02-01",
            "type": {
              "code": "001",
              "name_kz": "ҚР ПАСПОРТЫ",
              "name_ru": "ПАСПОРТ РК",
              "change_date": "2013-09-19T09:45:40+05:00"
            },
            "issue_organization": {
              "code": "002",
              "name_kz": "ҚР ІШКІ ІСТЕР МИНИСТРЛІГІ",
              "name_ru": "МИНИСТЕРСТВО ВНУТРЕННИХ ДЕЛ РК",
              "change_date": "2013-09-19T09:45:40+05:00"
            },
            "status": {
              "code": "11",
              "name_kz": "АЗАМАТТЫҢ ҚАЙТЫС БОЛУЫНА БАЙЛАНЫСТЫ",
              "name_ru": "В СВЯЗИ СО СМЕРТЬЮ ГРАЖДАНИНА",
              "change_date": "2013-09-19T09:45:41+05:00"
            }
          },
          {
            "number": "010034032",
            "begin_date": "1999-02-23",
            "end_date": "2020-02-01",
            "surname": "ИЩЕНКО",
            "name": "КОНСТАНТИН",
            "patronymic": "ФЕДОРОВИЧ",
            "birth_date": "1930-02-01",
            "type": {
              "code": "002",
              "name_kz": "ҚР ЖЕКЕ КУӘЛІГІ",
              "name_ru": "УДОСТОВЕРЕНИЕ РК",
              "change_date": "2013-09-19T09:45:40+05:00"
            },
            "issue_organization": {
              "code": "002",
              "name_kz": "ҚР ІШКІ ІСТЕР МИНИСТРЛІГІ",
              "name_ru": "МИНИСТЕРСТВО ВНУТРЕННИХ ДЕЛ РК",
              "change_date": "2013-09-19T09:45:40+05:00"
            },
            "status": {
              "code": "11",
              "name_kz": "АЗАМАТТЫҢ ҚАЙТЫС БОЛУЫНА БАЙЛАНЫСТЫ",
              "name_ru": "В СВЯЗИ СО СМЕРТЬЮ ГРАЖДАНИНА",
              "change_date": "2013-09-19T09:45:41+05:00"
            }
          }
        ]
      }
    },
    "person_photo_data": {
      "response": {
        "responseData": {
          "data": {
            "message_result": {
              "code": "",
              "name_ru": "",
              "name_kz": ""
            },
            "person_list": {}
          }
        }
      }
    }
  }
}'
```

**JSON тело:**
```json
{
  "code": "OK",
  "message": "Success",
  "data": {
    "request_id": "588525",
    "status_code": "VALID",
    "person_data": {
      "iin": "300201301013",
      "surname": "ИЩЕНКО",
      "engSurname": "",
      "name": "КОНСТАНТИН",
      "engFirstName": "",
      "patronymic": "ФЕДОРОВИЧ",
      "dob": "1930-02-01",
      "gender": {
        "code": "1",
        "name_kz": "Ер",
        "name_ru": "Мужской",
        "change_date": "2013-09-19T09:44:33+05:00"
      },
      "nationality": {
        "code": "005",
        "name_kz": "ҚАЗАҚ",
        "name_ru": "КАЗАХ",
        "change_date": "2013-09-19T09:45:37+05:00"
      },
      "citizenship": {
        "code": "398",
        "name_kz": "ҚАЗАҚСТАН",
        "name_ru": "КАЗАХСТАН",
        "change_date": "2013-09-19T09:45:48+05:00"
      },
      "life_status": {
        "code": "2",
        "name_kz": "Қайтыс болған",
        "name_ru": "Умерший",
        "change_date": "2013-09-19T09:45:30+05:00"
      },
      "birth_place": {
        "city": "ЛЕСНОЕ",
        "country": {
          "code": "398",
          "name_kz": "ҚАЗАҚСТАН",
          "name_ru": "КАЗАХСТАН",
          "change_date": "2013-09-19T09:45:48+05:00"
        },
        "district": {
          "code": "1917",
          "name_kz": "ШЫҒ-ҚАЗАҚСТАН",
          "name_ru": "В-КАЗАХСТАНСКАЯ",
          "change_date": "2013-09-19T09:45:41+05:00"
        },
        "region": {
          "code": "1917215",
          "name_kz": "ЗЫРЯНОВ АУДАНЫ",
          "name_ru": "ЗЫРЯНОВСКИЙ РАЙОН",
          "change_date": "2019-07-25T10:20:48+05:00"
        }
      },
      "address": {
        "street": "УЛИЦА Добролюбова",
        "building": "49",
        "flat": "",
        "beginDate": "2017-02-09",
        "country": {
          "code": "398",
          "name_kz": "ҚАЗАҚСТАН",
          "name_ru": "КАЗАХСТАН",
          "change_date": "2013-09-19T09:45:48+05:00"
        },
        "district": {
          "code": "1910",
          "name_kz": "АЛМАТЫ",
          "name_ru": "АЛМАТЫ",
          "change_date": "2013-09-19T09:45:41+05:00"
        },
        "region": {
          "code": "1910278",
          "name_kz": "МЕДЕУ",
          "name_ru": "МЕДЕУСКИЙ",
          "change_date": "2013-09-19T09:45:21+05:00"
        }
      },
      "addressTemp": {
        "Address": null
      },
      "documents": {
        "document": [
          {
            "number": "N1679922",
            "begin_date": "1999-02-24",
            "end_date": "2020-02-01",
            "surname": "ИЩЕНКО",
            "name": "КОНСТАНТИН",
            "patronymic": "ФЕДОРОВИЧ",
            "birth_date": "1930-02-01",
            "type": {
              "code": "001",
              "name_kz": "ҚР ПАСПОРТЫ",
              "name_ru": "ПАСПОРТ РК",
              "change_date": "2013-09-19T09:45:40+05:00"
            },
            "issue_organization": {
              "code": "002",
              "name_kz": "ҚР ІШКІ ІСТЕР МИНИСТРЛІГІ",
              "name_ru": "МИНИСТЕРСТВО ВНУТРЕННИХ ДЕЛ РК",
              "change_date": "2013-09-19T09:45:40+05:00"
            },
            "status": {
              "code": "11",
              "name_kz": "АЗАМАТТЫҢ ҚАЙТЫС БОЛУЫНА БАЙЛАНЫСТЫ",
              "name_ru": "В СВЯЗИ СО СМЕРТЬЮ ГРАЖДАНИНА",
              "change_date": "2013-09-19T09:45:41+05:00"
            }
          },
          {
            "number": "010034032",
            "begin_date": "1999-02-23",
            "end_date": "2020-02-01",
            "surname": "ИЩЕНКО",
            "name": "КОНСТАНТИН",
            "patronymic": "ФЕДОРОВИЧ",
            "birth_date": "1930-02-01",
            "type": {
              "code": "002",
              "name_kz": "ҚР ЖЕКЕ КУӘЛІГІ",
              "name_ru": "УДОСТОВЕРЕНИЕ РК",
              "change_date": "2013-09-19T09:45:40+05:00"
            },
            "issue_organization": {
              "code": "002",
              "name_kz": "ҚР ІШКІ ІСТЕР МИНИСТРЛІГІ",
              "name_ru": "МИНИСТЕРСТВО ВНУТРЕННИХ ДЕЛ РК",
              "change_date": "2013-09-19T09:45:40+05:00"
            },
            "status": {
              "code": "11",
              "name_kz": "АЗАМАТТЫҢ ҚАЙТЫС БОЛУЫНА БАЙЛАНЫСТЫ",
              "name_ru": "В СВЯЗИ СО СМЕРТЬЮ ГРАЖДАНИНА",
              "change_date": "2013-09-19T09:45:41+05:00"
            }
          }
        ]
      }
    },
    "person_photo_data": {
      "response": {
        "responseData": {
          "data": {
            "message_result": {
              "code": "",
              "name_ru": "",
              "name_kz": ""
            },
            "person_list": {}
          }
        }
      }
    }
  }
}
```

### <a name="save-getpermitdocumentsbyiin"></a>3. `GetPermitDocumentsByIin`

- **Endpoint:** `/gbdel-universal/v1/external/send-request`
- **Method:** `POST`

**Успешный ответ (NotRestricted):**

**curl команда:**
```bash
curl 'https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/gbdel-universal/v1/external/send-request&method=POST&http_status_code=200' \
  -u 'admin:secret' \
  -d '{ "Iin": "111222333444", "ActivityTypes": [ { "Code": "NotRestricted", "NameRu": "Деятельность разрешена", "NameKz": "Қызметке рұқсат етілген" } ] }'
```

**JSON тело:**
```json
{
  "Iin": "111222333444",
  "ActivityTypes": [
    {
      "Code": "NotRestricted",
      "NameRu": "Деятельность разрешена",
      "NameKz": "Қызметке рұқсат етілген"
    }
  ]
}
```

**Ответ с ограничением (Restricted):**

**curl команда:**
```bash
curl 'https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/gbdel-universal/v1/external/send-request&method=POST&http_status_code=200' \
  -u 'admin:secret' \
  -d '{ "Iin": "111222333444", "ActivityTypes": [ { "Code": "Restricted", "NameRu": "Деятельность ограничена", "NameKz": "Қызмет шектелген" } ] }'
```

**JSON тело:**
```json
{
  "Iin": "111222333444",
  "ActivityTypes": [
    {
      "Code": "Restricted",
      "NameRu": "Деятельность ограничена",
      "NameKz": "Қызмет шектелген"
    }
  ]
}
```

### <a name="save-createreport"></a>4. `CreateReport`

- **Endpoint:** `/xdata/v1/v1/report/create`
- **Method:** `POST`

**curl команда:**
```bash
curl 'https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/xdata/v1/v1/report/create&method=POST&http_status_code=200' \
  -u 'admin:secret' \
  -d '{ "report_id": "fee6942d-eae8-48d7-9d75-1bf93e47f238", "ready_percentage": 80, "status": "OK" }'
```

**JSON тело:**
```json
{
  "report_id": "fee6942d-eae8-48d7-9d75-1bf93e47f238",
  "ready_percentage": 80,
  "status": "OK"
}
```

### <a name="save-getreportstatus"></a>5. `GetReportStatus`

- **Endpoint:** `/xdata/v1/v1/report/status`
- **Method:** `GET`

**curl команда:**
```bash
curl 'https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/xdata/v1/v1/report/status&method=GET&http_status_code=200' \
  -u 'admin:secret' \
  -d '{ "report_id": "fee6942d-eae8-48d7-9d75-1bf93e47f238", "ready_percentage": 100, "status": "OK" }'
```

**JSON тело:**
```json
{
  "report_id": "fee6942d-eae8-48d7-9d75-1bf93e47f238",
  "ready_percentage": 100,
  "status": "OK"
}
```

### <a name="save-getreport"></a>6. `GetReport`

- **Endpoint:** `/xdata/v1/v1/report/get`
- **Method:** `GET`

**Пример: Ответ без долгов (KGD22 Value=0)**

**curl команда:**
```bash
curl 'https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/xdata/v1/v1/report/get&method=GET&http_status_code=200' \
  -u 'admin:secret' \
  -d '{ "reportID": "fee6942d-eae8-48d7-9d75-1bf93e47f238", "sources": [ { "code": "KGD29", "status": 4, "name": {"ru": "Комитет государственных доходов", "kk": "Мемлекеттік кірістер комитеті"}, "url": "", "date_updated": "2023-01-01", "date_actual": "2023-01-01" }, { "code": "KGD22", "status": 1, "name": {"ru": "Комитет государственных доходов", "kk": "Мемлекеттік кірістер комитеті"}, "url": "", "date_updated": "2023-01-01", "date_actual": "2023-01-01", "infos": [ { "kk": [ { "title": "Барлық берешек, оның ішінде (теңге)", "value": "0" } ], "ru": [ { "title": "Всего задолженности (тенге)", "value": "0" } ] } ] } ] }'
```

**JSON тело:**
```json
{
  "reportID": "fee6942d-eae8-48d7-9d75-1bf93e47f238",
  "sources": [
    {
      "code": "KGD29",
      "status": 4,
      "name": {
        "ru": "Комитет государственных доходов",
        "kk": "Мемлекеттік кірістер комитеті"
      },
      "url": "",
      "date_updated": "2023-01-01",
      "date_actual": "2023-01-01"
    },
    {
      "code": "KGD22",
      "status": 1,
      "name": {
        "ru": "Комитет государственных доходов",
        "kk": "Мемлекеттік кірістер комитеті"
      },
      "url": "",
      "date_updated": "2023-01-01",
      "date_actual": "2023-01-01",
      "infos": [
        {
          "kk": [
            {
              "title": "Барлық берешек, оның ішінде (теңге)",
              "value": "0"
            }
          ],
          "ru": [
            {
              "title": "Всего задолженности (тенге)",
              "value": "0"
            }
          ]
        }
      ]
    }
  ]
}
```

**Пример: Ответ с долгами (KGD22 Value=123456)**

**curl команда:**
```bash
curl 'https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/xdata/v1/v1/report/get&method=GET&http_status_code=200' \
  -u 'admin:secret' \
  -d '{ "reportID": "fee6942d-eae8-48d7-9d75-1bf93e47f238", "sources": [ { "code": "KGD22", "status": 1, "name": {"ru": "Комитет государственных доходов", "kk": "Мемлекеттік кірістер комитеті"}, "url": "", "date_updated": "2023-01-01", "date_actual": "2023-01-01", "infos": [ { "kk": [ { "title": "Барлық берешек, оның ішінде (теңге)", "value": "123456" } ], "ru": [ { "title": "Всего задолженности (тенге)", "value": "123456" } ] } ] } ] }'
```

**JSON тело:**
```json
{
  "reportID": "fee6942d-eae8-48d7-9d75-1bf93e47f238",
  "sources": [
    {
      "code": "KGD22",
      "status": 1,
      "name": {
        "ru": "Комитет государственных доходов",
        "kk": "Мемлекеттік кірістер комитеті"
      },
      "url": "",
      "date_updated": "2023-01-01",
      "date_actual": "2023-01-01",
      "infos": [
        {
          "kk": [
            {
              "title": "Барлық берешек, оның ішінде (теңге)",
              "value": "123456"
            }
          ],
          "ru": [
            {
              "title": "Всего задолженности (тенге)",
              "value": "123456"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="save-sendloanapplicationinfoPKB"></a>7. `SendLoanApplicationInfoPKB`

- **Endpoint:** `/credit-app-v2/credit/loan`
- **Method:** `POST`

**curl команда:**
```bash
curl 'https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/credit-app-v2/credit/loan&method=POST&http_status_code=200' \
  -u 'admin:secret' \
  -d '{ "id": "mock-loan-app-id-678", "iin": "mock-iin-uuid-format", "loan_app_date": "2025-04-08 01:00:00+05", "loan_app_date_unix": 1744000800, "current_date": "2025-04-08 01:00:00+05", "current_date_unix": 1744000800 }'
```

**JSON тело:**
```json
{
  "id": "mock-loan-app-id-678",
  "iin": "mock-iin-uuid-format",
  "loan_app_date": "2025-04-08 01:00:00+05",
  "loan_app_date_unix": 1744000800,
  "current_date": "2025-04-08 01:00:00+05",
  "current_date_unix": 1744000800
}
```

### <a name="save-sendloanapplicationinfoGKB"></a>8. `SendLoanApplicationInfoGKB`

- **Endpoint** `/creditHistoryDataServices/OnlineApplicationService/CreateApplication`
- **Method:** `POST`

**curl команда:**
```bash
curl 'https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/creditHistoryDataServices/OnlineApplicationService/CreateApplication&method=POST&http_status_code=200' \
  -u 'admin:secret' \
  -H 'Content-Type: text/xml' \
  -d '<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:data="http://data.chdb.scb.kz">
  <soapenv:Header>
    <userId>b16cadf5-5afe-453d-9685-3933dba966a5</userId>
    <creditorId>test-creditor</creditorId>
    <timestamp>2024-03-14T12:00:00</timestamp>
  </soapenv:Header>
  <soapenv:Body>
    <data:createApplicationResponse>
      <result>
        <resultCode>1</resultCode>
        <resultMessage>success</resultMessage>
      </result>
    </data:createApplicationResponse>
  </soapenv:Body>
</soapenv:Envelope>'
```

**Ответ сервера:**
```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:data="http://data.chdb.scb.kz" xmlns:rep="http://data.chdb.scb.kz" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <soapenv:Header>
    <userId>b16cadf5-5afe-453d-9685-3933dba966a5</userId>
    <creditorId>test-creditor</creditorId>
    <timestamp>2024-03-14T12:00:00</timestamp>
  </soapenv:Header>
  <soapenv:Body>
    <data:createApplicationResponse>
      <result>
        <resultCode>1</resultCode>
        <resultMessage>success</resultMessage>
      </result>
    </data:createApplicationResponse>
  </soapenv:Body>
</soapenv:Envelope>
```
### <a name="save-getsusnstatus"></a>9. `GetSusnStatus` (GetSusnSubject)

- **Endpoint:** `/susn-status-v2/v1/subject`
- **Method:** `POST`

**curl команда:**
```bash
curl 'https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/susn-status-v2/v1/subject&method=POST&http_status_code=200' \
  -u 'admin:secret' \
  -d '{ "result": { "birthDate": "1985-05-10", "firstName": "MOCK", "iin": "sender_iin", "secondName": "MOCK", "surname": "MOCK", "signature": { "signatureValue": "mock_signature" }, "status": [ { "statusCode": 1, "statusNameRu": "Mock Статус Активен", "statusDstart": "2020-01-01", "statusDend": "2025-12-31" } ] } }'
```

**JSON тело:**
```json
{
  "result": {
    "birthDate": "1985-05-10",
    "firstName": "MOCK",
    "iin": "sender_iin",
    "secondName": "MOCK",
    "surname": "MOCK",
    "signature": {
      "signatureValue": "mock_signature"
    },
    "status": [
      {
        "statusCode": 1,
        "statusNameRu": "Mock Статус Активен",
        "statusDstart": "2020-01-01",
        "statusDend": "2025-12-31"
      }
    ]
  }
}
```

### <a name="save-getarmystatus"></a>10. `GetArmyStatus`

- **Endpoint:** `/recruit/v1/{iin}` (Пример для IIN `555666777888`)
- **Method:** `GET`

**curl команда:**
```bash
curl 'https://pkb-pr.zaman.redmadrobot.com/save?user_id=e1aae679-79de-4a66-9d9f-8f7fcc7a7d9c&endpoint=/recruit/v1/555666777888&method=GET&http_status_code=200' \
  -u 'admin:secret' \
  -d '{ "iin": "555666777888", "status": true, "date": "2024-10-01" }'
```

**JSON тело:**
```json
{
  "iin": "555666777888",
  "status": true,
  "date": "2024-10-01"
}
```
