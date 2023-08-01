## Урок 6  


Наследование (расширение) шаблонов уменьшает объем дублируемого кода. Рассмотрим этот принцип на простейшем примере. В файле ex_main мы используем именнованные блоки title и content. Они используются для расширения базового шаблона страницы. Для расширения ex_main создаем шаблон about.  

Шаблон ex_main:

```HTML
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>{% block title %}{% endblock %}</title>
</head>
<body>

{% block content %}
{% endblock %}

</body>
</html>
```  

Шаблон about (расширение ex_main):

```HTML
{% extends "ex_main.html" %}

{% block title %}О сайте{% endblock %}

{% block content %}
<h1>О сайте</h1>
<p>Классный сайт, если его доделать.</p>
{% endblock %}
```  

В коде python подключение расширения будет выглядеть следующим образом: 
 
```python

from jinja2 import Environment, FileSystemLoader

file_loader = FileSystemLoader('templates') #шаблон загружаем из templates
env = Environment(loader=file_loader) #создаем объект Environment и указываем загрузчик

template = env.get_template('about.html') #получаем шаблон about.html

output = template.render() #вызываем метод render() для обработки шаблона
print(output)
```  

Если необходимо загрузить шаблон из подкаталога, то в about прописываем следующее:
```HTML
{% extends "layout/default.tpl" %}

{% block title %}О сайте{% endblock %}

{% block content %}
<h1>О сайте</h1>
<p>Классный сайт, если его доделать.</p>
{% endblock %}
```  

Чтобы не прописывать одно и тоже несколько раз мы можем вместо <О сайте> записать self.title() - вызов блока для вывода его содержимого  

```HTML

{% extends "layout/default.tpl" %}

{% block title %}О сайте{% endblock %}

{% block content %}
<h1>{{ self.title() }}</h1>
<p>Классный сайт, если его доделать.</p>
{% endblock %}
```  
super вызывает то же метод, но из базового шаблона и если при этом изменить базовый метод, то вывод изменится.  
```HTML
{% extends "layout/default.tpl" %}

{% block title %}О сайте{% endblock %}

{% block content %}
{{ super() }}
<h1>{{ self.title() }}</h1>
<p>Классный сайт, если его доделать.</p>
{% endblock %}
```  

Базовый метод:

```HTML
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>{% block title %}{% endblock %}</title>
</head>
<body>

{% block content %}
Блок контента
{% endblock %}

</body>
</html>
```  

Если не обратиться к базовому методу, то дочерний полностью переписывает содержимое базового.  
Шаблоны могут быть вложенными. Например внутрь block content мы можем вложить блок table_contents. 
```HTML
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>{% block title %}{% endblock %}</title>
</head>
<body>

{% block content %}
    {% block table_contents %}
    <ul>
    {% for li in list_table -%}
    <li>{{li}}</li>
    {% endfor -%}
    </ul>
    {% endblock table_contents %}
{% endblock %}

</body>
</html>
```  

А в программе укажем список, который будет итерироваться в блоке HTML

```python
from jinja2 import Environment, FileSystemLoader

subs = ['Математика', 'Физика', 'Информатика', 'Русский'] #создали список

file_loader = FileSystemLoader('templates')
env = Environment(loader=file_loader)

template = env.get_template('about.html')

output = template.render(list_table = subs) # передаем сохданный список для итерации по нему
print(output)
```  

Для того чтобы переменная li в файле default имела доступ к внешнему контексту пропишем scoped. Также более корректно обратимся к методу super. Еще пропишем block item в about, он сохраняет название предмета внутрь тега <p>.  
Файл about

```HTML
{% extends "layout/default.tpl" %}

{% block title %}О сайте{% endblock %}

{% block content %}
{% block table_contents %}{{ super() }}{% endblock %}
<h1>{{ self.title() }}</h1>
<p>Классный сайт, если его доделать.</p>
{% endblock %}

{% block item %}<p class="item">{{ super() }}</p>{% endblock %}
```  

Файл default  

```HTML
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>{% block title %}{% endblock %}</title>
</head>
<body>

{% block content %}
    {% block table_contents %}
    <ul>
    {% for li in list_table -%}
    <li>{% block item scoped %}{{ li }}{% endblock %}</li>
    {% endfor -%}
    </ul>
    {% endblock table_contents %}
{% endblock %}

</body>
</html>
```