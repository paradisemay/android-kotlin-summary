---
aliases: ["OkHttp", "HTTP client"]
tags: [network, okhttp, interview]
created: 2025‑05‑04
---

## 📝 Question  
Что такое OkHttp и как он связан с Retrofit?

## ✅ Answer  
**OkHttp** – это низкоуровневый HTTP-клиент (библиотека) от Square для Java/Android. Он отвечает за фактическое выполнение HTTP-запросов: откроет сокет, отправит запрос, прочитает ответ. OkHttp сам по себе можно использовать напрямую, создавая `Request` и вызывая `OkHttpClient.newCall(request)`. Пример без Retrofit:

```kotlin
val client = OkHttpClient()
val request = Request.Builder()
    .url("https://api.example.com/users/5")
    .build()
client.newCall(request).enqueue(object: Callback {
    override fun onResponse(call: Call, response: Response) {
        val body = response.body?.string()
        // parse JSON manually or using library
    }
    override fun onFailure(call: Call, e: IOException) {
        // handle error
    }
})
```

С OkHttp нужно вручную разбирать `body.string()` JSON и преобразовать в объекты, что делает код громоздким.

**Связь с Retrofit:** Retrofit _использует_ OkHttp под капотом как транспорт. Когда вы вызываете метод Retrofit API, он формирует `Request` и делегирует его OkHttp. OkHttp выполняет запрос и возвращает `Response`, Retrofit обрабатывает тело (через указанный конвертер). То есть, Retrofit – надстройка над OkHttp, добавляющая удобный интерфейс.

OkHttp сам по себе ценен, когда нужен большой контроль над запросами:

- Можно добавлять **Interceptor**: функции, которые перехватывают запросы или ответы. Например, логирование (`HttpLoggingInterceptor`), добавление общих заголовков (Authorization), кэширование (Cache Interceptor). В Retrofit, чтобы использовать Interceptor, вы все равно настраиваете его OkHttpClient и передаете в Retrofit.Builder.client(okHttpClient).
    
- **Connection pooling, HTTP/2, WebSocket:** OkHttp поддерживает современный HTTP (включая HTTP/2), имеет пул соединений для переиспользования, умеет автоматически следовать редиректам, поддерживает WebSocket. Retrofit не занимается этим – все это работа OkHttp.
    
- **Caching:** OkHttp можно настроить кэшировать ответы (Cache, с указанием кэша и контролей). Retrofit + OkHttp – вы тоже через OkHttp настраиваете.
    
- **Timeouts и настройки:** OkHttpClient позволяет выставлять таймауты (connect, read, write), ретраи.
    

В общем, **Retrofit = OkHttp + удобный слой**. Если Retrofit не подходит (например, нет потребности или вы делаете что-то нестандартное, типа стриминга данных), можно использовать OkHttp напрямую. Но 95% случаев – Retrofit с OkHttp.

**От интервьюируемого ожидают:** Понимание, что Retrofit – не самостоятельный HTTP-стек, а использует OkHttp (по умолчанию, хотя теоретически можно другой клиент, но обычно OkHttp). И что OkHttp можно использовать отдельно, а Retrofit – поверх него.

## 🔍 См. также  
- [[retrofit]]
