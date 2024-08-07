# System Design социальной сети для курса по System Design
## 1. Требования
### 1. Бизнес требования
Есть неограниченный бюджет для продвижения социальной сети.
Бизнес ограничение - не рассматриваем выход на зарубежные рынки за пределами стран СНГ.

### 1. Функциональные требования
Система должен включать в себя следующие возможности:
- публикация постов из путешествий. Посты включают с себя:
  - фотографии
  - небольшое описание
  - привязка к конкретному месту путешествия (геопозиция);
- оценка постов других путешественников;
- комментарии постов других путешественников;
- подписка на других путешественников, чтобы следить за их активностью;
- поиск популярных мест для путешествий и просмотр постов с этих мест;
- просмотр ленты других путешественников;


### 1. Нефункциональные требования
Система должна иметь следующие свойства:

| **Атрибут**            | **Идентификатор** | **Описание**                                                                                                                                                                                            |
|------------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Производительность     | PFR-001           | Время загрузки поста с фотографией по 99п не должно превышать 2 секунды. По 99.9п не должно превышать 3 секунд. Время загрузки должно обеспечиваться при скорости связи не менее 10 мегабит в сек.      |
| Производительность     | PFR-002           | Время успешной публикации комментариев к постам по 99 п не должно превышать 1 секунды. По 99.9п не должно превышать 2 секунд. Время должно обеспечиваться при скорости связи не менее 10 мегабит в сек. |
| Производительность     | PFR-003           | Время для просмотра ленты других путешественников должно быть аналогично требованию PFR-001                                                                                                             |
| Производительность     | PFR-004           | Время для поиска популярных мест для путешествий и просмотр постов с этих мест должно быть аналогично требованию PFR-001                                                                                |
| Производительность     | PFR-005           | Один пользователь в среднем будет оставлять 5 постов в день                                                                                                                                             |
| Производительность     | PFR-006           | Один пользователь в среднем будет смотреть ленту других путешественников 3 раза в день                                                                                                                  |
| Производительность     | PFR-007           | Один пользователь в среднем будет искать популярные места 1 раз в день                                                                                                                                  |
| Производительность     | PFR-008           | Один пользователь в среднем будет подписываться на других путешественников 1 раз в день                                                                                                                 |
| Производительность     | PFR-009           | Один пользователь в среднем будет комментировать или ставить реакцию на посты 1 раз в день                                                                                                              |
| Производительность     | PFR-010           | Размер фото не должен превышать 1 мегабайт. Если размер превышает, то сжимаем до 1 мегабайта                                                                                                            |
| Масштабируемость       | SCL-001           | Число активных уникальных пользователей в день через 1 год должно составить 10 000 000. Средний рост составляет примерно 840 000 в месяц                                                                |
| Доступность            | AVL-001           | Система должна быть доступна в 99.9% времени за месяц                                                                                                                                                   |
| Безопасность           | SEC-001           | Персональные данные клиентов не должны отдаваться в ответах от методов.                                                                                                                                 |
| Безопасность           | SEC-002           | Должна быть защита от ddos атак. При запросах от одного клиенского устройства чаще 10 RPS на каждый метод в отдельности производим блокировку пользователя на 60 минут                                  | |
| Совместимость          | CPB-001           | Система должна поддеживать работы на мобильных приложения IOS (версии не ниже), Android (версии не ниже), на вебе Google Chrome (версии не ниже)                                                        |
| Удобство использования | USL-001           | Интерфейс системы должен быть на русском языке                                                                                                                                                          |

## 2. Прогнозируемая нагрузка
### Динамика роста пользователей:

| **Месяц**           | **1 месяц** | **2 месяц** | **3 месяц** | **4 месяц** | **5 месяц** | **6 месяц** | **7 месяц** | **8 месяц** | **9 месяц** | **10 месяц** | **11 месяц** | **12 месяц** |
|---------------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|--------------|--------------|--------------|
| Рост пользователей  | 840 000     | 1 680 000   | 2 520 000   | 3 360 000   | 4 200 000   | 5 040 000   | 5 880 000   | 6 720 000   | 7 560 000   | 8 400 000    | 9 240 000    | 10 000 000   |

Каждый месяц рост на 820 000 пользователей. К году рост достигнет 10 000 000 уникальный пользователей в день.


