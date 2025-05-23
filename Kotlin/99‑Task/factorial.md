---
aliases: ["factorial"]
tags: [kotlin, task, algorithm]
created: 2025‑05‑04
---
## 📝 Question  
Практическая задача: Вычислить факториал числа n.

## ✅ Answer  
**Факториал** числа `n` (`n!`) – это произведение всех положительных целых чис от 1 до n. По определению, `0! = 1`. Например, `5! = 1*2*3*4*5 = 120`. Реализуем функцию для расчета факториала, используя цикл или функцию высшего порядка `reduce`:

```kotlin
fun factorial(n: Int): Long {
    require(n >= 0) { "Факториал определен только для n >= 0" }
    return if (n <= 1) 1L 
           else (1..n).map { it.toLong() }.reduce { acc, i -> acc * i }
}

// Проверка:
println(factorial(0))  // 1
println(factorial(5))  // 120
println(factorial(10)) // 3628800
```

Здесь для n > 1 мы создаем последовательность чисел от 1 до n и методом `reduce` последовательно перемножаем их (аккумулятор `acc` умножается на каждый следующий `i`). Мы приводим числа к Long, чтобы избежать переполнения при большом n (factorial растет очень быстро!). Также есть проверка `require(n >= 0)` – она выбросит IllegalArgumentException, если аргумент некорректный (отрицательный). Такой подход делает функцию безопасной и правильно работающей на всех неотрицательных n.

## 🔍 См. также

* [[squares‑of‑evens]] — ещё одна числовая трансформация коллекций
* [[char‑frequency]] — пример линейного алгоритма O(n)
* [[collections]] — генерация диапазона `1..n`