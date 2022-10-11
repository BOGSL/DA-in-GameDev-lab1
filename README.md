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

gc = gspread.service_account(filename='reference-bee-365214-98c171297ca4.json')
sh = gc.open("UnitySheets")

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

Таблица была заполнена ценами и значениями в 11 строк:

![image](https://user-images.githubusercontent.com/114569910/195170472-88836d14-b119-4fa3-96e8-f52084affbe1.png)

Далее был создан там 3D проект в Unity. Стоимость проекта увеличилась на пустой объект и два пакета с библиотеками и звуками. Затем был создан скрипт, получающий из гугл-таблицы значения и анализирующий их. Теперь воспроизводящий звук выводит значение параметра (код ниже)
```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1Z0GWJ17lsmWgwZCaj2fMoeFlYF3tHAvMOQhszr9YLRE/values/Лист1?key=AIzaSyDtPtt3x6uhC2e_yQHa3fuhu9HwAbmtXtE");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
```
К пустому объекту был прикреплен записанный скрипт. Также он получил набор звуков.

![image](https://user-images.githubusercontent.com/114569910/195173639-23f65ebf-43a1-4df6-b506-8f3c4270eb4f.png)

Скриншот результата сцены:

![image](https://user-images.githubusercontent.com/114569910/195173723-edcee45b-7323-4d8d-847a-390d21525b72.png)

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
