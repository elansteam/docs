#elan 
# Архитектура

Все файлы для работы с базой данных находятся  в `./src/db/`
Все следующие пути будут отсюда, если не сказано обратного

## Models

В папке `./models/` - Описания всех моделей базы данных

##### \_\_init\_\_.py

В нем определена функция `async def get_database() -> AbstractDatabaseManager`

```python
db = DatabaseManager()  
  
  
async def get_database() -> AbstractDatabaseManager:  
    return db
```

Сделано для того, чтобы получать объект db в функции обработки запроса через `Depends`

Например:

###### Пример использования
```python
@router.get('/')  
async def all_posts(db: AbstractDatabaseManager = Depends(get_database)):  
    posts = await db.get_posts()  
    return posts
```

Это сделано для единственности объекта db

## AbstractDatabaseManager

Абстрактный Singleton класс управления базой данных

находится в файле `./abstract_database_manager.py`

Есть поля `db: AsyncIOMotorDatabase`, `client: AsyncIOMotorClient`.

Есть определенный метод `async def connect_to_database(self, path: str)`, который подключает конкретный объект к базе данных, которая расположена по пути path
### DatabaseManager

Наследуется от `AbstractDatabaseManager`

Определение всех функций `AbstractDatabaseManager`

находится в файле `./impl/database_manager.py`

Все функции асинхронны

Пример функции для работы с базой данных:

```python
async def update_post(self, post_id: OID, post: PostDB):  
    await self.db.posts.update_one({'_id': ObjectId(post_id)},  
                                   {'$set': post.dict(exclude={'id'})})
```
# Схема работы в main.py

В `main.py` должны быть два метода:

```python
@app.on_event("startup")  
async def startup():  
    config = get_config()  
    await db.connect_to_database(path=config.db_path)  
  
  
@app.on_event("shutdown")  
async def shutdown():  
    await db.close_database_connection()
```

Эти два метода подключают и выключают базу данных

тут используется файл [[Config файл и его структура|config]], в котором есть поле db_path - путь к базе данных

