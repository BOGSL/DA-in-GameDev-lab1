# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил(а):
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
Познакомиться с програмными средствами для создания cистемы машинного обучения и ее интеграции в Unity.

## Постановка задачи.
В данной лабораторной работе мы создадим ML-агент. Далее будем тренировать нейросеть. Цель нейросети - управление шаром. Цель шара - оставаясь на плоскости, находить кубик, смещающийся в заданном случайном диапазоне координат.

## Задание 1
### Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity.
-	Создаем новый пустой 3D проект на Unity.
-	Скачиваем папку с ML агентом. 

![image](https://user-images.githubusercontent.com/114569910/197943922-8c1c3fcd-d4b5-43f5-94f8-aba7a1b67846.png)
- В созданный проект добавляем ML Agent, выбрав Window - Package Manager - Add Package from disk. Последовательно добавляем .json – файлы:
	ml-agents-release_19 / com,unity.ml-agents / package.json
	ml-agents-release_19 / com,unity.ml-agents.extensions / package.json
  
![image](https://user-images.githubusercontent.com/114569910/197944844-45e2d5d1-c8d9-4b1a-9061-52653348d942.png)
-	Затем запускаем Anaconda Prompt для возможности запуска команд через консоль.

![image](https://user-images.githubusercontent.com/114569910/197945025-c1e56b8a-fdf4-45bf-a23d-255f0713c980.png)
- Далее пишем серию команд для создания и активации нового ML-агента, а также для скачивания необходимых библиотек:
- `mlagents 0.28.0`

![image](https://user-images.githubusercontent.com/114569910/197945245-e580fd81-b797-4056-a015-35b56c177296.png)
- `torch 1.7.1`

![image](https://user-images.githubusercontent.com/114569910/197945538-f4b228ed-f5d3-4f8e-8acf-682a2f2f1052.png)
- Создаем на сцене плоскость, куб и сферу. Далее создаем простой C# скрипт и подключаем его к сфере:

![image](https://user-images.githubusercontent.com/114569910/197945817-ef8f180b-e340-43c3-b97f-d1928dbf0d7d.png)
- В скрипт-файл RollerAgent.cs добавляем код:

```C#

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    // Start is called before the first frame update
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

        if(distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }

}

```
- Объекту «сфера» добавляем компоненты Rigidbody, Decision Requester, Behavior Parameters. Настраиваем их.

![image](https://user-images.githubusercontent.com/114569910/197946384-746b3a43-4ddd-4d06-8876-46061d4be516.png)
- В корень проекта добавляем файл конфигурации нейронной сети.

![image](https://user-images.githubusercontent.com/114569910/197946441-b6d38a86-00a3-4dfa-9d97-1bd36343e79b.png)
- Запускаем ml-агент

![image](https://user-images.githubusercontent.com/114569910/197946480-77590d03-660d-4a35-908e-24b89877a747.png)
- Делаем 3, 9, 27 копий модели «Плоскость-Сфера-Куб», запускаем симуляцию сцены.

![image](https://user-images.githubusercontent.com/114569910/197946593-045279dd-6970-4f7c-b290-7c35d7fa373f.png)
- После завершения обучения проверяем работу модели.

## Задание 2
### 

## Задание 3
### 
При выполнении программы считается сумма отклонений y от предположительного, в случае если бы точки считались по функции, а не были бы заданы (da,db). a и b получают значение разности самих себя с этими отклонениями, умноженными на коэффицент Lr. Lr - "сдерживающий" коэффицент, он не позволяет a и b прийти к нулю, а значит, и обнулить функцию.
## Выводы
Абзац умных слов о том, что было сделано и что было узнано.

Во время выполнения данной лабораторной работы я познакомился с платформой Unity, установил всё необходимое ПО для будущих работ, научился писать и использовать скрипты в сценах Unity, освоил навыки работы в Python в новинку, а также владею знаниями о работе в Jupiter Notepad и Anaconda.

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
