## Jinja


Jinja - модуль, который служит для обработки шаблонов в Python. Входит в модуль Django.  
```python  
from jinja2 import Template

name = "Федор"

tm = Template("Привет {{ name }}")
msg = tm.render(name=name)

print(msg)