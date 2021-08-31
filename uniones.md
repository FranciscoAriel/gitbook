---
description: Combinción de 2 o más tablas
---

## Combinación de tablas

Hasta ahora se ha trabajado con información de una sola tabla, sin embargo es común tener información en 2 o mas tablas.

En esta sección se mostrará como trabajar con este tipo de información.

### Unión de tablas con llaves

Considere las siguientes tablas

**Nuevos**

id|nombre|edad
--|------|----|
1|Andrés|30
2|Bárbara|25
5|Cecilia|32

**Empleados**

id|posicion|fecha_ingreso
--|-----|-------|
1|Analista Jr.|10/01/2020
2|Analista Jr.|25/09/2019
3|Analista Sr.|01/01/2019
4|Gerente|05/06/2018

Note que ambas tablas tienen en común la variable _id_ y solo tiene el id 1 es común en ambaas tablas.

Si se desea unir ambas tablas, se puede usar el campo llave _id_ y usar el siguiente código:

````sas
PROC SQL;
   CREATE TABLE TABLA AS
   SELECT * FROM NUEVOS AS A
   FULL JOIN EMPLEADOS AS B
   ON A.ID EQ B.ID;
QUIT;
````

Sin embargo el log muestra el siguiente mensaje.

> WARNING: La variable id ya existe en el archivo WORK.TABLA

Al revisar la tabla, se obtiene el siguiente resultado:

id|nombre|edad|posicion|fecha_ingreso
--|------|----|--------|------------|
1|Andrés|30|Analista Jr.|10/01/2020
2|Bárbara|25|Analista Jr.|25/09/2019
.||.|Analista Sr.|01/01/2019
.||.|Gerente|05/06/2018
5|Cecilia|32||.

Observe que la variable _id_ tiene valores vacíos, esto se debe a que la tabla _A_ predomina y se conservan los valores.

{% hint style="info" %}
Se pueden usar variables indicadoras para saber de qué tabla proviene cada observación.
{% endhint %}

Para conservar todos los id en común se usa la función `COALESCE` (o `COALESCEC` para variables de tipo carcter).
