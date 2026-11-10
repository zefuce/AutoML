# Выбор датасетов для разработки и тестирования AutoML

## 1. Цель

Для разработки и тестирования системы, которая настраивает AutoML на основе пользовательского запроса и загруженных табличных данных, выбрано девять датасетов из разных предметных областей. Они охватывают бинарную и многоклассовую классификацию, регрессию, а также разные требования к подготовке данных, выбору метрик и валидации.

Выбор сделан не только по типу ML-задачи. Датасеты содержат разные трудности, которые необходимо учитывать при формировании конфигурации AutoML: пропуски, дисбаланс классов, категориальные коды, утечки целевой переменной и зависимые наблюдения.

Для каждого датасета подготовлены ноутбук с разведочным анализом данных (EDA) и Markdown-отчёт с проверками, выводами и ориентирами для будущего AutoML-агента. 

## 2. Группы датасетов

### 2.1. Бинарная классификация

| Датасет | Таргет | Наблюдений | Обоснование выбора |
|---|---|---:|---|
| [Bank Marketing](bank_marketing/bank_marketing.md) | `y` | 45 211 | Дисбаланс классов, `unknown`, специальный код `pdays = -1`, утечка через `duration` при прогнозе до звонка |
| [Default of Credit Card Clients](default_credit/default_credit.md) | `default payment next month` | 30 000 | История платежей, числовые коды категорий, неоднозначные статусы платежей и разная стоимость ошибок |
| [APS Failure](aps_failure/aps_failure.md) | `class` | 60 000 | Редкие отказы, многочисленные пропуски, обезличенные признаки и заданная стоимость FP и FN |
| [Online Shoppers Purchasing Intention](online_shoppers/online_shoppers.md) | `Revenue` | 12 330 | Дисбаланс, повторяющиеся записи, накопленные показатели сессии и риск утечки через `PageValues` |

### 2.2. Многоклассовая классификация

