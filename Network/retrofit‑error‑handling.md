---
aliases: ["Retrofit error handling", "обработка ошибок сети"]
tags: [network, retrofit, error, interview]
created: 2025‑05‑04
---

## 📝 Question  
Как обрабатывать ошибки сети и HTTP‑ответов при работе с Retrofit?

## ✅ Answer  
При взаимодействии с API всегда нужно предусматривать ошибки: отсутствие сети, таймауты, ответы сервера с кодом ошибки (4xx, 5xx), некорректные данные. В Retrofit обработка делится на две категории: **исключения (failures)** и **ошибочные ответы (HTTP errors)**.

- **Сетевые ошибки и исключения:** Если произошел сбой на уровне сети (нет подключения, таймаут, DNS ошибка), Retrofit (точнее OkHttp) бросает исключение, обычно `IOException`. В случае, когда используем `Call.enqueue`, оно попадет в `onFailure`. В случае coroutines (suspend функция), это исключение вылетит из функции. Обработать можно через try/catch:
    
    ```kotlin
    try {
        val user = api.getUser(id)  // suspend call
    } catch(e: IOException) {
        // нет интернета или другая сетевая ошибка
    }
    ```
    
    Также могут быть другие исключения, например `HttpException` (для плохого HTTP кода, если не использовали Response wrapper).
    
- **Ошибочные HTTP-коды:** Если метод API возвращает `Call<T>` или suspend fun без wrapping, то Retrofit по умолчанию считает ошибкой HTTP-код не в диапазоне 200-299 и бросает `HttpException`. Если же метод возвращает `Call<Response<T>>` или `suspend fun ...: Response<T>`, то вы сами получаете объект Response и можете проверить:
    
    ```kotlin
    val response = api.getUserResponse(id)
    if(response.isSuccessful) {
        val user = response.body()
    } else {
        val code = response.code()
        val errorBody = response.errorBody()?.string()
        // обработать код ошибки
    }
    ```
    
    `response.errorBody()` дает тело ошибки (которое сервер мог вернуть, например, JSON с сообщением об ошибке). Его можно распарсить через тот же Gson, если сервер структурированно возвращает ошибки.
    
- **Бизнес-ошибки:** Даже при коде 200, сервер мог вернуть поле, что операция не выполнена (например, {"success":false, "message":"wrong password"}). Это уже не уровень HTTP, а логики. Их тоже обрабатывают проверкой полей ответа. Тут Retrofit не поможет кроме как доставить объект. Нужно в коде проверить и решить, считать ли это ошибкой.
    

**Стратегии обработки:**

1. **Глобальные перехватчики:** Можно использовать OkHttp Interceptor или сам Retrofit callback. Например, с OkHttp можно перехватить все ответы, и если `response.code == 401`, автоматически выполнить какое-то действие (обновить токен или разлогинить). Или `HttpLoggingInterceptor` чтобы логировать ошибки.
    
2. **Возврат результатов оберткой:** Полезно писать функции API, которые возвращают не прямо данные, а **Result тип** (например, `Result<T>` или собственный sealed class). Например,
    
    ```kotlin
    sealed class ApiResult<out T> { data class Success<T>(val data: T): ApiResult<T>(); data class Error(val code: Int?, val message: String?): ApiResult<Nothing>() }
    ```
    
    И сделать репозиторий, который вызывает Retrofit и возвращает ApiResult:
    
    ```kotlin
    try {
        val response = api.getUserResponse(id)
        if(response.isSuccessful) {
            ApiResult.Success(response.body()!!)
        } else {
            ApiResult.Error(response.code(), parseError(response))
        }
    } catch(e: IOException) {
        ApiResult.Error(null, "Network failure")
    }
    ```
    
    Это особенно удобно, если не хотим, чтобы каждый раз UI ловил исключения – мы преобразуем все в единый результат.
    
3. **Корутины + kotlin.Result:** В Kotlin можно использовать `runCatching { }` для автоматического оборачивания исключений в Result, но надо еще ошибочный HTTP код учесть.
    
4. **Retry при ошибках:** Иногда нужно повторить запрос при сбое (например, Timeout). Можно реализовать логику ретра: ловим IOException, ждем немного, повторяем снова. Либо использовать библиотеку (например, Polly) или в Flow `.retryWhen`.
    
5. **UI/UX:** Отдельно стоит упомянуть: обработка – это не только логика, но и уведомление пользователя. При сетевой ошибке обычно показывают Toast/snackbar "Нет соединения", при ошибке сервера "Ошибка сервера, попробуйте позже", при ошибке бизнес-логики "Неверный пароль" (из текста ошибки). Эти сообщения формируются на основе пойманных ошибок.
    

**Вывод для собеседования:** показать, что кандидат не полагается, что "все всегда ок", а умеет обработать различные нештатные ситуации: и Java-исключения, и коды ответа, и знаешь, как получить тело ошибки. Возможно, упомянуть, что Retrofit2 + coroutines: если функция не Response, то нужно try/catch HttpException.

## 🔍 См. также  
- [[caching‑retry]]
