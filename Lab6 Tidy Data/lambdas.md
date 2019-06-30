## Lambdas

Se trata de crear funciones de manera rápida, `just in time`, para acciones requieran de una pequeña operación o comprobación. Toda función lambda puede expresarse como una función convencional pero no a la inversa.

Lambdas también son conocidas como funciones anónimas. Y es que más allá del sentido de función que tenemos, con su nombre y sus acciones internas, una función en su sentido más trivial significa realizar algo sobre algo. Por tanto podríamos decir que, mientras las funciones anónimas **lambda** sirven para realizar funciones simples, las funciones definidas con def sirven para manejar tareas más extensas.

Si deconstruimos una función sencilla, podemos llegar a una función lambda. Por ejemplo tomad la siguiente función para doblar un valor:

```python
def doblar(num):
    resultado = num*2
    return resultado

doblar(2)
```

Vamos a simplificar el código un poco:

```python
def doblar(num):
    return num*2
```

Todavía más, podemos escribirlo todo en una sola línea:

```python
def doblar(num): return num*2
```
Esta notación simple es la que una función lambda intenta replicar, se convierte la función en una anónima:

```python
lambda num: num*2
```
Aquí tenemos una función anónima con una entrada que recibe num, y una salida que devuelve num * 2.

Lo único que necesitamos hacer para utilizarla es guardarla en una variable y utilizarla tal como haríamos con una función normal:

```python
doblar = lambda num: num*2

doblar(2)
```

### Función filter()

A partir de una lista o iterador y una función condicional, es capaz de devolver una nueva colección con los elementos filtrados que cumplan la condición.

Supongamos que tenemos una lista varios números y queremos filtrarla, quedándonos únicamente con los múltiples de 5.
Al ejecutar el filtro se obtiene un un objeto de tipo filtro, se transforma en una lista haciendo un cast. 

```python
numeros = [2, 5, 10, 23, 50, 33]

list( filter(lambda numero: numero%5 == 0, numeros) )
```
### Función map()

Muy similar a filter(), con la diferencia que en lugar de aplicar una condición a un elemento de una lista o secuencia, aplica una función sobre todos los elementos y como resultado se devuelve un iterable de tipo map.

**map()** que permite ahorrar bucles for.

```python
a = [1, 2, 3, 4, 5]
b = [6, 7, 8, 9, 10]

list( map(lambda x,y : x*y, a,b) )
```