### Прогноз количества запросов:

| **Среднее число запросов на 1 пользователя** | **avg_requests (в сутки)** | **avg_requests (в секундах)** |
|----------------------------------------------|----------------------------|-------------------------------|
| Посты                                        | 5                          | 5,78704E-05                   |
| Реакции и комментарии                        | 1                          | 1,15741E-05                   |
| Лента                                        | 3                          | 3,47222E-05                   |
| Поиск популярных мест                        | 1                          | 1,15741E-05                   |
| Лента других путешественников                | 3                          | 3,47222E-05                   |


### Прогноз нагрузки в RPS:

| Бизнес контекст                                         | 1 месяц | 2 месяц | 3 месяц | 4 месяц | 5 месяц | 6 месяц | 7 месяц | 8 месяц | 9 месяц | 10 месяц | 11 месяц | 12 месяц |
|---------------------------------------------------------|---------|---------|---------|---------|---------|---------|---------|---------|---------|----------|----------|----------|
| Посты                                                   | 49      | 97      | 146     | 194     | 243     | 292     | 340     | 389     | 438     | 486      | 535      | 579      |
| Добавление реакции и комментарии. Просмотр комментариев | 10      | 19      | 29      | 39      | 49      | 58      | 68      | 78      | 88      | 97       | 107      | 116      |
| Лента                                                   | 29      | 58      | 88      | 117     | 146     | 175     | 204     | 233     | 263     | 292      | 321      | 347      |
| Поиск популярных мест                                   | 10      | 19      | 29      | 39      | 49      | 58      | 68      | 78      | 88      | 97       | 107      | 116      |
| Лента других путешественников                           | 29      | 58      | 88      | 117     | 146     | 175     | 204     | 233     | 263     | 292      | 321      | 347      |

Кол-во уникальный пользователей по динамике * avg_requests (в секундах).


### Прогноз трафика:
Средний размер запроса:

| 	Бизнес контекст                | 	Средний размер запроса (килобайт)|
|---------------------------------|-----------------------------------|
| 	Посты                          | 	1500                             |
| 	Реакции и комментарии          | 	1                                |
| 	Лента                          | 	4000                             |
| 	Поиск популярных мест          | 	4000                             |
| 	Лента других путешественников  | 	4000                             |

Расчет трафика (килобайты в секунду):

|	Бизнес контекст	|	1 месяц	|	2 месяц	|	3 месяц	|	4 месяц	|	5 месяц	|	6 месяц	|	7 месяц	|	8 месяц	|	9 месяц	|	10 месяц	|	11 месяц	| 	12 месяц	 |
|---------------------------------------------------------|---------|---------|---------|---------|---------|---------|---------|---------|---------|----------|----------|------------|
|	Посты	|	72917	|	145833	|	218750	|	291667	|	364583	|	437500	|	510417	|	583333	|	656250	|	729167	|	802083	| 	868056    |
|	Реакции и комментарии	|	10	|	19	|	29	|	39	|	49	|	58	|	68	|	78	|	88	|	97	|	107	| 	116	      |
|	Лента	|	116667	|	233333	|	350000	|	466667	|	583333	|	700000	|	816667	|	933333	|	1050000	|	1166667	|	1283333	| 	1388889	  |
|	Поиск популярных мест	|	38889	|	77778	|	116667	|	155556	|	194444	|	233333	|	272222	|	311111	|	350000	|	388889	|	427778	| 	462963	   |
|	Лента других путешественников	|	116667	|	233333	|	350000	|	466667	|	583333	|	700000	|	816667	|	933333	|	1050000	|	1166667	|	1283333	| 	1388889	  |

### Расчет одновременных соединений

| **Месяц**         | **1 месяц** | **2 месяц** | **3 месяц** | **4 месяц** | **5 месяц** | **6 месяц** | **7 месяц** | **8 месяц** | **9 месяц** | **10 месяц** | **11 месяц** | **12 месяц** |
|-------------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|--------------|--------------|--------------|
| Кол-во соединений | 84 000      | 168 000     | 252 000     | 336 000     | 420 000     | 504 000     | 588 000     | 672 000     | 756 000     | 840 000      | 924 000      | 1 000 000    |


