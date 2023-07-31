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