---
aliases: ["Caching Retry", "OkHttp cache", "Retry interceptor"]
tags: [network, cache, retry, interview]
created: 2025‑05‑04
---

## 📝 Question  
Как бы вы реализовали механизм кэширования или повторных попыток (retry) при сетевых запросах?

## ✅ Answer  
(Вопрос обширный, но если спросят, стоит рассказать про OkHttp cache и интерсепторы).

К примеру, OkHttp имеет возможность кэшировать ответы, если сервер присылает заголовки Cache-Control. Можно указать кэш:

```kotlin
val cacheSize = 5L * 1024 * 1024 // 5 MB
val cache = Cache(File(context.cacheDir, "http_cache"), cacheSize)
val okHttpClient = OkHttpClient.Builder().cache(cache).build()
```

Так OkHttp будет хранить ответы. Или вручную через NetworkInterceptor проверять сеть и при отсутствии выдавать кэш (этот паттерн "offline cache").

Повторные попытки можно делать вручную (цикл попыток) или через Interceptor:

```kotlin
class RetryInterceptor(val maxRetry: Int): Interceptor {
    override fun intercept(chain: Interceptor.Chain): Response {
       var tryCount = 0
       var response: Response
       var request = chain.request()
       do {
           response = chain.proceed(request)
           if(!response.isSuccessful && tryCount < maxRetry) {
               tryCount++
               // maybe sleep a bit 
           } else {
               break
           }
       } while(true)
       return response
    }
}
```

и добавлять его.

Но надо не вдаваться слишком, только если спрашивают.

## 🔍 См. также  
- [[okhttp]]
