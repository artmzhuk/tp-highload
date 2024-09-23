# Расчётно-пояснительная записка
## Тема и целевая аудитория
Для курсовой работы я выбрал сервис бронирования жилья Booking.com. Он соответсвует требованиям, предъявляемым для курсовой работы:
- **Имеет реальные аналоги:** на глобальном рынке это Airbnb, agoda, на рынке СНГ это ostrovok.ru
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
| Количество объектов недвижимости            | 3.4 млн                     |
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
### Разбиение по поддоменам
- booking.com (основной домен)
- secure.booking.com (домен оплаты)
- account.booking.com (домен аккаунтов пользователей)
- admin.booking.com (домен для владельцев недвижимости)

### Расположение ДЦ
Так как наибольшее число пользователей находится в ЕС и Северной Америке, то большая часть ДЦ будут размещены там
1. **Европа**
- Амстердам (Нидерланды)
- Вена (Австрия)
- Хельсинки (Финляндия)
2. **Северная Америка**
- Монреаль (Канада)
- Сан-Франциско (США)
- Бостон (США)
- Денвер (США)
3. **Южная Америка**
- Сан-Паулу (Бразилия)
4. **Австралия**
- Мельбурн
5. **Азия**
- Сингапур
### GeoDNS
Для балансировки нагрузки по регионам будем использовать GeoDNS, чтобы каждый клиент обслуживался тем ДЦ, который находится к нему ближе.
### Anycast
Для серверов в США будем использовать AnyCast, что позволит протоколу BGP определять, куда направить пользователя.
