# AI_SETUP — универсальная инструкция для любого ИИ-агента

> Этот файл можно отдать любому ИИ-помощнику (Claude, ChatGPT, Gemini, Codex, Antigravity и т. д.). Он описывает, как подключить телефон пользователя к Tuya Cloud API и настроить приложение Smart Heating AI. Ведите пользователя **по одному шагу** и после каждого ждите подтверждения.

## Правила безопасности для агента
1. **Никогда** не публикуйте и не пересылайте `Access Secret`, ключ Gemini, пароли. Пользователь вставляет их сам, в поле приложения на телефоне.
2. Не вводите пароли пользователя на сайтах за него. Вход и подтверждения выполняет пользователь.
3. Ничего не удаляйте в облаке Tuya и в приложении Smart Life без явного согласия.
4. Не меняйте расписания в термостатах без предпросмотра и подтверждения пользователя.

## Исходные данные
| Параметр | Значение / где взять |
|---|---|
| Приложение | Smart Heating AI (Android, package `com.smartheating.ai`) |
| APK | https://github.com/ivankoval921-bit/smartheating-ai-releases/releases/latest |
| Облако | Tuya IoT Platform https://platform.tuya.com/ (отдельный аккаунт разработчика) |
| Регион ЕС | Data Center **Central Europe**, Endpoint `https://openapi.tuyaeu.com` |
| Нужно получить | Access ID, Access Secret (Overview проекта), UID (Devices → Link Tuya App Account) |
| Устройства | Термостаты в приложении Tuya Smart / Smart Life (категория `wk`) |

## Алгоритм
### Шаг A. Проверить, что устройства есть в Smart Life / Tuya Smart
Попросите пользователя открыть приложение Smart Life и убедиться, что термостаты онлайн. Если их нет, сначала добавить их там (инструкция производителя термостата).

### Шаг B. Аккаунт разработчика и проект
1. Пользователь регистрируется на https://platform.tuya.com/ (e-mail + код).
2. **Cloud → Development → Create Cloud Project**: Industry `Smart Home`, Development Method `Smart Home`, **Data Center = тот же регион, что у аккаунта Smart Life** (для Германии и ЕС `Central Europe`).
3. Подтвердить подключение API-сервисов (IoT Core, Authorization) кнопкой **Authorize**.

### Шаг C. Привязать аккаунт приложения
Проект → **Devices → Link Tuya App Account → Add App Account** → QR-код → в Smart Life: «Я» → сканер → подтвердить → **Automatic Link**, права **Read, Write and Manage**. В таблице появится **UID**, во вкладке **All Devices** термостаты со статусом Online.

### Шаг D. Ключи
Проект → **Overview**: `Access ID/Client ID`, `Access Secret/Client Secret` (значок глаза). Если не видно: **Authorization → Cloud Authorization**.

### Шаг E. Установить приложение
Пользователь скачивает APK по ссылке выше и устанавливает (разрешить установку из браузера). Подробно: [INSTALL.md](INSTALL.md).

### Шаг F. Ввести ключи в приложение
Приложение → **Настройки** → Endpoint / Access ID / Access Secret / UID → **«Сохранить ключи и проверить связь»**.
Признак успеха: во вкладке «Комнаты» у каждой комнаты исчезает «Нет связи» и видна температура.

### Шаг G. Фоновая работа (обязательно)
Настройки → карточка **«Проверка готовности»**: все пункты ✅ (батарея без ограничений, уведомления, авто-планировщик). На realme/OPPO/OnePlus/Xiaomi дополнительно: «Батарея → Разрешить фоновую активность» и «Автозапуск».

### Шаг H. Пробный период Tuya
Сервис IoT Core в Tuya пробный. Дата окончания: проект → Service API → IoT Core → View details. За 1–2 недели до окончания нажать **Extend Trial Period** (бесплатно, причина: personal smart home use).

## Диагностика
| Симптом | Причина | Решение |
|---|---|---|
| `1004 sign invalid` | неверный Access ID/Secret | скопировать заново без пробелов |
| `1106 permission deny` | не привязан аккаунт или другой дата-центр | шаг C, проверить регион |
| «quota used up» / `28841105` | закончился пробный период | шаг H |
| Ступени срабатывают с опозданием | телефон усыпляет приложение | шаг G |
| Голос не работает без интернета | нет офлайн-пакета языка | Google → Голосовой ввод → Распознавание офлайн → Русский |

## Техническая справка для агента (API)
- Подпись запросов: Tuya OpenAPI v2 (HMAC-SHA256, `client_id`, `t`, `nonce`, `sign_method=HMAC-SHA256`, токен `GET /v1.0/token?grant_type=1`).
- Чтение всех устройств одним запросом: `GET /v1.0/devices?device_ids=id1,id2,...` (возвращает `online` и `status[]`).
- Команды: `POST /v1.0/iot-03/devices/{id}/commands` с телом `{"commands":[{"code":"temp_set","value":215}]}`. Температура в десятых долях °C.
- Коды DP термостата Ecogaz XR806: `mode` (auto / Comfort / eco / Standby / Anti_forst / on), `temp_set`, `temp_current`, `temp_correction` (−100..100), `upper_temp`, `lower_temp`, `child_lock`, `window_check`, `window_state`, `work_state`, `battery_percentage`, `week_program_1..7` (Base64, 25 байт: день + 6 точек × 4 байта).
- Сборка из исходников (только у владельца): `gradle testDebugUnitTest assembleDebug` в папке `android/`, JDK 17, Android SDK 35.
