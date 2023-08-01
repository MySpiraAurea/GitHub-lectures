## Урок 2


Пример простейшего кода для взаимодействия с БД:
```python

import sqlite3 as sq #импортируем модуль


con = sq.connect("saper.db") #устанавливает связь с БД saper.db
# файл с БД должен находиться в том же каталоге что и исполняемый файл на питоне
# если файл там есть, то связь будет установлена, а если нет то создаст новую БД

cur = con.cursor() #для взаимодействия с БД создаем объект класса Cursor

# Передаем пустой запрос БД
cur.execute('''
''')

con.close() #после работы обязательно закрываем БД
```  

Код выполняется и создает пустую БД.  
Тоже самое можно сделать с помощью контекст менеджера.  
```python
import sqlite3 as sq #импортируем модуль


with sq.connect("saper.db") as con:
   cur = con.cursor()
   cur.execute('''
   ''')
```  

Этот способ предпочтительнее, т.к. контекст менеджера автоматически закрывает БД, даже если произошла ошибка, что хорошо сказывается на безопасности при работе с БД.  


Типы данных в SQL:  
1. NULL - значение NULL  
2. INTEGER - целочисленный тип (занимает от 1 до 8 байт)  
3. REAL - вещественный тип (8 байт в формате IEEE)  
4. TEXT - строковый тип (в кодировке данных базы, обычно UTF-8)  
5. BLOB - двоичные данные, хранятся как есть, например, для небольших изображений  

Далее пропишем создание таблицы с соответствующими полями:  
```python
import sqlite3 as sq #импортируем модуль


with sq.connect("saper.db") as con:
   cur = con.cursor()
# CREATE table создает таблицу, далее прописываем поля и тип данных для них
   cur.execute('''CREATE TABLE users (
   name TEXT,
   sex INTEGER,
   old INTEGER,
   score INTEGER
   )''')
```  

Чтобы избежать ошибки когда таблица уже создана добавляем:  
```python
import sqlite3 as sq #импортируем модуль


with sq.connect("saper.db") as con:
   cur = con.cursor()
   cur.execute('''CREATE TABLE IF NOT EXISTS users (
   name TEXT,
   sex INTEGER,
   old INTEGER,
   score INTEGER
   )''')
```  

Мы можем получить уникальный идентификатор для каждой записи при помощи команды. Он нужен для связи таблиц в БД.
```SQLite
SELECT rowid, * FROM users
```  

Ключевые слова, удаление и создание таблицы:  

```python
import sqlite3 as sq #импортируем модуль


with sq.connect("saper.db") as con:
   cur = con.cursor()
   cur.execute('DROP TABLE IF EXISTS users')
   cur.execute('''CREATE TABLE IF NOT EXISTS users (
   user_id INTEGER PRIMARY KEY AUTOINCREMENT,
   name TEXT NOT NULL, 
   sex INTEGER NOT NULL DEFAULT 1,
   old INTEGER,
   score INTEGER
   )''')

#NOT NULL если не хотим чтобы поле было пустым
# DEFAULT устанавливает значение по умолчанию
# user_id INTEGER PRIMARY KEY говорит что поле id должно содержать уникальное значение
# IF EXISTS проверяет существует ли таблица
# AUTOINCREMENT увеличивает PRIMARY KEY на 1
```
