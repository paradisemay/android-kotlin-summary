---
aliases:
  - Dark theme
  - Light theme
  - DayNight
tags:
  - ui
  - themes
  - nightmode
  - interview
created: 2025‑05‑04
---

## 📝 Question  
Как реализовать поддержку тёмной и светлой темы в приложении?

## ✅ Answer  
Поддержка темной/светлой темы означает, что приложение может переключать свою цветовую палитру и ресурсы в зависимости от предпочтения пользователя (или системной настройки ночного режима). В Android это делается на уровне тем и ресурсов:

- **DayNight тема:** Использование базовой темы с поддержкой ночного режима, например `Theme.MaterialComponents.DayNight`. Эта тема автоматически подстраивается под текущий режим (ночь или день) если цвета определены через `?attr` ресурсы, у которых есть день/ночь варианты.
    
- **Ресурсы с квалификатором -night:** Можно определить альтернативные значения цветов и других ресурсов в папке `values-night`. Например, `values/colors.xml` содержит `<color name="background">#FFFFFF</color>` (для светлой темы), а `values-night/colors.xml` содержит `<color name="background">#000000</color>` (для темной). При переключении режима система сама возьмет цвета из нужного файла. То же можно сделать для стилей: `values-night/styles.xml` может переопределять некоторые стили/темы.
    
- **Тема приложения:** Обычно создаются две темы – одна наследуется от `.DayNight` или конкретно `.Light`, другая от `.Night`. Например:
    
    ```xml
    <style name="AppTheme.Light" parent="Theme.MaterialComponents.Light.NoActionBar">
        <!-- light colors -->
        <item name="colorPrimary">@color/blue</item>
        <item name="colorBackground">@color/white</item>
    </style>
    <style name="AppTheme.Dark" parent="Theme.MaterialComponents.Dark.NoActionBar">
        <!-- dark colors -->
        <item name="colorPrimary">@color/blue_dark</item>
        <item name="colorBackground">@color/black</item>
    </style>
    ```
    
    Затем в манифесте или программно назначается соответствующая тема. Если использовать `DayNight`, можно одной темой обойтись, где colorPrimary, etc, указываются с использованием @color, которые имеют day/night варианты. Это проще.
    
- **Включение ночного режима:** Начиная с Android 10, есть системная настройка "Темная тема". Приложения с DayNight темой могут реагировать автоматически. Нужно в манифесте указать:
    
    ```xml
    <application ... android:theme="@style/Theme.MyApp.DayNight">
        <meta-data android:name="android.support.appcompat.NightMode" android:value="auto" />
    </application>
    ```
    
    Либо если используете AppCompat, вы можете программно включить через `AppCompatDelegate.setDefaultNightMode(MODE_NIGHT_YES or MODE_NIGHT_FOLLOW_SYSTEM)`.
    
- **Работа в Compose:** (Если применимо) Compose MaterialTheme тоже поддерживает темную/светлую тему: `MaterialTheme(colors = if(darkTheme) DarkColors else LightColors, ...)`.
    

**Практические шаги:**

1. Определить палитру цветов для светлой и темной темы.
    
2. Поместить цвета в `values/colors.xml` и `values-night/colors.xml` с одинаковыми именами.
    
3. Сделать тему DayNight, либо две темы (Light/Dark) и переключать. Если DayNight – просто применяем в приложении.
    
4. Если DayNight, то ничего больше – система сама переключит при смене режима. Если две темы, то нужно реагировать, например, на выбор пользователя (в настройках апп) – тогда на уровне кода (Activity) в `onCreate` перед `setContentView` установить тему: `setTheme(isDark ? R.style.AppTheme_Dark : R.style.AppTheme_Light)`.
    
5. Тестировать, что все основные цвета/стили читаются из night-ресурсов. Не забыть картинки/иконки – для них тоже можно делать night версию (например, более светлая иконка для темного фона).
    

Android Studio поддерживает Preview для night mode, можно сразу видеть.

Современный совет – использовать `DayNight` подход, чтобы позволить пользователю просто системно управлять. Но если нужно in-app toggle, можно все равно `AppCompatDelegate`-метод использовать.

Итого: с точки зрения собеседования, главное понимать, что **темная тема** реализуется через ресурсы с суффиксом `-night` и/или через темы DayNight, а переключение происходит либо автоматически (следует за системной настройкой), либо вручную (через вызов API).

## 🔍 См. также  
- [[styles‑themes]]
