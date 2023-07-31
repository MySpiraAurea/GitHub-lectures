## Jinja

### Урок 1


Jinja - модуль, который служит для обработки шаблонов в Python. Входит в модуль Django.  
```python  
from jinja2 import Template #импортируем библиотеку

name = "Федор"

tm = Template("Привет {{ name }}")  #в фигурных скобках стоит ссылка на переменную name
msg = tm.render(name=name)  # указатель будет ссылаться на переменную name

print(msg)
```