# Расчётно-пояснительная записка
## Тема и целевая аудитория
Для курсовой работы я выбрал сервис бронирования жилья Booking.com. Он соответсвует требованиям, предъявляемым для курсовой работы:
- **Имеет реальные аналоги:** на глобальном рынке это Airbnb, agoda, на рынке СНГ это ostrovok.ru/
- **Аудитория сервиса:** ежемесячная аудитория превышает 500 миллионов
  
| Регион                 | Количество пользователей |
|------------------------|--------------------------|
| **Северная Америка**   | 110 млн                  |
| **Евросоюз**           | 100 млн                  |
| **СНГ**                | 20 млн                   |
| **Всего по миру**      | 650 млн                  |

Источники: [SimilarWeb](https://www.similarweb.com/website/booking.com/), [BusinessOfApps](https://www.businessofapps.com/data/booking-statistics/).


## Функционал
1. **Бронирование жилья**
2. **Бронирование авиабилетов**
3. **Аренда автомобилей**
4. **Продажа экскурсий и билетов на достопримечательности**

## MVP
На мой взгляд, MVP этого сервиса -- **бронирования жилья**:
### Функционал MVP
1. **поиск жилья для заданной локации**
2. **просмотр страницы конкретного отеля/апартаментов**
3. **фильтр по поиску**
4. **список избранного**
5. **список отзывов, рейтинг жилья**
6. **сообщение хозяину жилья**
7. **панель управления своими бронированиями**
8. **панель для владельцев объектов размещения**
### Ключевые функциональные решения
 - удобный поиск с большим количеством фильтров
 - система отзывов
 - наличие объектов размещения в большинстве стран мира
## Продуктовые метрики
1. **Месячная аудитория (MAU)**: В 2024 году ежемесячное количество уникальных пользователей составляет 260 миллионов. Количество визитов в месяц -- 670 миллионов
2. **Дневная аудитория (DAU)** Ежедневная аудитория составляет 22 миллионов пользователей
3. **Средний размер хранилища пользователя** В хранилище пользователя хранятся только личные данные, список избранного и информация о бронированиях (~500кб). У пользователя в среднем 2-3 бронирования, информация о каждом из них занимает 100кб.  У некоторых пользователей есть аватарка(1-2мб), так что средний объём хранилища составляет ~1.7 мб. 
4. **Средний размер хранилища объекта размещения** Для каждого объекта хранятся текстовые данные (адрес, владелец, телефон и т.д) -- ~500кб. А также по 30-40 фотографий, каждая по 100кб.
5. **Прирост объектов размещения** Согласно отчётам в конце 2023 года на сервисе было 3,4кк объектов размещения, в конце 2022 года -- 2,7кк. Это даёт прирост в 700к объектов размещения в год
6. **Среднее количество действий пользователя в день** В среднем пользователь ищет жилье по 3-4 запросам, сохраняет 2-3 жилья в избранное и делает бронирование. В сумме выходит 7-8 действий в день
   Источник: [HypeStat](https://hypestat.com/info/booking.com), [Комиссия по ценным бумагам и биржам США, стр.3](https://www.sec.gov/ix?doc=/Archives/edgar/data/1075531/000107553124000014/bkng-20231231.htm)

| Метрика                             | Значение                   |
|-------------------------------------|----------------------------|
| Месячная аудитория (MAU)            | 260 млн                    |
| Дневная аудитория (DAU)             | 22 млн                     |
| Средний размер хранилища на юзера   | 1.7 MB                     |
| Количество объектов недвижимости    | 3.4 млн                     |
| Средний размер хранилища на объект недвижимости   | 4 MB                     |
| Среднее количество действий в день  | 5-6 действий               |

## Технические метрики
1. **Размер хранения по типам данных**:
   - Данные всех пользователей занимают 670kk * 1.7мб = 1140тб.
   - Данные всех объектов недвижимости занимают 3.4кк * (500кб + (40 * 100кб)) = 15тб
2. **Сетевой трафик**:
   - **Пиковое потребление**: пиковая скорость может достигать 600гбит/c.
      В среднем пользователь за сессию получает 70-80мб данных от сервера(замер через devtools).\
     (70мб * 30кк)/24/60/60/8=195гбит/c.\
     Из лекции следует что пиковое потребление x2.5-3 от среднего. 195*3=~600гбит/c 
   - **Суммарный суточный трафик**: составляет порядка 2 петабайт в сутки.
3. **RPS (requests per second)**: через devtools установлено, что в среднем в секунду от одного пользователя на сервер отпралвяется 10 запросов. Значит от всех пользователей поступает 3-5 тысяч запросов в секунду, в пиковое время до 20 тысяч.

| Метрика                     | Среднее значение   | Пиковое значение   |
|-----------------------------|--------------------|--------------------|
| Размер хранения (всего)     | 1.5 ПБ             | —                  |
| Сетевой трафик (в сутки)    | ~2 ПБ              | -                  |
| Сетевой трафик (в секунду)  | 200гбит/c          | 600гбит/c          |
| RPS по запросам             | 3-5к               | ~20к               |

## Глобальная балансировка нагрузки

### Расположение ДЦ
Так как наибольшее число пользователей находится в ЕС и Северной Америке, то большая часть ДЦ будут размещены там
1. **Европа**
- Амстердам (Нидерланды)
- Хельсинки (Финляндия)
2. **Северная Америка**
- Монреаль (Канада)
- Сан-Франциско (США)
- Бостон (США)
3. **Южная Америка**
- Сан-Паулу (Бразилия)
- Буэнос-Айрэс (Аргентина)
4. **Австралия**
- Мельбурн
5. **Азия**
- Стамбул (Турция)
- Сеул (Южная Корея)
- Сингапур
6. **Африка**
- Йоханнесбург (ЮАР)

| Регион           | Нагрузка по RPS  |
|------------------|------------------|
| Европа           | 7,5% * 2 дц      |
| Северная Америка | 6% * 3 дц        |
| Остальной мир    | 9% * 7 дц        |

### GeoDNS
Для балансировки нагрузки по регионам будем использовать GeoDNS, чтобы каждый клиент обслуживался тем ДЦ, который находится к нему ближе.
### Anycast
Для всех серверов будем использовать AnyCast, что позволит протоколу BGP определять, куда направить пользователя.
### CDN
Так как на сервисе достаточно много статики, то можно использовать CDN. (Booking использует от Amazon)

## Локальная балансировка нагрузки
### Схема балансировки входящих запросов
Будем использовать kubernetes кластеры, совместно с L4 балансировщиками. (с x2 запасом)
L4 будет распределять трафик по least connections алгоритму. Также будем использовать Envoy.
### Схема балансировки внутренних запросов
Envoy + least conn
### Отказоустойчивость
Отказоустойчивость будет обеспечена за счёт самого Kubernetes, который при отказе пода поднимет новый экземпляр сервиса. На уровне кластеров L4-балансировщик будет ежесекундно проводить healthcheck kubernetes, и переключать на другие кластеры при отказе
## Логическая схема БД
![20241007_180604](https://github.com/user-attachments/assets/d58db122-3cd7-424a-9b82-6db83dc625f1)
| **Таблица**      | **Размеры данных**                                                         | **Консистентность** |
|------------------|----------------------------------------------------------------------------|---------------------|
| User             | 814 байт на юзера * 670кк = 545гб                                          |                     |
| UserAvatar       | 1мб * 0.2 * 670кк = 134тб                                                  |                     |
| Session          | 260кк сессий * (13*3 + 255) = 77гб                                         |                     |
| Property         | 364 байта на объект * 27кк = 10гб                                          |                     |
| Room             | 96 байт * 27кк * 3типа = 7гб                                               |                     |
| Booking          | 154 байта на бронирование * 2 бронирования на пользователя * 670кк = 206гб |                     |
| Property Photo   | 30фото на объект * 27кк * 1мб =810тб                                                     |                     |
| Property Feature | 232 байта * 15 фич на объект * 27 кк = 94гб                                |                     |
| Review           | 1кб * 15 отзывов на объект *27кк = 405гб                                                                          |                     |


| Таблица         | Поле           | Тип данных        | Описание                                     |
|-----------------|----------------|-------------------|----------------------------------------------|
| User        | ID             | INT               | Первичный ключ, ID пользователя              |
|                 | Avatar_ID      | INT               | Внешний ключ на таблицу UserAvatar           |
|                 | FirstName      | VARCHAR(50)       | Имя                                          |
|                 | LastName       | VARCHAR(50)       | Фамилия                                      |
|                 | Phone          | VARCHAR(15)       | Телефон                                      |
|                 | Email          | VARCHAR(100)      | Электронная почта                            |
|                 | Country        | VARCHAR(50)       | Страна                                       |
|                 | DateOfBirth    | DATE              | Дата рождения                                |
|                 | PasswordHash   | VARCHAR(255)      | Хэш пароля                                   |
|                 | CreatedAt      | TIMESTAMP         | Дата создания                                |
|                 | UpdatedAt      | TIMESTAMP         | Дата обновления                              |

| Таблица         | Поле           | Тип данных        | Описание                                     |
|-----------------|----------------|-------------------|----------------------------------------------|
| UserAvatar  | ID             | INT               | Первичный ключ, ID аватара                   |
|                 | User_ID        | INT               | Внешний ключ на таблицу User                 |
|                 | File           | BLOB              | Файл изображения                             |
|                 | CreatedAt      | TIMESTAMP         | Дата создания                                |

| Таблица         | Поле           | Тип данных        | Описание                                     |
|-----------------|----------------|-------------------|----------------------------------------------|
| Property    | ID             | INT               | Первичный ключ, ID недвижимости              |
|                 | Name           | VARCHAR(100)      | Название недвижимости                        |
|                 | Location       | VARCHAR(100)      | Местоположение                               |
|                 | City           | VARCHAR(50)       | Город                                        |
|                 | Country        | VARCHAR(50)       | Страна                                       |
|                 | Price          | DECIMAL(10, 2)    | Цена недвижимости                            |
|                 | Owner_ID       | INT               | Внешний ключ на пользователя (владелец)      |

| Таблица         | Поле           | Тип данных        | Описание                                     |
|-----------------|----------------|-------------------|----------------------------------------------|
| PropertyFeature | ID          | INT               | Первичный ключ, ID характеристики            |
|                     | Name        | VARCHAR(100)      | Название характеристики                      |
|                     | Description | TEXT              | Описание характеристики                      |

| Таблица         | Поле           | Тип данных        | Описание                                     |
|-----------------|----------------|-------------------|----------------------------------------------|
| Room        | ID             | INT               | Первичный ключ, ID комнаты                   |
|                 | Property_ID    | INT               | Внешний ключ на таблицу Property             |
|                 | PricePerDay    | DECIMAL(10, 2)    | Цена за день                                 |

| Таблица         | Поле           | Тип данных        | Описание                                     |
|-----------------|----------------|-------------------|----------------------------------------------|
| Booking     | ID             | INT               | Первичный ключ, ID бронирования              |
|                 | Property_ID    | INT               | Внешний ключ на таблицу Property             |
|                 | User_ID        | INT               | Внешний ключ на таблицу User                 |
|                 | DateFrom       | DATE              | Дата начала бронирования                     |
|                 | DateTo         | DATE              | Дата окончания бронирования                  |
|                 | Price          | DECIMAL(10, 2)    | Цена бронирования                            |

| Таблица         | Поле           | Тип данных        | Описание                                     |
|-----------------|----------------|-------------------|----------------------------------------------|
| PropertyPhoto | ID           | INT               | Первичный ключ, ID фото недвижимости         |
|                   | Property_ID  | INT               | Внешний ключ на таблицу Property             |
|                   | File         | BLOB              | Файл изображения                             |
|                   | CreatedAt    | TIMESTAMP         | Дата создания                                |

| Таблица         | Поле           | Тип данных        | Описание                                     |
|-----------------|----------------|-------------------|----------------------------------------------|
| Review      | ID             | INT               | Первичный ключ, ID отзыва                    |
|                 | Booking_ID     | INT               | Внешний ключ на таблицу Booking              |
|                 | CreatedAt      | TIMESTAMP         | Дата создания                                |
|                 | UpdatedAt      | TIMESTAMP         | Дата обновления                              |
|                 | Stars          | INT               | Оценка (количество звезд)                    |
|                 | Text           | TEXT              | Текст отзыва                                 |

| Таблица         | Поле           | Тип данных        | Описание                                     |
|-----------------|----------------|-------------------|----------------------------------------------|
| Session     | ID             | INT               | Первичный ключ, ID сессии                    |
|                 | User_ID        | INT               | Внешний ключ на таблицу User                 |
|                 | LoginTime      | TIMESTAMP         | Время входа в систему                        |
|                 | LogoutTime     | TIMESTAMP         | Время выхода из системы                      |
|                 | SessionKey     | VARCHAR(255)      | Ключ сессии                                  |
