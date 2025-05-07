---
aliases:
  - Fragment lifecycle
tags:
  - android
  - sdk
  - lifecycle
  - interview
created: 2025‑05‑04
---
## 📝 Question  
Чем отличается жизненный цикл Fragment от Activity?

## ✅ Answer  
**Fragment** – это компонент UI, который живет внутри Activity. Он имеет схожие методы жизненного цикла, но с некоторыми особенностями:

- У Fragment есть дополнительные стадии: **onAttach()** (когда прикрепляется к Activity), **onCreateView()** (создание отображения из layout-файла), **onViewCreated()**, **onDestroyView()** (когда удаляется связанный с ним View) и **onDetach()** (открепление от Activity).
    
- Последовательность основных этапов Fragment, когда он добавляется на экран, выглядит так: **onAttach -> onCreate -> onCreateView -> onViewCreated -> onStart -> onResume**. При удалении или замене Fragment: **onPause -> onStop -> onDestroyView -> onDestroy -> onDetach**.
    
- Важное отличие: Fragment зависит от Activity. Например, когда Activity переходит в onPause, все фрагменты внутри нее тоже переходят как минимум в onPause. Fragment не может существовать без Activity (кроме специальных случаев с `FragmentDialog`, у которого свой диалоговый хост, но там тоже есть Activity контекст).
    
- Fragment имеет свой _View lifecycle_ (между onCreateView и onDestroyView), что позволяет освобождать ресурсы, связанные с интерфейсом, отдельно от самого объекта Fragment. Например, можно в onDestroyView обнулить ссылки на View, чтобы избежать утечки, когда Fragment удаляется, но объект Fragment может ещё существовать (например, при добавлении в back stack).
    

Иными словами, **жизненный цикл Fragment тесно связан с жизненным циклом Activity**, но более сложный из-за дополнительных стадий создания/уничтожения представления. Разработчику важно учитывать эти отличия, особенно при работе с UI внутри фрагмента (например, инициализацию UI делать в onViewCreated, а не раньше) и при освобождении ресурсов во `onDestroyView` (например, обнулить binding). Кроме того, при замене фрагментов через back stack, фрагмент может пройти onDestroyView (вью уничтожена), но сам Fragment (onDestroy) не вызван, пока не убран из back stack окончательно.

## 🔍 См. также  
- [[activity‑lifecycle]]  
- [[save‑state]]
