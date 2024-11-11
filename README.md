# Расчётно-пояснительная записка

#### 1. [Тема и целевая аудитория](#1)

#### 2. [Расчет нагрузки](#2)

#### 3. [Глобальная балансировка нагрузки](#3)

#### 4. [Локальная балансировка нагрузки](#4)

#### 5. [Логическая схема базы данных](#5)

#### 6. [Физическая схема базы данных](#6)

#### 7. [Алгоритмы](#7)

#### 8. [Технологии](#8)

#### 9. [Надёжность](#9)

#### 10. [Схема проекта](#10)


<h1 id="1"><b>Тема и целевая аудитория</b></h1>
Для курсовой работы я выбрал сервис бронирования жилья Booking.com. Он соответсвует требованиям, предъявляемым для курсовой работы:

- **Имеет реальные аналоги:** на глобальном рынке это Airbnb, agoda, на рынке СНГ это ostrovok.ru/
- **Аудитория сервиса:** ежемесячная аудитория превышает 500 миллионов

| Регион               | Количество пользователей |
|----------------------|--------------------------|
| **Северная Америка** | 110 млн                  |
| **Евросоюз**         | 100 млн                  |
| **СНГ**              | 20 млн                   |
| **Всего по миру**    | 650 млн                  |

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

<h1 id="2"><b>Расчет нагрузки</b></h1>

## Продуктовые метрики

1. **Месячная аудитория (MAU)**: В 2024 году ежемесячное количество уникальных пользователей составляет 260 миллионов.
   Количество визитов в месяц -- 670 миллионов
2. **Дневная аудитория (DAU)** Ежедневная аудитория составляет 22 миллионов пользователей
3. **Средний размер хранилища пользователя** В хранилище пользователя хранятся только личные данные, список избранного и
   информация о бронированиях (~500кб). У пользователя в среднем 2-3 бронирования, информация о каждом из них занимает
   100кб. У некоторых пользователей есть аватарка(1-2мб), так что средний объём хранилища составляет ~1.7 мб.
4. **Средний размер хранилища объекта размещения** Для каждого объекта хранятся текстовые данные (адрес, владелец,
   телефон и т.д) -- ~500кб. А также по 30-40 фотографий, каждая по 100кб.
5. **Прирост объектов размещения** Согласно отчётам в конце 2023 года на сервисе было 3,4кк объектов размещения, в конце
   2022 года -- 2,7кк. Это даёт прирост в 700к объектов размещения в год
6. **Прирост пользователей** Прироста количества посещений не наблюдается (2021-2024 гг.). Следовательно, прирост
   пользователей на сетевой трафик влиять не будет.
7. **Среднее количество действий пользователя в день** Согласно источникам, в среднем пользователь посещает 6.3 страницы
   в день

