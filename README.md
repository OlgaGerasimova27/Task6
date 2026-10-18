# Task6
Практическая работа №6: Двумерные прямоугольные массивы (матрицы) в C# и генерация игровых локаций
### Вариант 6. Шахматный турнир (Сетка гроссмейстеров)

**Сетка:** Таблица результатов тура `6x6`.  
**Символика:** `W` — победа белых (1 очко), `D` — ничья (0.5), `B` — победа черных (0), `-` — игра сам с собой.

1. Объявить матрицу турнирных очков `double[,] tournament` размером `6x6`.
2. Заполнить главную диагональ символическими нулями (шахматист не играет против себя).
3. Заполнить остальные ячейки случайными очками из набора: `0.0`, `0.5`, `1.0`.
4. Сформировать строковый массив имен 6 участников турнира.
5. Вывести турнирную таблицу в консоль с ровным табличным выравниванием.
6. Подсчитать суммарный балл каждого игрока (суммы по строкам) и определить лидера.
7. Найти строку гроссмейстера с наименьшей суммой очков (аутсайдер).
8. Подсчитать общее количество ничейных партий (`0.5`) во всем турнире.
9. Проверить симметричность результатов партий: зеркальны ли результаты относительно диагонали.
10. Заменить все значения ниже `0.5` на штрафной маркер `-1.0` при дисквалификации.

---
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

class Program
{
    // Константы для размера матрицы
    const int Size = 6;

    static void Main()
    {
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        Console.WriteLine("=== Шахматный турнир: Сетка гроссмейстеров ===\n");

        // 4. Формирование строкового массива имен 6 участников турнира
        string[] players = { "Карпов", "Каспаров", "Фишер", "Спасский", "Ананд", "Карлсен" };

        // 1. Объявить матрицу турнирных очков double[,] tournament размером 6x6
        double[,] tournament = new double[Size, Size];
        Random rng = new Random();

        // 2. Заполнить главную диагональ символическими нулями (шахматист не играет против себя)
        // 3. Заполнить остальные ячейки случайными очками из набора: 0.0, 0.5, 1.0
        for (int row = 0; row < Size; row++)
        {
            for (int col = 0; col < Size; col++)
            {
                if (row == col)
                {
                    tournament[row, col] = 0.0; // Сам с собой не играет
                }
                else
                {
                    // Генерируем случайное число 0, 1 или 2 и делим на 2, чтобы получить 0.0, 0.5, 1.0
                    tournament[row, col] = rng.Next(0, 3) / 2.0;
                }
            }
        }

        // 5. Вывести турнирную таблицу в консоль с ровным табличным выравниванием
        Console.WriteLine("Турнирная таблица (6x6):");
        Console.Write("       ");
        for (int i = 0; i < Size; i++)
        {
            Console.Write($"{players[i],-9}");
        }
        Console.WriteLine();

        for (int row = 0; row < Size; row++)
        {
            Console.Write($"{players[row],-7}|");
            for (int col = 0; col < Size; col++)
            {
                Console.Write($"{tournament[row, col],-9:F1}");
            }
            Console.WriteLine();
        }
        Console.WriteLine();

        // 6. Подсчитать суммарный балл каждого игрока (суммы по строкам) и определить лидера
        Console.WriteLine("Суммарные баллы игроков:");
        double[] totalScores = new double[Size];
        double maxScore = -1;
        int leaderIndex = -1;

        for (int row = 0; row < Size; row++)
        {
            double rowSum = 0;
            for (int col = 0; col < Size; col++)
            {
                rowSum += tournament[row, col];
            }
            totalScores[row] = rowSum;
            Console.WriteLine($"{players[row]}: {rowSum:F1}");

            if (rowSum > maxScore)
            {
                maxScore = rowSum;
                leaderIndex = row;
            }
        }
        Console.WriteLine($"\nЛидер турнира: {players[leaderIndex]} (балл: {maxScore:F1})\n");

        // 7. Найти строку гроссмейстера с наименьшей суммой очков (аутсайдер)
        double minScore = 999;
        int outsiderIndex = -1;

        for (int row = 0; row < Size; row++)
        {
            if (totalScores[row] < minScore)
            {
                minScore = totalScores[row];
                outsiderIndex = row;
            }
        }
        Console.WriteLine($"Аутсайдер турнира: {players[outsiderIndex]} (балл: {minScore:F1})\n");

        // 8. Подсчитать общее количество ничейных партий (0.5) во всем турнире
        int totalDraws = 0;
        for (int row = 0; row < Size; row++)
        {
            for (int col = 0; col < Size; col++)
            {
                if (tournament[row, col] == 0.5)
                {
                    totalDraws++;
                }
            }
        }
        Console.WriteLine($"Общее количество ничейных партий в турнире: {totalDraws}\n");

        // 9. Проверить симметричность результатов партий: зеркальны ли результаты относительно диагонали
        bool isSymmetric = true;
        for (int row = 0; row < Size; row++)
        {
            for (int col = 0; col < Size; col++)
            {
                // Если результат в ячейке [row, col] не совпадает с [col, row] (исключая диагональ, но там нули)
                if (tournament[row, col] != tournament[col, row])
                {
                    isSymmetric = false;
                    break;
                }
            }
            if (!isSymmetric) break;
        }

        if (isSymmetric)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("Проверка симметричности: Таблица СИММЕТРИЧНА относительно главной диагонали.");
        }
        else
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Проверка симметричности: Таблица НЕ СИММЕТРИЧНА (результаты партий не зеркальны).");
        }
        Console.ResetColor();
        Console.WriteLine();

        // 10. Заменить все значения ниже 0.5 на штрафной маркер -1.0 при дисквалификации
        Console.WriteLine("Применение дисквалификации (замена значений < 0.5 на -1.0)...");
        for (int row = 0; row < Size; row++)
        {
            for (int col = 0; col < Size; col++)
            {
                if (tournament[row, col] < 0.5 && row != col)
                {
                    tournament[row, col] = -1.0;
                }
            }
        }

        // Вывод финальной таблицы
        Console.WriteLine("\nИтоговая таблица после дисквалификации:");
        Console.Write("       ");
        for (int i = 0; i < Size; i++)
        {
            Console.Write($"{players[i],-9}");
        }
        Console.WriteLine();

        for (int row = 0; row < Size; row++)
        {
            Console.Write($"{players[row],-7}|");
            for (int col = 0; col < Size; col++)
            {
                // Выделяем дисквалифицированные результаты красным
                if (tournament[row, col] == -1.0)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.Write($"{tournament[row, col],-9:F1}");
                    Console.ResetColor();
                }
                else
                {
                    Console.Write($"{tournament[row, col],-9:F1}");
                }
            }
            Console.WriteLine();
        }

        Console.WriteLine("\n=== Турнир завершён ===");
        Console.WriteLine("Нажмите любую клавишу для выхода...");
        Console.ReadKey();
    }
}
```
