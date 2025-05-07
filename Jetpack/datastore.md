---
aliases: ["DataStore", "Preferences DataStore", "Proto DataStore"]
tags: [jetpack, datastore, persistence, interview]
created: 2025‑05‑04
---

## 📝 Question  
Что такое DataStore и чем он лучше SharedPreferences?

## ✅ Answer  
**DataStore** – это современный способ хранения пар "ключ-значение" в Android, пришедший на смену `SharedPreferences`. DataStore находится в Jetpack (AndroidX) и предоставляет два варианта: **Preferences DataStore** (аналог SharedPreferences, хранит неструктурированные key-value) и **Proto DataStore** (хранит типизированные объекты посредством протобуф-схем).

Преимущества DataStore над SharedPreferences:

- **Асинхронность:** SharedPreferences работает синхронно: методы `commit()` выполняют запись на диск в текущем потоке, что может приводить к тормозам, если вызывается на главном потоке. DataStore же построен поверх корутин и Flow – все операции автоматические происходят в фоновом потоке. Вы получаете Flow для чтения данных и функции suspend для записи. Нет риска блокировки UI потока.
    
- **Последовательность записей:** SharedPreferences при частых записях может потерять промежуточные данные (если несколько commit подряд, или commit vs apply). DataStore гарантирует последовательное применение транзакций записи. Записи выстраиваются в очередь.
    
- **Типобезопасность (в случае Proto DataStore):** Вы определяете .proto-схему (как для gRPC) с типами, генерируется класс, и DataStore обеспечивает сохранение/чтение этого объекта. Это исключает ошибки с опечаткой в ключах, несоответствием типов. Preferences DataStore также можно использовать, но там ключи-строки, похож на SharedPrefs, хотя и с Flow.
    
- **Reactive API:** Чтение DataStore возвращает **Flow** – это означает, вы можете наблюдать за изменениями настроек в реальном времени. Например,
    
    ```kotlin
    dataStore.data.map { prefs -> prefs[SettingsKeys.USERNAME] ?: "Unknown" }
    ```
    
    вернет Flow, который эмитит новое значение, когда оно изменилось. В SharedPreferences есть слушатели, но API менее удобный и не поддерживает coroutines natively.
    
- **Нет deprecated:** SharedPreferences хоть и не объявлен официально устаревшим, но Google рекомендует переходить на DataStore для новых разработок, потому что DataStore решает ряд известных проблем SharedPreferences (как синхронность и транзакционность).
    

**Использование DataStore (Preferences):**

```kotlin
val Context.dataStore by preferencesDataStore(name = "settings")

// Запись:
suspend fun saveUsername(name: String) {
    context.dataStore.edit { prefs ->
        prefs[SettingsKeys.USERNAME] = name
    }
}
// Чтение:
val userNameFlow: Flow<String> = context.dataStore.data
    .map { prefs -> prefs[SettingsKeys.USERNAME] ?: "Unknown" }
```

где `SettingsKeys.USERNAME` – это ключ, созданный через `preferencesKey<String>("username")`.

**Использование Proto DataStore:** требует определенной схемы .proto и генерации класс, но результат –

```kotlin
val Context.protoDataStore by dataStore(
    fileName = "user_prefs.pb",
    serializer = UserPrefsSerializer  // handles read/write of the proto object
)
// To update:
dataStore.updateData { current ->
    current.toBuilder().setUsername(name).build()
}
```

В Proto DataStore `updateData` lambda дает вам текущий объект настроек, и вы возвращаете измененный. DataStore сам запишет.

В итоге DataStore – более современное, безопасное и эффективное API для хранения небольших настроек и предпочтений пользователя. SharedPreferences – старое API, работает, но для новых проектов DataStore предпочтительнее.

## 🔍 См. также  
- [[room]]
- [[livedata]]
