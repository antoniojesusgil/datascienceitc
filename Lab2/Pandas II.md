
<h2><font color="#004D7F" size=5>Módulo 1</font></h2>



<h1><font color="#004D7F" size=6>Introducción a Pandas II</font></h1>

<br>
<div style="text-align: right">
<font color="#004D7F" size=3>Antonio Jesús Gil</font><br>
<font color="#004D7F" size=3>Ciencia de Datos</font><br>
</div>

---

<a id="indice"></a>
<h2><font color="#004D7F" size=5>Índice</font></h2>

* [6. DataFrames](#section6)
    * [Estructura](#section61)
    * [Construcción de DataFrames](#section62)
    * [Lectura desde archivos](#section63)
    * [Acceso a elementos](#section64)
    * [Manipulación de la estructura](#section65)  
    * [Descripción de los datos](#section66)
* [7. Selección y ordenación](#section7)
    * [Consulta y selección](#section71)  
    * [Ordenación](#section72)
* [8. Operaciones sobre elementos](#section8)
    * [Operaciones básicas](#section81)
    * [Transformación de datos: <font face="monospace">applymap() y transform()</font>](#section82)    
* [9. Agregación](#section9)   
    * [Estadísticos descriptivos](#section91)
    * [Agregación de datos: <font face="monospace">apply() y agg()</font>](#section92)

---

<a id="section6"></a>
# <font color="#004D7F"> 6. DataFrames</font>

<br>
Un `DataFrame` es una estructura _bidimensional_ de datos que se indexa por filas, y cuyas columnas pueden ser accedidas individualmente. Al igual que en una hoja de cálculo o una tabla SQL, cada columna puede almacenar datos de un tipo diferente, y es tratada como un objeto de tipo `Series`. 

```python
import pandas as pd

# Lee los datos de las personas que viajaban en el Titanic      
df_titanic = pd.read_csv('datos/Titanic.csv', index_col=0) 
# Muestra los 5 primeros casos 
df_titanic.head(5)                                           
```

El siguiente código accede a la columna _Name_ del `DataFrame` anterior (los accesos se tratarán con detalle en este tutorial) y muestra su tipo, que es `Series`. Es decir, es posible operar sobre columnas mediante los métodos descritos en la primera parte del tutorial. 


```python
nombres = df_titanic['Name']
print('\n',type(nombres))
print(nombres[:5])
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>


---

<a id="section61"></a> 
## <font color="#004D7F"> Estructura </font>



Un `DataFrame` está formado por tres componentes principales:

1. Los _datos_, almacenados en el campo `DataFrame.values`, que es un array NumPy.
2. El _índice_, almacenado en el campo `DataFrame.index`, y que permite indexar las filas.
3. El _índice de columnas_, almacenado en el campo `DataFrame.columns`. 


```python
print('Valores (5 primeras filas)')
print(df_titanic.values[:5,:])
print(type(df_titanic.values))

print('\nÌndice:')
print(df_titanic.index)
print(type(df_titanic.index))

print('\nColumnas:')
print(df_titanic.columns)
print(type(df_titanic.columns))
```

Es posible acceder a muchas de las propiedades de la estructura de un `DataFrame`, como sus dimensiones, los tipos de datos de las columnas, o incluso la ocupación en memoria. 


```python
print('Tamaño del conjunto de datos: {:d} filas, {:d} columnas.'.format(df_titanic.shape[0], df_titanic.shape[1]))
#print('Tamaño del conjunto de datos: {:d} filas, {:d} columnas.'.format(len(df_titanic.index), len(df_titanic.columns)))
#print('Número de filas del Dataframe: ', len(df_titanic))

print("\nTipo de datos de cada columna: \n", df_titanic.dtypes)

print("\nToda la información:\n")
print(df_titanic.info())
```

Incluso es posible acceder a información más detallada sobre el uso de memoria (por columnas) mediante `DataFrame.memory_usage()`.


```python
print(df_titanic.memory_usage())
```

<div class="alert alert-block alert-info">
<i class="fa fa-info-circle" aria-hidden="true"></i>
&nbsp;  Aunque el uso de información a este nivel __excede el nivel de este tutorial__, sí que puede ser útil ocasionalmente si se hace necesario reducir el tamaño del `DataFrame` en memoria. Por ejemplo, el siguiente código convierte el tipo de datos de la columna `SexCode` a un entero de un byte, con la correspondiente ganancia. 
</div>


```python
import numpy as np
df_titanic['SexCode'] = df_titanic['SexCode'].astype(np.int8)
print(df_titanic.memory_usage())
```

Con respecto a los índices, tanto el propio índice como las columnas se representan con tipos de datos específicos que optimizan los accesos, aunque la colección de valores se representa internamente mediante un array Numpy que es accesible a través del campo `values`. También se pueden convertir en listas mediante el método `tolist()`. 


```python
print("Columnas")
print("Array interno (values): ")
print(df_titanic.columns.values)
print(type(df_titanic.columns.values))
print("\nComo lista:")

print(df_titanic.columns.tolist())
print(type(df_titanic.columns.tolist()))
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section62"></a> 
## <font color="#004D7F"> Construcción de DataFrames </font>

<br>
Existen varias formas de construir un objeto `DataFrame` a partir de otros objetos como colecciones, diccionarios, series, etc. Éstas son útiles, por ejemplo,  a la hora de construir el `DataFrame` a partir de datos procedentes de fuentes heterogéneas. 

### <font color="#004D7F"> Construcción a partir de diccionarios </font>

Cada uno de los diccionarios representa una fila del `DataFrame`. Las claves representan el nombre de la columna, y los valores el valor correspondiente de la columna para la fila. Si no se proporciona un índice, las filas se indexan con enteros.


```python
compra_1 =  {'Nombre': 'Álvaro', 'Producto': 'Queso', 'Precio': 22.50}
compra_2 =  {'Nombre': 'Benito', 'Producto': 'Vino', 'Precio': 14.50}
compra_3 =  {'Nombre': 'Fernando','Producto': 'Jamón', 'Precio': 50.00}
compra_4 =  {'Nombre': 'Martín', 'Producto': 'Aceite', 'Precio': 20.00}
compra_5 =  {'Nombre': 'Hernán'} # Deja a NaN los campos para los que no se proporciona valor.

df_compras = pd.DataFrame([compra_1, compra_2, compra_3, compra_4, compra_5], 
                          index=['Tienda 1', 'Tienda 1', 'Tienda 2', 'Tienda 3', 'Tienda 3'])
df_compras
```

También se puede construir el `DataFrame` a partir de __un solo diccionario__ en el que cada clave corresponde al nombre de una columna, y cada valor colección u objeto `Series` con los valores correspondientes a esa columna. En este caso, existe la restricción de que todas las colecciones y el índice (en caso de que se proporcione) han de tener un tamaño similar. 


```python
# Crea un índice.
indice = ['Tienda 1', 'Tienda 1', 'Tienda 2', 'Tienda 3','Tienda 3']

# Crea un diccionario con una entrada (nombre:Serie) para cada columna.
compras = {'Nombre': pd.Series(['Álvaro', 'Benito', 'Fernando', 'Martín','Hernán'], index=indice),
           'Producto': ['Queso', 'Vino', 'Jamón', 'Aceite',np.NaN],
           'Precio': [22.5, 14.50, 50.00, 20.00, np.NaN]}

# Crea el dataframe
df_compras = pd.DataFrame(compras)
df_compras
```

La función `DataFrame.from_dict()` también construye un `DataFrame` a partir de un diccionario. Mediante el parámetro `orient`, permite especificar si las claves corresponden a las columnas (igual que en el caso anterior) o al índice, es decir, a las filas. 


```python
compras = {'Nombre': ['Álvaro', 'Benito', 'Fernando','Martín','Hernán'],
          'Precio': [22.5, 14.5, 50.0, 20.0],
          'Producto':['Queso', 'Vino', 'Jamón', 'Aceite']}

df_compras = pd.DataFrame.from_dict(compras, orient="index")
df_compras
```

---

### <font color="#004D7F"> Construcción a partir de una lista o colección</font>

Se hace mediante el método `DataFrame.from_records()`. Cada elemento de la lista representa una fila. Si no se proporcionan las columnas e índice, en ambos casos se utilizan enteros.


```python
ventas = [('Álvaro', 22.5, 'Queso'),
         ('Benito', 14.5, 'Vino'),
         ('Fernando', 50, 'Jamón'),
         ('Martín', 20.0, 'Aceite'),
         ('Hernán',np.NaN, np.NaN)]

columnas = ['Nombre', 'Precio', 'Producto']
indice =['Tienda 1', 'Tienda 1', 'Tienda 2', 'Tienda 3','Tienda 3']

df_compras = pd.DataFrame.from_records(ventas, columns=columnas, index=indice)
df_compras
```

El método `DataFrame.from_items()` permite también construir el `DataFrame` a partir de una lista. El parámetro `orient` permite determinar si cada elemento corresponde a una fila o a una columna. Si corresponden a filas, es necesario especificar el nombre de las columnas. 


```python
compras = [('Nombre', ['Álvaro', 'Benito', 'Fernando','Martín']),
         ('Precio', [22.5, 14.5, 50.0, 20.0]),
         ('Producto', ['Queso', 'Vino', 'Jamón', 'Aceite'])]

df_compras = pd.DataFrame.from_items(compras, orient="index", columns=["C.1", "C.2", "C.3", "C.4"])
print(df_compras)
print()

df_compras = pd.DataFrame.from_items(compras, orient="columns")
df_compras
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section63"></a> 
## <font color="#004D7F"> Lectura desde archivos </font>

Pandas proporciona funciones muy flexibles que permiten leer objetos `DataFrame` desde diversas fuentes de datos, como archivos csv, excel, JSON, HDF5, HTML, fuentes SQL, o incluso el portapapeles del sistema ([documentación](http://pandas.pydata.org/pandas-docs/version/0.20/io.html)). 

A continuación se describen los métodos que se utilizarán con más frecuencia en este curso.

### <font color="#004D7F">  Lectura de archivos en formato csv (_comma separated values_) </font>

Se lleva a cabo mediante la función `read_csv()`. El parámetro `index_col` permite especificar si alguna de las columnas ha de ser utilizada como índice del `DataFrame`. Esta función acepta otros muchos parámetros, que permiten por ejemplo descartar líneas (`skiprows`), elegir el separador entre columnas (`sep`), especificar el nombre de cada columna (`name`), o incluso su tipo de datos (`dtype`). Además, puede acceder a la fuente de datos a través de una URL ([documentación](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_csv.html)).


```python
#df_titanic = pd.read_csv('datos/Titanic.csv', index_col=0, sep=',')
#df = pd.read_csv('https://vincentarelbundock.github.io/Rdatasets/csv/datasets/Titanic.csv', index_col=0)
df_titanic = pd.read_csv('datos/Titanic.csv', sep=',', skiprows=1, index_col=1,
                         names=['ID','Nombre','Clase','Edad','Sexo','Superviviente','Código (Sexo)'])
df_titanic.head()
```

---
### <font color="#004D7F">  Lectura de archivos en formato excel </font>

Se hace mediante la función `read_excel()`. Al igual que la anterior, permite especificar numerosos parámetros como la hoja concreta del archivo (`sheet_name`), tipos de datos, etc ([documentación](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_excel.html)).


```python
df_titanic = pd.read_excel('datos/Titanic.xlsx', sheet_name='Hoja1', skiprows=0, index_col=1,
                          names=['ID','Clase','Edad','Sexo','Superviviente','Código (Sexo)'])
df_titanic.index.name='Nombre'
df_titanic.head()
```


```python
!pip install xlrd
```

<div class="alert alert-block alert-danger">
<i class="fa fa-exclamation-triangle" aria-hidden="true"></i>
 Para trabajar con excel es necesario instalar el paquete `xlrd`.
</div>


```python
#! pip install xlrd
```

### <font color="#004D7F">  Lectura de archivos en formato JSON </font>

La función `read_json` permite leer un `DataFrame` a partir de un conjunto de datos, que puede ser accedido a través de una URL. También acepta como parámetro un `String`. El formato del objeto JSON ha de ajustarse al del `DataFrame`, y puede ser indicado a través del parámetro `orient` que, en este caso, además de `columns` e `index`, puede tomar los valores `split`, `records` y `values` ([documentación](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_json.html)). 


```python
url = 'https://raw.githubusercontent.com/chrisalbon/simulated_datasets/master/data.json'
df = pd.read_json(url, orient='records')
df.head(5)
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section64"></a> 
## <font color="#004D7F"> Acceso a elementos </font>

Para ilustrar el acceso a los elementos, se utilizará este  `DataFrame`, descrito anteriormente.


```python
compra_1 =  {'Nombre': 'Álvaro', 'Producto': 'Queso', 'Compra':15.0, 'Venta': 22.50}
compra_2 =  {'Nombre': 'Benito', 'Producto': 'Vino', 'Compra':10.0, 'Venta': 14.50}
compra_3 =  {'Nombre': 'Fernando','Producto': 'Jamón', 'Compra':35.0, 'Venta': 50.00}
compra_4 =  {'Nombre': 'Martín', 'Producto': 'Aceite', 'Compra':12.0, 'Venta': 20.00}
compra_5 =  {'Nombre': 'Hernán'} 

df = pd.DataFrame([compra_1, compra_2, compra_3, compra_4, compra_5], 
                  index=['Tienda 1', 'Tienda 1', 'Tienda 2', 'Tienda 3', 'Tienda 3'])
df
```


###  <font color="#004D7F"> Acceso a las filas de un DataFrame </font>

Del mismo modo que en los objetos `Series`, el acceso a las filas del `DataFrame` se hace mediante los métodos `loc[]` e `iloc[]`. Si el índice corresponde a una sola fila, estos devuelven un objeto de tipo `Series`; sin embargo, si el índice corresponde a varias filas, devuelven otro `Dataframe`.


```python
fila = df.loc['Tienda 2']               # Accede a la fila con índice 'Tienda 2'
print(fila)
print(type(fila),'\n')                  # Imprime el tipo.

filas = df.loc['Tienda 1']              # Accede a las filas con índice 'Tienda 1'
print(filas)
print(type(filas),'\n')

filas = df.iloc[0]
print(filas)
print(type(filas))
```

La funcionalidad de ambos métodos también es similar a la descrita en el caso de las `Series`. Es decir, aceptan indexación a partir de colecciones del tipo del índice o booleanos (en el caso de `loc[]`), y de enteros (en el caso de `iloc[]`).


```python
print(df.loc[['Tienda 2','Tienda 1']])      
print()

print(df.loc['Tienda 2':])
print()

print(df.iloc[[0,3]])
```

<div class="alert alert-block alert-info">
<i class="fa fa-info-circle" aria-hidden="true"></i> Como ya se vió en el caso de los objetos `DatatimeIndex`, los índices de cada tipo ofrecen funcionalidades específicas. Por ejemplo, en el caso de índices alfanuméricos, se puede hacer _slicing_ lexicográfico sin necesidad de especificar los valores concretos de los elementos.
</div>


```python
df_titanic = df_titanic.sort_index()
df_titanic.loc['Ae':'Al']
```

---

###  <font color="#004D7F"> Acceso a las columnas de un DataFrame </font>

Se pueden acceder los elementos de una columna (o varias) mediante el operador `[]`. También se devuelve un objeto de tipo `Series` o `DataFrame` según se accedan, respectivamente, una o varias columnas.


```python
productos = df['Producto']
productos
```


```python
columnas = ['Producto','Compra','Venta']
productos_coste = df[columnas]
# productos_coste = df[['Producto','Compra','Venta']]   # Equivalente 
productos_coste
```

Las columnas puden accederse individualmente como un campo del objeto `DataFrame`.


```python
productos = df.Producto          # Es equivalente a  df['Producto']
print(productos)
type(df.Producto)
```

---
### <font color="#004D7F"> Acceso a elementos del DataFrame. </font>

Con `loc` se puede acceder también a elemento dados el valor (o valores) de su índice y columna. No se recomienda utilizar la segunda de las alternativas, ya que puede dar lugar a comportamientos inesperados (particularmente en escrituras).


```python
print(df.loc[['Tienda 2','Tienda 1'],'Producto'])
print()
print(df.loc[['Tienda 2','Tienda 1']]['Producto'])
print()
print(df.loc[['Tienda 2','Tienda 1'],['Producto','Compra','Venta']])
print()
print(df.iloc[0:3,0:2])      
```

<div class="alert alert-block alert-info">
<i class="fa fa-info-circle" aria-hidden="true"></i> El método `at` es equivalente a `loc`, (e `iat` a `iloc`) con la salvedad de que `at` e `iat` __solamente aceptan escalares como índice__. Debido a esto, también son más eficientes. 
</div>


```python
fila = df.at['Tienda 1','Producto']            
print(fila,'\n')

fila = df.iat[2,1]            
print(fila)
```

---
### <font color="#004D7F"> Escritura en elementos del DataFrame. </font>

El acceso de para escritura es similar del acceso para lectura. Cuando se escriben varias posiciones, las dimensiones de los conjuntos de elementos a ambos lados de la asignación han de ser similares (salvo en el caso en que se asignen escalares).


```python
df
```

Este código asigna un escalar a varias posiciones.


```python
df.loc[['Tienda 1','Tienda 3'],'Venta']=100
df
```

Restaura los valores (los tamaños corresponden).


```python
df.loc[['Tienda 1','Tienda 3'],'Venta']=[22, 14.5, 0, 0]
df
```

También se puede escribir el valor de columnas completas.


```python
df['Producto'] = ['Queso manchego', 'Vino manchego', 
                  'Jamón Extremeño', 'Aceite Andaluz', 'Azafrán manchego']
df
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section65"></a> 
## <font color="#004D7F"> Manipulación de la estructura</font>

### <font color="#004D7F"> Cambio de índice </font>

El índice se puede cambiar mediante la función `set_index`.  Esta función genera un nuevo DataFrame, salvo que explícitamente se indique lo contrario mediante el atributo `inplace`.


```python
df2 = df.copy()
df2.set_index('Nombre', inplace=True)
df2.head()
```

También es posible asignar una columna del `DataFrame` al campo `DataFrame.index`. En este caso hay que hacer dos consideraciones importantes:

1. El índice anterior se pierde, con lo que no hay manera de recuperarlo.
2. La columna se mantiene, por lo que hay que eliminarla manualmente. 


```python
df2 = df.copy()
df2.index = df2['Nombre']
df2.head()
```

El resultado de esta operación es la creación de un índice a partir de la columna, que permanece en el `DataFrame`.


```python
print(type(df2['Nombre']))
print(type(df2.index))
```

### <font color="#004D7F"> Creación de nuevas columnas </font>


Cuando se asignan valores a una columna no existente, se crea ésta automáticamente. Si el valor es una colección, asigna uno por uno, según el orden del índice, los elementos. El tamaño de la colección ha de coincidir con el número de filas del `DataFrame`.


```python
df['C.P'] = pd.Categorical(['02001', '02001', '02003', '02004','02004'])
df
```

También se puede añadir una columna y fijar los elementos en todas las filas a un valor determinado.


```python
df['Localidad'] = 'Albacete'
df
```

Se pueden añadir valores a algunas filas solamente mediante un diccionario en el que las claves corresponden al índice, dejando el resto con valor indeterminado o NaN.


```python
df['Entregado'] = pd.Series({'Tienda 1': True, 'Tienda 2': True})
df
```

### <font color="#004D7F"> Renombrado de índice y de columnas </font>


Pueden renombrarse mediante el método `rename()`. Puede tomar como argumento un diccionario con las correspondencias, o una función de transformación ([documentación](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.rename.html)).

En caso de tomar una función de transformación, es necesario especificar si ésta actúa sobre índices o sobre columnas mediante el atributo `axis` (que por defecto toma el valor `index`). El siguiente ejemplo, renombra todos los índices anteponiéndoles el caracter '#' y transformándolo en un `String`. Esta operación devuelve un nuevo `DataFrame`, a menos que se indique que actúe sobre el mismo mediante el parámetro `inplace`.


```python
df2 = df.copy()              # Copia el original (por claridad en los ejemplos)
df2.rename(lambda x: '#'+str(x), axis='index', inplace=True)
df2.head()
```

También se pueden renombrar índices o columnas asignando un diccionario a los atributos `index` o  `columns`.


```python
df2 = df.copy()       # Copia el original (por claridad en los ejemplos)
df2.rename(columns = {'Venta': 'PVP', 'Entregado':'Disponible'}, inplace = True);
df2.head()
```

Por último, es posible renombrar las columnas directamente asignando una colección de valores al campo `DataFrame.columns`.


```python
df2.columns = ['COMPRA','NOMBRE', 'PVP','PRODUCTO','C.P', 'LOCALIDAD','ENTREGADO']
df2

```

### <font color="#004D7F"> Reordenación de columnas </font>

Al acceder a una colección de columnas se genera un nuevo `DataFrame` en el que las columnas se colocan en el orden de la colección. 


```python
df2 = df2[['NOMBRE','LOCALIDAD','C.P', 'PRODUCTO','COMPRA','PVP']]
df2
```

### <font color="#004D7F"> Eliminación de filas y columnas </font>

El método `drop()` permite eliminar filas y columnas. Sin embargo, devuelve un nuevo objeto, y el original permanece en su estado. Para que la eliminación se haga sobre el objeto original, ha de indicarse fijando el parámetro `inplace=True`.

<div class="alert alert-block alert-warning">
<i class="fa fa-exclamation-circle" aria-hidden="true"></i>
__Importante__: La mayor parte de operaciones que manipulan la estructura del `DataFrame` no los elementos, producen una copia del mismo, pero aceptan el parámetro `inplace`.
</div>


```python
df2 = df.copy()                             # Copia el original (por claridad en los ejemplos))
df2.drop('Tienda 2', inplace = True)
df2
```

La eliminación de columnas puede hacerse con el mismo método, utilizando el parámetro `axis=1` (el valor por defecto de `axis` es 0, que indica que se eliminen filas).


```python
df2 = df.copy()                             # Copia el original (por claridad en los ejemplos))
df2.drop('Entregado', axis=1, inplace=True)
df2
```

Se pueden borrar __columnas__ también mediante la función `del`.


```python
del df2['Localidad']
df2
```

<div class="alert alert-block alert-warning">
<i class="fa fa-exclamation-circle" aria-hidden="true"></i>
__Importante__: Esta función solamente permite borrar __columnas__, no filas. 
</div>

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section66"></a> 
## <font color="#004D7F" > Descripción de los datos </font>

<br>
La función `describe()` devuelve diversa información con respecto a las columnas. Esta información varía según el tipo de datos. Además, se puede especificar sobre qué columnas se aplica la función con los parámetros `include` \ `exclude`.

La siguiente llamada incluye todas las columnas. Puede apreciarse que para las numéricas muestra unos datos, y para las no numéricas otros. 


```python
df2.describe(include="all")
```

En el caso de las variables numéricas, se pueden determinar los percentiles que se han de mostrar. 


```python
df2.describe(include="all", percentiles=[0.32, 0.61])
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section7"></a>
# <font color="#004D7F"> 7. Selección y ordenación </font>

<a id="section71"></a> 
## <font color="#004D7F"> Consulta y selección </font>



```python
df_titanic.head()
```

La aplicación de un operador booleano sobre una columna, que es de tipo `Series`, genera como resultado un objeto de tipo `Series` cuyo índice es el mismo del `DataFrame` y en el que los elementos correspoden al valor de la operación correspondiente a cada caso.

Por ejemplo, el siguiente código comprueba si los pasajeros tienen más de 18 años.


```python
consulta = df_titanic['Edad']>18
consulta[:5]                      # Muestra los 5 primeros elementos.
```

Es posible utilizar indexación lógica en un `DataFrame` para devolver los casos de interés.


```python
adultos = df_titanic[df_titanic['Edad']>18]
adultos.head(5)
```

Se pueden utilizar las operaciones entre series para generar condiciones más complejas. 


```python
varones_adultos = df_titanic[(df_titanic['Edad']>18) & (df_titanic['Sexo']=='male')]
varones_adultos.head()
```

<div class="alert alert-block alert-danger">
<i class="fa fa-exclamation-triangle" aria-hidden="true"></i>
Debido a que los operadores binarios tienen precedencia sobre las comparaciones, la siguiente expresión, en la que se han quitado los paréntesis de la expresión, devolvería un error.
</div>


```python
# varones_ancianos = df_titanic[df_titanic['Edad']>65 & df_titanic['Sexo']=='male']
```

### <font color="#004D7F" face="monospace"> where() </font>

Las consultas pueden hacerse mediante  el método `where()`. En ese caso, se devuelven todas las filas, pero se fija el valor de que no cumplen la condición se reemplazan con un valor (por defecto NaN).



```python
#adultos = df_titanic.where(df_titanic['Edad']>18)
adultos = df_titanic.where(df_titanic['Edad']>18, '---')
adultos.head()
```

Del mismo modo que la indexación mediante valores booleanos, `where` admite condiciones obtenidas mediante operaciones booleanas entre series. 


```python
adultas = df_titanic.where((df_titanic['Edad']>18) & (df_titanic['Sexo']=='female'),'---')
adultas.head()
```

### <font color="#004D7F" face="monospace"> mask() </font>

La función `mask` es opuesta a `where`, es decir, devuelve los valores que __no__ cumplen la condición.


```python
adultas = df_titanic.mask((df_titanic['Edad']<18) | (df_titanic['Sexo']=='male'),'---')
adultas.head()
```

### <font color="#004D7F" face="monospace"> isin() </font>

La función `isin` también es de suma utiliad, ya que permite seleccionar las filas cuyo valor esté dentro de un conjunto. 


```python
df_titanic[df_titanic['Edad'].isin([14,35,53])]
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section72"></a> 
## <font color="#004D7F"> Ordenación </font>

La operación `DataFrame.sort_index` ordena el `DataFrame` en función de su índice. Mediante el parámetro `ascending` se establece el orden de la ordenación. Además, por defecto produce un nuevo `DataFrame`, a no ser que se indique lo contrario mediante el parámetro `inplace`.


```python
df_titanic.sort_index(ascending=False, inplace=True)
df_titanic.head()
```

El método `DataFrame.sort_values` ordena el `DataFrame` en función de una o varias columnas. El siguiente código ordena descendentemente en función del campo _Edad_ y, en segunda instancia, ascendentemente en función del campo _ID_ (en realidad se hace al revés).


```python
df_titanic.sort_values(['Edad', 'ID'], ascending=[False, True], inplace=True)
df_titanic.head()
```

<div class="alert alert-block alert-warning">
<i class="fa fa-exclamation-circle" aria-hidden="true"></i>
__Importante__: El parámetro `kind` permite determinar el algoritmo de ordenación. En este caso, es interesante saber que `mergesort` es un algoritmo de ordenación estable. Es decir, cuando ordena por un campo, para los elementos con un mismo valor de ese campo, preserva el orden relativo de los elementos anterior a la ordenación.
</div>

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section8"></a>
# <font color="#004D7F" size=5> 8. Operaciones sobre elementos </font>

<a id="section81"></a> 
## <font color="#004D7F"> Operaciones básicas </font>

Es posible operar sobre las columnas del mismo modo que se hace en el caso de las series.

El siguiente ejemplo añade una columna con el _IVA_ de los productos, calculada a partir de los valores en la columna _Precio_.


```python
df = df.copy()                             # Copia el original (por claridad en los ejemplos))
df['IVA']= df['Venta']*0.21                # Calcula y añade el iva
df
```

En el siguiente ejemplo, se crea una columna mediante una suma de otras dos, y borra el _IVA_. 


```python
df['P.V.P'] = df['Venta']+df['IVA']
del df['IVA']
df
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section82"></a> 
## <font color="#004D7F">Operaciones de transformación </font>


### <font color="#004D7F" face="monospace"> apply() y map()</font>

Las funciones `apply()` y `map()` se pueden aplicar sobre columnas, a cada uno de los elementos, del mismo modo que se aplicaba con las series.


```python
df2 = df.copy()                 # Copia el original (por claridad en los ejemplos))

def IVA(x):                     # Crea la función que aplica el IVA
    return x*0.21

df2['IVA'] = df2['Venta'].apply(IVA) 
#df2['IVA'] = df2['Venta'].map(IVA)
  
#df2['IVA'] = df2['Venta'].apply(lambda pr: pr*0.21)  # Hace lo mismo, pero utilizando una función lambda
#df2['IVA'] = df2['Venta'].map(lambda pr: pr*0.21) 
df2
```


### <font color="#004D7F" face="monospace"> applymap() </font>

<br>
La función `applymap` aplica la función de transformación sobre todos los elementos del DataFrame. En este ejemplo concreto, copiamos todas las columnas numéricas (tipo `np.number`) en el `DataFrame` y se aplica una función que convierte los valores a String con tres decimales.


```python
df2 = df.select_dtypes(include=np.number)
df2 = df2.applymap(lambda x: '{:.3f}'.format(x))
df2
```


### <font color="#004D7F" face="monospace"> transform() </font>

La función `transform()` permite llevar a cabo una transformación de los elementos de un `DataFrame`. Permite especificar qué columnas se transforman, y también aplicar varias transformaciones. Como resultado, devuelve una estructura que contiene todas las transformaciones llevadas a cabo  ([documentación](http://pandas.pydata.org/pandas-docs/version/0.20/basics.html#transform-api)).


```python
df2 = df.copy()
df2.transform({'P.V.P': [lambda p: p*1.21, lambda p: "{:.1f} euros".format(p*1.21)], 'Localidad':str.upper})
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section9"></a>
# <font color="#004D7F" size=5> 9. Agregación </font>

<a id="section91"></a> 
## <font color="#004D7F" > Estadísticos descriptivos </font>

Cuando las funciones para la obtención de medidas resumen datos estadísticos se aplican sobre un objeto `DataFrame`, es necesario especificar sobre qué eje se hacen mediante el parámetro `axis` que como anteriormente, puede tomar los valores `index/0` o `columns/1`. Un resumen de las funciones disponbiles puede encontrarse en la ([documentación](http://pandas.pydata.org/pandas-docs/version/0.20/basics.html#descriptive-statistics)).

El siguiente código muestra la suma  y media de las columnas. Puede especificarse que solamente lo haga con las numéricas, aunque este es el comportamiento por defecto en algunas funciones cuando no se pueden sumar las filas o columnas.    


```python
# OBtienen estadísticos por columnas
print(df2.sum(axis=0, numeric_only=True))
print()
print(df2.mean(axis=0,numeric_only=True))
print()
print(df2.max(axis=0, numeric_only=True))
```

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section92"></a> 

## <font color="#004D7F" > Agregación de datos: <font face="monospace">apply() y agg()</font></font>

### <font color="#004D7F" face="monospace"> apply()  </font>

A diferencia de `Series.apply()` en este caso __también__ se pueden aplicar funciones que generen un escalar (no elemento por elemento). El parámetro `axis=0` o `axis='index'` indica que se aplique la función a los elementos a lo largo del índice, es decir, se aplica para cada columna.


```python
df_precios = df[['Compra','Venta']]
print(df_precios)
print()

def min_max(valores):
    return (np.min(valores),np.max(valores))

# Valor mínimo y máximo por cada producto
print(df_precios.apply(min_max, axis=0))
```

Con el parámetro `axis=1` o `axis='columns'` se aplica la función a lo largo de las columnas, es decir, para cada índice. En este caso, las funciones pueden acceder a los valores individuales de cada columna.

<div class="alert alert-block alert-warning">
<i class="fa fa-exclamation-circle" aria-hidden="true"></i>
__Importante__: Esta es una de las formas de crear columnas como resultado de la operación o transformación sobre otras. 
</div>


```python
df_precios = df[['Producto','Compra','Venta']]
print(df_precios)
print()

def anuncio(entrada):
    return '¡'+entrada['Producto']+' a '+str(entrada['Venta']-entrada['Compra']) + ' euros!'

print(df_precios.apply(anuncio, axis=1))
```

<div class="alert alert-block alert-danger">
<i class="fa fa-exclamation-triangle" aria-hidden="true"></i>
El parámetro `axis=1` indica columnas, pero en este caso quiere decir que se aplica sobre cada fila. Este hecho puede generar confusión.
</div>

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<a id="section73"></a> 
## <font color="#004D7F"> Agregación </font>
<br>

Las funciones `aggregate()` o `agg()` (son la misma) permiten aplicar funciones de agregación a filas o columnas. A diferencia de `apply()`, descrito anteriormente, permite aplicar varias funciones en una operación. Éstas pueden ser referidas por un nombre (String), identificador, incluso ser funciones `lambda` ([documentación](http://pandas.pydata.org/pandas-docs/version/0.20/basics.html#aggregation-api)).

Por ejemplo, el código siguiente aplica las funciones suma, media y rango de dos columnas (en realidad, se crea un `DataFrame` con dos columnas y se aplica la agregación). 


```python
df_precios[['Compra','Venta']].agg(['sum', np.mean, lambda col: col.max()-col.min()], axis=0)
```

`agg()` permite que se especifique, mediante un diccionario, qué función o funciones se aplican a cada columna.


```python
df_precios.agg({'Producto':np.min, 'Compra':[np.mean, np.sum]}, axis=0)
```

Por último, es posible aplicar por filas.


```python
df_precios[['Compra','Venta']].agg(lambda p:p['Venta']-p['Compra'], axis=1)
```

<div class="alert alert-block alert-danger">
<i class="fa fa-exclamation-triangle" aria-hidden="true"></i>
Parece ser que existe un bug en Pandas que no permite pasar una lista de funciones a `agg()` cuando `axis=1` ([enlace](https://github.com/pandas-dev/pandas/issues/16679)). Por lo cual, el uso de la función en este caso es equivalente a `apply()`.
</div>

<div style="text-align: right"> <font size=5> [<i class="fa fa-arrow-circle-up" aria-hidden="true" style="color:#004D7F">](#indice)</i></font></div>

---

<div style="text-align: right"> <font size=6><i class="fa fa-coffee" aria-hidden="true" style="color:#004D7F"></i> </font></div>
