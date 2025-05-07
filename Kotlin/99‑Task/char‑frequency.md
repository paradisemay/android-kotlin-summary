---
aliases: ["character frequency"]
tags: [kotlin, task, algorithm]
created: 2025‑05‑04
---
## 📝 Question  
Практическая задача: Подсчитать частоту каждого символа в строке.

## ✅ Answer 
Требуется определить, сколько раз каждый символ встречается в строке. Эту задачу можно решить с помощью словаря (Map), где ключ – символ, значение – частота. Пример: для строки `"Hello"` должно получиться `{'H':1, 'e':1, 'l':2, 'o':1}`. Реализация:

```kotlin
fun charFrequency(text: String): Map<Char, Int> {
    val freqMap = mutableMapOf<Char, Int>()
    for (ch in text) {
        freqMap[ch] = (freqMap[ch] ?: 0) + 1
    }
    return freqMap
}

// Пример:
println(charFrequency("Hello"))  
// Вывод: {H=1, e=1, l=2, o=1}
```

С помощью Kotlin-функций высшего порядка то же можно сделать в одну строчку, используя группировку:

```kotlin
val freqMap = "Hello".groupingBy { it }.eachCount()
```

Функция `groupingBy { it }.eachCount()` сгруппирует одинаковые символы и посчитает их количество. Результат в обоих подходах одинаковый. Эти решения имеют линейную сложность O(n) – один проход по строке.

## 🔍 См. также

* [[palindrome]] — ещё одна строковая задача
* [[collections]] — основы List/Map, применяемые в решении
* [[lambdas]] — `groupingBy { it }.eachCount()` использует лямбды
