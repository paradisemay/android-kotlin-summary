---
aliases: ["Context types", "Application Context", "Activity Context"]
tags: [android, sdk, context, interview]
created: 2025‑05‑04
---

## 📝 Question  
Что такое Context в Android? Какие бывают типы Context и где их использовать?

## ✅ Answer  
**Context** – это объект, предоставляющий доступ к ресурсам, компонентам и среде выполнения Android. Проще говоря, контекст дает доступ к системным сервисам (например, `LayoutInflater`, `ClipboardManager`), к ресурсам приложения (строки, изображения), к работе с файлами, к запуску Activity/Service/Broadcast. Многие классы Android требуют Context для своей работы (например, создание Toast, AlertDialog, адаптеры и т.д.).

Существует несколько видов контекста:

- **Context приложения (Application Context)** – ссылается на всё приложение. Можно получить через `getApplicationContext()` или внутри классов Application/Service. Этот контекст живет столько же, сколько приложение, и не привязан к UI. Он полезен для операций, которые должны переживать смену экранов или жить в фоне (например, инициализация библиотек, доступ к БД, где не нужен UI). Application Context не знает про темы UI, иногда при его использовании могут не применяться стили, зависящие от темы Activity.
    
- **Context активности (Activity Context)** – контекст конкретной Activity. Доступен через `this` в Activity или `ActivityName.this`. Он привязан к жизни экрана. Используется для большинства UI-операций: показать диалог, всплывающее меню, получить ресурсы с учетом темы Activity. Этот контекст **не должен переживать Activity** – если сохранить ссылку на Activity Context и использовать после уничтожения Activity, произойдет **утечка памяти**. Поэтому важное правило: _не хранить неявно ссылки на Activity Context дольше, чем живет сама Activity_.
    
- **Context сервиса (Service Context)** – похож на Application Context, т.к. Service не имеет UI. Фактически сервис наследует `android.app.ContextImpl` (как и Application), и его контекст близок по возможностям к Application Context.
    
- **Context провайдера (BroadcastReceiver & ContentProvider)** – BroadcastReceiver получает контекст через метод `onReceive(Context context, Intent intent)`. Этот контекст особенный: он живет очень коротко (пока выполняется onReceive) и зачастую ограничен – например, нельзя показывать долгие UI из него (Toast можно, а запуск долгой Activity нужно делать с флагом нового таска). ContentProvider имеет свой Context через `getContext()`, обычно это контекст приложения.
    

**Когда какой использовать:**

- **Activity Context** – для операций, связанных с UI или ограниченных временем жизни экрана. Например, показывать диалоги, inflate UI Layouts, навигировать на другие Activity.
    
- **Application Context** – для долгоживущих операций: работа с БД, SharedPreferences, регистрация broadcast слушателей, если результат не привязан к UI. Также удобно передавать Application Context в утилитные классы или библиотеки, чтобы не допустить утечки Activity.
    

Пример проблемы: запуск диалога требует Activity (иначе тема может не примениться). А вот создание `Toast` можно сделать и с Application Context. **Важно**: Утечки часто возникают, если фоновый поток или Singleton хранит Activity Context. Чтобы избежать утечек, либо не хранить контекст, либо использовать Application Context, если UI не нужен.

## 🔍 См. также  
- [[intent‑types]]  
- [[viewmodel]]
