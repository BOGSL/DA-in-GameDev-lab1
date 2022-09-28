# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #1 выполнил(а):
- Слинько Богдан Денисович
- НМТ-211507
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | # | 60 |
| Задание 2 | # | 20 |
| Задание 3 | # | 20 |

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
Ознакомиться с основными операторами зыка Python на примере реализации линейной регрессии.

## Задание 1
### Написать программы Hello World на Python и Unity
Для написания однострочной программы, которая выводит надпись "Hello World", использовалось приложение Jupiter Notebook. Скриншот результата выполнения программы:
![image](https://user-images.githubusercontent.com/114569910/192793971-332f336b-c098-4a17-bbb8-cbe5fe7a5e66.png) 

Код программы:
```py

print('Hello World')

```

Был создан 2D проект в Unity. В проект добавлен скрипт HelloWorld.cs (код приведен ниже). Он прикреплен к камере
```py

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class HelloWorld : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        Debug.Log("Hello World");
    }

    // Update is called once per frame
    void Update()
    {
        
    }
}

```
Скриншот результата запуска сцены:
![image](https://user-images.githubusercontent.com/114569910/192810969-7fb33f96-d8cf-4544-bce4-611e620ad8a8.png)

## Задание 2
### В разделе «Ход работы» пошагово выполнить каждый пункт с описанием и примером реализации задачи по теме лабораторной работы.
Для выполнения следующего задания я переписал код из методических указаний, а затем видоизменил его:
```py

import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

x=[3,21,22,34,54,34,55,67,89,99]
x=np.array(x)
y=[2,22,24,65,79,82,55,130,150,199]
y=np.array(y)

plt.scatter(x,y)


def model (a,b,x):
    return a*x+b

def loss_function(a,b,x,y):
    num = len(x)
    prediction = model(a,b,x)
    return (0.5/num)*(np.square(prediction-y)).sum()

def optimize(a,b,x,y):
    num = len(x)
    prediction = model(a,b,x)
    da=(0.1/num)*((prediction-y)*x).sum()
    db=(0.1/num)*((prediction-y).sum())
    a=a-Lr*da
    b=b-Lr*db
    return a,b

def iterate(a,b,x,y,times):
    for i in range(times):
        a,b = optimize(a,b,x,y)
        return(a,b)
    
a=np.random.rand(1)
print(a)
b=np.random.rand(1)
print(b)
Lr = 0.0000001
a,b = iterate(a,b,x,y,100)
prediction = model(a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)

```
При выполнении кода появляется график:

![image](https://user-images.githubusercontent.com/114569910/192820717-161683a2-6e30-4cdc-8440-d88558281f4b.png)

### Изучить код на Python и ответить на вопросы:
- Должна ли величина loss стремиться к нулю при изменении исходных данных? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ.

Величина Loss будет равна нулю, если входной y всегда будет равен y[i]=ax[i]+b. Введем равенство: y[i]=ax[i]+b=y(!). Если разность между y(1) и y[i] будет уменьшаться, то loss тоже будет уменьшаться, а это означает стремление к нулю. Чтоб доказать это, были изменены входные данные таким образом, что разница между ожидаемыми и входными значениями уменьшалась, а также была равна нулю. И для удобства были установлены коэффиценты a и b = 1. Далее представлен код:
```py

import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

x=[1,2,3,4,5,6,7,8,9,10]
x=np.array(x)
y=[70,60,50,40,30,7,8,9,10,11]
y=np.array(y)

plt.scatter(x,y)


def model (a,b,x):
    return a*x+b

def loss_function(a,b,x,y):
    num = len(x)
    prediction = model(a,b,x)
    return (0.5/num)*(np.square(prediction-y)).sum()

def optimize(a,b,x,y):
    num = len(x)
    prediction = model(a,b,x)
    da=(0.1/num)*((prediction-y)*x).sum()
    db=(0.1/num)*((prediction-y).sum())
    a=a-Lr*da
    b=b-Lr*db
    return a,b

def iterate(a,b,x,y,times):
    for i in range(times):
        a,b = optimize(a,b,x,y)
        return(a,b)
    
a=1
print(a)
b=1
print(b)
Lr = 0.000001
a,b = iterate(a,b,x,y,100)
prediction = model(a,b,x)
loss = loss_function(a,b,x,y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)

``` 
При выполнении программы генерируется график:

![image](https://user-images.githubusercontent.com/114569910/192839090-beb964be-ff52-4b49-93cf-ad54c46e6618.png)

### Какова роль параметра Lr? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ. В качестве эксперимента можете изменить значение параметра.
При выполнении программы считается сумма отклонений y от предположительного, в случае если бы точки считались по функции, а не были бы заданы (da,db). a и b получают значение разности самих себя с этими отклонениями, умноженными на коэффицент Lr. Lr - "сдерживающий" коэффицент, он не позволяет a и b прийти к нулю, а значит, и обнулить функцию.
## Выводы
Абзац умных слов о том, что было сделано и что было узнано.
Во время выполнения данной лабораторной работы я познакомился с платформой Unity, установил всё необходимое ПО для будущих работ, научился писать и использовать скрипты в сценах Unity, освоил навыки работы в Python в новинку, а также владею знаниями о работе в Jupiter Notepad и Anaconda.

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
