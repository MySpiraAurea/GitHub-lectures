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
4. # ## - строковый комментарий 


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