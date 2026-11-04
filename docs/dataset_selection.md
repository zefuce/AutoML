# Выбор датасетов для разработки и тестирования AutoML-системы

## 1. Цель

Подобрать набор табличных датасетов для разработки и тестирования системы автоматической конфигурации ML-пайплайнов на основе запросов на естественном языке. Набор должен покрывать разные ML-задачи, типы признаков, особенности подготовки данных, способы валидации и ограничения вычислительных ресурсов.

**Хранилище датасетов (Google Диск):** [Открыть папку с датасетами](https://drive.google.com/drive/folders/1z19UQctlvwtJoeKPuO8WFF-c9DAvu0bv?usp=drive_link)

## 2. Подобранные датасеты

| № | Датасет | Зачем выбран / что проверяет | Результат аудита |
|---|---|---|---|
| 1 | [Iris](https://archive.ics.uci.edu/dataset/53/iris) | Простая многоклассовая классификация; быстрый тест пайплайна. | Есть различия и дубликаты между версиями файла. |
| 2 | [Adult](https://archive.ics.uci.edu/dataset/2/adult) | Смешанные признаки и пропуски; бинарная классификация и предобработка. | Обработать `?` и метки в test-файле. |
| 3 | [Bank Marketing](https://archive.ics.uci.edu/dataset/222/bank+marketing) | Дисбаланс классов и выбор метрик. | Исключить `duration` при прогнозировании до звонка. |
| 4 | [Dry Bean](https://archive.ics.uci.edu/dataset/602/dry+bean+dataset) | Семь классов; многоклассовые метрики. | 68 полных дубликатов. |
| 5 | [Concrete Strength](https://archive.ics.uci.edu/dataset/165/concrete+compressive+strength) | Небольшая задача регрессии; MAE, RMSE, R². | Повторы рецептур требуют осторожной валидации. |
| 6 | [California Housing](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.fetch_california_housing.html) | Регрессия на более крупной таблице; ограничения времени обучения. | 20 640 строк, 8 признаков, без пропусков и дубликатов. У таргета 965 значений на верхней границе. |
| 7 | [House Prices (Ames)](https://www.openml.org/d/42165) | Смешанные признаки и много пропусков; сложная регрессия. | Убрать `Id`, различать отсутствие объекта и неизвестное значение. |
| 8 | [Bike Sharing](https://archive.ics.uci.edu/dataset/275/bike+sharing+dataset) | Данные с временной структурой; хронологическая валидация. | `cnt = casual + registered`, составляющие исключить из признаков. |
| 9 | [Car Evaluation](https://archive.ics.uci.edu/dataset/19/car+evaluation) | Полностью категориальные признаки; кодирование категорий. | Лучше для базового теста, чем для итогового benchmark. |
| 10 | [Mushroom](https://archive.ics.uci.edu/dataset/73/mushroom) | Бинарная классификация, категориальные признаки и специальные пропуски. | `?` в `stalk-root`, `veil-type` константен. |
| 11 | [APS Failure](https://archive.ics.uci.edu/dataset/421/aps+failure+at+scania+trucks) | Сильный дисбаланс и разная стоимость ошибок. | 60 000/16 000 записей, 170 признаков; много `na`, константный `cd_000`, сильный дисбаланс. |
| 12 | [Covertype](https://archive.ics.uci.edu/dataset/31/covertype) | Крупный набор; многоклассовая классификация и ресурсные ограничения. | 581 012 записей, использовать отдельно для нагрузочных тестов. |
| 13 | [Human Activity Recognition](https://archive.ics.uci.edu/dataset/240/human+activity+recognition+using+smartphones) | Много признаков; валидация с разделением по участникам. | 7 352/2 947 записей, 561 признак; участники train/test не пересекаются, названия некоторых признаков повторяются. |
| 14 | [Energy Efficiency](https://archive.ics.uci.edu/dataset/242/energy+efficiency) | Два возможных таргета; проверка определения целевой переменной. | Не включать второй таргет в признаки без обоснования. |
| 15 | [Wine Quality](https://archive.ics.uci.edu/dataset/186/wine+quality) | Регрессия либо классификация в зависимости от запроса. | Много дубликатов. |
| 16 | [Air Quality](https://archive.ics.uci.edu/dataset/360/air+quality) | Нестандартные пропуски и временная структура. | `-200`, десятичная запятая, пустые строки; сложный сценарий. |
| 17 | [Online Shoppers](https://archive.ics.uci.edu/dataset/468/online+shoppers+purchasing+intention+dataset) | Бинарная классификация со смешанными признаками и дисбалансом. | Числовые коды категорий и возможная зависимость от момента прогноза. |
| 18 | [Diabetes 130 Hospitals](https://archive.ics.uci.edu/dataset/296/diabetes+130-us+hospitals+for+years+1999-2008) | Сложная предобработка и групповая валидация. | Пропуски, повторы пациентов, чувствительные данные. |

**Для основной разработки** использовать простые наборы и датасеты со смешанными признаками (Iris, Adult, Dry Bean, Concrete Strength, Ames, Car Evaluation и другие). **Для дополнительных проверок** — Bike Sharing и Air Quality (временная валидация), Diabetes и HAR (групповая валидация), Covertype и APS Failure (нагрузка, дисбаланс, стоимость ошибок).
