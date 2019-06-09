
<h1><font color="#004D7F" size=6>Introducción a Pandas III</font></h1>

<br>
<div style="text-align: right">
<font color="#004D7F" size=3>Antonio Jesús Gil</font><br>
<font color="#004D7F" size=3>Ciencia de Datos</font><br>

</div>

---

<a id="indice"></a>
<h2><font color="#004D7F" size=5>Índice</font></h2>

* [10. Agrupamiento: <font face="monospace">groupby()</font>](#section10)
    * [Agregación por grupos](#section101)
* [11. Multi-índices](#section11)
* [12. Combinación de DataFrames](#section12)
    * [<font face="monospace">append()</font>](#section121)
    * [<font face="monospace">concat()</font>](#section122)
    * [<font face="monospace">merge()</font>](#section123)
    * [<font face="monospace">join()</font>](#section124)

---

<a id="section10"></a>
# <font color="#004D7F"> 10. Agrupamiento: <font face="monospace"> groupby()</font></font>

<br>
La función `groupby()` permite agrupar los datos del `DataFrame` según en función de los valores de su índice o columnas. Devuelve una estructura del tipo `DataFrameGroupBy`, que implementa estructuras de datos necesarias para que las operaciones sobre grupos se apliquen de manera eficiente.


```python
import pandas as pd
import numpy as np
df = pd.read_csv('datos/county.txt', sep='\t')
df.head()
```

La siguiente celda de código agrupa las entradas del conjunto de datos anterior en función del valor del campo _state_.


```python
# grupos_df = df.groupby(df['state']); # Las dos formas son equivalentes. La primera permite entender mejor el 
grupos_df = df.groupby('state');       # funcionamiento de la función. La segunda es más cómoda. 
print(type(grupos_df))
```

Nos crea un ojeto de tipo `DataFrameGroupBy`. La estructura `GroupBy.indices`, contiene un diccionario con las posiciones de las filas que corresponden a cada uno de los grupos. Otra estructura, `GroupBy.groups`, devuelve los índices de las filas correspondientes a cada grupo. 


```python
indices = df.groupby('state').indices
print("Índices")
print(type(indices),'\n')
```
```python
print(indices.keys())
print()
```
```python
print(indices['Alabama'])
print()
```

Es posible obtener un `DataFrame` con los elementos correspondientes a cada grupo mediante la función `GroupBy.get_group()`.

```python
df.groupby('state').get_group('Alabama').head()
```

Ejercicio: muestra el tamaño del df correspondiente al grupo de `Alabama`
```python
# Escribe el código necesario
```

<div class="alert alert-block alert-info">
<i class="fa fa-info-circle" aria-hidden="true"></i> __Nota__: Aunque estas estructuras y modo de acceso están disponibles, y permiten entender el funcionamiento de  `groupby()`, no es habitual el trabajo directo con ellas. 
</div>

Es posible iterar sobre la estructura `DataFrameGroupBy` y obtener tanto el grupo como el `DataFrame` correspondiente a cada grupo. 

En este caso agrupamos en función de la columna `state`. El iterador devuelve una tupla con el grupo y el DataFrame.

```python
print("Imprime el primer grupo: \n")
for grupo, df_grupo in df.groupby('state'):
    print("Grupo: ",grupo)
    print(df_grupo.head())                  # Imprime la cabecera
    break                                   # Para imprimir solamente un grupo
```

La estructura `DataFrameGroupBy` implementa la mayoría de las funciones que implementa un `DataFrame`, pero éstas se aplican de manera independiente a cada uno de los grupos. El resultado de la aplicación es un `DataFrame`.

```python
grupos_df = df.groupby('state')
grupos_df.mean().head()
#grupos_df.describe()
```

```python
grupos_df.describe()
```

El acceso a columnas también se aplica de manera independiente a cada grupo, de manera que genera un objeto `SeriesGroupBy` solo para una columna (o `DataFrameGroupBy` si se accede a varias columnas), en el que los datos están agrupados con el mismo criterio que el `DataFrame`.

Ejercicio: Muestra la columna `pop2010` para los grupos `state`. En otro renglon, imprime el tipo de estructura y comprueba que es `SeriesGroupBy`.

```python
# Escribe el código necesario
```
Es posible iterar sobre los datos devueltos y disponemos el grupo y los datos.

Ejercicio: Crea un bucle for python que nos muestre los datos relativos a la serie anterior. Añadimos break al final para que tan solo muestre `Alabama`.

```python
# Escribe el código necesario 
for in :

    break;
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section101"></a> 
## <font color="#004D7F">Agregación por grupos </font>

<br> ¿Para qué agrupamos? Para disponer de datos agregados. Una de los usos más frecuentes de la agrupación es la agregación por grupos. La función `agg()` lleva a cabo la agrupación de manera independiente para cada grupo.

```python
# A la funcion agg se le pueden pasar parámetros de tres formas
# Para la columna 2010 obtiene la media y el valor máximo utilizando dos funciones
# Para la columna med_income obtenemos la media mediante un string
media_estado = df.groupby('state').agg({'pop2010': [np.mean, lambda pop: np.max(pop)], 'med_income':'mean'})
media_estado.head()
```

Existe otro modo de llevar a cabo la agregación. Consiste en acceder a la columna determinada, y llevar a cabo la agregación sobre ella pasando la lista de funciones a utilizar.

Ejercicio: Muestra un `df.groupby` utilizando `agg` equivalente a la anterior pasando una lista de funciones. Nota: recuerda utilizar numpy para el máximo y el mínimo (np.max, np.min)

```python
# Escribe el código necesario
media_estado.head()
```

### <font color="#004D7F"> Eficiencia </font>

Aunque la agregación por grupos se puede llevar a cabo de otros modos, el uso de una estructura `GroupBy` permite hacerlo de manera más eficiente. La siguiente celda obtiene la media de población en 2010 para las filas correspondientes a cada estado mediante indexación con booleanos. 

```python
%%timeit -n 10 
for state in df['state'].unique():
    avg = np.average(df[df['state']==state]['pop2010'])
```

La siguiente celda hace la misma operación, pero iterando dobre una estructura `GroupBy`.

```python
%%timeit -n 10
for grupo, frame in df.groupby('state'):
    avg = np.average(frame['pop2010'])
```

Por último, en esta celda se lleva a cabo el mismo cálculo, pero sin iterar. 


```python
%%timeit -n 10
avg = df.groupby('state')['pop2010'].mean()
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section11"></a> 
# <font color="#004D7F">11. Multi-índices </font>

<br>
Pandas permite utilizar varios niveles de indexación, tanto para filas como para columnas. En este tutorial se describen los conceptos necesarios para el uso más común. Se puede encontrar más información al respecto en la ([documentación](https://pandas.pydata.org/pandas-docs/stable/advanced.html)). 

El índice se divide en niveles, organizados de manera jerárquica. Retrocede a la sección anterior y revisa la salida del ejemplo `media_estado`.

La siguiente celda de código lee un conjunto de datos y establece un índice con un nivel principal, _state_, y otro secundario, _name_. La jerarquía corresponde al orden en que aparecen las columnas. 

```python
import pandas as pd
df = pd.read_csv('datos/county.txt', sep='\t')
df.set_index(['state','name'], inplace=True)
df.head()
```

Cuando se utiliza un índice a varios niveles, el acceso natural a filas se hace mediante tuplas cuyo tamaño corresponde al número de índices, y con los valores del índice en cada nivel. Utilizamos `loc` en vez de `iloc`.

Al disponer de dos índices, se puede proporcionar una tupla donde cada posición contenga el valor relativo.

```python
#df.loc[('Alabama','Bibb County')]      
df.loc['Alabama','Bibb County']             # Estas dos notaciones son equivalentes
```

<div class="alert alert-block alert-warning">
<i class="fa fa-exclamation-circle" aria-hidden="true"></i>
__Importante__: Hablamos de varios niveles porque pueden ser más de dos. 
</div>

No es necesario especificar valores en todos los niveles para localizar elementos. Es posible omitir los valores a partir de un nivel. Es decir, partiendo del nivel cero.

```python
df.loc['Alabama'].head()
```
Ejercicio: Mediante el comando anterior, intenta acceder directamente a `Bibb County`.

```python
# Escribe el código necesario
```

Este tipo de indexación, mediante el valor en el índice principal, permite _slicing_.

```python
df.loc['Alabama':'California'].head()
# Prueba con Wyoming
```

También es posible utilizar multi-índices en las columnas. El siguiente código crea un multi-índice y lo establece en el `DataFrame` anterior.

Aprovecharemos para construir un multi-índice por código:

1. Mostramos las columnas del DataFrame y creamos dos listas con los campos necesarios

```python
df.columns
```

```python
# Crea un multi-índice
level1 = ['population', 'population', 'money', 'money', 'money','money','money','money']
level2 = df.columns
```

2. Construimos una lista de tuplas utilizando la función `zip`

```python
tuples = list(zip(level1,level2))
tuples
```

3. A partir de la lista de tuplas, creamos un multi-índice y además podemos nombrar los índices

```python
m_columns = pd.MultiIndex.from_tuples(tuples, names=['principal', 'secundario'])
#m_columns
```
4. Aplicamos el multi-índice a las columnas del DataFrame

```python
# Establece un multi índice como índice de columnas.
df.columns = m_columns
df.head()
```

A veces es más cómodo mostrar los índices como tuplas. Es posible hacerlo fijando la opción `display.multi_sparse` de Pandas a `False`. 

```python
pd.set_option('display.multi_sparse', False)
df.head()
```

Cuando el `DataFrame` está ordenado, es posible hacer slicing con tuplas. 

```python
df.loc[('Alabama','Coosa County'):('Wyoming','Carbon County')].head()
```

Por último, la función `groupby` acepta un parámetro, denominado `level`, que permite agrupar los datos según el valor del índice en un nivel. 


```python
grupos_df = df.groupby(level=0)
for grupo, datos in grupos_df:
    print(grupo)
    print(datos.head())
    break;    # Solamente el primer grupo.
```

Ejercicio: Agrupa por nombre de condado, y muestra las columnas que corresponden al índice _ money_.


<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section12"></a>
# <font color="#004D7F"> 12. Combinación de Dataframes</font>

<br>

La funcionalidad relativa a combinación de `DataFrame` y `Series` es completa y compleja, ya que uno de los usos principales de Pandas es el de herramienta para la agregación de datos. La documentación oficial de la librería ilustra con ejemplos la mayoría de casos de uso ([documentación](https://pandas.pydata.org/pandas-docs/stable/merging.html)).

Para ilustrar los ejemplos de este tutorial, se utilizarán estos tres `DataFrame`. 


```python
pos1_df = pd.DataFrame([{'Nombre': 'Diego Costa', 'Posición': 'Delantero', 'País':'Brasil'},
                        {'Nombre': 'Sergio Ramos', 'Posición': 'Defensa', 'País':'España'},
                        {'Nombre': 'Gerard Piqué', 'Posición': 'Defensa', 'País':'España'},
                        {'Nombre': 'Cristiano Ronaldo', 'Posición': 'Delantero', 'País':'Portugal'}])

pos2_df = pd.DataFrame([{'Nombre': 'Leo Messi', 'Posición': 'Delantero', 'País':'Argentina'},
                        {'Nombre': 'Luca Modric', 'Posición': 'Centrocampista', 'País':'Croacia'},
                        {'Nombre': 'Saúl Ñíguez', 'Posición': 'Centrocampista', 'País':'España'},
                        {'Nombre': 'Kareem Benzema', 'Posición': 'Delantero', 'País':'Francia'}])

eqp_df = pd.DataFrame([{'Nombre': 'Diego Costa',  'Equipo': 'Atlético de Madrid', 'País':'España'},
                       {'Nombre': 'Cristiano Ronaldo','Equipo': 'Real Madrid', 'País':'España'},
                       {'Nombre': 'Leo Messi','Equipo': 'FC Barcelona', 'País':'España'},
                       {'Nombre': 'Koke','Equipo': 'Atlético de Madrid', 'País':'España'}])

print(pos1_df)
print()
print(eqp_df)
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section121"></a> 
## <font color="#004D7F"><font face="monospace"> append()</font></font>
    
Es la función más sencilla. Permite añadir a un `DataFrame` las filas de otro u otros `Dataframe`. Como resultado, genera un nuevo `DataFrame`.

```python
print(pos1_df.append(pos2_df))
```
Vemos la unión realizada por sus índices originales. También se puede facilitar un nuevo índice que sea una columna común en los df.

Ejercicio: Imprime el nuevo df cuyo `set_index` sea la columna **Nombre**

```python
# Escribe el código para el nuevo df
print()
```

`append()` toma un parámetro, denominado `ignore_index` que permite crear un nuevo índice (numérico) e ignorar el de los `DataFrames` originales. 

Ejercicio: Añade `ignore_index` al ejercicio anterior 

```python
# Escribe el código necesario
print()
```

<div class="alert alert-block alert-info">
<i class="fa fa-info-circle" aria-hidden="true"></i> __Nota__: La función `append()` es en realidad un caso específico de la función más general `concat()`.
</div>

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section122"></a> 
## <font color="#004D7F"><font face="monospace"> concat()</font></font>

<br>
Esta función implementa la concatenación de `DataFrames`. Puede hacerse a nivel de filas (similar a `append()`) como de columnas. Toma varios parámetros. Los más importantes, además de la lista de `DataFrame` que se han de concatenar, son:

* `axis`. Determina el eje a lo largo del cual se concatenan los datos, y puede tomar los valores 0 (filas) y 1 (columnas).
* `join`. Determina si se considera la unión (`outer`) o la intersección (`inner`) de elementos (según el índice). 
* `join_axes`. Permite especificar qué elementos se incluyen (se usa en lugar de `join`) en el nuevo `DataFrame`.
* `keys`. Es un vector de claves. Si se utiliza, crea un multi-índice, y utiliza estas claves en el primer nivel para marcar el `DataFrame` original en el resultante. 

La siguiente llamada es equivalente a `append()`. Por defecto lleva a cabo la concatenación a nivel de filas, y une las columnas de ambos `DataFrame`.

```python
pd.concat([pos1_df, pos2_df])
#pd.concat([pos1_df, pos2_df], join="outer", axis=0)       # Es equivalente
```

El parámetro join determina qué conjunto de índices (en el eje que no se concatena) se incluye en el `DataFrame` resultado. El siguiente ejemplo concatena las filas de ambos `DataFrame`, y solamente incluye las columnas que aparecen en ambos. Además, crea un nuevo índice, prescindiendo de los anteriores.


```python
pd.concat([pos1_df, eqp_df], join="inner", axis=0, ignore_index=True)      
```

Cuando se unen `DataFrame` con distintas columnas, los valores indeterminados se fijan a _NaN_ en el nuevo `DataFrame`. Este código, además, añade una clave que permite identificar el origen de los datos. 

Ejercicio: Contatena los df pos1_df y eqp_df utilizando el eje de las filas `axis=0`

```python
# Escribe el código necesario
```

Ejercicio: Utiliza el vector de claves se parando los dataframes en 'Jugadores' y 'Equipos'

```python
# Escribe el código necesario
```
La elección de `axis=1` permite concatenar las columnas. En este ejemplo, indicamos que solamente se consideren aquellas filas cuyo índice aparece en ambos `DataFrame` mediante `join=inner`.

```python
# Hacemos una copia de los df para evitar desastres
# Previamente, establecemos el nombre del jugador como índice. 
cp_pos1_df=pos1_df.set_index('Nombre')
cp_pos2_df=pos2_df.set_index('Nombre')
cp_eqp_df = eqp_df.set_index('Nombre')
```

```python
pd.concat([cp_pos1_df, cp_eqp_df], axis=1, join='inner')
```

Ejercicio: Une los df utilizando columnas y con el tipo de union `outer`  

```python
# Escribe el código necesario
```

En lugar de `join`, es posible determinar qué índices se incluyen mediante `join_axes`. El siguiente ejemplo incluye todas las filas del primer `DataFrame`.

```python
pd.concat([cp_pos1_df, cp_eqp_df], axis=1, join_axes=[cp_pos1_df.index])
```
Ejercicio: Muestra los elementos del segundo dataframe

```python
# Escribe el código necesario
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section123"></a> 
## <font color="#004D7F"><font face="monospace"> merge()</font></font>

<br>

Esta función permite unir las columnas de dos `DataFrame`. A diferencia de `concat()`, permite especificar el modo en que se lleva a cabo esa unión mediante funcionalidades propias de lenguajes de bases de datos relacionales como SQL. Éstas se caracterizan, a _grosso modo_, por establecer una relación entre los dos conjuntos de datos que es función de una columna (que puede o no ser el índice).

La función `merge()`, cuenta con numerosos parámetros ([documentación](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.merge.html)) que rigen la unión. Algunos de los más importantes son:

* `left`, `right`. Son argumentos posicionales que se refieren a los dos `DataFrame` que son unidos. 
* `left_index`, `right_index`. Determinan si los índices respectivos se usan como claves de unión.
* `on`, `left_on`, `right_on`. Determinan qué columnas (si no se usan índices) son utilizadas como claves de unión. `on` se utiliza cuando las columnas aparecen en ambos `DataFrame`.
* `how`. Determina qué elementos se incluyen en la unión. Puede tomar los valores `left`, `right`, `outer`, e `inner` según se consideren, respectivamente, los índices del primer `DataFrame`, del segundo, la unión, o la intersección de ambos.  

Además, admite otros parámetros de utilidad a la hora de presentar el conjunto de datos resultante de la unión.

* `suffixes`. Es una lista de `Strings` (dos). Cuando existen columnas comunes en ambos `DataFrame`, y no son utilizadas como clave de unión, permite identificarlas en el `DataFrame` resultante. Para ello, añade cada `String` al nombre de la columna correspondiente según incluya los valores de uno u otro `DataFrame`.  
* `indicator`. Añade una columna, denominada `_merge` con información sobre el origen de cada fila (un `DataFrame` concreto o los dos.
* `validate`. Es un `String` que permite determinar si se cumple una determinada relación entre las claves de unión. Puede tomar los valores `1:1`, `1:m`, `m:1` y `m:m`. Heredado de bases de datos relacionales.

<br>
En la siguiente celda se lleva a cabo la unión entre los dos `DataFrame` definidos anteriormente en función del nombre del jugador, y considerando la unión de todas las filas. Como la columna _País_ aparece en ambos `DataFrame`, se añade también un sufijo para determinar la correspondencia en el `DataFrame` resultante.

```python
pd.merge(pos1_df,eqp_df, how='outer', left_on='Nombre', right_on='Nombre', suffixes=['_jug','_equ'])
```

Ejercicio: Busca una equivalencia al código anterior utilizando las claves de unión por columnas

```python
# Escribe el código necesario
```

En este caso, suponemos que los `DataFrame` están indexados según el nombre del jugador. Además, añadimos un indicador, que permite determinar el origen de cada entrada. 


```python
pd.merge(pos1_df.set_index('Nombre'), eqp_df.set_index('Nombre'), how='outer', 
         left_index=True, right_index=True, suffixes=['_jug','_equ'], indicator=True)
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section124"></a> 

## <font color="#004D7F"><font face="monospace"> join()</font></font>

Es similar a `merge()`, aunque permite unir varios `DataFrame` y utiliza solo algunos parámetros. Por defecto utiliza los índices como clave de unión y el conjunto de elementos del `DataFrame` de la izquierda, es decir, `how=left` ([documentación](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.join.html)).

```python
pos1_df.set_index('Nombre', inplace=True)
pos2_df.set_index('Nombre', inplace=True)
eqp_df.set_index('Nombre', inplace=True)
```

Ejercicio: Crea con `join` un dataframe equivalente a este:

```python
pd.merge(pos1_df, eqp_df, left_index=True, right_index=True, how='left', suffixes=['_jug','_equ'], sort=False)
# Con sort a `True` nos ordena los datos.
```

```python
# Escribe el código necesario
```