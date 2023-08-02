## Урок 6


### Оператор JOIN для формирования сводного отчета  
Пришло время разобраться как выполняется объединение данных из разных таблиц для формирования сводного отчета. Для этого используется оператор  

```
JOIN <таблица> ON <условие связывания>
``` 
 
который следует сразу после FROM. Например, у нас есть две таблицы games и users. Таблица games содержит поле user_id (идентификатор пользователя), которое является внешним ключом для связывания с таблицей users по полю rowid, которое является первичным ключом и мы хотим получить сводные данные с полями: name, sex, score. Причем, поле score следует брать из таблицы games, а не users. Запрос будет выглядеть так: 
 
```SQL
SELECT name, sex, games.score FROM games
JOIN users ON games.user_id = users.rowid

```  
Здесь мы указываем поля сводной таблицы, причем, поле score записано как games.score, тем самым уточняя, что его следует брать из таблицы games. Если этого не сделать, то СУБД возвратит ошибку неопределенности имени поля, т.к. score присутствует в обеих таблицах. Далее указывается главная таблица games, с которой будут ассоциированы данные из второй таблицы users. Условие связывания прописано после оператора ON как равенство полей games.user_id и users.rowid выбираемой записи. То есть, сначала отбирается запись из главной таблицы games и, затем, берется запись из таблицы users, у которой rowid равен user_id. В итоге получается следующая сводная выборка.  
То есть, первые две игры сыграл Михаил с числом очков 200 и 300, затем, Яна и набрала 500 очков, потом, снова Михаил и так далее. Благодаря объединению данных мы видим не просто id игрока, а его имя, что гораздо удобнее для человеческого восприятия.  
Здесь же я сразу хочу отметить такой момент. Объединять данные из таблиц можно и без оператора JOIN, просто указав их имена после FROM, например, так:
  
```SQL
SELECT name, sex, games.score FROM users, games
```  

Здесь была взята первая запись из таблицы users и ей в соответствие были поставлены все записи из второй таблицы games. И так для всех трех. В итоге произошло такое своеобразное соединение двух таблиц без какого-либо критерия. Но, так тоже можно делать и знать про этот функционал полезно, т.к. иногда он тоже используется.  
Теперь давайте вернемся к рассмотрению оператора JOIN в действительности, когда мы его вот так записывали в SQL-запросе:  

```SQL
SELECT name, sex, games.score FROM games
JOIN users ON games.user_id = users.rowid
```

то это аналог оператора INNER JOIN, то есть, соединение записей из двух таблиц, если соответствия найдены в обеих из них. Чтобы было понятнее о чем речь, рассмотрим такой пример. Удалим из таблицы users последнего игрока с именем «Федор». Теперь, выполняя запрос увидим отсутствие записи с этим игроком.  
Кажется, это вполне естественный и ожидаемый результат. Но такое поведение не всегда может нас устраивать. Иногда важно иметь все записи из главной таблицы (games), а дополнительные сведения из второй таблицы добавлять, если они там есть. Для такого объединения данных используется модификация LEFT JOIN следующим образом:  

```SQL
SELECT name, sex, games.score FROM games
LEFT JOIN users ON games.user_id = users.rowid
```  

Видите, теперь вместо отсутствующего пользователя записаны значения NULL и в результате была сохранена полная выборка из главной таблицы games.  
В заключении этого занятия реализуем запрос, который будет формировать список ТОП игроков, используя данные обеих таблиц. На предыдущем занятии мы уже делали подобную реализацию для одной таблицы и она выглядела так:  

```SQL
SELECT user_id, sum(score) as sum 
FROM games
GROUP BY user_id
ORDER BY sum DESC
```  

Давайте теперь вместо user_id будем выводить имя и пол игрока. Вернем третьего игрока:  
```
Федор        1       34     500
```  
И запишем следующий запрос:  

```SQL
SELECT name, sex, sum(games.score) as score
FROM games
JOIN users ON games.user_id = users.rowid
GROUP BY user_id
ORDER BY score DESC
```

На выходе получим таблицу с игроками, набравших максимальное число очков.  
В команде SELECT можно указывать несколько операторов JOIN для объединения данных из трех, четырех и большего числа таблиц:  

```
SELECT <поля> FROM <таблица 1>
JOIN <таблица 2> JOIN <таблица 3> … JOIN <таблица N>
ON <условие связывания>
```  

Вот так работает агрегирование таблиц с помощью оператора JOIN. На следующем занятии мы рассмотрим еще один способ объединения данных через оператор UNION.