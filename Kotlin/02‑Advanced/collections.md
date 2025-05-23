---
aliases: ["MutableList vs List", "Kotlin collections"]
tags: [kotlin, interview, advanced]
created: 2025‑05‑04
---

## 📝 Question  
Какие коллекции есть в Kotlin? Чем отличаются MutableList от List?

## ✅ Answer  
В Kotlin коллекции делятся на **изменяемые (mutable)** и **неизменяемые (immutable)**. Основные интерфейсы коллекций: **List**, **Set**, **Map**.

- **List** представляет собой упорядоченную коллекцию элементов, доступных по индексу. В Kotlin есть `List` (неизменяемый список) и `MutableList` (изменяемый список). `List` только для чтения – добавление или удаление элементов недоступно. `MutableList` расширяет `List` методами `add`, `remove`, позволяя менять содержимое. Например: `val list = listOf(1,2,3)` – неизменяемый список; `val mlist = mutableListOf(1,2,3)` – можно добавлять элементы.
    
- **Set** – коллекция уникальных элементов (без повторений). Есть неизменяемый `Set` и `MutableSet` с теми же принципами.
    
- **Map** – коллекция пар ключ-значение. Неизменяемый `Map` не позволяет добавлять/удалять пары после создания, `MutableMap` – позволяет.
    

Важно понимать, что неизменяемые коллекции в Kotlin фактически могут быть реализацией тех же Mutable-коллекций, но интерфейс предоставляет только операции чтения. Попытка привести `List` к `MutableList` и изменить может привести к исключению во время выполнения. Поэтому использовать правильный тип важно для безопасности. Также в Kotlin есть специальный тип последовательностей (**Sequence**), который лениво вычисляет элементы и операции (аналог Streams в Java), но его используют для больших объемов данных, когда не нужна мгновенная материализация списка. Основные коллекции же – List, Set, Map – широко применяются для хранения данных в памяти.

## 🔍 См. также  
- [[lambdas]]  
- [[coroutines]]
