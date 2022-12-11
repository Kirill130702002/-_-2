# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #1 выполнил(а):
- Упаев Кирилл Анатольевич 
- РИ-210943
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
- ✨Magic ✨

## Цель работы
Ознакомиться с основными операторами зыка Python на примере реализации линейной регрессии.

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity.
Ход работы: 
1. Я создал google таблицу и добавил пользователя, а после написал код на python для заполнения ячеек нужными данными в таблице
```py
import gspread
import numpy as np

gc = gspread.service_account(filename='unitydatascince-370917-682594e4a037.json')
sh = gc.open('UnitySheets')
price = np.random.randint(2000, 10000, 11)
mon = list(range(1, 11))

i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        temp_inf = ((price[i - 1] - price[i - 2]) / price[i - 2]) * 100
        temp_inf = str(temp_inf)
        temp_inf = temp_inf.replace('.', ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(price[i - 1]))
        sh.sheet1.update(('C' + str(i)), str(temp_inf))
        print(temp_inf)
```

2. Результаты выполнения в простанстве имен main.py и заполненная автоматическими этими же данными google таблица:
![image](https://user-images.githubusercontent.com/104893843/206854024-0d545429-7528-4524-af95-a0ef5e95e7b9.png)
![image](https://user-images.githubusercontent.com/104893843/206854035-720ce92c-ce19-4340-b209-3bd78f08a02b.png)
![image](https://user-images.githubusercontent.com/104893843/206920345-150cae1c-ccb8-44c1-a0a8-97ecc985cda4.png)

После этого был написанн C# скрипт для юнити чтобы подгрузить данные из гугл таблиц в юнити и связать это с звуком в зависимости от данных.

Код записанный в скрипте С№:
```c#
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
    private Dictionary<string, float> dataSet = new Dictionary<string, float>();
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

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] <= 100 & statusStart == false & i != dataSet.Count)
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
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/11OrVsuBftaqx-aVzEqo8SA8FyxlovmJQdGOqDjuu2VE/values/Лист1?key=AIzaSyCkKLOv5Wvmfpkco4Bp-LfoCPYLK3YyldU");
        yield return curentResp.SendWebRequest();
        string rowResp = curentResp.downloadHandler.text;
        var rowJson = JSON.Parse(rowResp); 
        foreach (var itemRowJson in rowJson["values"]) 
        {
            var parseJson = JSON.Parse(itemRowJson.ToString());
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
Результаты выполнения скрипта:
![image](https://user-images.githubusercontent.com/104893843/206923040-8adfb059-bbd5-4564-8fc8-212d35e0092e.png)



## Задание 2
### Сделать подгрузку результатов 1 лабы в гугл таблицы.
Файл 1 лабораторной работы был модифицирован и были сделанны 4 измерения на 1, 10, 100, 1000 итераций после чего данные об A, B, LOSS были внесенны в таблицу


## Задание 3



### Выводы

