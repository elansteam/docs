#elan 
Хранится в `./config.py`

Шаблон класса

```python
from functools import lru_cache    
  
class Config:  
    app_name: str = "MongoDB API"  
    db_path: str  
  
@lru_cache()  
def get_config():  
    return Config()
```

Чтобы получить конфиг, нужно вызвать функцию `get_config`

Тут описываются все настройки проекта

>Вызов функции `get_config` - хэшируется