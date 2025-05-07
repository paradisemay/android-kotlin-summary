---
aliases: ["Room ORM", "Room database"]
tags: [jetpack, room, database, interview]
created: 2025‑05‑04
---

## 📝 Question  
Что такое Room и как с ней работать?

## ✅ Answer  
**Room** – это ORM (Object-Relational Mapping) библиотека из Jetpack для удобной работы с локальной базой данных SQLite. Она предоставляет слой абстракции над SQLite, позволяя использовать аннотации и DAO (Data Access Object) интерфейсы для выполнения запросов, вместо написания SQL вручную в коде.

Основные компоненты Room:

- **Entity (сущность):** Класс данных, помеченный `@Entity`, который соответствует таблице в базе. Поля класса – это колонки. Например:
    
    ```kotlin
    @Entity(tableName = "users")
    data class User(
        @PrimaryKey val id: Int,
        val name: String,
        val age: Int
    )
    ```
    
    Здесь Room создаст таблицу "users" с колонками id, name, age. Аннотации типа `@PrimaryKey`, `@ColumnInfo`, `@Ignore` используются для настройки схемы.
    
- **DAO (Data Access Object):** Интерфейс, помеченный `@Dao`, содержащий методы доступа к данным. Методы помечаются аннотациями запросов: `@Query`, `@Insert`, `@Update`, `@Delete`. Например:
    
    ```kotlin
    @Dao
    interface UserDao {
        @Query("SELECT * FROM users WHERE age > :minAge")
        fun getUsersOlderThan(minAge: Int): List<User>
    
        @Insert
        fun insertUser(user: User)
    }
    ```
    
    Room во время компиляции генерирует реализацию этого интерфейса. Метод `getUsersOlderThan` будет выполнять соответствующий SELECT-запрос и возвращать список объектов User. Можно возвращать `LiveData<List<User>>` или `Flow<List<User>>`, тогда Room сам будет отслеживать изменения и эмитить обновленные данные.
    
- **Database:** Абстрактный класс, помеченный `@Database`, представляющий базу данных. В нем нужно перечислить entities и получить DAO. Пример:
    
    ```kotlin
    @Database(entities = [User::class], version = 1)
    abstract class AppDatabase : RoomDatabase() {
        abstract fun userDao(): UserDao
    }
    ```
    
    В рантайме создается синглтон экземпляр БД через `Room.databaseBuilder(context, AppDatabase::class.java, "app.db").build()`.
    

**Как работать:**

1. Определяем Entity классы для всех таблиц.
    
2. Определяем DAO интерфейсы с методами запросов.
    
3. Создаем класс Database, который связывает всё.
    
4. Получаем экземпляр базы (обычно в Application или через DI) и вызываем DAO методы. Например:
    
    ```kotlin
    val db = Room.databaseBuilder(appContext, AppDatabase::class.java, "app.db").build()
    val users = db.userDao().getUsersOlderThan(18)
    ```
    
    Если метод помечен как suspend или возвращает LiveData/Flow, можно вызывать его асинхронно (Room сам выполнит запросы вне главного потока, т.к. это запрещено напрямую).
    

**Особенности и преимущества Room:**

- **Компиляция SQL:** при сборке Room проверяет SQL-запросы в `@Query` на корректность. Если опечатка или несоответствие колонок – получите ошибку компиляции, а не runtime. Это надежнее.
    
- **Минимум кода:** Room генерирует много шаблонного кода за вас – маппинг курсоров в объекты, обновление схемы.
    
- **Миграции:** Room умеет автоматически мигрировать БД, если повысить версию. Нужно предоставить стратегию миграции (например, добавить колонку) или указать `fallbackToDestructiveMigration` (удалить данные и пересоздать).
    
- **Интеграция с LiveData/Flow:** можно получать потоки обновлений данных, Room будет отслеживать изменения в таблице и эмитить новые списки. Это удобно для MVVM – ViewModel может просто подписаться на Flow из DAO.
    
- **TypeConverters:** Room позволяет определить конвертеры (`@TypeConverter`) для несоединяемых типов (например, конвертация Date -> Long).
    

В целом, Room – стандартная библиотека для локального хранения в Android. Она заменяет старый способ использования SQLiteDatabase напрямую, делая код чище и безопаснее.

## 🔍 См. также  
- [[repository‑pattern]]
- [[datastore]]
