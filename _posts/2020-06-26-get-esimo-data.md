---
toc: true
layout: post
title: Скачиваем данные об уровнях воды из ЕСИМО
description: С использованием python и библиотек requests и pandas
categories: [tips&tricks]
---

## Что такое ЕСИМО?

[**ЕСИМО**](http://esimo.ru/portal/) -- единая государственная система информации об обстановке в Мировом океане. Несмотря на название, содержит в себе базы данных наблюдений за многими параметрами окружающей среды ([посмотреть что есть](http://esimo.ru/portal/portal/esimo-user/data)).

## Скачать данные вручную

Представление баз данных в **ЕСИМО** примерно одинаковое. Здесь и далее в качестве примера будем использовать оперативную базу данных измеренных уровней воды на гидропостах [RU_RIHMI-WDC_1325_1](http://esimo.ru/dataview/viewresource?resourceId=RU_RIHMI-WDC_1325).

Веб-интерфейс **ЕСИМО** для работы с базами данных монструозный, но дело свое делает -- можно отсортировать, сделать выборку по дате.

На скриншоте показаны базовые элементы и какую кнопку нажать для скачивания. Я предпочитаю скачивать данные в `csv` -- этот формат является более универсальным и с ним легче потом работать, чем с `xls`.

![]({{ site.baseurl }}/images/2020-06-25-ESIMO.png "ЕСИМО в действии")

{% include info.html text="Даже если вы выберете один день и один пост, все равно скачаются все доступные на данный момент данные" %}

Недостатки такого варианта скачивания данных:

* Все делается руками,
* Так как данные доступны только за последние 7 дней, то заходить за оперативной информацией нужно регулярно.

## Код на python для автоматической загрузки данных

Чтобы не травмировать себя ежедневным общением с  **ЕСИМО** нужно написать немного кода. Будем использовать стандартную библиотеку `python`, а также библиотеки `request` (для отправки запросов на сервер) и `pandas` (для обработки полученных данных).

```python
# Импортируем необходимые библиотеки
import io
import datetime
import requests
import pandas as pd

# Корневой адрес к которому мы будем делать запрос
base_url = 'http://esimo.ru/dataview/getresourceexport' 

# Идентификатор нашей базы данных
db_id = 'RU_RIHMI-WDC_1325_1'

# Определяем даты
# Будем скачивать данные за последнюю неделю

# Сегодняшняя дата
today = datetime.date.today()
# Запрос устроен таким образом, что отсчет начинается с завтрашнего дня
tomorrow = (today + datetime.timedelta(days=1)).strftime("%Y-%m-%d")
week_back = (today - datetime.timedelta(days=6)).strftime("%Y-%m-%d")

# Задаем параметры запроса
params = {'format': 'csv',

          'resourceid': db_id,

          'filter': f"(((m4400 between '{week_back}' and '{tomorrow}') or (CAST(m4400 as text) like \'%2099-12-31%\')))"

         }

# Отправляем POST запрос на сервер
response = requests.post(url=base_url, data=params)

# Считываем пришедшие данные
raw_data = response.content

# Получаем данные в сыром виде, поэтому требуется 
# дополнительная обработка с помощью pandas
data = pd.read_table(io.StringIO(raw_data.decode('utf-8')),
                     header=0,
                     sep='","',
                     usecols=[0, 1, 2, 3, 4, 5, 7],
                     names=["id", 
                            "hydropost_name", 
                            "latitude", 
                            "longitude", 
                            "timestamp", 
                            "Level", 
                            "Level_mean"],
                     engine='python')

data["id"] = data["id"].apply(lambda x: str(x[1:]))
data = data.drop_duplicates()

# Сохраняем данные
data.to_csv(f"~/Downloads/esimo_{today.strftime('%Y%m%d')}.csv")

```

[Попробовать в Google Colab](https://colab.research.google.com/drive/1-SUHELtPx_oEDDEzdzmf6K7Jgh2Ll_fw?usp=sharing).


Посмотрим на данные с гидропоста в Новокузнецке:

`data[data["id"]=="10240"]`

![]({{ site.baseurl }}/images/2020-06-25-table.png "Данные по гидропосту в Новокузнецке")


Теперь у нас в руках универсальный инструмент получения оперативных данных об измеренных уровнях воды. 

Представленный фрагмент кода я использовал для создания системы краткосрочного прогнозирования уровня воды [OpenLevels](https://openlevels.github.io/). Надеюсь, что и вам он пригодится.


## Заключение

Несомненно, Росгидромету есть куда стремиться в улучшении доступа к результатам мониторинговых наблюдений на сети метеорологических станций и гидрологических постов. **ЕСИМО** в зубы не смотрят, но хотя бы посмотрите как обстоят дела в США:

* [Гидрологические данные](https://waterservices.usgs.gov/),
* [Метеорологические данные](https://www.weather.gov/).

Отдельно отмечу оторванность нашей мониторинговой сети от глобальной сети обмена гидрологической информации [WHOS](http://www.wmo.int/pages/prog/hwrp/chy/whos/index.php), созданной в рамках Всемирной метеорологической организации. 

Такая оторванность имеет тяжелые последствия. Например, в нашей [недавней работе](https://www.tandfonline.com/doi/full/10.1080/02626667.2020.1762886) мы показали, что имея доступ к национальному архиву наблюдений за речным стоком, можно существенно улучшить качество регионального гидрологического реанализа. К сожалению, отсутствие этих данных в открытом доступе ограничивает эффективность моделей, на основе которых мы можем оценивать располагаемые водные ресурсы, а также возможную динамику их изменения в будущем. 

Но эта уже совсем другая история.