# Выбор датасетов для разработки и тестирования AutoML

## 1. Цель

Для разработки и тестирования системы, которая настраивает AutoML на основе пользовательского запроса и загруженных табличных данных, выбрано десять датасетов из разных предметных областей. Они охватывают бинарную и многоклассовую классификацию, регрессию, а также случаи, когда целевую переменную требуется сформировать или уточнить.

Выбор сделан не только по типу ML-задачи. Датасеты позволяют исследовать дисбаланс классов, пропуски и специальные значения, связи нескольких таблиц, различия train/test, временные и групповые зависимости, а также риск утечки целевой переменной.

Для каждого датасета подготовлены ноутбук с разведочным анализом данных (EDA) и Markdown-отчёт с результатами проверок, выводами и особенностями настройки AutoML.

## 2. Группы датасетов

### 2.1. Бинарная классификация

| Датасет | Таргет | Наблюдений | Обоснование выбора |
|---|---|---:|---|
| [IEEE-CIS Fraud Detection](ieee_fraud_detection/EDA_ieee_fraud_detection.md) | `isFraud` | 590 540 | Редкие мошеннические операции, разреженные признаки, частичное покрытие `identity`, отличия схем train/test и временная структура |
| [Bosch Production Line Performance](bosch_production_line_performance/EDA_bosch_production_line_performance.md) | `Response` | 1 183 747 | Редкий производственный брак, высокая размерность, структурные пропуски и измерения разных производственных станций |
| [Home Credit Default Risk](home_credit_default_risk/EDA_home_credit_default_risk.md) | `TARGET` | 307 511 | Кредитный риск, специальные значения, многочисленные связанные таблицы, неполная история и ограничения доступности признаков |
| [Olist Brazilian E-Commerce](olist_brazilian_ecommerce/EDA_olist_brazilian_ecommerce.md) | `late_delivery` (формируется из дат) | 96 470 с меткой | Прогноз просрочки доставки, создание целевой переменной, связи девяти таблиц, неоднозначные исходы и временная валидация |
| [SM Lab / Спортмастер](sm_lab_sales_funnel/EDA_sm_lab_sales_funnel.md) | `add_to_cart_flag` / `create_order_flag` | 7 000 000 | Два этапа воронки, утечка через последующие действия, повторные пользователи и несовместимость полных и сокращённых версий |
| [Т-Банк: Воронка авиабилетов](tbank_avia_funnel/EDA_tbank_avia_funnel.md) | Успешная покупка (по статусу сессии) | 38 561 сессия | Формирование исхода из событий экранов, редкие подтверждённые покупки, неоднозначные метки и зависимость сессий одного клиента |

### 2.2. Многоклассовая классификация

| Датасет | Таргет | Наблюдений | Обоснование выбора |
|---|---|---:|---|
| [Airbnb New User Bookings](airbnb_new_user_bookings/EDA_airbnb_new_user_bookings.md) | `country_destination` | 213 451 | Двенадцать классов, преобладание `NDF`, специальные значения, утечка через `date_first_booking`, сдвиг train/test и ограничения журнала сессий |

### 2.3. Регрессия

| Датасет | Таргет | Наблюдений | Обоснование выбора |
|---|---|---:|---|
| [Avito Demand Prediction](avito_demand_prediction/EDA_avito_demand_prediction.md) | `deal_probability` | 1 503 424 | Числовая цель с преобладанием нулей, тексты, пропуски, аномалии цен и временные ограничения вспомогательных таблиц |
| [Rossmann Store Sales](rossmann_store_sales/EDA_rossmann_store_sales.md) | `Sales` | 1 017 209 | Прогноз выручки, закрытые магазины, сезонность, структурные пропуски и утечка через фактический `Customers` |

### 2.4. Задача, требующая уточнения целевой переменной

| Датасет | Возможная цель | Наблюдений | Обоснование выбора |
|---|---|---:|---|
| [Т-Банк: Коммуникации в Городе](tbank_gorod_communications/EDA_tbank_gorod_communications.md) | Факт или количество заказов выбранного сервиса (`orders_amt_*`) | 118 189 | Шесть счётчиков заказов вместо единого таргета, редкие события, нерегулярные наблюдения клиентов и неизвестный горизонт прогноза |

Для Т-Банка «Коммуникации в Городе» бинарная классификация или регрессия определяется только после выбора сервиса и целевого исхода. Датасеты не объединяются между собой и не предполагают использования одной общей модели.

## 3. Какие возможности агента проверяются