Источник: [HypeStat](https://hypestat.com/info/booking.com), [HypeStat 2021](https://web.archive.org/web/20210812062253/https://hypestat.com/info/booking.com), [Комиссия по ценным бумагам и биржам США, стр.3](https://www.sec.gov/ix?doc=/Archives/edgar/data/1075531/000107553124000014/bkng-20231231.htm)

| Метрика                                         | Значение    |
|-------------------------------------------------|-------------|
| Месячная аудитория (MAU)                        | 260 млн     |
| Дневная аудитория (DAU)                         | 22 млн      |
| Средний размер хранилища на юзера               | 1.7 MB      |
| Количество объектов недвижимости                | 3.4 млн     |
| Средний размер хранилища на объект недвижимости | 4 MB        |
| Среднее количество посещенных страниц в день    | 6.3 страниц |

|                                                      | на пользователя в день |                                                                                                                                |
|------------------------------------------------------|------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| 1\. поиск жилья для заданной локации                 | 6,3\*17%=1,07          | согласно semrush ~17% посещений -- посещения страницы /searchresults, следовательно столько и поисков                          |
| 2\. просмотр страницы конкретного отеля/апартаментов | 6,3\*21%=1,32          | согласно semrush ~21% посещений -- посещения страницы /hotel                                                                   |
| 3\. фильтр по поиску                                 | 1,07\*0,5=0,53         | общедоступных данных нет, предпологается что каждый 2ой поиск -- с фильтрами                                                   |
| 4\. список избранного                                | 6,3\*10%=0,63          | согласно semrush ~10% посещений -- посещения страницы /mywishlist                                                              |
| 5\. список отзывов, рейтинг жилья                    | 1,32\*3=3,96           | вместе с загрузкой страницы конкретного объекта загружается 3 отзыва                                                           |
| 6\. сообщение хозяину жилья                          | 6,3*4%=0,25            | согласно semrush ~4% посещений -- посещения страницы /messages                                                                 |
| 7\. панель управления своими бронированиями          | 6,3\*7%=0,44           | посещения домена secure.booking.com(на котором и находится панель управления) составляют 7% от общего числа посещений          |
| 8\. панель для владельцев объектов размещения        | 6,3\*2%=0,12           | посещения домена admin.booking.com(на котором и находится панель управления владельца) составляют 2% от общего числа посещений |

## Технические метрики

1. **Размер хранения по типам данных**:
    - Данные всех пользователей занимают 670kk * 1.7мб = 1140тб.
    - Данные всех объектов недвижимости занимают 3.4кк * (500кб + (40 * 100кб)) = 15тб
2. **Сетевой трафик**:

|                                                      | трафик на пользователя в день | сетевая нагрузка, гбит/c |                                                                  |
|------------------------------------------------------|-------------------------------|--------------------------|------------------------------------------------------------------|
| 1\. поиск жилья для заданной локации                 | 1,07 \* 25мб = 27             | 55,01                    | страница поиска весит ~25мб                                      |
| 2\. просмотр страницы конкретного отеля/апартаментов | 1,32\*20мб = 26               | 52,98                    | страница конкретного объекта недвижимости весит ~25мб            |
| 3\. фильтр по поиску                                 | незначительно                 | незначительна            | запрос о применении фильтра весит мало                           |
| 4\. список избранного                                | 0,63 \* 20мб = 12,6           | 25,67                    | страница избранного весит ~20мб                                  |
| 5\. список отзывов, рейтинг жилья                    | 3,96 \* 2кб = 8кб             | незначительна            | каждый отзыв весит примерно 2 кб (тк не содержит фото или видео) |
| 6\. сообщение хозяину жилья                          | 0,25\*10кб = 2,5кб            | незначительна            | одно сообщение весит примерно 10кб                               |
| 7\. панель управления своими бронированиями          | 0,44\*15мб=6,6                | 13,45                    | страница своих бронирований весит ~15мб                          |
| 8\. панель для владельцев объектов размещения        | 0,12\*20мб=2,4                | 4,89                     | страница администрирования весит ~20мб                           |
| сумма                                                | 74,6                          | 152                      |                                                                  |

(74,6мб * 22кк DAU)/24/60/60 * 8бит=152гбит/c

- **Пиковое потребление**:Из лекции следует что пиковое потребление x2.5-3 от среднего. 152*3=~455гбит/c
- **Суммарный суточный трафик**: составляет порядка 1,6 петабайт в сутки.

3. **RPS (requests per second)**: Средний RPS составляет около 16к запросов в секунду, соответственно пиковый RPS около
   48к (х3 от среднего).[Источник](https://youtu.be/RzO2tmrPRfo?t=129)

|                                                      | RPS средний        | RPS пиковый |
|------------------------------------------------------|--------------------|-------------|
| 1\. поиск жилья для заданной локации                 | 3619,3             | 10857,9     |
| 2\. просмотр страницы конкретного отеля/апартаментов | 3485,3             | 10455,8     |
| 3\. фильтр по поиску                                 | 3619,3\*0,5=1809,5 | 5428,5      |
| 4\. список избранного                                | 1689,0             | 5067,0      |
| 5\. список отзывов, рейтинг жилья                    | 3485,3             | 10857,9     |
| 6\. сообщение хозяину жилья                          | 905                | 2715        |
| 7\. панель управления своими бронированиями          | 884,7              | 2654,2      |
| 8\. панель для владельцев объектов размещения        | 321,7              | 965,1       |
| сумма                                                | 16200,0            | 48600,0     |

RPS посчитан пропорционально сетевому трафику.
RPS для сообщений посчитан исходя из среднего количества посещений страницы /messages

#### Прирост

Согласно статистике Hypestat, прироста посещений не наблюдается (август 2021 - август 2024). Следовательно, прироста
сетевого трафика не будет. Прирост по хранилищу будет ~25% в год (исходя из прироста количества объектов размещения).

#### Сводная таблица

| Метрика                    | Среднее значение | Пиковое значение |
|----------------------------|------------------|------------------|
| Размер хранения (всего)    | 1.5 ПБ           | —                |
| Сетевой трафик (в сутки)   | ~1,6 ПБ          | -                |
| Сетевой трафик (в секунду) | 152гбит/c        | 455гбит/c        |
| RPS по запросам            | 16к              | ~48к             |

<h1 id="3"><b>Глобальная балансировка нагрузки</b></h1>

| Страна         | Процент пользователей |
|----------------|-----------------------|
| США            | 8,30                  |
| Германия       | 8,00                  |
| Великобритания | 7,50                  |
| Италия         | 6,10                  |
| Испания        | 6,00                  |
| Франция        | 5,60                  |
| Бразилия       | 4,30                  |
| Польша         | 3,50                  |
| Индия          | 3,50                  |
| Нидерланды     | 2,60                  |
| Канада         | 2,10                  |
| Остальной мир  | 42,50                 |

### Расположение ДЦ

Для оптимального размещения дата-центров на основании географического распределения пользователей я выбрал такое
расположение:

| Локация                    | Процент пользователей | Обоснование                                                                                            |
|----------------------------|-----------------------|--------------------------------------------------------------------------------------------------------|
| **США (Даллас)**           | 10,4% (1k RPS)        | Обслуживает США (8,3%) и Канаду (2,1%). Близость к крупным АЭС                                         |
| **Германия (Франкфурт)**   | 15,5% (1,5k RPS)      | Обслуживает Германию (8%) и Великобританию (7,5%). ДЦ может также поддерживать Испанию и Польшу.       |
| **Нидерланды (Амстердам)** | 17,8% (1,8k RPS)      | Обслуживает Францию (5,6%), Нидерланды (2,6%), Италию (6,1%) и Польшу (3,5%). Развитая инфраструктура. |
| **Индия (Бангалор)**       | 15-20% (~1.7k RPS)    | Обслуживает Индию (3,5%) и азиатские страны. Близость к источникам энергии.                            |
| **Бразилия (Сан-Паулу)**   | 15-20% (~1.7k RPS)    | Обслуживает Бразилию (4,3%) и Южную Америку. Доступ к электростанциям в Сан-Паулу.                     |
| **Сингапур**               | 10-15% (~1.2k RPS)    | Обслуживает Юго-Восточную Азию, Австралию. Крупная точка обмена трафика                                |

![image](https://github.com/user-attachments/assets/39b0fde1-b23b-406e-84ef-bb84d7f3019f)

### GeoDNS

Для балансировки нагрузки по регионам будем использовать GeoDNS, чтобы каждый клиент обслуживался тем ДЦ, который
находится к нему ближе.

### Anycast

Для всех серверов будем использовать AnyCast, что позволит протоколу BGP определять, куда направить пользователя.

### CDN

Так как на сервисе достаточно много статики, то будем использовать Amazon Cloudfront. Он имеет сервера по всему миру (в
т.ч. в каждой из локаций
ДЦ) [Источник](https://aws.amazon.com/ru/cloudfront/features/?whats-new-cloudfront.sort-by=item.additionalFields.postDateTime&whats-new-cloudfront.sort-order=desc)


<h1 id="4"><b>Локальная балансировка нагрузки</b></h1>
### Схема балансировки входящих запросов
Будем использовать kubernetes кластеры, совместно c L7 балансировщиками. (с x2 запасом)

В качестве L7 балансировщика будет выступать nginx. В одном ДЦ разместим 2 экземпляра NGINX одинаковой конфигурации(для
устранения точки отказа). Nginx будет заниматься SSL-termination (что разгружает backend). Nginx будет распределять
запросы между подами используя алгоритм Round Robin.

### Схема балансировки внутренних запросов

В качестве прокси для взаимодействия c внутренними запросами будем использовать Envoy. Для устранения точки отказа, на
каждом ДЦ будет поднято по 2 экземпляра.

### Отказоустойчивость

Отказоустойчивость будет обеспечена за счёт самого Kubernetes, который при отказе пода поднимет новый экземпляр сервиса.
Nginx хорошо интегрируется с Kubernetes (K8s сам масштабирует приложение, а nginx сразу же посылает трафик на новые
поды).

<h1 id="5"><b>Логическая схема БД</b></h1>

![image](https://github.com/user-attachments/assets/35a77b66-8f38-4b31-9bc9-a914993c26d3)

### Размеры данных

**User**: 4(ID) + 4(Avatar ID) + 50(Name) + 50(Surname) + 15(Phone) + 100(email) + 50(country) + 3(date of birth) + 255(
пароль) + 255(хэш) + 8(created_at) + 8(updated_at) = 802 байт * 537кк = 545гб

**UserAvatar**: ID(4 байта) + User_ID(4 байта) + 1мб(сама аватарка) ~ 1 мб. У ~20% процентов пользователей есть
аватарка. 1 * 0,2 * 670кк = 134тб

**Session**: 4(ID) + 4(User_ID) + 8(LoginTime) + 8(LogoutTime) + 255(SessionKey) = 279 байт * 260kk сессий = 77гб

**Property**: 4(ID) + 100(Name) + 4(latitude) + 4(longtitude) + 50(City) + 50(Country) + 5(Price) + 4(owner_ID) = 221
байт * 27kk = 6гб

**Property_Feature**: 4(ID) + 100(Name) + 1000(text) = 1104 * 15 фич на объект * 27 кк = 44гб

**PropertyPhoto**: 4 (ID) + 4 (Property_ID) + File примерно 1мб + 8 (CreatedAt) = 30фото на объект * 27кк * 1мб =810тб

**Room**: 4(ID) + 4(Property_ID) + 8(Price per day) = 16 байт * 3 комнаты на объект * 27кк = 1,3гб

**Booking**: 4(ID) + 4(Property_ID) + 4(User_ID) + 3 (DateFrom) + 3 (DateTo) + 8 (Price) = 26 байт * 2 бронирования на
пользователя * 670кк = 34гб

**Review**: 4 (ID) + 4 (Booking_ID) + 8 (CreatedAt) + 8 (UpdatedAt) + 4 (Stars) + ~500 байт текста = 528 байт * 15
отзывов на объект * 27кк = 200гб

**Favourites**: 4 (ID) + 4 (User_ID) + 4 (Property_ID) = 12 байт *~13.5kk сохранений = 150мб

**Сhat**: 4(ID) + 4(User_ID1) + 4(User_ID2) + 4 (Booking_ID) = 16байт * 2 бронирования (следовательно и чата) на
пользователя * 670кк = 21гб

**Message**: 4(ID) + 4(Chat_ID) + 4(User_ID) + ~500байт текст = 512байт * ~ 2 сообщения в чате * 2 бронирования на
пользователя * 670кк = 1,3тб

| Таблица | Поле         | Тип данных   | Описание                           |
|---------|--------------|--------------|------------------------------------|
| User    | ID           | INT          | Первичный ключ, ID пользователя    |
|         | Avatar_ID    | INT          | Внешний ключ на таблицу UserAvatar |
|         | FirstName    | VARCHAR(50)  | Имя                                |
|         | LastName     | VARCHAR(50)  | Фамилия                            |
|         | Phone        | VARCHAR(15)  | Телефон                            |
|         | Email        | VARCHAR(100) | Электронная почта                  |
|         | Country      | VARCHAR(50)  | Страна                             |
|         | DateOfBirth  | DATE         | Дата рождения                      |
|         | PasswordHash | VARCHAR(255) | Пароль                             |
|         | PasswordHash | VARCHAR(255) | Хэш пароля                         |
|         | CreatedAt    | TIMESTAMP    | Дата создания                      |
|         | UpdatedAt    | TIMESTAMP    | Дата обновления                    |

| Таблица    | Поле      | Тип данных | Описание                     |
|------------|-----------|------------|------------------------------|
| UserAvatar | ID        | INT        | Первичный ключ, ID аватара   |
|            | User_ID   | INT        | Внешний ключ на таблицу User |
|            | File      | BLOB       | Файл изображения             |
|            | CreatedAt | TIMESTAMP  | Дата создания                |

| Таблица  | Поле       | Тип данных     | Описание                                |
|----------|------------|----------------|-----------------------------------------|
| Property | ID         | INT            | Первичный ключ, ID недвижимости         |
|          | Name       | VARCHAR(100)   | Название недвижимости                   |
|          | Latitude   | Decimal(8,6)   | Широта                                  |
|          | Longtitude | Decimal(9,6)   | Долгота                                 |
|          | City       | VARCHAR(50)    | Город                                   |
|          | Country    | VARCHAR(50)    | Страна                                  |
|          | Price      | DECIMAL(10, 2) | Цена недвижимости                       |
|          | Owner_ID   | INT            | Внешний ключ на пользователя (владелец) |

| Таблица         | Поле        | Тип данных   | Описание                          |
|-----------------|-------------|--------------|-----------------------------------|
| PropertyFeature | ID          | INT          | Первичный ключ, ID характеристики |
|                 | Name        | VARCHAR(100) | Название характеристики           |
|                 | Description | TEXT         | Описание характеристики           |

| Таблица | Поле        | Тип данных     | Описание                         |
|---------|-------------|----------------|----------------------------------|
| Room    | ID          | INT            | Первичный ключ, ID комнаты       |
|         | Property_ID | INT            | Внешний ключ на таблицу Property |
|         | PricePerDay | DECIMAL(10, 2) | Цена за день                     |

| Таблица | Поле        | Тип данных     | Описание                         |
|---------|-------------|----------------|----------------------------------|
| Booking | ID          | INT            | Первичный ключ, ID бронирования  |
|         | Property_ID | INT            | Внешний ключ на таблицу Property |
|         | User_ID     | INT            | Внешний ключ на таблицу User     |
|         | DateFrom    | DATE           | Дата начала бронирования         |
|         | DateTo      | DATE           | Дата окончания бронирования      |
|         | Price       | DECIMAL(10, 2) | Цена бронирования                |

| Таблица       | Поле        | Тип данных | Описание                             |
|---------------|-------------|------------|--------------------------------------|
| PropertyPhoto | ID          | INT        | Первичный ключ, ID фото недвижимости |
|               | Property_ID | INT        | Внешний ключ на таблицу Property     |
|               | File        | BLOB       | Файл изображения                     |
|               | CreatedAt   | TIMESTAMP  | Дата создания                        |

| Таблица | Поле       | Тип данных | Описание                        |
|---------|------------|------------|---------------------------------|
| Review  | ID         | INT        | Первичный ключ, ID отзыва       |
|         | Booking_ID | INT        | Внешний ключ на таблицу Booking |
|         | CreatedAt  | TIMESTAMP  | Дата создания                   |
|         | UpdatedAt  | TIMESTAMP  | Дата обновления                 |
|         | Stars      | INT        | Оценка (количество звезд)       |
|         | Text       | TEXT       | Текст отзыва                    |

| Таблица | Поле       | Тип данных   | Описание                     |
|---------|------------|--------------|------------------------------|
| Session | ID         | INT          | Первичный ключ, ID сессии    |
|         | User_ID    | INT          | Внешний ключ на таблицу User |
|         | LoginTime  | TIMESTAMP    | Время входа в систему        |
|         | LogoutTime | TIMESTAMP    | Время выхода из системы      |
|         | SessionKey | VARCHAR(255) | Ключ сессии                  |

| Таблица   | Поле        | Тип данных | Описание                         |
|-----------|-------------|------------|----------------------------------|
| Favorites | ID          | INT        | Первичный ключ, ID               |
|           | User_ID     | INT        | Внешний ключ на таблицу User     |
|           | Property_ID | INT        | Внешний ключ на таблицу Property |

| Таблица | Поле       | Тип данных | Описание                        |
|---------|------------|------------|---------------------------------|
| Chat    | ID         | INT        | Первичный ключ, ID              |
|         | User_ID 1  | INT        | Внешний ключ на таблицу User    |
|         | User_ID 2  | INT        | Внешний ключ на таблицу User    |
|         | Booking_ID | INT        | Внешний ключ на таблицу Booking |

| Таблица | Поле       | Тип данных | Описание                     |
|---------|------------|------------|------------------------------|
| Message | ID         | INT        | Первичный ключ, ID           |
|         | User_ID    | INT        | Внешний ключ на таблицу User |
|         | Chat_ID    | INT        | Внешний ключ на таблицу Chat |
|         | Text       | TEXT       | Текст сообщения              |
|         | created_at | TIMESTAMP  | Время создания               |

<h1 id="6"><b>Физическая схема БД</b></h1>

![Physical DB drawio](https://github.com/user-attachments/assets/eae48afd-bc5b-41b8-8661-bccd13fa0bac)

Хранение

- S3 хранилище: Будет использоватья для хранения статических данных (аватарки, фотографии недвижимости). S3 хорошо
  подходит для работы с медиа и доступом через интернет.

- Tarantool: Используется для хранения сессий. Tarantool — это in-memory база данных с поддержкой высокой
  производительности чтения/записи. Она также поддерживает механизмы репликации и восстановления.

- PostgreSQL: Основная база данных для хранения чатов, так как они редко используются на данном сервисе.

- Cassandra: Используется для хранения всех остальных данных.

- ElasticSearch: Используется для поиска по объектам недвижимости

### **Основные сущности и СУБД**

| **Название таблиц**                                                   | **Выбранная СУБД** |
|-----------------------------------------------------------------------|--------------------|
| **Sessions**                                                          | **Tarantool**      |
| **User, Property, PropertyFeature, Room, Booking, Review, Favorites** | **Cassandra**      |
| **PropertyPhoto, UserAvatar**                                         | **S3**             |
| **PropertySearch**                                                    | **ElasticSearch**  |
| **Сhat, messgage**                                                    | **Postgres**       |

### **Денормализация**

Денормализуем useravatar, перенеся его в S3, а в основной таблице оставим только ссылку на S3.
Денормализуем propertyFeature, добавив к каждому объекту размещения его опции. Оставим отдельую таблицу для ведения
общего списка опций.
Денормализуем Review, добавив топ3 отзывов к каждому объекту размещения. Уберем связь с Booking. Добавим в review имя
пользователя
Денормализуем Room, добавив цену самой дешевой комнаты к объекту размещения. Будем хранить в Cassandra, в качестве
primary key сделаем property_id.
Денормализуем Favorites, сделав в качестве key -- user_id, будем хранить массивы лайкнутых объектов.
Денормализуем Booking, добавив туда property (для избавления связи через Room)

### **Шардирование и репликация**

Для масштабирования данных и обеспечения высокой доступности мы применяем шардирование (разбиение данных на несколько
частей) и репликацию (создание копий данных).

| **Название таблицы** | **Шардирование**            | **Репликация** |
|----------------------|-----------------------------|----------------|
| **User**             | Нет                         | Да             |
| **Favorites**        | Нет                         | Да             |
| **Property**         | По Location (город, страна) | Да             |
| **Property Feature** | По Property_ID              | Да             |
| **Room**             | По Property_ID              | Да             |
| **Booking**          | По временной метке          | Да             |
| **Review**           | По временной метке          | Да             |
| **Chat**             | Нет                         | Да             |
| **Message**          | Нет                         | Да             |
| **Session**          | Нет                         | Нет            |

Балансировка запросов:

- PostgreSQL: PgBouncer для подключения к пулу запросов и балансировки нагрузки
- Cassandra: внутренние механизмы
- Tarantool: репликация

### **Механизмы резервирования**

Для обеспечения отказоустойчивости и сохранности данных мы применяем различные механизмы резервного копирования для
каждой СУБД.

| **Название СУБД** | **Схема резервирования**                         |
|-------------------|--------------------------------------------------|
| **Cassandra**     | Снимки (Snapshots)                               |
| **Tarantool**     | Механизмы восстановления данных (Мастер-реплика) |
| **PostgreSQL**    | Регулярные дампы                                 |
| **S3**            | Полагаться на встроенные механизмы S3            |

<h1 id="7"><b>Алгоритмы</b></h1>

- Алгоритм рекомендации объектов размещения (на основе предыдущих поисков пользователя, истории его бронирований и
  списка избранных)

  Для сбора данных от пользователей используется отдельный сервис, который отправляет их в Kafka. Kafka распределяет
  данные на микросервисы, где модель
  нейронной сети формирует оценки объектов и передает их в базу данных. Формирование предложений происходит в
  Hadoop-кластере с использованием Apache Spark, после чего данные передаются в MongoDB для репликации и обработки.
  И в конце основной backend получает рекомендации в
  MongoDB. [Источник](https://publications.hse.ru/pubs/share/direct/213010543.pdf)
  ![image](https://github.com/user-attachments/assets/6811bc9e-3b62-49b7-8627-f860b0ee006f)

<h1 id="8"><b>Технологии</b></h1>

| Технология | Область примерения               | Мотивационная часть                                                             |
|------------|----------------------------------|---------------------------------------------------------------------------------|
| Angular    | Frontend                         | Популярный фреймворк, использующийся на больших проектах (Gmail, Neflix)        |
| Go         | Backend                          | Встроенная асинхронность, множество библиотек, скорость исполнения              |
| Nginx      | Reverse-Proxy                    | Имеет встроенную L7 балансировку, SSL-терминацию                                |
| kubernetes | Оркестрация                      | Удобная автоматизация управления контейнерами, используется на больших проектах |
| Docker     | Контейнеризация                  | Распространенность                                                              |
| Kafka      | Брокер сообщений в рекомендациях | Масштабируемость, распространенность                                            |
| Hadoop     | Выполнение расчёта рекомендаций  | Масштабируемость                                                                |
| TensorFlow | Обучение нейронной сети          | Распространенность                                                              |
| Hbase      | БД сбора данных о рекомендациях  | Быстрый доступ, интеграция с Hadoop                                             |
| Spark      | Обработки данных о рекомендациях | Скорость работы, интеграция с Hadoop                                            |
| MongoDB    | БД для выдачи рекомендаций       | NoSQL, скорость работы                                                          |
| Grafana    | Визуализация метрик              |                                                                                 |
| Prometheus | Хранение метрик                  |                                                                                 |

Отличия YT от Hadoop:

- Стек: YT написан на C++, Hadoop на Java
- В YT более продвинутые парадигмы работы с метаинформацией (информация о файлах в системе храниться на разных
  компьютерах в распределенном режиме = шардирование)
- В YT данные в основном хранятся в таблицах — наборах строк, состоящих из колонок с данными, в отличие от связки HDFS с
  Hadoop Mapreduce, где основной видимой пользователю единицей хранения является файл

[Источник](https://habr.com/en/companies/yandex/articles/721526/), [Источник](https://habr.com/en/companies/yandex/articles/311104/), [Источник](https://vc.ru/opinions/640768-pochemu-mir-big-data-zapomnit-20032023)

<h1 id="9"><b>Надежность</b></h1>

| Технология                       | Способы резервирования                                                                                                                                                                   |
|----------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Физическое резервирование ДЦ** | Будем использовать Tier4 ДЦ                                                                                                                                                              |
| **Электричество**                | Два независимых ввода от двух разных подстанций, установим дизель-генераторы и ИБП. Каждый ввод должке держать полную нагрузку ДЦ                                                        |
| **Сеть**                         | По два ввода, от двух разных провайдеров                                                                                                                                                 |
| **Кондиционировние**             | Два контура охлаждения                                                                                                                                                                   |
| **DNS**                          | Прописывание нескольких A-записей                                                                                                                                                        |
| **Anycast**                      | Распределение нагрузки между разными ДЦ                                                                                                                                                  |
| **Nginx**                        | Настройка нескольких экземпляров Nginx с автоматическим переключением (failover) через keepalived                                                                                        |
| **Kubernetes**                   | Использование ReplicaSets и пулов подов для резервирования, auto-healing подов при сбоях, настройка многозонного развертывания для повышения отказоустойчивости и защиты от сбоев узлов. |
| **ElasticSearch**                | 	Репликация данных между узлами для обеспечения отказоустойчивости, автоматическая перераспределение данных при отказе узлов                                                             |
| **Cassandra**                    | Шардирование, использование журнала коммитов                                                                                                                                             |
| **Amazon S3**                    | Встроенное резервное копировние, географически разнесенные узлы                                                                                                                          |
| **Grafana**                      | Репликация инстансов Grafana для повышения доступности и регулярное резервное копирование базы данных. Дублирование ключевых алертов на нескольких инстансах для отказоустойчивости.     |
| **Prometheus**                   | Настройка резервных экземпляров Prometheus для сохранения метрик, а также репликация алертов и конфигураций для защиты от потери данных при сбоях.                                       |

<h1 id="10"><b>Схема проекта</b></h1>

![Схема](https://github.com/user-attachments/assets/3bbaf849-911b-4e31-9182-3d3217a18f69)
