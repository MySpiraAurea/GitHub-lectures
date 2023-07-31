## Урок 5  
Часто шаблон страницы делят на 3 части:
1. header - заголовок
2. content - содержание
3. footer - подвал страницы  
Мы можем разбить страницу на 3 части и каждую поместить в свой отдельный файл, а затем соединить. Для этого нам понадобится конструкция include.  
```python
from jinja2 import Environment, FileSystemLoader, FunctionLoader

persons = [
    {'name': 'Алексей', 'old': 18, 'weight': 78.5},
    {'name': 'Николай', 'old': 28, 'weight': 82.3},
    {'name': 'Иван', 'old': 33, 'weight': 94.8},
]

file_loader = FileSystemLoader('templates')
env = Environment(loader=file_loader) #создаем класс окружения со ссылкой на загрузчик

tm = env.get_template('page.html') # получаем шаблон из page.html
msg = tm.render()

print(msg)
```  

include мы записываем в файле с содержимым страницы, включаем в нее header и footer:  
```HTML
{% include 'header.html' %}
<p>Содержимое страницы
{% include 'footer.html' %}
```  

Если нам необходимо проигнорировать файл, которого нет пишем следующее: 
 
```HTML
{% include 'header2.html' ignore missing%}
<p>Содержимое страницы
{% include 'footer.html' %}
```  

Если мы хотим иметь возможность изменить домен и заголовок, то в header мы указываем переменные, а в метод render передаем значения этих переменных:

```HTML
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <base href='{{domain}}'>
    <title>{{title}}</title>
</head>
<body>
```

Код программы будет выглядеть так:
  
```python
from jinja2 import Environment, FileSystemLoader, FunctionLoader

persons = [
    {'name': 'Алексей', 'old': 18, 'weight': 78.5},
    {'name': 'Николай', 'old': 28, 'weight': 82.3},
    {'name': 'Иван', 'old': 33, 'weight': 94.8},
]

file_loader = FileSystemLoader('templates')
env = Environment(loader=file_loader) #создаем класс окружения со ссылкой на загрузчик

tm = env.get_template('page.html')
msg = tm.render(domain='http://proproprogs.ru', title='Про Jinja')

print(msg)
```  
Если необходимо подключить несколько шаблонов, то перечисляем их через запятую в квадратных скобках:
```HTML
{%include['page1.htm','page2.htm']ignore missing%}
```  

Конструкция import. jinja позволяет не только включать отдельные файлы в общий шаблон, но и импортировать их. При импорте файл не добавляется, но мы можем использовать его функционал. Приведем в пример файл dialoge, из него мы импортируем макрос:

```HTML
{% macro dialog_1(title, msg='') -%}
<div class="dialog">
    <p class="title">{{title}}</p>
    <p class="message">{{msg}}</p>
    <p>input type='button' value='Закрыть'</p>
</div>
{%- endmacro %}
```  

Затем импортируем его в page, при этом в коде программы ничего менять не нужно:

```HTML
{% import 'dialogs.html' as dlg%}
{% include 'header.html' ignore missing%}
<p>Содержимое страницы
{{dlg.dialog_1('Внимание', 'Это тестовый диалог')}}
{% include 'footer.html' %}
```  

Также последнее пожно записать как
```HTML
{% from 'dialogs.html' import dialog_1 as dlg%}
{% include 'header.html' ignore missing%}
<p>Содержимое страницы
{{dlg('Внимание', 'Это тестовый диалог')}}
{% include 'footer.html' %}
```  

или  

```HTML
{% from 'dialogs.html' import dialog_1 %}
{% include 'header.html' ignore missing%}
<p>Содержимое страницы
{{dialog_1('Внимание', 'Это тестовый диалог')}}
{% include 'footer.html' %}
```