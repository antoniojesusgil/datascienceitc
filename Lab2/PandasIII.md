
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
* [13. Tablas dinámicas y reestructuración](#section13)
    * [Creación de tablas dinámicas](#section131)
    * [Reestructuración de tablas](#section132)

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

La estructura `GroupBy.indices`, contiene un diccionario con las posiciones de las filas que corresponden a cada uno de los grupos. Otra estructura, `GroupBy.groups`, devuelve los índices de las filas correspondientes a cada grupo. 


```python
indices = df.groupby('state').indices
print("Índices")
print(type(indices),'\n')
print(indices.keys())
print()
print(indices['Alabama'])
print()
```

Es posible obtener un `DataFrame` con los elementos correspondientes a cada grupo mediante la función `GroupBy.get_group()`.


```python
df.groupby('state').get_group('Alabama').head()
```

<div class="alert alert-block alert-info">
<i class="fa fa-info-circle" aria-hidden="true"></i> __Nota__: Aunque estas estructuras y modo de acceso están disponibles, y permiten entender el funcionamiento de  `groupby()`, no es habitual el trabajo directo con ellas. 
</div>

Es posible iterar sobre la estructura `DataFrameGroupBy` y obtener el `DataFrame` correspondiente a cada grupo.


```python
print("Imprime el primer grupo: \n")
for grupo, df_grupo in df.groupby('state'):
    print("Grupo: ",grupo)
    print(df_grupo.head())
    break                                   # Para imprimir solamente un grupo
```

La estructura `DataFrameGroupBy` implementa la mayoría de las funciones que implementa un `DataFrame`, pero éstas se aplican de manera independiente a cada uno de los grupos. El resultado de la aplicación es un `DataFrame`.


```python
grupos_df = df.groupby('state')
grupos_df.mean().head()
#grupos_df.describe()
```

El acceso a columnas también se aplica de manera independiente a cada grupo, de manera que genera un objeto `SeriesGroupBy` (o `DataFrameGroupBy` si se accede a varias columnas), en el que los datos están agrupados con el mismo criterio que el `DataFrame`.


```python
#grupos_pop2010 = df.groupby(df['state'])['pop2010']      # Equivalente
#grupos_pop2010 = df['pop2010'].groupby(df['state'])      # Equivalente
grupos_pop2010 = df.groupby('state')['pop2010']
print(type(grupos_pop2010)) 
print()

for grupo, serie_grupo in grupos_pop2010:
    print(grupo,": ",len(serie_grupo),"\n")
    print(serie_grupo.head())
    break;                     # Procesa solamente la primera iteración
```

Es posible grupar los datos según el resultado de una función aplicada sobre el índice del `DataFrame`. La siguiente celda de código implementa una función que determina si un estado está o no en la costa, agrupa las entradas según el resultado de esta función, y devuelve la población total en 2010 de cada uno de los grupos.


```python
costa_este = {'Maine', 'New Hampshire', 'Massachusetts', 'Rhode Island', 'Connecticut', 'New York', 
              'New Jersey', 'Delaware', 'Maryland', 'Virginia', 'North Carolina', 'South Carolina', 
              'Georgia', 'Florida'}

costa_oeste = {'Alaska','California', 'Oregon', 'Washington'}

def es_costa(estado):
    return (estado in costa_este) or (estado in costa_oeste)

for grupo, data in df.set_index('state').groupby(es_costa):
    print(grupo)
    print(data.index.unique())
    print("Población total: ",data['pop2010'].sum())
    print()
```

La operación anterior se puede sintetizar.


```python
df.set_index('state').groupby(es_costa)['pop2010'].sum()
```

Indirectamente, es posible agrupar a partir de una función aplicada sobre una o varias columnas. El siguiente código es equivalente al anterior, pero no se establece la columna _state_ como índice. 


```python
for grupo, data in df.groupby(df['state'].apply(es_costa)):
    print(grupo)
    print(data.index.unique())
    print("Población total: ",data['pop2010'].sum())
    print()
```

La forma de trabajar anterior permite hacer la agrupación mediante una función aplicada sobre varias columnas. 
El siguiente código determina si un condado ha crecido más del 50% desde el 2000 al 2010, y agrupa los datos en función de ese criterio.


```python
def ha_crecido(county):
    return (county['pop2010']/county['pop2000'])>1.5

grupos_df = df.groupby(df.apply(ha_crecido, axis=1))
# Muestra los que pertenecen al grupo que sí ha crecido más del 50%.
grupos_df.get_group(True).head()
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section101"></a> 
## <font color="#004D7F">Agregación por grupos </font>

<br> Una de los usos más frecuentes de la agrupación es la agregación por grupos. La función `agg()` lleva a cabo la agrupación de manera independiente para cada grupo.


```python
media_estado = df.groupby('state').agg({'pop2010': [np.mean, lambda pop: np.max(pop)], 'med_income':'mean'})
media_estado.head()
```

Existe otro modo de llevar a cabo la agregación. Consiste en acceder a la columna determinada, y llevar a cabo la agregación sobre ella.


```python
media_estado = df.groupby('state')['pop2010'].agg([np.max, np.min, 'mean'])
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
# <font color="#004D7F">Multi-índices </font>

<br>
Pandas permite utilizar varios niveles de indexación, tanto para filas como para columnas. En este tutorial se describen los conceptos necesarios para el uso más común. Se puede encontrar más información al respecto en la ([documentación](https://pandas.pydata.org/pandas-docs/stable/advanced.html)). 

<br>
Cuando se proporcionan varias colecciones como índice en la construcción del `DataFrame`, se crea un multi-indice. 


```python
compra_1 =  {'Nombre': 'Álvaro', 'Producto': 'Queso', 'Precio': 22.50}
compra_2 =  {'Nombre': 'Benito', 'Producto': 'Vino', 'Precio': 14.50}
compra_3 =  {'Nombre': 'Fernando','Producto': 'Jamón', 'Precio': 50.00}
compra_4 =  {'Nombre': 'Martín', 'Producto': 'Aceite', 'Precio': 20.00}
compra_5 =  {'Nombre': 'Hernán'} # Deja a NaN los campos para los que no se proporciona valor.

df_compras = pd.DataFrame([compra_1, compra_2, compra_3, compra_4, compra_5], 
                          index=[['Tienda 1', 'Tienda 1', 'Tienda 2', 'Tienda 3', 'Tienda 3'],
                                 ['Albacete', 'Albacete', 'Villarrobledo', 'Tomelloso', 'Tomelloso']])
df_compras
```

El índice se divide en niveles, organizados de manera jerárquica. 


```python
df_compras.index
```

La función `swaplevel` permite cambiar la jerarquía de índices. 


```python
df_compras.swaplevel(i=1,j=0)
```

La siguiente celda de código lee un conjunto de datos y establece un índice con un nivel principal, _state_, y otro secundario, _name_. La jerarquía corresponde al orden en que aparecen las columnas. 


```python
import pandas as pd
df = pd.read_csv('datos/county.txt', sep='\t')
df.set_index(['state','name'], inplace=True)
df.head()
```

Cuando se utiliza un índice a varios niveles, el acceso natural a filas se hace mediante tuplas cuyo tamaño corresponde al número de índices, y con los valores del índice en cada nivel.


```python
#df.loc[('Alabama','Bibb County')]      
df.loc['Alabama','Bibb County']             # Estas dos notaciones son equivalentes
```

<div class="alert alert-block alert-warning">
<i class="fa fa-exclamation-circle" aria-hidden="true"></i>
__Importante__: Hablamos de varios niveles porque pueden ser más de dos. 
</div>

No es necesario especificar valores en todos los niveles para localizar elementos. Es posible omitir los valores a partir de un nivel. 


```python
df.loc['Alabama'].head()
```

Este tipo de indexación, mediante el valor en el índice principal, permite _slicing_.


```python
df.loc['Alabama':'Wyoming'].head()
```

También es posible utilizar multi-índices en las columnas. El siguiente código crea un multi-índice y lo establece en el `DataFrame` anterior.


```python
# Crea un multi-índice
level1 = ['population', 'population', 'money', 'money', 'money','money','money','money']
level2 = df.columns
tuples = list(zip(level1,level2))
m_columns = pd.MultiIndex.from_tuples(tuples, names=['principal', 'secundario'])

# Establece un multi índice como índice de columnas.
df.columns = m_columns
df.head()
```

A veces es más cómodo mostrar los índices como tuplas. Es posible hacerlo fijando la opción `display.multi_sparse` de Pandas a `False`. 


```python
pd.set_option('display.multi_sparse', False)
df.head()
```


```python
pd.set_option('display.multi_sparse', True)
```

También pueden accederse las columnas a través del nivel (o niveles) más bajos del índice. 


```python
df.loc['Alabama','population'].head()
```


```python
df.loc['Alabama':'Wyoming','population'].head()
```

Mediante la función `DataFrame.xs` se puede acceder a los datos especificando el nivel del índice. Esto permite la indexación simple con índices en cualquier nivel.


```python
df.xs('Park County', level=1)
```

Este tipo de acceso también puede hacerse para columnas. 


```python
# El nivel se llama 'secundario' porque anteriormente le llamamos así. 
df.xs('pop2000', level='secundario', axis=1).head()   
```

Es posible ordenar el `DataFrame` en función del índice jerárquico. En ese caso, la jerarquía se utiliza también en la ordenación.


```python
df.sort_index(inplace=True)
df.head()
```

También se puede ordenar el `DataFrame` en función de un nivel del índice.


```python
df.sort_index(level=1)
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

Otro ejemplo. Agrupa por nombre de condado, y muestra las columnas que corresponden al índice _ money_.


```python
grupos_df = df.groupby(level=1)['money']
for grupo, datos in grupos_df:
    print(grupo)
    print(datos.head())
    break;    # Solamente el primer grupo.
```

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
print(pos1_df.set_index("Nombre").append(pos2_df.set_index("Nombre")))
```

`append()` toma un parámetro, denominado `ignore_index` que permite crear un nuevo índice (numérico) e ignorar el de los `DataFrames` originales. 


```python
print(pos1_df.set_index("Nombre").append(pos2_df.set_index("Nombre"), ignore_index=True))
```

<div class="alert alert-block alert-info">
<i class="fa fa-info-circle" aria-hidden="true"></i> __Nota__: La función `append()` es en realidad un caso específico de la función más general `concat()`, que se verá a continuación.
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

Cuando se unen `DataFrame` con distintas columnas,  los valores indeterminados se fijan a _NaN_ en el nuevo `DataFrame`. Este código, además, añade una clave que permite identificar el origen de los datos. 


```python
pd.concat([pos1_df, eqp_df], join="outer", axis=0, keys=["jugadores", "Equipos"])
```

La elección de `axis=1` permite concatenar las columnas. En este ejemplo, indicamos que solamente se consideren aquellas filas cuyo índice aparece en ambos `DataFrame` mediante `join=inner`.


```python
# Previamente, establecemos el nombre del jugador como índice. 
cp_pos1_df=pos1_df.set_index('Nombre')
cp_pos2_df=pos2_df.set_index('Nombre')
cp_eqp_df = eqp_df.set_index('Nombre')
```


```python
pd.concat([cp_pos1_df, cp_eqp_df], axis=1, join='inner')
#pd.concat([cp_pos1_df, cp_eqp_df], axis=1, join='outer')
```

En lugar de `join`, es posible determinar qué índices se incluyen mediante `join_axes`. El siguiente ejemplo incluye todas las filas del primer `DataFrame`.


```python
pd.concat([cp_pos1_df, cp_eqp_df], axis=1, join_axes=[pos1_df.index])
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
* `validate`. Es un `String` que permite determinar si se cumple una determinada relación entre las claves de unión. Puede tomar los valores `1:1`, `1:m`, `m:1` y `m:m`.

<br>
En la siguiente celda se lleva a cabo la unión entre los dos `DataFrame` definidos anteriormente en función del nombre del jugador, y considerando la unión de todas las filas. Como la columna _País_ aparece en ambos `DataFrame`, se añade también un sufijo para determinar la correspondencia en el `DataFrame` resultante.


```python
pd.merge(pos1_df,eqp_df, how='outer', left_on='Nombre', right_on='Nombre', suffixes=['_jug','_equ'])
# pd.merge(pos1_df,eqp_df, how='outer', on='Nombre', suffixes=['_jug','_equ'])  # Equivalente
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


```python
pos1_df.join(eqp_df, lsuffix='_pos', rsuffix='_equ')
```

La llamada anterior es equivalente a ésta. 


```python
pd.merge(pos1_df, eqp_df, left_index=True, right_index=True, how='left', suffixes=['_jug','_equ'], sort=False)
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section13"></a>
# <font color="#004D7F"> 13. Tablas dinámicas y reestructuración </font>

<br>
Para ilustrar esta sección, utilizaremos este conjunto de datos. 


```python
def franja_edad(edad):
    if edad<18:
        return 'joven'
    elif edad>65:
        return 'anciano'
    else:
        return 'adulto'

df_titanic = pd.read_csv('datos/Titanic.csv', sep=',', skiprows=1, index_col=1,
                         names=['ID','Nombre','Clase','Edad','Sexo','Superviviente','Código (Sexo)'])

df_titanic['FranjaEdad']=df_titanic['Edad'].apply(franja_edad)
del df_titanic['ID']
df_titanic.head()
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section131"></a>

## <font color="#004D7F">Creación de tablas dinámicas</font>
</font>

<br>
Las tablas dinámicas se generan  utilizando los valores de una o varias columnas en la tabla origen como nombres de índices o columnas. Se utilizan para mostrar, generalmente, agregaciones de datos.

### <font color="#004D7F"> <font face="monospace">pivot_table() </font></font>

La función `pivot_table` permite construir una tabla dinámica a partir de los datos de un `DataFrame`. Toma principalment cuatro parámetros:

* `index`/`columns`. La columna o columnas cuyos valores serán utilizados como índices/columnas en la nueva tabla. 
* `values`. Los valores de interés, sobre los que se lleva a cabo la agregación.
* `aggfunc`. La función o funciones de agregación.

La siguiente función muestra los supervivientes en función de los campos _Sexo_ y _Clase_. (La función `dropna` elimina los registros vacíos).


```python
pt = df_titanic.pivot_table(index='Clase', columns='Sexo', values='Superviviente', aggfunc=np.sum).dropna()
pt
```

En este otro ejemplo, se utilizan como columnas tanto el sexo como la franja de edad. 


```python
df_titanic.pivot_table(index='Clase', columns=['Sexo', 'FranjaEdad'], 
                       values=['Superviviente'], aggfunc=np.mean).round(2)
```

También se pueden especificar varias columnas como valores, o varias funciones de agregación.


```python
df_titanic.pivot_table(index='Clase', columns=['Sexo'], 
                       values=['Superviviente','Edad'], aggfunc=[np.mean, np.sum]).round(2)
```

### <font color="#004D7F"> <font face="monospace">unstack() </font></font>

La función `unstack()` es más simple que la anterior, ya que trabaja con multi-índices, y no agrega datos, solamente los muestra.  


```python
df_classex = df_titanic.groupby(['Clase','Sexo'])['Superviviente','Edad'].agg(np.sum)
df_classex
```


```python
df_classex.unstack('Sexo')
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section132"></a>

## <font color="#004D7F">Reestructuración de tablas</font>
</font>

Es posible llevar a cabo el proceso inverso al descrito anteriormente. Es decir, convertir los nombres de columnas en valores. 


```python
df_precios = pd.DataFrame({'Casa':[10,20,30], 'Coche':[40,50,60], 'Transporte':[1,2,3]},
                         index = ['Madrid', 'Barcelona', 'Paris'])
df_precios
```

### <font color="#004D7F"> <font face="monospace">stack() </font></font>

El método más sencillo para despivotar tablas es `stack`, que toma un nivel de índices en las columnas, y lo convierte en una índice de la tabla (fila).


```python
df_precios.stack(level=0)
```

### <font color="#004D7F"> <font face="monospace">melt() </font></font>

Otra posibilidad más completa es `melt()`. Esta función convierte los nombres de varias columnas a valores. Toma tres parámetros principales. 

* `id_vars`. Lista de columnas de referencia. 
* `value_vars`. Lista de columnas que son despivotadas. 
* `var_name` y `value_name`. Nombre de las columnas con los nombres de las variables, y los valores correspondientes. 


```python
df_precios.reset_index(inplace=True)
df_precios.columns = ['Ciudad','Casa','Coche','Transporte']
df_precios
```


```python
df_precios.melt(id_vars='Ciudad', value_vars=['Casa','Coche'], var_name='Concepto', value_name='Precio')
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<div style="text-align: right"> <font size=6><i class="fa fa-coffee" aria-hidden="true" style="color:#004D7F"></i> </font></div>
