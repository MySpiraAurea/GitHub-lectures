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


