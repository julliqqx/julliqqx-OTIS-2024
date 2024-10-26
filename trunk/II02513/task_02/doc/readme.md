<p align="center"> Министерство образования Республики Беларусь</p>
<p align="center">Учреждение образования</p>
<p align="center">“Брестский Государственный технический университет”</p>
<p align="center">Кафедра ИИТ</p>
<br><br><br><br><br><br><br>
<p align="center">Лабораторная работа №2</p>
<p align="center">По дисциплине “Общая теория интеллектуальных систем”</p>
<p align="center">Тема: “ПИД-регуляторы”</p>
<br><br><br><br><br>
<p align="right">Выполнил:</p>
<p align="right">Студент 2 курса</p>
<p align="right">Группы ИИ-26</p>
<p align="right">Рубцов Д.А.</p>
<p align="right">Проверил:</p>
<p align="right">Иванюк Д. С.</p>
<br><br><br><br><br>
<p align="center">Брест 2024</p>

---

# Общее задание #
1. Написать отчет по выполненной лабораторной работе №1 в .md формате (readme.md) и с помощью запроса на внесение изменений (**pull request**) разместить его в следующем каталоге: **trunk\ii0xxyy\task_02\doc** (где **xx** - номер группы, **yy** - номер студента, например **ii02302**).
2. Исходный код написанной программы разместить в каталоге: **trunk\ii0xxyy\task_02\src**.

# Задание #
Задание. На C++ реализовать программу, моделирующую рассмотренный выше ПИД-регулятор. В качестве объекта управления использовать математическую модель, полученную в предыдущей работе. В отчете также привести графики для разных заданий температуры объекта, пояснить полученные результаты.
---
# Код программы: #
```C++    
#include <iostream>
#include <vector>
#include <cmath>
#include <iomanip>
using namespace std;

// Коэффициенты системы
double a, b, c, d;
// Параметры системы
double усиление, врем_пост_0, врем_задерж, врем_пост, жел_выход;
// Параметры регулятора
double рег_0, рег_1, рег_2;

void нелинейная_система() {
    // Начальные параметры
    const short int нач_выход = 2;
    vector<double> выход = { нач_выход, нач_выход };
    double упр_сигнал = 1.0;
    vector<double> ошибка = { жел_выход - нач_выход, жел_выход - нач_выход };
    vector<double> пред_упр_сигнал = {упр_сигнал, упр_сигнал};

    while (abs(жел_выход - выход.back()) > 0.01) {
        ошибка.push_back(жел_выход - выход.back());
        упр_сигнал = пред_упр_сигнал.back() + рег_0 * ошибка.back() + рег_1 * ошибка[ошибка.size() - 2] + рег_2 * ошибка[ошибка.size() - 3];
        выход.push_back(a * выход.back() - b * выход[выход.size() - 2] + c * упр_сигнал + d * sin(пред_упр_сигнал.back()));
        пред_упр_сигнал.push_back(упр_сигнал);
    }

    cout << setfill('-') << setw(55) << "-" << endl;
    cout << setfill(' ');
    cout << "| " << setw(4) << "Шаг" << " | " 
         << setw(10) << "Выход" << " | " 
         << setw(10) << "Ошибка" << " | " 
         << setw(15) << "Упр. сигнал" << " |" << endl;
    cout << setfill('-') << setw(55) << "-" << endl;
    cout << setfill(' ');

    for (int i = 0; i < выход.size(); i++) {
        cout << "| " << setw(4) << i + 1 << " | "
             << fixed << setprecision(4)
             << setw(10) << выход[i] << " | "
             << setw(10) << ошибка[i] << " | "
             << setw(15) << пред_упр_сигнал[i] << " |" << endl;
    }
    cout << setfill('-') << setw(55) << "-" << endl;
}

int main() {
    setlocale(LC_ALL, "RU");
    cout << "Введите коэффициенты системы:\n";
    cout << "a: "; cin >> a;
    cout << "b: "; cin >> b;
    cout << "c: "; cin >> c;
    cout << "d: "; cin >> d;

    cout << "Введите параметры системы:\n";
    cout << "усиление: "; cin >> усиление;
    cout << "врем_пост_0: "; cin >> врем_пост_0;
    cout << "врем_задерж: "; cin >> врем_задерж;
    cout << "врем_пост: "; cin >> врем_пост;
    cout << "жел_выход (желаемое значение выхода): "; cin >> жел_выход;

    рег_0 = усиление * (1 + (врем_задерж / врем_пост_0));
    рег_1 = -усиление * (1 + 2 * (врем_задерж / врем_пост_0) - (врем_пост_0 / врем_пост));
    рег_2 = усиление * (врем_задерж / врем_пост_0);

    нелинейная_система();
    return 0;
}
```
Вывод:
```
Введите коэффициенты системы:
a: 1
b: 0.5
c: 0.9
d: 0.1
Введите параметры системы:
усиление: 1
врем_пост_0: 1
врем_задерж: 1
врем_пост: 1
жел_выход (желаемое значение выхода): 10
-------------------------------------------------------
| Шаг | Выход | Ошибка | Упр. сигнал |
-------------------------------------------------------
|    1 |     2.0000 |     8.0000 |          1.0000 |
|    2 |     2.0000 |     8.0000 |          1.0000 |
|    3 |     9.1841 |     8.0000 |          9.0000 |
|    4 |    10.5939 |     0.8159 |          2.6317 |
|    5 |    13.0816 |    -0.5939 |          7.8122 |
|    6 |    11.1719 |    -3.0816 |          3.6526 |
|    7 |    10.7725 |    -1.1719 |          6.8781 |
|    8 |     9.3784 |    -0.7725 |          4.5953 |
|    9 |     9.4833 |     0.6216 |          6.2116 |
|   10 |     9.4933 |     0.5167 |          5.2293 |
|   11 |     9.9125 |     0.5067 |          5.8308 |
|   12 |    10.0804 |     0.0875 |          5.5092 |
|   13 |    10.1663 |    -0.0804 |          5.6801 |
|   14 |    10.1056 |    -0.1663 |          5.5958 |
|   15 |    10.0321 |    -0.1056 |          5.6368 |
|   16 |     9.9748 |    -0.0321 |          5.6174 |
|   17 |     9.9608 |     0.0252 |          5.6265 |
|   18 |     9.9725 |     0.0392 |          5.6223 |
|   19 |     9.9925 |     0.0275 |          5.6242 |
-------------------------------------------------------
```
![График](Lab02_Graph.png)