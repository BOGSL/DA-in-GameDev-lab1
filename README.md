# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе №1 выполнил(а):
- Слинько Богдан Денисович
- НМТ-211507
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.

## Цель работы
Познакомиться с программными средствами для организации передачи данных между инструментами Google, Python и Unity

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python — Google-таблицы — Unity.
В ходе выполнения работы был создан проект в Google Cloud, где подключился API для работы с Google Sheets. Также был создан сервисный аккаунт. Его данные отправляются в гугл-таблицы
![image](https://user-images.githubusercontent.com/114569910/195168998-af68f14d-28aa-4f04-a007-fd09c54af873.png)
Затем был написан код на Python после настройки передачи данных. Код генерировал случайные числа, являющиеся ценами, а также рассчитывал инфляцию по этим ценам и загружал их в таблицу (код ниже)
```py
import gspread
import numpy as np

gc = gspread.service_account(filename='lab2-364110-a302ae311266.json')
sh = gc.open("lab2z2test")

x = [3, 21, 22, 34, 54, 34, 55, 67, 89, 99]
x = np.array(x)
y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)


def model(a, b, x):
    return a * x + b


def optimize(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    da = (0.1 / num) * ((prediction - y) * x).sum()
    db = (0.1 / num) * ((prediction - y).sum())
    a = a - Lr * da
    b = b - Lr * db
    return a, b


def iterate(a, b, x, y, times):
    for i in range(times):
        a, b = optimize(a, b, x, y)
        return (a, b)


a = np.random.rand(10)

b = np.random.rand(10)

Lr = 0.005
a, b = iterate(a, b, x, y, 100)
prediction = model(a, b, x)

price = np.array(a+b)*100

mon = list(range(1, 10))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        tempInf = ((price[i - 1] - price[i - 2]) / price[i - 2]) * 100
        tempInf = str(tempInf)
        tempInf = tempInf.replace('.', ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(round(price[i - 1])))
        sh.sheet1.update(('C' + str(i)), str(tempInf))
        print(tempInf)
```
## Задание 2
### В разделе «Ход работы» пошагово выполнить каждый пункт с описанием и примером реализации задачи по теме лабораторной работы.
Для выполнения следующего задания я переписал код из методических указаний, а затем видоизменил его:

### Изучить код на Python и ответить на вопросы:


### Какова роль параметра Lr? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ. В качестве эксперимента можете изменить значение параметра.
При выполнении программы считается сумма отклонений y от предположительного, в случае если бы точки считались по функции, а не были бы заданы (da,db). a и b получают значение разности самих себя с этими отклонениями, умноженными на коэффицент Lr. Lr - "сдерживающий" коэффицент, он не позволяет a и b прийти к нулю, а значит, и обнулить функцию.
## Выводы
Абзац умных слов о том, что было сделано и что было узнано.

Во время выполнения данной лабораторной работы я познакомился с платформой Unity, установил всё необходимое ПО для будущих работ, научился писать и использовать скрипты в сценах Unity, освоил навыки работы в Python в новинку, а также владею знаниями о работе в Jupiter Notepad и Anaconda.

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
