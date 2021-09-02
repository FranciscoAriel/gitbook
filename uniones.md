---
description: Combinción de 2 o más tablas
---

# Uniones de tablas

En esta sección se hablará acerce de cómo hacer uniones de 2 o más tablas. Primero se estudiarán las uniones de 2 más tablas por medio de una llave y después se tratará el tema de agregar nuevos datos.

## Combinación de tablas con llaves únicas

Hasta ahora se ha trabajado con información de una sola tabla, sin embargo es común tener información en 2 o más tablas.

En esta sección se mostrará como trabajar con este tipo de información, especialmente con aquellas que tienen una llave en común.

### Unión de tablas completas (Full Join)

Considere las siguientes tablas

Nuevos:

id|nombre|edad
--|------|----|
1|Andrés|30
2|Bárbara|25
5|Cecilia|32

Empleados:

id|posicion|fecha_ingreso
--|-----|-------|
1|Analista Jr.|10/01/2020
2|Analista Jr.|25/09/2019
3|Analista Sr.|01/01/2019
4|Gerente|05/06/2018

Note que las dos tablas tienen en común la variable _id_ y sólo tiene los ids 1 y 2 son comunes en ambas tablas.

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

Esto se debe a que las dos tablas tienen un mismo nombrede variable (_id_). Al revisar la tabla, se obtiene el siguiente resultado:

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

El siguiente código soluciona dichos problemas:

````sas
PROC SQL;
   CREATE TABLE TABLA AS
   SELECT 
   COALESCE(A.id,B.id) AS id, *
   FROM NUEVOS AS A
   FULL JOIN EMPLEADOS AS B
   ON A.ID EQ B.ID;
QUIT;
````

### Unión de tablas con elementos en común (inner join)

Si se desea unir únicamente los elementos en común, se puede aplicar el siguiente código.

````sas
PROC SQL;
   CREATE TABLE TABLA AS
   SELECT 
   COALESCE(A.id,B.id) AS id, *
   FROM NUEVOS AS A
   INNER JOIN EMPLEADOS AS B
   ON A.ID EQ B.ID;
QUIT;
````

El resultado sería el siguiente:

id|nombre|edad|posicion|fecha_ingreso
--|------|----|--------|------------|
1|Andrés|30|Analista Jr.|10/01/2020
2|Bárbara|25|Analista Jr.|25/09/2019

{% hint style="success" %}
Puede usarse simplemente la palabra `JOIN` en lugar de `INNER JOIN` para uniones con elementos en común.
{% endhint %}
