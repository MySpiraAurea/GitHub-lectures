## Урок 4


Environment - класс, с помощью которого шаблоны можно загружать из текстовых файлов.
Для того чтобы загрузить шаблон нужно воспользоваться одним из загрузччиков, который предоставляет jinja. Воспользуемся FileSystemLoader.  
```python
from jinja2 import Environment, FileSystemLoader

persons = [
    {'name': 'Алексей', 'old': 18, 'weight': 78.5},
    {'name': 'Николай', 'old': 28, 'weight': 82.3},
    {'name': 'Иван', 'old': 33, 'weight': 94.8},
]

#формируем загрузчик
file_loader = FileSystemLoader('templates') #указываем подкаталог в папке с программой
env = Environment(loader=file_loader) #создаем класс окружения со ссылкой на загрузчик

tm = env.get_template('main.html') # получаем шаблон из main.htm. get_template формирует экземпляр класса Template на основе mail.htm
msg = tm.render(users=persons)

print(msg)
```  

Ниже код файла html, которым мы воспользовались как шаблоном:  

```HTML
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <base href='https://proproprogs.ru/'>
    <title>Про программирование</title>
</head>
<body>

<ul>
{% for u in users -%}
    <li>{{ u.name }}
{% endfor -%}
</ul>

</body>
</html>
```  

Загрузчики в пакете jinja:
1. PackageLoader - для загрузки шаблонов из пакета  
2. DictLoader - для загрузки шаблонов из словаря
3. FunctionLoader - для загрузки на основе функции
4. PrefixLoader - загрузчик, использующий словарь для построения подкаталогов
5. ChoiceLoader - загрузчик, содержащий список других загрузчиков (если один не сработает, выбираем следующий)
6. ModuleLoader - загрузчик для скомпилированных шаблонов  

Пример с FunctionLoader:  
```python
from jinja2 import Environment, FileSystemLoader, FunctionLoader

persons = [
    {'name': 'Алексей', 'old': 18, 'weight': 78.5},
    {'name': 'Николай', 'old': 28, 'weight': 82.3},
    {'name': 'Иван', 'old': 33, 'weight': 94.8},
]

#функция, на основе которой работает FunctionLoader
def loadTpl(path): #указываем шаблон, который хотим загрузить
    if path == "index": #если принимает значение index
        return '''Имя {{ u.name }}, возраст {{ u.old }}'''
    else:
        return '''Данные: {{u}}'''

#формируем загрузчик
file_loader = FunctionLoader(loadTpl) #создаем загрузчик на основе функции, в аргумент даем ссылку на функцию
env = Environment(loader=file_loader) #создаем класс окружения со ссылкой на загрузчик

tm = env.get_template('index') # получаем шаблон из main.htm. get_template формирует экземпляр класса Template на основе mail.htm
msg = tm.render(u=persons[0]) #берем только 1 значение, тк шаблон отображает только 1 значение

print(msg)
```