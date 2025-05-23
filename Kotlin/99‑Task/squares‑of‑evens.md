---
aliases: ["squares of evens", "filter map example"]
tags: [kotlin, task, algorithm]
created: 2025‑05‑04
---
## 📝 Question  
Практическая задача: Из списка целых чисел получить список квадратов чётных чисел.

## ✅ Answer  
Дано, например, список `[1, 2, 3, 4, 5]`. Нужно отфильтровать только четные (`2, 4`) и преобразовать их в квадраты (`4, 16`). В Kotlin это удобно сделать комбинацией функций `filter` и `map`:

```kotlin
fun squaresOfEvens(numbers: List<Int>): List<Int> {
    return numbers
        .filter { it % 2 == 0 }   // оставить только четные
        .map { it * it }          // заменить число на его квадрат
}

// Пример:
val nums = listOf(1, 2, 3, 4, 5)
println(squaresOfEvens(nums))  
// Вывод: [4, 16]
```

Разберем: `.filter { it % 2 == 0 }` оставляет в списке только элементы, для которых лямбда возвращает true (проверка на четность). Затем `.map { it * it }` применяет функцию ко всем оставшимся элементам, возводя в квадрат. Эти операции не изменяют исходный список, а создают новый. Обратите внимание, что можно легко изменить лямбды для других условий или преобразований. Например, если нужно в итоге посчитать сумму квадратов, можно дополнять цепочку вызовом `.sum()` или `.reduce { acc, x -> acc + x }`. В функциональном стиле такие задачи решаются кратко и читабельно.

## 🔍 См. также

* [[factorial]] — другая числовая операция с использованием `reduce`
* [[lambdas]] — `filter { }` и `map { }` базируются на лямбдах
* [[collections]] — работа с immutable List