## Jinja

### Урок 1


Jinja - модуль, который служит для обработки шаблонов в Python. Входит в модуль Django.  
```python  
from jinja2 import Template #импортируем класс Template из библиотеки Jinja


name = "Федор"

tm = Template("Привет {{ name }}")  #в фигурных скобках стоит ссылка на переменную name
msg = tm.render(name=name)  # указатель будет ссылаться на переменную name

print(msg)
```


Конструкции внутри шаблонов Jinja:  
1. {%%} - спецификатор шаблона  
2. {{}} - выражение для вставки конструкций Python в шаблон  
3. {##} - блок комментариев  
4. '# ##' - строковый комментарий 


```python  
from jinja2 import Template #импортируем класс Template из библиотеки Jinja

name = "Федор"
age = 28

tm = Template("Привет, меня зовут {{ n }}. Мне {{ a }} лет")  #в фигурных скобках стоят ссылки на переменные name и age
msg = tm.render(n=name, a = age)  # указатель будет ссылаться на переменную name

print(msg)
```


Именнованные параметры внутри метода render воспринимаются как словарь, где ключ это указатель на переменную (n или a в примере выше), а значение - сами переменные (name, age).  
Внутри фигурных скобок можно писать любые конструкции языка Python:  
```python  
from jinja2 import Template #импортируем класс Template из библиотеки Jinja

name = "Федор"
age = 28

tm = Template("Привет, меня зовут {{ n.upper() }}. Мне {{ a*2 }} лет")  # возраст усножаем на 2, а имя выводится заглавными буквами

print(msg)
```  


Пример можно усложнить классом:
 ```python  
from jinja2 import Template

class Person(): #создаем класс Person
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def get_name(self): #геттер имени
        return self.name

    def get_age(self): # геттер возраста
        return self.age

person = Person("Федор", 28)

tm = Template("Привет, меня зовут {{ p.get_age() }}, мне {{ p.get_name()  }} лет") #внутри скобок обращаемся к соответствующим свойствам класса Person при помощи указателя p
msg = tm.render(p=person) #создаем ссылку на экземпляр класса

print(msg)
```  


Данные в шаблон можно передать с помощью словаря.
 ```python  
from jinja2 import Template

per = {'name': 'Федор', 'age': 34}

tm = Template("Привет, меня зовут {{ p.age }}, мне {{ p.name }} лет") #внутри скобок прописываем ключи словаря per
msg = tm.render(p=person) #создаем ссылку на экземпляр класса

print(msg)
``` 


Также можем обратиться к ключу при помощи следующей конструкции:
 ```python  
from jinja2 import Template

per = {'name': 'Федор', 'age': 34}

tm = Template("Привет, меня зовут {{ p.['age'] }}, мне {{ p.['name'] }} лет") #обращаемся по имени ключа
msg = tm.render(p=person) #создаем ссылку на экземпляр класса

print(msg)
```


### Урок 2


Способы экранирования данных в строках. Экранирование необходимо когда мы хотим чтобы {{ name }} не изменялось, а передавалось в print как есть, не подставляя значение 'Федор'  

```python  
from jinja2 import Template

data = '''Модуль jinja вместо определения  {{ name }} подставляет соответствующее значение'''

tm = Template(data)
msg = tm.render(name='Федор')

print (msg)  
```  

Для этого в jinja есть специальный блок {%raw%}...{%endraw%} все что помещается внутрь него никак не преобразуется.  

```python  
from jinja2 import Template

data = '''{%raw%}Модуль jinja вместо определения  {{ name }} подставляет соответствующее значени{%endraw%}е'''

tm = Template(data)
msg = tm.render(name='Федор')

print (msg) 
```  

При работе с текстовыми html шаблонами часто возникает необходимость экранирования некоторых символов, которые браузером воспринимаются как определения тегов.  
Экранирование в браузерах заключается в том чтобы текст в браузерах выводить не как тэг ссылку, а в виде текста.  

```python  
from jinja2 import Template

link = '''В HTML-документе ссылки определяются так:
<a href="#">Ссылка</a>''' #специальные символы

tm = Template("{{link | e}}") #формируем шаблон с переменной link. После вертикальной черты идет флаг e, который означает экранирование специальных символов
msg = tm.render(link=link) #Присваиваем параметру строку link

print(msg)
# в выводе текст представлен в виде специальных символов. В выводе увидим:
# &lt;a href=&#34;#&#34;&gt;Ссылка&lt;/a&gt
```  
Теперь в браузере отображается не ссылка, а текст ('<a href="#">Ссылка</a>').  
Так как на практике операция выполняется довольно часто, в модуле jinja есть класс escape, который выполняет эту операцию.
```python  
from jinja2 import Template
from markupsafe import escape #импортируем класс escape

link = '''В HTML-документе ссылки определяются так:
<a href="#">Ссылка</a>''' #специальные символы

msg = escape(link)
print(msg)  
```  

Блок for позволяет формировать список на основе любого итерируемого объекта.  
Он выглядит как:  
  
```python  
#{%for<выражение>-%}
#    <повторяемый фрагмент>
#{%endfor%}

from jinja2 import Template

cities = [{'id': 1, 'city': 'Москва'}, #список из словарей с городами и их id
          {'id': 5, 'city': 'Тверь'},
          {'id': 7, 'city': 'Минск'},
          {'id': 8, 'city': 'Смоленск'},
          {'id': 11, 'city': 'Калуга'},]

# Мы хотим на основе списка cities сформировать список для html документа.
# Будет формироваться тэг select, внутри которого будет прописаны строки из списка
# Для этого перебираем список внутри цикла for
link = '''<select name="cities"> 
{% for c in cities%}
    <option value="{{c['id']}}">{{c['city']}}</option>
{% endfor %}
</select'''

tm = Template(link)
msg = tm.render(cities=cities)

print(msg)
```  

Для того чтобы вывести построчно без отступов записываем (-%), который убирает отступ:  
  
```python  
from jinja2 import Template

cities = [{'id': 1, 'city': 'Москва'},
          {'id': 5, 'city': 'Тверь'},
          {'id': 7, 'city': 'Минск'},
          {'id': 8, 'city': 'Смоленск'},
          {'id': 11, 'city': 'Калуга'},]
# -% убирает лишний отступ
link = '''<select name="cities"> 
{% for c in cities -%}
    <option value="{{c['id']}}">{{c['city']}}</option>
{% endfor -%}
</select'''

tm = Template(link)
msg = tm.render(cities=cities)

print(msg)
```  
  
Цикл if выглядит следующим образом:  

```python
#{%if<условие>%}
#    <фрагмент при исполнении условия>
#{%enfif%}

from jinja2 import Template

cities = [{'id': 1, 'city': 'Москва'},
          {'id': 5, 'city': 'Тверь'},
          {'id': 7, 'city': 'Минск'},
          {'id': 8, 'city': 'Смоленск'},
          {'id': 11, 'city': 'Калуга'},]

# условие проверяет id больше 6
# Далее идет проверка elif для Москвы и  выводится тэг <option>
#Если нет, то печатает просто имя города (условие else)
link = '''<select name="cities"> 
{% for c in cities -%}
{% if c.id > 6 -%}
    <option value="{{c['id']}}">{{c['city']}}</option>
{%elif c.city == 'Москва' -%}
    <option>{{c['city']}}</option>    
{%else -%}
    {{c['city']}}
{%endif -%}
{% endfor -%}
</select'''

tm = Template(link)
msg = tm.render(cities=cities)

print(msg)
```


## Урок 3


Фильтры в Jinja нужны для получения более сложных представлений.  
Фильтр Sum выводит сумму по определенному полю. Указывается коллекция, для который будет вызываться этот фильтр, затем указываем фильтр sum и указываем что суммирование нужно проводить по аттрибуту price
```python  
from jinja2 import Template

cars = [{'model': 'Ауди', 'price': 23000},
        {'model': 'Шкода', 'price': 17300},
        {'model': 'Вольво', 'price': 43300},
        {'model': 'Фольксваген', 'price': 21300}
]

#выводим суммарную цену автомобилей при помощи фильтра sum для коллекции, eказав атnрибут price
tpl = 'Суммарная цена автомобилей {{ cs | sum(attribute="price")}}'
tm = Template(tpl) #формируем шаблон
msg = tm.render(cs = cars) #выполняем шаблон


print(msg) #вывод

```  
В общем случае синтаксиз фильтра такой sum(iterable, attribute=None, start=0). Iterable - итерируемый объект; attribute - поле, по которому суммируем, указывается если необходимо(например для списков пропускаем); start - то что прибавляем к вычисленной сумме.  
Полный список фильтров гуглим.  
Фильтр max/min:
```python
from jinja2 import Template

cars = [{'model': 'Ауди', 'price': 23000},
        {'model': 'Шкода', 'price': 17300},
        {'model': 'Вольво', 'price': 43300},
        {'model': 'Фольксваген', 'price': 21300}
]
#выводим строчку с максимальной ценой
tpl = 'Суммарная цена автомобилей {{ cs | max(attribute="price")}}'
tm = Template(tpl) #формируем шаблон
msg = tm.render(cs = cars) #выполняем шаблон


print(msg) #вывод
```  

```pyton
from jinja2 import Template

cars = [{'model': 'Ауди', 'price': 23000},
        {'model': 'Шкода', 'price': 17300},
        {'model': 'Вольво', 'price': 43300},
        {'model': 'Фольксваген', 'price': 21300}
]
#выводим марку с минимальной ценой
tpl = 'Суммарная цена автомобилей {{ (cs | min(attribute="price")).model}}'
tm = Template(tpl) #формируем шаблон
msg = tm.render(cs = cars) #выполняем шаблон


print(msg) #вывод
```  

Фильтр random выводит случайное значение  

``` python  
from jinja2 import Template
cars = [{'model': 'Ауди', 'price': 23000},
        {'model': 'Шкода', 'price': 17300},
        {'model': 'Вольво', 'price': 43300},
        {'model': 'Фольксваген', 'price': 21300}
]
#выводим рандомную модель
tpl = 'Автомобиль: {{ (cs | random()).model}}'
tm = Template(tpl) #формируем шаблон
msg = tm.render(cs = cars) #выполняем шаблон


print(msg) #вывод
```
  
Фильтр replace заменяет значения  

```python
from jinja2 import Template
cars = [{'model': 'Ауди', 'price': 23000},
        {'model': 'Шкода', 'price': 17300},
        {'model': 'Вольво', 'price': 43300},
        {'model': 'Фольксваген', 'price': 21300}
]
#заменяет о на О
tpl = 'Автомобиль: {{ cs | replace("o", "O")}}'
tm = Template(tpl) #формируем шаблон
msg = tm.render(cs = cars) #выполняем шаблон


print(msg) #вывод
```  

Фильтры также можно применять внутри шаблонов. Для этого используется следующая конструкция:  

```python
#{{%filter<название фильтра>%}
# <фрагмент для применения фильтра>
# {%endfilter%}

from jinja2 import Template

persons = [
    {'name': 'Алексей', 'old': 18, 'weight': 78.5},
    {'name': 'Николай', 'old': 28, 'weight': 82.3},
    {'name': 'Иван', 'old': 33, 'weight': 94.8},
]

# перебираем список с помощью блока for
# К каждому имени из списка применяем фильтр upper
# аналогично работает lower
tpl = ''' 
{%- for u in users -%}
{% filter upper%}{{ u.name }}{% endfilter %}
{% endfor -%}
'''

tm = Template(tpl)
msg = tm.render(users=persons)

print(msg)

```


Макроопределения полезны для того чтобы избежать повторяющихся определений.

```python
from jinja2 import Template

#macro input - имя макроопределения
# Далее следует набор параметров, если такие необходимы
# type принимает значение type, по умолчанию это будет текст
# name принимает значение name, которое мы укажем
# value принимает значение value, причем к нему применяется флаг е
html = ''' 
{%macro input(name, value='', type='text', size=20) -%}
    <input type="{{ type }}" name="{{ name }}" value="{{ value|e }}" size="{{ size }}">
{%- endmacro %}

<p>{{input('username') }}
<p>{{ input('email')}}
<p>{{ input('password')}}
 
'''
# <p> - это тэг абзаца, далее идет тэг input, который в name принимает username

tm = Template(html)
msg = tm.render()

print(msg)
# В выводе все параметры кроме тех, что мы указали останутся дефолтными
#<p><input type="text" name="username" value="" size="20">
#<p><input type="text" name="email" value="" size="20">
#<p><input type="text" name="password" value="" size="20">

```

Jinja содержит специальное определение call, которое позволяет создавать вложенные макросы. Для примера поработаем с html файлом:
```python
from jinja2 import Template

persons = [
    {'name': 'Алексей', 'old': 18, 'weight': 78.5},
    {'name': 'Николай', 'old': 28, 'weight': 82.3},
    {'name': 'Иван', 'old': 33, 'weight': 94.8},
]

# формируем макроопределение list_users и передаем ему списокlist_of_user
# внутри макроопределения формируем список <ul>
# внутри тега <ul> будут формироваться теги <li> при помощи цикла for для каждого человека
# в конце вызываем макроопределение с параметром users из указателя на persons
html = '''
{% macro list_users(list_of_user) -%}
<ul>
{% for u in list_of_user -%}
    <li>{{u.name}}
{%- endfor %}
</ul>
{%- endmacro %}

{{list_users(users)}}
'''

tm = Template(html)
msg = tm.render(users=persons)

print(msg)
```

Теперь для каждого списка li добавим вложенный список с помощью блока call. Шаблон будет выглядеть следующим образом.

```python
#{%macro list_users(list_of_user)-%}
#<ul>
#{% for u in users -%}
#    <li>{{u.name}}{{caller(u)}} вызывается специальный метод caller от u
#{%-endfor%}
#</ul>
#{%-endmacro%}
#
#-------------------------------------
#
#{%call(user)list_users(users)%} call вызывается caller при помощи list_users, те вместо caller , будет блок call
#    <ul>
#    <li>age: {{user.age}}
#    <li.weight>: {{user.weight}}
#    </ul>
#{%endcall-%%}

from jinja2 import Template

persons = [
    {'name': 'Алексей', 'old': 18, 'weight': 78.5},
    {'name': 'Николай', 'old': 28, 'weight': 82.3},
    {'name': 'Иван', 'old': 33, 'weight': 94.8},
]

# формируем макроопределение list_users и передаем ему списокlist_of_user
# внутри макроопределения формируем список <ul>
# внутри тега <ul> будут формироваться теги <li> при помощи цикла for для каждого человека
#вместо вызова макроса будем вызывать метод call
html = '''
{% macro list_users(list_of_user) -%}
<ul>
{% for u in list_of_user -%}
    <li>{{u.name}}{{caller(u)}}
{%- endfor %}
</ul>
{%- endmacro %}

{% call(user) list_users(users) %}
    <ul>
    <li>age: {{user.old}}
    <li>weight: {{user.weight}}
    </ul>
{%endcall -%}    
'''

tm = Template(html)
msg = tm.render(users=persons)

print(msg)

```