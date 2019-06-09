# 10. Agrupamiento: groupby()

Comenzamos viendo una base de datos que contiene información relativa al censo en USA entre los años 2000 y 2010 dividida en dos niveles,  Estados y Condados

```python
import pandas as pd
import numpy as np
df = pd.read_csv('datos/county.txt', sep='\t')
df.head()
```

Ejercicio: muestra el tamaño del df correspondiente al grupo de `Alabama`

```python
df.groupby('state').get_group('Alabama').shape
```
Ejercicio: Muestra la columna `pop2010` para los grupos `state`

```python
grupos_pop2010 = df.groupby(df['state'])['pop2010']
#grupos_pop2010 = df['pop2010'].groupby(df['state'])      # Equivalente
#grupos_pop2010 = df.groupby('state')['pop2010']          # Sintaxis sugar
print(type(grupos_pop2010)) 
```
Ejercicio: Crea un bucle for python que nos muestre los datos relativos a la serie anterior. Nota: añade un break al final para que tan solo muestre `Alabama`

```python
for grupo, serie_grupo in grupos_pop2010:
    print(grupo,": ",len(serie_grupo),"\n")
    print(serie_grupo.head())
    break;                     # Procesa solamente la primera iteración
```

Ejercicio: Muestra un `df.groupby` utilizando `agg` equivalente a la anterior pasando una lista de funciones. Nota: recuerda utilizar numpy para el máximo y el mínimo (np.max, np.min)

```python
media_estado = df.groupby('state')['pop2010','med_income'].agg([np.max, np.min, 'mean'])
media_estado.head()
```

# 11. Multi-índices </font>

Ejercicio: Mediante el comando anterior, intenta acceder directamente a `Bibb County`.

```python
df.loc['Bibb County'].head()
```

Ejercicio: Agrupa por nombre de condado, y muestra las columnas que corresponden al índice _ money_.

```python
grupos_df = df.groupby(level=1)['money']
for grupo, datos in grupos_df:
    print(grupo)
    print(datos.head())
    break;    # Solamente el primer grupo.
```

# <font color="#004D7F"> 12. Combinación de Dataframes</font>

## Append

Ejercicio: Imprime el nuevo df cuyo `set_index` sea la columna **Nombre**
 
```python
print(pos1_df.set_index("Nombre").append(pos2_df.set_index("Nombre")))
```

Ejercicio: Añade `ignore_index` al ejercicio anterior 

```python
print(pos1_df.set_index("Nombre").append(pos2_df.set_index("Nombre"), ignore_index=True))
```
## concat()

* `axis`. Determina el eje a lo largo del cual se concatenan los datos, y puede tomar los valores 0 (filas) y 1 (columnas).
* `join`. Determina si se considera la unión (`outer`) o la intersección (`inner`) de elementos (según el índice).  
* `join_axes`. Permite especificar qué elementos se incluyen (se usa en lugar de `join`) en el nuevo `DataFrame`.
* `keys`. Es un vector de claves. Si se utiliza, crea un multi-índice, y utiliza estas claves en el primer nivel para marcar el `DataFrame` original en el resultante. 

Ejercicio: Contatena los df pos1_df y eqp_df utilizando el eje de las filas `axis=0`

```python
pd.concat([pos1_df, eqp_df], join="outer", axis=0), keys=["jugadores", "Equipos"])
```

Ejercicio: Utiliza el vector de claves se parando los dataframes en 'Jugadores' y 'Equipos'

```python
pd.concat([pos1_df, eqp_df], join="outer", axis=0, keys=["jugadores", "Equipos"])
```

Ejercicio: Une los df utilizando columnas y con el tipo de union `outer`

```python
pd.concat([cp_pos1_df, cp_eqp_df], axis=1, join='outer')
```
Ejercicio: Muestra los elementos del segundo dataframe

```python
pd.concat([cp_pos1_df, cp_eqp_df], axis=1, join_axes=[cp_pos2_df.index])
```
## merge()

Ejercicio: Busca una equivalencia al código anterior utilizando las claves de unión por columnas

```python
pd.merge(pos1_df,eqp_df, how='outer', on='Nombre', suffixes=['_jug','_equ'])
```
## join()

Ejercicio: Crea con `join` un dataframe equivalente a este:

```python
pd.merge(pos1_df, eqp_df, left_index=True, right_index=True, how='left', suffixes=['_jug','_equ'], sort=False)
```

```python
pos1_df.join(eqp_df, lsuffix='_jug', rsuffix='_equ')
```