| Датасет | Таргет | Наблюдений | Обоснование выбора |
|---|---|---:|---|
| [Predict Students' Dropout and Academic Success](student_dropout/student_dropout.md) | `Target` | 4 424 | Три исхода обучения, категориальные коды, доступность академических результатов первого и второго семестров |
| [Diabetes 130-US Hospitals](diabetes/diabetes.md) | `readmitted` | 101 766 | Три класса, повторные госпитализации одних пациентов, пропуски `?`, признаки, доступные только к выписке |

### 2.3. Регрессия

| Датасет | Таргет | Наблюдений | Обоснование выбора |
|---|---|---:|---|
| [Ames Housing](ames_housing/ames_housing.md) | `SalePrice` | 1 460 | Числовые и категориальные признаки, структурные пропуски, необычные сочетания площади и цены |
| [Bike Sharing](bike_sharing/bike_sharing.md) | `cnt` | 17 379 | Почасовые наблюдения, отсутствующие часы, утечка через `casual` и `registered`, временное разбиение |
| [Air Quality](air_quality/air_quality.md) | `CO(GT)` | 9 357 | Специальное обозначение пропусков `-200`, отсутствие части значений таргета, временная структура и сенсорные измерения |

Такой состав позволяет проверять конфигурацию AutoML на задачах разного типа и сложности. Датасеты не объединяются в одну таблицу и не предполагают использования одной общей модели.

## 3. Какие возможности агента проверяются

| Проверяемая особенность | Датасеты | Ожидаемое действие агента |
|---|---|---|
| Определение таргета и типа задачи | Все девять | Определить или уточнить целевую переменную; выбрать классификацию или регрессию |
| Дисбаланс классов и стоимость ошибок | Bank Marketing, Default Credit, APS Failure, Online Shoppers | Не ориентироваться только на accuracy; согласовать метрику с задачей пользователя |
| Пропуски и специальные значения | Bank Marketing, APS Failure, Ames Housing, Air Quality, Diabetes | Различать `NaN`, специальные коды и отсутствие объекта; выбирать обработку с учётом смысла признака |
| Категориальные признаки с числовыми кодами | Default Credit, Student Dropout, Bike Sharing, Diabetes | Не принимать коды категорий за непрерывные измерения |
| Утечка и доступность признаков | Bank Marketing, Bike Sharing, Online Shoppers, Student Dropout, Diabetes | Уточнять момент прогноза, предупреждать о недоступных признаках и исключать утечки |
| Временные и групповые зависимости | Bike Sharing, Air Quality, Ames Housing, Diabetes | Учитывать время при разбиении; не разделять госпитализации одного пациента между обучением и проверкой |
| Ошибки, выбросы и редкие значения | Все девять | Проверять предметный смысл необычных значений; не удалять строки только по формальному правилу IQR |

Это предварительные ориентиры, которые будут использоваться для оценки того, насколько корректно агент интерпретирует запрос и настраивает AutoML для конкретных данных.

## 4. Структура и воспроизведение EDA

Каждая папка содержит ноутбук `.ipynb`, отчёт `.md` и распакованные исходные данные, необходимые для запуска. Оригинальные ZIP-архивы в репозиторий не добавляются, чтобы не дублировать данные.

| Папка | Ноутбук | Данные для запуска | Источник |
|---|---|---|---|
| `bank_marketing/` | [`bank_marketing.ipynb`](bank_marketing/bank_marketing.ipynb) | `bank-full.csv` | [UCI](https://archive.ics.uci.edu/dataset/222/bank+marketing) |
| `default_credit/` | [`default_credit.ipynb`](default_credit/default_credit.ipynb) | `default of credit card clients.xls` | [UCI](https://archive.ics.uci.edu/dataset/350/default+of+credit+card+clients) |
| `aps_failure/` | [`aps_failure.ipynb`](aps_failure/aps_failure.ipynb) | `aps_failure_training_set.csv`, `aps_failure_test_set.csv` | [UCI](https://archive.ics.uci.edu/dataset/421/aps+failure+at+scania+trucks) |
| `ames_housing/` | [`ames_housing.ipynb`](ames_housing/ames_housing.ipynb) | `train.csv`, `data_description.txt` | [Kaggle](https://www.kaggle.com/competitions/house-prices-advanced-regression-techniques/data) |
| `bike_sharing/` | [`bike_sharing.ipynb`](bike_sharing/bike_sharing.ipynb) | `hour.csv` | [UCI](https://archive.ics.uci.edu/dataset/275/bike+sharing+dataset) |
| `student_dropout/` | [`student_dropout.ipynb`](student_dropout/student_dropout.ipynb) | `data.csv` | [UCI](https://archive.ics.uci.edu/dataset/697/predict+students+dropout+and+academic+success) |
| `air_quality/` | [`air_quality.ipynb`](air_quality/air_quality.ipynb) | `AirQualityUCI.csv` | [UCI](https://archive.ics.uci.edu/dataset/360/air+quality) |
| `online_shoppers/` | [`online_shoppers.ipynb`](online_shoppers/online_shoppers.ipynb) | `online_shoppers_intention.csv` | [UCI](https://archive.ics.uci.edu/dataset/468/online+shoppers+purchasing+intention+dataset) |
| `diabetes/` | [`diabetes.ipynb`](diabetes/diabetes.ipynb) | `diabetic_data.csv`, `IDS_mapping.csv` | [UCI](https://archive.ics.uci.edu/dataset/296/diabetes+130-us+hospitals+for+years+1999-2008) |

Для запуска необходимы Python, Jupyter, `pandas`, `numpy`, `matplotlib` и `seaborn`. Для чтения исходного XLS в Default Credit дополнительно используется `xlrd`.

Ноутбуки выполняются сверху вниз в папке соответствующего датасета. Расчёты и графики сохраняются в `.ipynb`, а выводы по каждому исследованию приведены в одноимённом `.md`.

## 5. Результат этапа

Для девяти датасетов определены целевые переменные, исходные типы задач, особенности данных, возможные утечки и ограничения применения признаков. Эти результаты составляют основу для дальнейшего сравнения ручного анализа с конфигурациями, которые будет формировать AutoML-агент по пользовательским запросам.