| Проверяемая особенность | Датасеты | Ожидаемое действие агента |
|---|---|---|
| Определение и формирование таргета | Olist, SM Lab, Т-Банк Авиа, Т-Банк Город, остальные датасеты | Определить готовую цель, построить её из исходных полей или запросить уточнение; выбрать тип задачи |
| Дисбаланс классов и стоимость ошибок | IEEE-CIS, Bosch, Home Credit, Olist, Airbnb, SM Lab, Т-Банк Авиа | Не ограничиваться accuracy; выбирать метрику с учётом постановки и редкости положительных исходов |
| Пропуски и специальные значения | IEEE-CIS, Bosch, Home Credit, Avito, Airbnb, Olist, Т-Банк Авиа | Различать обычные пропуски, специальные коды, структурное отсутствие данных и неизвестные исходы |
| Связи таблиц и уровень наблюдения | IEEE-CIS, Bosch, Home Credit, Avito, Olist, Airbnb, Т-Банк Авиа | Определять ключи и кратность связей; агрегировать события без размножения основных наблюдений |
| Утечки и доступность признаков | Rossmann, Airbnb, SM Lab, Olist, Avito, Home Credit, Т-Банк Авиа, Т-Банк Город | Учитывать момент прогноза; исключать недоступные будущие события и признаки, раскрывающие ответ |
| Временные и групповые зависимости | IEEE-CIS, Bosch, Rossmann, Avito, Airbnb, Olist, SM Lab, оба датасета Т-Банка | Выбирать временное или групповое разбиение в соответствии со сценарием применения модели |
| Различия схем и выборок | IEEE-CIS, Home Credit, Rossmann, Avito, Airbnb, SM Lab | Проверять согласованность train/test или версий данных, не считать пересекающиеся выборки независимыми |
| Аномалии и ограничения интерпретации | Все десять | Проверять необычные значения с учётом предметной области и не объявлять неподтверждённые причины доказанными |

Эти результаты служат ориентирами для дальнейшего сравнения ручного анализа с конфигурациями, которые будет формировать AutoML-агент.

## 4. Структура и воспроизведение EDA

Для каждого датасета предусмотрена отдельная папка с ноутбуком `.ipynb` и одноимённым отчётом `.md`. Исходные данные загружаются отдельно по ссылкам в отчётах и размещаются рядом с ноутбуком; большие архивы и исходные таблицы в репозиторий не включаются.

| Папка | Ноутбук | Данные для запуска | Источник |
|---|---|---|---|
| `ieee_fraud_detection/` | [`EDA_ieee_fraud_detection.ipynb`](ieee_fraud_detection/EDA_ieee_fraud_detection.ipynb) | `ieee-fraud-detection.zip` | [Kaggle](https://www.kaggle.com/competitions/ieee-fraud-detection/data) |
| `bosch_production_line_performance/` | [`EDA_bosch_production_line_performance.ipynb`](bosch_production_line_performance/EDA_bosch_production_line_performance.ipynb) | Шесть архивов `train_*.csv.zip` и `test_*.csv.zip` | [Kaggle](https://www.kaggle.com/competitions/bosch-production-line-performance/data) |
| `home_credit_default_risk/` | [`EDA_home_credit_default_risk.ipynb`](home_credit_default_risk/EDA_home_credit_default_risk.ipynb) | Архивы с заявками и кредитными историями (`*.zip`) | [Kaggle](https://www.kaggle.com/competitions/home-credit-default-risk/data) |
| `olist_brazilian_ecommerce/` | [`EDA_olist_brazilian_ecommerce.ipynb`](olist_brazilian_ecommerce/EDA_olist_brazilian_ecommerce.ipynb) | `archive.zip` (девять CSV) | [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) |
| `sm_lab_sales_funnel/` | [`EDA_sm_lab_sales_funnel.ipynb`](sm_lab_sales_funnel/EDA_sm_lab_sales_funnel.ipynb) | `SM Lab DATASET-20261119T095343Z-1-001.zip` | [DANO 2024](https://dano.hse.ru/data2024) |
| `tbank_avia_funnel/` | [`EDA_tbank_avia_funnel.ipynb`](tbank_avia_funnel/EDA_tbank_avia_funnel.ipynb) | `dano_dataset_avia_hashed_corrected_s.xlsx` | DANO, предоставленный набор данных |
| `airbnb_new_user_bookings/` | [`EDA_airbnb_new_user_bookings.ipynb`](airbnb_new_user_bookings/EDA_airbnb_new_user_bookings.ipynb) | Шесть архивов `*.csv.zip` | [Kaggle](https://www.kaggle.com/competitions/airbnb-recruiting-new-user-bookings/data) |
| `avito_demand_prediction/` | [`EDA_avito_demand_prediction.ipynb`](avito_demand_prediction/EDA_avito_demand_prediction.ipynb) | `train.zip`, `test.zip`, `*_active.zip`, `periods_*.zip` | [Kaggle](https://www.kaggle.com/competitions/avito-demand-prediction/data) |
| `rossmann_store_sales/` | [`EDA_rossmann_store_sales.ipynb`](rossmann_store_sales/EDA_rossmann_store_sales.ipynb) | `rossmann-store-sales.zip` | [Kaggle](https://www.kaggle.com/competitions/rossmann-store-sales/data) |
| `tbank_gorod_communications/` | [`EDA_tbank_gorod_communications.ipynb`](tbank_gorod_communications/EDA_tbank_gorod_communications.ipynb) | `коммуникации по Городу.csv` | DANO, предоставленный набор данных |

Для запуска используются Python, Jupyter, `pandas`, `numpy`, `matplotlib`; в отдельных ноутбуках также используется `seaborn`. Точный перечень исходных файлов и ссылки на них приведены в соответствующих `.md`.

Ноутбуки выполняются последовательно сверху вниз в папке датасета. Расчёты и графики находятся в `.ipynb`, а результаты исследования и выводы - в `.md`.

## 5. Результат этапа

Для десяти датасетов исследованы целевые переменные и возможные типы задач, качество и структура данных, зависимости наблюдений, различия выборок и риски утечек. Отдельно выделены случаи, когда до обучения необходимо сформировать метку, определить горизонт прогноза или подтвердить доступность признаков. Полученные результаты составляют основу для дальнейшего сравнения ручного анализа с конфигурациями AutoML-агента.
