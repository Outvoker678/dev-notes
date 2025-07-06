Установка
```
$ pip install SQLAlchemy
```

Импорт базового класса `DeclarativeBase` из SQLAlchemy ORM. От него наследуются все классы-модели
```Python
from sqlalchemy.orm import DeclarativeBase
```

Создание таблицы *(класса-модели)* БД, наследуем от класса DeclarativeBase. В конце принято писать `Base`
```Python
class Base(DeclarativeBase):
	pass
```

Присвоение имени для таблицы
```Python
__tablename__ = "humans"
```

Импорт класса который определяет тип данных колонки
```Python
from sqlalchemy.orm import Mapped
```

Определение типа данных колонки `id` 
```Python
id: Mapped[int] = mapped_column()
```

Импорт класса который позволяет задать валидацию данных
```Python
from sqlalchemy.orm import mapped_column
```

Настройка данных, максимальная длина строки `String(30)`, определить первичный ключ, т.е. id или uuid `primary_key=True`
```Python
id: Mapped[int] = mapped_column(primary_key=True)	
name: Mapped[str] = mapped_column(String(30))
```

Добавить к таблице столбик к строке из другой таблицы, присваивание
```Python
owner_id: Mapped[int] = mapped_column(ForeignKey("имя_таблицы.id"))
```

Получить строку по ID
```Python
human = session.get(Human, 1) # Второй аргумент айди первичного ключа
print(human.name)  # → "Joe"
```

Создание движка для БД (SQLite)
```Python
engine = create_engine("sqlite:///example.db")
```

Генерация таблицы
```Python
Base.metadata.create_all(engine)
```

Создание сессии
```Python
Session = sessionmaker(engine)
```

Создание контекстного менеджера
```Python
with Session() as session:
```

Создание и добавление объекта в БД
```Python
joe = UserBase(name="Joe", email="joe@example.com")

def create_user(user: UserBase, session) -> None:
	session.add(user)
 
with Session() as session:
    try:
        create_user(joe, session)
    except:
        session.rollback()
        raise
    else:
        session.commit()
```

Получение объекта из БД
```Python
# +1 к списку импортов
from sqlalchemy import select
# какой-то код, например тот, который писали выше

def get_by_name(name: str, session) -> list[UserBase]:
	statement = select(UserBase).where(UserBase.name == name)
	db_object = session.scalars(statement).one()
	return db_object

with Session() as session:
	print(get_by_name("Joe", session))
```