



<br><br><br>
<h2><font color="#004D7F" size=5>Módulo 1</font></h2>



<h1><font color="#004D7F" size=6>Introducción a Pandas I</font></h1>

<br>
<div style="text-align: right">
<font color="#004D7F" size=3>Antonio Jesús Gil</font><br>
<font color="#004D7F" size=3>Etic Data scientist Course</font><br>

</div>

---

<a id="indice"></a>
<h2><font color="#004D7F" size=5>Índice</font></h2>


* [1. Introducción](#section1)
* [2. Series](#section2)
   * [Creación de series](#section21)
   * [Acceso a los elementos de una serie](#section22)
* [3. Operaciones y funciones sobre series](#section3)
   * [Operadores binarios](#section31)
   * [<font face="monospace">apply() y map()</font>](#section32)
   * [Operaciones sobre Strings](#section33)   
   * [Descripción y resumen](#section34)
   * [Manipulación de series](#section35)   
   * [Consulta y selección](#section36)
   * [Vectorización](#section37)
* [4. Datos categóricos](#section4)
    * [El objeto <font face="monospace">Categorical</font> y el tipo <font face="monospace">CategoricalDType</font>](#section41)
    * [Series con valores categóricos](#section42)    
    * [Operaciones con valores categóricos](#section43) 
    * [Indexación con valores categóricos](#section44)    
* [5. Datos temporales en Pandas](#section5)
   * [Marcas de tiempo: <font face="monospace"> Timestamp</font>](#section51)

---

<a id="section1"></a>
# <font color="#004D7F" size=5> 1. Introducción</font>

[**Pandas**](http://pandas.pydata.org) es una librería de Python que proporciona estructuras y herramientas para el preprocesamiento y análisis exploratorio de conjuntos de datos. Trabaja principalemente con objetos denominados `DataFrame`, que representan tablas indexadas de datos, e implementan funciones avanzadas para selección, consultas, agrupamiento, procesamiento, etc. El siguiente código lee un archivo de datos y lo almacena y muestra en un `DataFrame`.


```python
import pandas as pd

# Lee los datos de las personas que viajaban en el Titanic      
df_titanic = pd.read_csv('datos/Titanic.csv', index_col=0) 
# Muestra los 5 primeros casos 
df_titanic.head(5)   
```

En estas libretas se proporciona una descripción inicial de Pandas, y se presentarán, mediante ejemplos, las funcionalidades básicas de uso más común. Esta introducción será extendida en _módulo 2_ del curso en el que, además se dará una visión formal del conjunto de los datos y su tratamiento. No obstante, Pandas es una librería _completa y compleja_, por lo que será indispensable recurrir ocasionalmente a la abundante información existente en la red, a foros como [Stack Overflow](https://stackoverflow.com/questions/tagged/pandas), y a la [documentación oficial de Pandas](http://pandas.pydata.org/pandas-docs/stable/). 

<a id="section2"></a>
# <font color="#004D7F" size=5> 2. Series </font>

Un objeto de la clase `Series` almacena una colección de valores e _indexarlos_ mediante etiquetas. Además, esta clase implementa multitud de operaciones, y es la forma en que se accede a columnas o filas individuales en objetos de tipo `DataFrame`, que serán descritos con detalle en la próxima libreta. 

<div class="alert alert-block alert-info">
<i class="fa fa-info-circle" aria-hidden="true"></i>
Este último uso de las series, que se verá posteriormente, es el más común. 
</div>

<a id="section21"></a> 
## <font color="#004D7F"> Creación de series </font>

Es posible crear objetos de tipo `Series` de distintos modos a partir de estructuras estándar de Python y Numpy.

### <font color="#004D7F"> Creación a partir de una colección de elementos</font>

Se puede construir una serie a partir de una colección de datos. En caso de no especificar un índice, la serie se indexa internamente con enteros.


```python
import pandas as pd

equipos = ['Real Madrid', 'Manchester United', 'Milán']
serie_equipos = pd.Series(equipos)
print(serie_equipos)                       # Se indexa con enteros
print()

numeros = [2, 21, 23]                      # La serie también puede contener datos de tipos básicos.
serie_numeros = pd.Series(numeros)         # Se indexa también con enteros
print(serie_numeros)
```

### <font color="#004D7F"> Creación a partir de una colección y un índice </font>

Es posible especificar la secuencia que contiene el índice. Éste, que por defecto es un entero, puede estar formado por datos de cualquier tipo al que se pueda aplicar una función _hash_ (función _hashable_).


```python
serie_equipos = pd.Series(['Real Madrid', 'Manchester United', 'Milán'], 
                          index=['España', 'Inglaterra', 'Italia'])
print(serie_equipos)
```

### <font color="#004D7F"> Creación a partir de un diccionario </font>

También se puede construir la serie a partir de un diccionario. En ese caso, las claves corresponden a los índices, y los valores a los elementos de la serie. Si además del diccionario se pasa una lista de índices, ésta se usa para determinar qué valores del diccionario se incluyen.


```python
equipos = {'España': 'Real Madrid',
           'Inglaterra': 'Manchester United',
           'Italia': 'Milán',
           'Francia': 'PSG',
           'Alemania': 'Bayern Munich' }
serie_equipos = pd.Series(equipos)
print(serie_equipos)
print()

# En este caso, se indican índices, por lo que solo se tomarán del diccionario entradas cuya clave coincida
# con alguno de los índices.
serie_equipos = pd.Series(equipos, index=['España', 'Inglaterra', 'Italia'])
print('Segunda serie:')
print(serie_equipos)
```

Es posible nombrar la serie de valores, y también es posible dar un valor al índice. 


```python
serie_equipos.name='Equipo'
serie_equipos.index.name = 'País'
serie_equipos
```

<div class="alert alert-block alert-info">
<i class="fa fa-info-circle" aria-hidden="true"></i>
**Nota**: Posteriormente se verá que, cuando las series se obtienen a partir de un `DataFrame`, toman como nombre el de la columna (o índice) correspondiente.
</div>

<a id="section22"></a> 
## <font color="#004D7F"> Acceso a los elementos de una serie </font>

El acceso natural a los elementos de una serie se hace mediante su índice, con `loc[índice]`, aunque también se pueden acceder mediante la posición, como el resto de colecciones, a través de `iloc[posición]`. 

También es posible acceder a los elementos mediante el operador `[]`, que llama internamente al método correspondiente (`loc` o `iloc`). 


```python
print(serie_equipos, '\n')

print("Mediante loc e iloc:")
print(serie_equipos.loc['España'])
print(serie_equipos.iloc[1])

print("\nMediante []")
print(serie_equipos['España'])
print(serie_equipos[0])
```

<div class="alert alert-block alert-danger">
<i class="fa fa-exclamation-triangle" aria-hidden="true"></i>
__Importante__: Cuando el índice de la serie es un entero, llama a `loc[]`, es decir, usa el entero como como índice, y no como posición. 
</div>


```python
print("Este ejemplo muestra la situación que se da cuando el índice es un entero: \n")
equipos = {9: 'Real Madrid',
           4: 'Manchester United',
           8: 'Milán',
           2: 'PSG',
           1: 'Bayern Munich'}

s = pd.Series(equipos)
print(s,'\n')
print(s.loc[2])            # Imprime el elemento de índice 2.
print(s.iloc[2])           # Imprime el elemento en la posición 2.
print(s[2])                # En este caso, llama a loc[]
#print(s[0])               # Esta sentencia daría error, porque no hay ningún elemento con índice 0.
```

### <font color="#004D7F"> Indexación mediante listas y arrays. </font>

Tanto la función `loc` como `iloc` admiten el uso de colecciones, es decir, acceder a varios elementos de la serie a la vez.



```python
equipos = {'España': 'Real Madrid',
           'Inglaterra': 'Manchester United',
           'Italia': 'Milán',
           'Francia': 'PSG',
           'Alemania': 'Bayern Munich'}
serie_equipos = pd.Series(equipos)
print(serie_equipos, '\n')

print(serie_equipos.loc[['España', 'Italia', 'Francia']],'\n')
print(serie_equipos.iloc[[0,2,3]])
```

<div class="alert alert-block alert-warning">
<i class="fa fa-exclamation-circle" aria-hidden="true"></i>
__Importante__: Tanto `loc` como `iloc` toman _un solo argumento_, que ha de ser una colección (no varios datos).
</div>

### <font color="#004D7F"> Slicing </font>

El acceso a los elementos de una serie también se puede hacer mediante _slicing_.



```python
equipos = {'España': 'Real Madrid',
           'Inglaterra': 'Manchester United',
           'Italia': 'Milán',
           'Francia': 'PSG',
           'Alemania': 'Bayern Munich'}
serie_equipos = pd.Series(equipos)
print(serie_equipos,'\n')

print(serie_equipos.iloc[0:3],'\n')
print(serie_equipos.loc['España':'Inglaterra'])
```

### <font color="#004D7F"> Indexación mediante valores booleanos </font>

Por último, es posible acceder a las series mediante una colección de datos de tipo booleano.


```python
equipos = {'España': 'Real Madrid',
           'Inglaterra': 'Manchester United',
           'Italia': 'Milán',
           'Francia': 'PSG',
           'Alemania': 'Bayern Munich'}
serie_equipos = pd.Series(equipos)
print(serie_equipos,'\n')

serie_equipos[[True,False,True,False,True]]
```

El uso más común de esta funcionalidad es el filtrado de elementos según una condición. 


```python
import numpy as np
mundiales = {'España': 1 ,
             'Inglaterra': 1,
             'Italia': 4,
             'Francia': 1,
             'Alemania': 4}
serie_mundiales = pd.Series(mundiales)
print(serie_mundiales,'\n')

print(serie_mundiales>1,'\n')                     # Genera una serie de valores booleanos

print(serie_mundiales[serie_mundiales>1])         # Accede a los elementos con ese índice de valores booleanos.
```

Se puede hacer un acceso con condiciones más complejas, siempre y cuando éstas den lugar a un conjunto de valores booleanos.


```python
champions = {'Real Madrid':12, 'Manchester':3, 'Milán':7, 'Bayern Munich':5, 'PSG': 0}

equipos = {'España': 'Real Madrid',
           'Inglaterra': 'Manchester',
           'Italia': 'Milán',
           'Francia': 'PSG',
           'Alemania': 'Bayern Munich'}

serie_equipos = pd.Series(equipos)

# Esta línea de código mira para cada elemento de la serie, si el número de champions es mayor que 5.
print(list(map(lambda equipo: champions[equipo]>=5, serie_equipos)),'\n')

# Se puede utilizarla expresión anterior para obtener una serie con los equipos.
serie_equipos[list(map(lambda equipo: champions[equipo]>=5, serie_equipos))]
```

### <font color="#004D7F">  Acceso para escritura  </font>



Se hace de manera similar. Es posible escribir varias posiciones a la vez.


```python
print(serie_equipos)
print()

serie_equipos.loc['Alemania'] = 'Borussia Dormund'  
serie_equipos.iloc[3] = 'Chelsea'                    
serie_equipos['España']='Atlético de Madrid'
serie_equipos[[2,4]]=['Mónaco', 'Juventus']
serie_equipos['Portugal']='Benfica'            # Si se asigna un valor a un nuevo índice, se añade a la serie
print(serie_equipos)
```

### <font color="#004D7F">  Acceso a índices y valores </font>

Es posible acceder al índice y lista de valores contenidos en la serie por separado.


```python
print(serie_equipos.index.values)
print(serie_equipos.index.values[0])
print()

print(serie_equipos.values)
print(serie_equipos.values[0])
```

<a id="section3"></a>
# <font color="#004D7F" size=5> 3. Operaciones y funciones sobre series </font>

<br>
La clase `Series` implementa una gran cantidad de operadores y funciones. Las de uso más general se organizan y describen a continuación. Para información completa, se puede consultar el [API de la clase Series](https://pandas.pydata.org/pandas-docs/stable/api.html#series).

<a id="section31"></a> 
## <font color="#004D7F"> Operadores binarios </font>

Al igual que con los arrays Numpy, es posible llevar a cabo operaciones a nivel de elemento entre series, o entre series y escalares. Éstas pueden hacerse mediante operadores o funciones.


```python
champions = {'Real Madrid':12, 'Manchester United':3, 'Milán':7, 'Bayern Munich':5, 'PSG': 0}
serie_champions = pd.Series(champions)

europa_league = {'Real Madrid':2, 'Manchester United':1, 'Milán':0, 'Bayern Munich':1, 'PSG': 0}
serie_europa_league = pd.Series(europa_league)

# Operación entre series
print(serie_champions+serie_europa_league,'\n')      # Operador
print(serie_champions.add(serie_europa_league),'\n') # Función

# Operación entre serie y valor escalar
print(serie_champions>3)
```

Las operaciones que involucran varias series se llevan a cabo entre elementos con el mismo índice. 


```python
europa_league = {'Real Madrid':2, 'Manchester United':1, 'Milán':0, 
                 'Bayern Munich':1, 'PSG': 0, 'Atlético de Madrid':2}
serie_europa_league = pd.Series(europa_league)

print(serie_champions+serie_europa_league)      # De igual modo se pueden aplicar otros operadores
```

<div class="alert alert-block alert-warning">
<i class="fa fa-exclamation-circle" aria-hidden="true"></i>
__Importante__: Cuando uno de los elementos no aparece en alguna de las series implicadas, el resultado es NaN.
</div>

Es posible definir funciones que se implementen a partir de estos operadores u otros definidos para series, y aplicarlas sobre las series. 


```python
import numpy as np

def titulos_decada(titulos):
    ''' Numero medio de títulos ganados por cada década de competición'''
    return titulos/6.0

champions = {'Real Madrid':12, 'Manchester United':3, 'Milán':7, 'Bayern Munich':5, 'PSG': 0}
serie_champions = pd.Series(champions)

titulos_decada(serie_champions) # Se aplica la función sobre cada elemento de la serie. 
```

<a id="section32"></a> 
## <font color="#004D7F" face="monospace"> apply() y map()</font>

Las funciones `apply()`  y `map()` permiten _aplicar_ una función cualquiera a cada elemento de una serie. Como resultado de su aplicación, se genera otra serie. 


<div class="alert alert-block alert-info">
<i class="fa fa-info-circle" aria-hidden="true"></i>
**Nota**: Estas funciones se utilizan de manera muy frecuente, ya permiten utilizar _cualquier_ función para transformar los valores de la serie. 
</div>


```python
champions = {'Real Madrid':12, 'Manchester United':3, 'Milán':7, 'Bayern Munich':5, 'PSG': 0}
europa_league = {'Real Madrid':2, 'Manchester United':1, 'Milán':0, 'Bayern Munich':1, 'PSG': 0}

# Ahora solamente se dispone de una serie que se indexa por países. 
equipos = {'España': 'Real Madrid',
           'Inglaterra': 'Manchester United',
           'Italia': 'Milán',
           'Francia': 'PSG',
           'Alemania': 'Bayern Munich'}
serie_equipos = pd.Series(equipos)

def copas_equipo(equipo):                                        # Utilizamos get en lugar de [] en el acceso a los
    return champions.get(equipo,0)+europa_league.get(equipo,0)   # diccionarios para poder devolver un valor por
                                                                 # defecto (0) si el equipo no existe. 
serie_equipos.apply(copas_equipo)
```

Es muy frecuente utilizar funciones `lambda` junto con `apply` o `map`.


```python
serie_equipos.map(lambda equipo: champions.get(equipo,0)+europa_league.get(equipo,0))
```

<div class="alert alert-block alert-info">
<i class="fa fa-info-circle" aria-hidden="true"></i>
**Nota**: Las funciones `lambda` se usan por agilidad y claridad en el código. No obstante, las funciones estándar son más eficientes.
</div>

<a id="section33"></a> 
## <font color="#004D7F"> Operaciones sobre Strings </font>

Pandas implementa un conjunto de operaciones para el manejo de series de Strings, y que se aplican a nivel de elemento. Muchas de estas funciones corresponden con las equivalentes en las librerías de Python. El listado completo de funciones puede encontrarse en la sección correspondiente de la [API](https://pandas.pydata.org/pandas-docs/stable/api.html#string-handling).


```python
equipos = {'España': 'Real Madrid',
           'Inglaterra': 'Manchester',
           'Italia': 'Milán',
           'Francia': 'PSG',
           'Alemania': 'Bayern Munich'}
serie_equipos = pd.Series(equipos)

# Pasa a mayúsculas cada elemento de la serie. 
serie_equipos = serie_equipos.str.upper()
print(serie_equipos,'\n')

# Ahora deja solo cada inicial en mayúscula.
serie_equipos = serie_equipos.str.capitalize()
print(serie_equipos)
```

<a id="section34"></a> 
## <font color="#004D7F"> Descripción y resumen </font>

Los objetos de tipo `Series` implementan también una gran cantidad de funciones que actúan sobre el conjunto general de los datos. Algunas de ellas permiten obtener algunos datos de interés sobre la serie. El siguiente fragmento de código contiene ejemplos de llamadas a algunas de ellas. 


```python
champions = {'Real Madrid':12, 'Manchester United':3, 'Milán':7, 'Bayern Munich':5, 'PSG': 0}
serie_champions = pd.Series(champions)

print("Numero de elementos: ",serie_champions.count())                   
print("Suma de los valores: ",serie_champions.sum())                      
print("Índice del máximo valor: ",serie_champions.idxmax())                   
print("Máximo valor: ",serie_champions.max())                     
print("Media: ",serie_champions.mean())                      
print("Desviación estándar: ", serie_champions.std())                      
print("2 mayores valores: ",serie_champions.nlargest(2))  
```

La función `describe` devuelve un resumen descriptivo de los datos. Admite algunos parámetros, como por ejemplo los percentiles. 


```python
equipos = {'España': 'Real Madrid', 'Inglaterra': 'Manchester', 'Italia': 'Milán','Francia': 'PSG', 'Alemania': 'Bayern Munich'}
serie_equipos = pd.Series(equipos)

champions = {'Real Madrid':12, 'Manchester United':3, 'Milán':7, 'Bayern Munich':5, 'PSG': 0}
serie_champions = pd.Series(champions)

print("Serie con valores numéricos:")
print(serie_champions.describe(percentiles=[0.2,0.4,0.6,0.8]),'\n')                 # Resumen de los valores (datos numéricos) 

print("Serie con valores no numéricos:")
print(serie_equipos.describe(),'\n')                   # Resumen de los valores (datos no numéricos)
```

Mediante la función `unique()` es posible determinar qué valores aparecen en la serie. Esta funcionalidad es especialmente  útil cuando los valores son categóricos.


```python
europa_league = {'Real Madrid':2, 'Manchester United':1, 'Milán':0, 
                 'Bayern Munich':1, 'PSG': 0, 'Atlético de Madrid':2}
serie_europa_league = pd.Series(europa_league)
                                
print("Valores distintos: ",serie_europa_league.unique())               
print("Número de valores distintos:", serie_europa_league.nunique())             
print("Veces que aparece cada valor:\n", serie_europa_league.value_counts())          
```

<a id="section35"></a> 
## <font color="#004D7F"> Manipulación de series </font>

### <font color="#004D7F"> Eliminación de elementos </font>

Es posible eliminar elementos de la serie mediante la función `drop()`. Ésta recibe como argumento una clave.


```python
champions = {'Real Madrid':12, 'Manchester United':3, 'Milán':7, 'Bayern Munich':5, 'PSG': 0}
serie_champions = pd.Series(champions)

serie_champions.drop('Real Madrid')
print(serie_champions)
```

### <font color="#004D7F"> Concatenación de series </font>

El método `append()` permite concatenar series. 


```python
champions = {'Real Madrid':12, 'Manchester United':3, 'Milán':7, 'Bayern Munich':5, 'PSG': 0}
serie_champions = pd.Series(champions)

europa_league = {'Real Madrid':2, 'Manchester United':1, 'Milán':0, 'Bayern Munich':1, 'PSG': 0}
serie_europa_league = pd.Series(europa_league)

print(serie_champions.append(serie_europa_league))
```

### <font color="#004D7F">Sustitución de elementos </font>

El método `replace()` permite reemplazar valores. Admite como parámetro un diccionario, permitiendo así sustituir varios valores a la vez. Incluso es posible utilizar expresiones regulares.


```python
champions = {'Real Madrid':12, 'Manchester United':3, 'Milán':7, 'Bayern Munich':5, 'PSG': 0, 'Atlético de Madrid':0}
serie_champions = pd.Series(champions)
print(serie_champions,'\n')
print(serie_champions.replace(0,'Nunca'),'\n')

equipos = {'España': 'Real Madrid', 'Inglaterra': 'Manchester United', 'Italia': 'Milán', 'Francia': 'PSG', 'Alemania': 'Bayern Munich'}
serie_equipos = pd.Series(equipos)

print(serie_equipos, '\n')
print(serie_equipos.replace({'Real Madrid':'Atlético de Madrid', 'Milán':'Juventus'}))
```

<a id="section36"></a> 
## <font color="#004D7F"> Consulta y selección </font>

Además de las posibilidades que ofrece la indexación, existen algunas funciones que permiten llevar a cabo diversas operaciones de consulta y selección sobre `Series`.

### <font color="#004D7F" face="monospace"> head() / tail() </font>


La función `head()` muestra los primeros elementos de la serie; la función `tail()` los últimos.


```python
champions = {'Real Madrid':12, 'Manchester United':3, 'Milán':7, 'Bayern Munich':5, 'PSG': 0}
serie_champions = pd.Series(champions)

print(serie_champions.head(3),'\n')                 # Devuelve los 3 primeros elementos. 
print(serie_champions.tail(2))                      # Devuelve los 2 últimos elementos. 
```

### <font color="#004D7F" face="monospace"> any() / all() </font>

Las funciones denominadas `any()` y `all()`  permiten determinar si uno o todos  (respectivamente) los valores de la serie, son distintos de `False` (o 0). Estas funciones se suelen utilizar también junto a una condición. 


```python
champions = {'Real Madrid':12, 'Manchester United':3, 'Milán':7, 'Bayern Munich':5, 'PSG': 0}
serie_champions = pd.Series(champions)

print(serie_champions.any())
print(serie_champions.all(),'\n')

print(serie_champions>2,'\n')                      # Esta comparación genera una serie. 

print((serie_champions>=2).any())                  # Se le puede aplicar la función any().
```

### <font color="#004D7F" face="monospace">isna()/isnull() y notna()/notnull() </font>

También existen las funciones `isna()`/`isnull()` y `notna()`/`notnull()` que permiten determinar qué elementos de la serie son `NaN` (o `None`, dependiendo del tipo de datos) o no lo son.


```python
import numpy as np

champions = {'Real Madrid':12, 'Manchester United':3, 'Milán':7, 'Bayern Munich':5, 'PSG': 0}
serie_champions = pd.Series(champions)
serie_champions['Real Madrid']=np.nan       # Muestra qué elementos son null o na.

print(serie_champions.isnull())
```

### <font color="#004D7F" face="monospace"> where() y mask() </font>

La función `where(condición)` devuelve una serie en la que los elementos que __no__ cumplen la conadición toman un valor predefinido, que por defecto es `NaN`, pero puede ser un escalar, o incluso el valor correspondiente en otra serie. 


```python
print(serie_champions.where((serie_champions > 3) & (serie_champions<10)))
print()
print("Con un 0 por defecto:")
print(serie_champions.where((serie_champions > 3) & (serie_champions<10),0))
print()
print("A los que tienen menos de 3 champions, o más de 10, les asigna el número de Europa League")
print(serie_champions.where((serie_champions > 3) & (serie_champions<10),serie_europa_league))
```

<div class="alert alert-block alert-warning">
<i class="fa fa-exclamation-circle" aria-hidden="true"></i>
Además de la posibilidad de reemplazar los elementos que no cumplen la condición, la diferencia de esta función con la indexación es que esta última opción solamente devuelve los elementos que cumplen la condición.
</div>


```python
serie_champions[(serie_champions>3) & (serie_champions<12)]
```

La función `mask()` es similar a la anterior, pero copia tal cual los elementos que __no__ cumplen la condición.


```python
print("Con un 0 por defecto:")
print(serie_champions.mask((serie_champions > 5),0))
```

<a id="section37"></a> 
## <font color="#004D7F"> Vectorización </font>

Internamente, Pandas utiliza Numpy para almacenar los datos. Por tanto, algunas de las  peraciones que se pueden llevar sobre los objetos `Series` (y los `DataFrame` que se verán después) son vectorizadas. Esto supone un aumento importante en la eficiencia.


```python
import numpy as np
# Crea una serie de números aleatorios
serie = pd.Series(np.random.randint(0,1000,10000))
print(serie.shape)
```

En las dos celdas siguientes, calcula la suma de los elementos de la serie, con y sin vectorización, respectivamente.


```python
%%timeit -n 10
suma = 0
for elem in serie:
    suma+=elem
```


```python
%%timeit -n 10
suma = np.sum(serie)
```

En el siguiente ejemplo se compara el tiempo de ejecución de una suma con un escalar mediante un bucle, y mediante _Broadcast_. (En este caso, se inicializa la estructura en la misma linea de `timeit`, pero ese tiempo no se computa).


```python
%%timeit -n 10 serie = pd.Series(np.random.randint(0,1000,10000))
for indice, valor in serie.iteritems():
    serie.loc[indice]= valor+2
```


```python
%%timeit -n 10 serie = pd.Series(np.random.randint(0,1000,10000))
serie+=2
```

<a id="section4"></a>
# <font color="#004D7F"> 4. Datos categóricos </font>

<br>
Pandas implementa un tipo de datos específico para el trabajo con datos categóricos, es decir, variables que pueden tomar un número limitado de valores. Aunque estos se visualizan generalmente como Strings, internamente se representan mediante (enteros), lo cual permite aumentar la eficiencia en su tratamiento.

<a id="section41"></a>
## <font color="#004D7F"> El objeto <font face="monospace">Categorical</font> y el tipo  <font face="monospace">CategoricalDType </font></font>

<br>
El objeto `pd.Categorical` permite representar __una colección__ (_no una serie_) de datos relativos a categorías. Por defecto, considera tantas categorías como detecta en los datos, pero éstas se pueden especificar mediante el argumento `categories`.


```python
calidades = pd.Categorical(["primera","segunda","tercera","primera", "tercera", "especial"])
print(calidades,'\n')

# Solo considera tres categorías. 
calidades = pd.Categorical(["primera","segunda","tercera","primera", "tercera", "especial"],
                           categories=["primera","segunda","especial"])
print(calidades)
```

<div class="alert alert-block alert-info">
<i class="fa fa-info-circle" aria-hidden="true"></i>

En una colección de valores categóricos, aquellos cuya categoría no ha sido especificada se codifican como `np.NaN`.
</div>

También es posible asignar un orden en las categorías.


```python
calidades = pd.Categorical(["primera","segunda","tercera","primera", "tercera", "especial"]
                          ,categories=["primera","segunda","especial"], ordered=True)
print(calidades)
print(type(calidades))
```

Una categoría se define por dos parámetros:

1. `categories`: La secuencia de valores que representan las categorías.
2. `ordered`: Un valor booleano que representa si se establece un orden o no en las categorías. 

Ambos se representan en el tipo `CategoricalDType`.


```python
from pandas.api.types import CategoricalDtype

tipo_calidades = CategoricalDtype(categories=["primera","segunda","especial"], ordered=True)
```

Es posible construir una secuencia de valores categóricos pasando como parámetro un objeto de tipo `CategoricalDtype` a través del parámetro `dtype`.


```python
calidades = pd.Categorical(["primera","segunda","tercera","primera", "tercera", "especial"], dtype=tipo_calidades)
print(calidades)
```

<a id="section42"></a>
## <font color="#004D7F"> Series con valores categóricos</font>


Se puede crear una serie de valores categóricos directamente, especificando (a través de `dtype`) el tipo `category`.


```python
# Crea una serie con valores de tipo category
calidades = pd.Series(["primera","segunda","tercera","primera", "tercera", "especial"], dtype="category")
print(calidades,'\n')

# Crea una serie de valores de tipo String
calidades = pd.Series(["primera","segunda","tercera","primera", "tercera", "especial"])
print(calidades,'\n')
print()

# Convierte la serie a valores de tipo category
calidades = calidades.astype("category")
print(calidades)
```

También es posible crear la serie pasando como tipo un objeto  `CategoricalDtype`.


```python
tipo_calidades = CategoricalDtype(categories=["primera","segunda","especial"], ordered=True)
calidades = pd.Series(["primera","segunda","tercera","primera", "tercera", "especial"], dtype=tipo_calidades)
print(calidades)
```

### <font color="#004D7F"> Manipulación de las categorías </font>

En series categóricas, es posible acceder a las propiedades de las categorías a través del atributo `cat`.


```python
print(calidades.cat.categories)
print(calidades.cat.ordered)
```

<div class="alert alert-block alert-info">
<i class="fa fa-info-circle" aria-hidden="true"></i>
Internamente, la serie contiene, en el atributo `values`, un objeto del tipo `Categorical`. Es posible acceder a sus campos y métodos, pero es preferible hacer este acceso a traves de serie.cat. 
</div>

Es posible renombrar las categorías accediendo directamente al atributo `cat.categories`.


```python
tipo_calidades = CategoricalDtype(categories=["primera","segunda","especial"], ordered=True)
calidades = pd.Series(["primera","segunda","tercera","primera", "tercera", "especial"], dtype=tipo_calidades)
print(calidades,'\n')

calidades.cat.categories = ['1ª','2ª','Extra']
print(calidades)
```

También mediante el método `rename_categories()` de `Categorical`.

<div class="alert alert-block alert-warning">
<i class="fa fa-exclamation-circle" aria-hidden="true"></i>
Las funciones que se aplican a `Categorical` se pueden aplicar a `Series.cat`. La aplicación de éstas genera una nueva serie. Para modificar la original, hay que pasar el parámetro `inplace=True`.
</div>


```python
tipo_calidades = CategoricalDtype(categories=["primera","segunda","especial"], ordered=True)
calidades = pd.Series(["primera","segunda","tercera","primera", "tercera", "especial"], dtype=tipo_calidades)
calidades.cat.rename_categories(['1ª','2ª','Extra'], inplace=True)
print(calidades,'\n')

# También puede tomar un diccionario
calidades.cat.rename_categories({'1ª':'primera otra vez','2ª':'segunda otra vez','Extra':'extra otra vez'}, inplace=True)
print(calidades)
```

Es posible añadir y borrar categorías con las funciones`add_categories`, `remove_categories`, respectivamente. 


```python
tipo_calidades = CategoricalDtype(categories=["primera","segunda","especial"], ordered=True)
calidades = pd.Series(["primera","segunda","tercera","primera", "tercera", "especial"], dtype=tipo_calidades)
print(calidades,'\n')

calidades.cat.add_categories(["otra"], inplace=True)
print(calidades)
```

También es posible borrar solamente las categorías que no aperecen mediante `remove_unused_categories`.


```python
calidades=calidades.cat.remove_unused_categories()
print(calidades)
```

El método `set_categories()` permite fijar las categorías directamente.  Al aplicarlo, todos aquellos valores que no correspondan con alguna de las nuevas categorías, pasan a valer `NaN`.


```python
calidades.cat.set_categories(["2ª","primera","Extra"], ordered=True, inplace=True)
print(calidades)
```

También es posible cambiar solamente el orden entre las categorías mediante `reorder_categories()`.


```python
tipo_calidades = CategoricalDtype(categories=["primera","segunda","especial"], ordered=True)
calidades = pd.Series(["primera","segunda","tercera","primera", "tercera", "especial"], dtype=tipo_calidades)
print(calidades,'\n')

calidades.cat.reorder_categories(["segunda","primera","especial"], ordered=True)
```

### <font color="#004D7F"> Ordenación según categorías</font>

Es posible ordenar las series en función de las etiquetas mediante `sort_values()`. 


```python
tipo_calidades = CategoricalDtype(categories=["segunda","primera","especial"], ordered=True)
calidades = pd.Series(["primera","segunda","tercera","primera", "tercera", "especial"], dtype=tipo_calidades)

print(calidades.sort_values(),'\n')
```

Igualmente, es posible transformar series ordenadas a no ordenadas o viceversa. Estas operaciones producen nuevas series a menos que se fije `inplace=True`. 


```python
print(calidades.cat.as_ordered(),'\n')
print(calidades.cat.as_unordered())
```

### <font color="#004D7F">Unión  categorías</font>

Las colecciones de datos categóricos pueden unirse mediante `union_categoricals`.


```python
from pandas.api.types import union_categoricals

calidades1 = pd.Categorical(["Extra", "Especial", "Extra", "Especial"])
calidades2 = pd.Categorical(["Primera", "Primera", "Segunda", "Segunda"])
union_categoricals([calidades1, calidades2])
```

<a id="section43"></a>
## <font color="#004D7F"> Operaciones con valores categóricos</font>

Es posible llevar a cabo operaciones de comparación entre series categóricas cuando estas utilizan la misma categoría. 


```python
tipo_calidades = CategoricalDtype(categories=["segunda","primera","especial"], ordered=True)
calidades1 = pd.Series(["primera","segunda","tercera","especial", "tercera", "especial"], dtype=tipo_calidades)
calidades2 = pd.Series(["segunda","segunda","segunda","primera", "especial", "especial"], dtype=tipo_calidades)

print(calidades1>calidades2)
print(calidades1==calidades2)
print(calidades1>"primera")
```

<a id="section44"></a> 
## <font color="#004D7F"> Indexación con valores categóricos. </font>

<br>
También es posible utilizar valores categóricos en el índice de las series. Esta indexación permite también cierto grado de _slicing_.


```python
calidades = pd.Categorical(["primera","segunda","segunda","primera", "especial"], 
                           categories=["especial","primera","segunda"], ordered=True)
equipos = ["Real Madrid", "Albacete", "Granada", "Barcelona", "Atlético de Madrid"]

serie_equipos = pd.Series(equipos, index=calidades)
print(serie_equipos,'\n')

print(type(serie_equipos.index),'\n')


print(serie_equipos.loc["primera"])
serie_equipos["segunda":]
```

<a id="section5"></a>
# <font color="#004D7F"> 5. Datos temporales en Pandas </font>

<br>
Pandas proporciona objetos y una gran cantidad de funcionalidades para el manejo de datos relativos a fechas y horas. 
Una descripción detallada puede encontrarse en la [documentación relativa a series temporales en Pandas](http://pandas.pydata.org/pandas-docs/stable/timeseries.html#).

Los objetos utilizados en la representación de datos temporales son:

* `TimeStamp`. Que representa marcas de tiempo (en concreto, nanosegundos desde el primer instante del 01/01/1970), y que pueden ser convertidas a distintos formatos.

* `DatetimeIndex`. Que permite construir índices con marcas de tiempo.

* `Period`. Representa periodos de tiempo.

* `PeriodIndex`. Permite construir índices con periodos de tiempo.

* `Timedelta`. Permite representar intervalos de tiempo. 

A continuación se describen estos, así como las principales funciones para su manejo.

<div class="alert alert-block alert-danger">
<i class="fa fa-exclamation-triangle" aria-hidden="true"></i>
Python, Numpy y Pandas manejan de manera distinta las marcas temporales (Pandas extiende la funcionalidad de Numpy). También proporcionan funciones para transformar unas en otras. En este tutorial nos centraremos en las funcionalidades de Pandas.
</div>

<a id="section51"></a> 
## <font color="#004D7F"> Marcas de tiempo: <font face="monospace">Timestamp </font> </font>

<br>
El objeto `Timestamp` se puede crear de varios modos, aunque el más frecuente es a partir de Strings. Como puede apreciarse en el fragmento de código, Pandas acepta multitud de formatos.


```python
momento = pd.Timestamp('02/2/2018 10:05AM')
print(momento)
print(type(momento),"\n")

momento = pd.Timestamp('Feb 02 2018 10:05AM')
print(momento)

ahora = pd.Timestamp('Now')
print(ahora)

hoy = pd.Timestamp('Today')
print(hoy)
```

También pueden accederse de manera independiente los distintos campos del objeto `Timestamp` (se muestran algunos).


```python
ahora = pd.Timestamp('Now')
print("Timestamp legible: ", ahora)
print("Día: ", ahora.date())
print("Hora: ", ahora.hour)
print("Mes: ", ahora.month)
print("Día de la semana: ", ahora.dayofweek)
print("Días en el mes: ", ahora.daysinmonth)
```

Es posible comparar valores del tipo `Timestamp` mediante operadores.


```python
momento = pd.Timestamp('2/2/2017 10:05AM')
momento2 = pd.Timestamp('1/5/2018 12:15AM')
print(momento>momento2)

momento3 = pd.Timestamp('2/2017')     # Si solo se especifica el mes, considera el primer instante. 
print(momento>momento3)
```

### <font color="#004D7F"  face="monospace">strftime()</font>

La función `strftime` permite imprimir las marcas de tiempo con varios formatos. El siguiente código toma el momento actual, y lo imprime con el formato especificado. Se puede encontrar información completa sobre el formato que acepta la función `strftime` [aquí](http://strftime.org/).


```python
ahora = pd.Timestamp('Now')

print(hoy.strftime("%d/%m/%Y"))
print(hoy.strftime("%d of %B. %Y"))
```

Esta función es similar a la implementada en el módulo `time` de Python.


```python
import datetime

ahora = datetime.datetime(year=2018, month=1, day=15, hour=11, minute=15, second=19)
print(type(ahora))
print(ahora.strftime("%d/%m/%Y"))
print(ahora.strftime("%d of %B. %Y"))
```

### <font color="#004D7F"> <font face="monospace">to_datetime() </font>

La función `to_datetime` permite construir objetos de tipo `Timestamp` del mismo modo que el constructor de este tipo de objetos, pero es mucho más potente y flexible. La referencia completa de esta función puede encontrarse [aquí](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.to_datetime.html).

El siguiente código muestra algunos ejemplos de construcción de objetos `Timestamp` mediante esta función.


```python
momento = pd.to_datetime('02/2/2017 10:05AM')
print(momento)

inicio_curso=pd.to_datetime(21, unit='D', origin='2018-1-1')
print(inicio_curso)

fecha = pd.to_datetime('2018-15-1', dayfirst=True)
print(fecha)
```

El siguiente caso es ilustrativo de una circunstancia que puede dar lugar a errores inesperados.


```python
momento = pd.to_datetime('3/2/2017 10:05AM')
print(momento.strftime("%d of %B. %Y"))

momento = pd.to_datetime('3/2/2017 10:05AM', dayfirst=True)
print(momento.strftime("%d of %B. %Y"))
```

<div class="alert alert-block alert-danger">
<i class="fa fa-exclamation-triangle" aria-hidden="true"></i>
Hay que tener en cuenta que en el sistema anglosajón, el mes se suele colocar antes que el año. La fecha anterior, que en España se interpreta como tres de febrero, representa en realidad al dos de marzo. Esta circunstancia es fuente común de errores. 
</div>

La función `to_datetime` permite convertir fechas en formatos muy diversos, y no predefinidos. Esta funcionalidad puede utilizarse para llevar a cabo conversiones en series.


```python
momento = pd.to_datetime('Fecha de finalización: Sep 30, 2018 a las: 10:30 pm',
                         format='Fecha de finalización: %b %d, %Y a las: %I:%M %p')
print(momento)
```

Si la función `to_datetime` se aplica a una serie de Strings, entonces devuelve otra serie con objetos `Timestamp`.


```python
serie = pd.Series(['2 June 2013', 'Aug 29, 2014', '2015-06-26', '7/12/16', '02/2/2017 10:05AM'])

print('Serie de Strings')
print(serie)
print()

serie_dt = pd.to_datetime(serie) 
print('Serie de elementos datetime')
print(serie_dt)

type(serie_dt[0])
```
