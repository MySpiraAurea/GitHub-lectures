## Урок 3


INSERT - добавление записи в таблицу  
SELECT - выборка данных из таблиц (в том числе и при создании сводной выбоки из нескольких таблиц).  
Синтаксиз команды INSERT:  
```SQL
INSERT INTO <table_name>(<column_name1>,<column_name2>,...)VALUES(<value1>, <value2>, ...)
```  

Если записываем во все поля по порядку:  
```SQL
INSERT INTO <table_name>VALUES(<value1>, <value2>, ...)
```  

Например:  
```SQL
INSERT INTO users (name, old, score) VALUES('Фёдор', 32, 200)  
```

В самом простом случае команда SELECT записывается так:
```SQL
SELECT col1, col2, ...FROM <table_name>
```  

Например:  
```SQL
SELECT name, old, score FROM users
```  

Если хотим вывести все поля пишем:  
```SQL
SELECT * FROM users
```  

Обычно команду SELECT используют с ключевым словом WHERE, которое определяет условие выборки записей. Пример:
```SQL
SELECT * FROM users WHERE score < 1000
```  

В качестве сравнения можно использовать следующие операторы:
```
= или ==, >, <, >=, <=, !=, BETWEEN
```  

Пример: 
```SQL
SELECT * FROM users WHERE score BETWEEN 500 AND 1000
```

Пример:
```SQL
SELECT * FROM users WHERE score == 200
```  

Также можно делать составное условие с помощью следующих операторов:  
1. AND - условное И: exp1 AND exp2. Истинно, если одновременно истинны exp1 и exp2.  
2. OR - условное ИЛИ: exp1 OR exp2. Истинно, если истинно exp1 или exp2 или оба выражения  
3. NOT - условное НЕ: NOT exp. Преобразует ложное условие в истинное и, наоборот, истинное - в ложное.  
4. IN - вхождение во множество значений: col IN (val1, val2)
5. NOT IN - не вхождение во множество значений: col NOT IN (val1, val2, ...)  
Примеры:  
```SQL
SELECT * FROM users WHERE old > 20 AND score < 1000
```  

```SQL
SELECT * FROM users WHERE old IN(19, 32) AND score < 1000
```  

```SQL
SELECT * FROM users WHERE old IN(19, 32) AND score < 1000
```  

```SQL
SELECT * FROM users WHERE old IN(19, 32) AND score < 1000 OR sex = 1
```  

Самый высокий приоритет выполнения у операции NOT, затем идет AND и потом OR.  
Для изменения приоритета выполнения операций следует ставить круглые скобки.  
```SQL
SELECT * FROM users WHERE old IN(19, 32) AND (score < 1000 OR sex = 1)
```  

Для сортировки определенного поля следует прописать:
```SQL
SELECT * FROM users
WHERE old IN(19, 32) AND score < 1000 OR sex = 1
ORDER BY old
```  

Для сортировки по убыванию прописываем DESC:  
```SQL
SELECT * FROM users
WHERE old IN(19, 32) AND score < 1000 OR sex = 1
ORDER BY old DESC
```  

Для явного указания сортировки следует прописать ASC, получим тоже самое что в 1 случае сортировки:  
```SQL
SELECT * FROM users
WHERE old IN(19, 32) AND score < 1000 OR sex = 1
ORDER BY old ASC
```  

Оператор LIMIT говорит сколько записей мы будем отбирать в нашей выборке. В данном случае выведет 2 первые записи:
```SQL
SELECT * FROM users
WHERE old IN(19, 32) AND score < 1000 OR sex = 1
ORDER BY old ASC
LIMIT 2
```  

Данный оператор имеет следующий синтаксиз. Offset - смещение от 1 записи, max - число записей, которое войдет в выборку:
```
LIMIT<max>[OFFSET offset]
```  

Пример применения более сложного фильтра:  
```SQL
SELECT * FROM users
WHERE score > 100 ORDER BY score DESC LIMIT 5 OFFSET 2 
```  

Или:  

```  
SELECT * FROM users
WHERE score > 100 ORDER BY score DESC LIMIT 2, 5
```  

Через python взаимодействие с БД c помощью метода fetchall выглядит следующим образом:  
```python
import sqlite3 as sq #импортируем модуль


with sq.connect("saper.db") as con:
   cur = con.cursor()

   cur.execute('SELECT * FROM users WHERE score > 100 ORDER BY score DESC LIMIT 5')
   result = cur.fetchall() # метод fetchall() получает результат отбора SQL запроса
   print(result)
```  

Предпочтительнее прописать тоже самое для построчного вывода кортежей данных. Тогда мы построчно перебираем список, что существенно экономит память:  
```python
import sqlite3 as sq #импортируем модуль


with sq.connect("saper.db") as con:
   cur = con.cursor()

   cur.execute('SELECT * FROM users WHERE score > 100 ORDER BY score DESC LIMIT 5')
   for result in cur:
      print(result)
```  

Также в python есть следующие методы  
1. fetchmany(size) - возвращает число записей, но не более size  
2. fetchone() - возвращает первую запись  

```python
import sqlite3 as sq #импортируем модуль


with sq.connect("saper.db") as con:
   cur = con.cursor()

   cur.execute('SELECT * FROM users WHERE score > 100 ORDER BY score DESC LIMIT 5')
   result = cur.fetchone()
   result2 = cur.fetchmany(2)
   print(result)
   print(result2)
```