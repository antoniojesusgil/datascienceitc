```python
re.match(r'[a-zA-Z\._0-9-]+@[a-zA-Z\._0-9-]+\.[a-zA-Z]{2,}', 'antonio_jesusGIL2019@antonio_jesusGIL2019.com')
```

```python
def list_emails(text):
    # Debe devolver todos los emails encontrados en cadena
    # return re.findall(r'[a-zA-Z\._0-9-]+@[a-zA-Z\._0-9-]+\.[a-zA-Z]{2,}', text)
    # Optimizando la expresion regular
    return re.findall(r'[\w-]+@[\w\._0-9-]+\.[\w]{2,}', text)
    pass
    
list_emails(email_uai)
```

```python
file = open("data/access_log", "r")
content = file.read()

# Expreseion regular mediante grupos conforme a lo que se indica abajo

patron = '^(?P<host>\S+) \S+ \S+ \[(?P<fecha>[\w/]+):(?P<hora>[\d:]+)[^"]+"(?P<verbo>[A-Z]+) (?P<recurso>\S+) [^"]+" (?P<codigo>\d+)'


regex = re.compile(patron, re.MULTILINE)
data = []
for match in regex.finditer(content):
    data.append({
        'host': match.group('host'),
        'fecha': match.group('fecha'),
        'hora': match.group('hora'),
        'verbo': match.group('verbo'),
        'recurso': match.group('recurso'),
        'codigo': match.group('codigo')
    })
    
print(data[0:4])