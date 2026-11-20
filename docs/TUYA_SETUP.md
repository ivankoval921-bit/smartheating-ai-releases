# Подключение к облаку Tuya: пошагово

Приложению нужны 4 значения: **Endpoint**, **Access ID**, **Access Secret** и **UID**. Их выдаёт бесплатный аккаунт разработчика Tuya. Это **отдельный** аккаунт, не тот, что в приложении Smart Life или Tuya Smart.

> На всё уходит около 15 минут, делается один раз. Удобнее с компьютера.

## Шаг 0. Термостаты уже работают в приложении Tuya
Батареи должны быть добавлены в приложение **Tuya Smart** или **Smart Life** на телефоне и управляться из него.
- Tuya Smart: [Google Play](https://play.google.com/store/apps/details?id=com.tuya.smart) · Smart Life: [Google Play](https://play.google.com/store/apps/details?id=com.tuya.smartlife)

## Шаг 1. Регистрация разработчика
1. Откройте **[platform.tuya.com](https://platform.tuya.com/)** (старый адрес [iot.tuya.com](https://iot.tuya.com/) ведёт туда же).
2. Нажмите **Sign Up / Регистрация**, укажите e-mail, подтвердите его кодом из письма.
3. Язык сайта переключается вверху справа (English, 简体中文 и т. д.).

## Шаг 2. Облачный проект
1. В меню слева: **Cloud → Development** (云开发 → 项目管理).
2. Нажмите **Create Cloud Project**:
   - Name: любое, например `SmartHeatingAI`;
   - Industry: `Smart Home`; Development Method: `Smart Home`;
   - **Data Center: `Central Europe`** (для Германии и всего ЕС). Это важно: дата-центр должен совпадать с регионом аккаунта Smart Life.
3. На следующем экране сервисы **IoT Core** и **Authorization** будут отмечены сами. Нажмите **Authorize**.

## Шаг 3. Access ID и Access Secret
Откройте проект → вкладка **Overview** (概况). Если ключей там нет, загляните в **Authorization → Cloud Authorization**.
- **Access ID / Client ID**: скопируйте кнопкой «копировать».
- **Access Secret / Client Secret**: нажмите на глазик и скопируйте.

> ⚠️ Access Secret никому не пересылайте и не публикуйте. Он даёт управление вашими устройствами.

## Шаг 4. Привязка аккаунта Smart Life (откуда берутся батареи)
1. В проекте: вкладка **Devices** (设备) → **Link Tuya App Account** (关联App账号) → **Add App Account**.
2. Появится QR-код. В приложении **Smart Life / Tuya Smart** на телефоне откройте **«Я» → значок сканера** вверху справа и отсканируйте код, затем подтвердите.
3. Выберите **Automatic Link** и права **Read, Write and Manage**.
4. В таблице появится ваш аккаунт. Колонка **UID** — это четвёртое значение для приложения.
5. Вкладка **All Devices**: здесь должны появиться все термостаты со статусом **Online**.

## Шаг 5. Endpoint
Для дата-центра **Central Europe** адрес такой:
```
https://openapi.tuyaeu.com
```
Для других регионов: Western America `https://openapi.tuyaus.com`, China `https://openapi.tuyacn.com`, India `https://openapi.tuyain.com`.

## Шаг 6. Ввод в приложение
**Настройки** → поля **Endpoint, Access ID, Access Secret, UID** → **«Сохранить ключи и проверить связь»**. Через несколько секунд во вкладке «Комнаты» появятся температуры.

## Шаг 7. Пробный период ⏰
Бесплатный сервис **IoT Core** выдаётся как пробный на ~1 месяц, его можно продлевать бесплатно.
- Где посмотреть: проект → **Service API** → **IoT Core** → «View details». Там видна дата окончания (Expiration).
- Как продлить: на той же странице кнопка **«Extend Trial Period»** (申请延长体验期). В анкете достаточно указать «Personal smart home use».
- Пока подписка действует, приложение тратит лимит экономно: 1 запрос на все 4 комнаты при каждой проверке.

## Частые ошибки
| Код | Значение | Что делать |
|---|---|---|
| `1004 sign invalid` | Неверный Access ID или Secret | Скопируйте заново, без пробелов |
| `1010 token invalid` | Токен устарел | Приложение обновит его само, повторите |
| `1106 permission deny` | Аккаунт не привязан или выбран другой дата-центр | Шаг 4, проверьте Central Europe |
| `28841105` / «quota used up» | Закончился пробный период или лимит | Шаг 7, продлите |

Полезные ссылки: [документация Tuya Cloud](https://developer.tuya.com/en/docs/cloud/) · [привязка устройств через аккаунт приложения](https://developer.tuya.com/en/docs/iot/Platform_Configuration_smarthome?id=Kamcgamwoevrx)
