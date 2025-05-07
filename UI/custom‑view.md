---
aliases:
  - Custom View
  - создание компонента
tags:
  - ui
  - customview
  - interview
created: 2025‑05‑04
---

## 📝 Question  
Как создать собственный View‑компонент (custom View) в Android?

## ✅ Answer  
В традиционном Android (Views + XML) создание собственного View предполагает наследование от существующего класса View или ViewGroup и переопределение необходимых методов. Шаги для создания custom view:

1. **Наследование класса:** Решите, от чего наследовать. Если это нечто совершенно кастомное (например, рисуем что-то с нуля), наследуйте от `View`. Если ваш компонент составной (включает в себя несколько внутренних стандартных View), может иметь смысл наследовать от `ViewGroup` (или конкретного Layout типа LinearLayout) и собрать внутренние виджеты.
    
2. **Конструкторы:** Обязательно реализовать конструкторы, особенно тот, который принимает `Context` и `AttributeSet` – это используется при создании view из XML. В Kotlin достаточно одного primary конструктора:
    
    ```kotlin
    class MyCustomView @JvmOverloads constructor(
        context: Context, 
        attrs: AttributeSet? = null, 
        defStyleAttr: Int = 0
    ) : View(context, attrs, defStyleAttr) {
        // ...
    }
    ```
    
    Аннотация `@JvmOverloads` позволяет вызывать его с разным числом параметров (как нужны Android при инфлейте из XML).
    
3. **Переопределение `onDraw` (для View):** Если вы рисуете кастомный виджет с нуля (не состоящий из других View), то переопределяете метод `onDraw(canvas: Canvas)`. Используя объект Canvas и Paint, можно рисовать формы, текст, изображения. Например, для круговой диаграммы, рисуем с помощью Canvas.drawCircle, drawArc и т.д. Также может понадобиться переопределить `onMeasure(widthMeasureSpec, heightMeasureSpec)`, чтобы правильно рассчитывать размеры view (если не вызывать `setMeasuredDimension`, то базовый View установит размеры 0).  
    Если custom view содержит логику измерения (например, зависит от какого-то текстового значения), `onMeasure` обязателен для расчета `width` и `height`. Если, напротив, вы наследуете от готового элемента (например, TextView), можно обойтись без onMeasure, используя логику родителя.
    
4. **Обработка атрибутов:** Если view имеет настраиваемые атрибуты (через XML), вы можете определить собственные в `attrs.xml`. Например:
    
    ```xml
    <declare-styleable name="MyCustomView">
        <attr name="circleColor" format="color"/>
    </declare-styleable>
    ```
    
    В конструкторе custom view вы можете получить `val attributes = context.obtainStyledAttributes(attrs, R.styleable.MyCustomView)` и прочитать `circleColor = attributes.getColor(R.styleable.MyCustomView_circleColor, Color.RED)`. Не забудьте вызвать recycle() у атрибутов. Эти атрибуты позволят разработчикам, использующим ваш view, настроить его через XML.
    
5. **Составной кастомный View (если ViewGroup):** Если компонент состоит из нескольких под-виджетов, можно либо программно создавать их внутри конструктора, либо задать layout в `init`. Пример: вы хотите сделать CustomCompoundView, состоящий из ImageView и TextView. Можно унаследоваться от LinearLayout, в конструкторе inflate из XML:
    
    ```kotlin
    init {
        LayoutInflater.from(context).inflate(R.layout.my_compound_view, this, true)
        // findViewById for inner views if needed
    }
    ```
    
    И выставить необходимые настройки. Тогда ваш custom view – это просто обертка вокруг группы view с удобным API.
    
6. **Использование:** Готовый кастомный класс можно использовать в XML как тег, указав полный путь, например:
    
    ```xml
    <com.example.MyCustomView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:circleColor="@color/purple"/>
    ```
    
    Или создать в коде как обычный View через конструктор.
    

**Пример сценария:** Нужно нарисовать кастомный график. Вы наследуетесь от View, в onDraw рисуете линии графика по данным. Добавляете метод `setData(data: List<Float>)`, вызываете `invalidate()` чтобы перерасовать. В onMeasure устанавливаете желаемый размер (или `resolveSize`).

**Compose путь:** (Отдельно замечу, в Jetpack Compose создание кастомного компонента — это просто написание Composable-функции, т.е. куда проще. А в традиционном подходе – как выше).

## 🔍 См. также  
- [[styles‑themes]]
