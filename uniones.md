---
description: Combinción de 2 o más tablas
---

# Uniones de tablas

En esta sección se hablará acerca de cómo hacer uniones de 2 o más tablas. Primero se estudiarán las uniones de 2 más tablas por medio de una llave y después se tratará el tema de agregar nuevos datos.

## Unión de tablas con llaves únicas

Hasta ahora se ha trabajado con información de una sola tabla, sin embargo es común tener información en 2 o más tablas.

En esta sección se mostrará como trabajar con este tipo de información, especialmente con aquellas que tienen una llave sin duplicados en común.

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

{% hint style="info" %}
Cuando la información proviene de observaciones no empatadas, a esa unión se le conoce como _outer join_.
{% endhint %}

{% hint style="info" %}
Cuando la información está presente en todas las fuentes, a esa unión se le conoce como _full join_.
{% endhint %}

Si se desea unir ambas tablas, admitiendo información que esté en ambas tablas, se puede usar el campo llave _id_ y usar el siguiente código:

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
.| |.|Analista Sr.|01/01/2019
.| |.|Gerente|05/06/2018
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

Note que se tiene información en donde las llaves coinciden, de otra forma, las observaciones son descartadas.

### Unión de tablas asimétricas (left join y right join)

Si se desea conservar la información de una tabla más la información que coincide con otra se puede usar una unión asimétrica.

Por ejemplo, supóngase que se desea conservar la toda  la tabla de empleados nuevos. El código que se podría usar sería el siguiente.

````sas
PROC SQL;
   CREATE TABLE TABLA AS
   SELECT 
   *
   FROM NUEVOS AS A
   LEFT JOIN EMPLEADOS AS B
   ON A.ID EQ B.ID;
QUIT;
````

El resultado sería el siguiente.

id|nombre|edad|posicion|fecha_ingreso
--|------|----|--------|------------|
1|Andrés|30|Analista Jr.|10/01/2020
2|Bárbara|25|Analista Jr.|25/09/2019
5|Cecilia|32||.

Si se quisiera mantener únicamente la tabla derecha (es decir la tabla de empleados)
se puede usar el siguiente código:

````sas
PROC SQL;
   CREATE TABLE TABLA AS
   SELECT 
   *
   FROM NUEVOS AS A
   RIGHT JOIN EMPLEADOS AS B
   ON A.ID EQ B.ID;
QUIT;
````

El resultado es el siguiente

id|nombre|edad|posicion|fecha_ingreso
--|------|----|--------|------------|
1|Andrés|30|Analista Jr.|10/01/2020
2|Bárbara|25|Analista Jr.|25/09/2019
.||.|Analista Sr.|01/01/2019
.||.|Gerente|05/06/2018

Obsérvese que debido a que en la tabla *nuevos* no se tiene información de los ids 3 y 4, ni sus edades, el resultado arroja un valor missing debido a que la tabla A **predomina** al hacer la consulta. Si se quieren conservar los valores de los ids, se debe hacer uso de la función `COALESCE`.

````sas
PROC SQL;
   CREATE TABLE TABLA AS
   SELECT 
   COALESCE(A.id,B.id) AS id,
   *
   FROM NUEVOS AS A
   RIGHT JOIN EMPLEADOS AS B
   ON A.ID EQ B.ID;
QUIT;
````

## Unión de tablas con llaves duplicadas

Si las tablas que se van a unir tienen llaves duplicadas, el resultado es muy distinto, debido a que SQL realiza un producto cartesiano, es decir, cada llave de la primer tabla, se empareja con su par de la segunda. El siguiente ejemplo ilustra el producto cartesiano.

Suponga que se tienen las siguientes tablas

Nuevos:

id|nombre|edad
--|------|----|
1|Andrés|30
1|Andrea|30
2|Bárbara|25
5|Cecilia|32

Empleados:

id|posicion|fecha_ingreso
--|-----|-------|
1|Analista Jr.|10/01/2020
1|Gerente|10/10/2020
2|Administrador|12/12/2020
2|Analista Jr.|25/09/2019
3|Analista Sr.|01/01/2019
4|Gerente|05/06/2018

Al realizar un *inner join* se obtiene el siguiente resultado:

id|nombre|edad|posicion|fecha_ingreso
--|------|----|---------|-----------|
1|Andrés|30|Analista Jr.|10/01/2020
1|Andrea|30|Analista Jr.|10/01/2020
1|Andrés|30|Gerente|10/10/2020
1|Andrea|30|Gerente|10/10/2020
2|Bárbara|25|Administrador|12/12/2020
2|Bárbara|25|Analista Jr.|25/09/2019

Como se puede observar, cada id de la primer se empata con el id de la segunda tabla. Por ejemplo, Andrés puede ser ya sea Gerente o Analista Jr. y así sucesivamente para los otros empleados.

## Unión con más de 2 tablas

Supóngase que se tiene una tercer tabla que contiene el **id** y **area**.

id|area
--|----
1|Finanzas
2|Sistemas
3|Contabilidad
4|Sistemas
5|Finanzas

El siguiente código muestra cómo unir las tres tablas. Nótese que se desea conservar la tabla A como principal.

````sas
PROC SQL;
    CREATE TABLE tabla AS
    SELECT COALESCE(A.id, B.id,C.id) AS id,*
    FROM nuevos AS a 
    FULL JOIN empleados AS b 
    ON a.id EQ b.id
    FULL JOIN areas AS c
    ON COALESCE(a.id,b.id) EQ c.id;
QUIT;
````

Al unir las tres tablas por **id** se obtiene el siguiente resultado.

id|nombre|edad|posicion|fecha_ingreso|departamento
--|------|----|--------|-------------|------------|
1|Andrés|30|Analista Jr.|10/01/2020|Finanzas
2|Bárbara|25|Analista Jr.|25/09/2019|Sistemas
3| |.|Analista Sr.|01/01/2019|Contabilidad
4| |.|Gerente|05/06/2018|Sistemas
5|Cecilia|32| |.|Finanzas

## Agregar nuevas observaciones

Supóngase que se desean agregar nuevas observaciones a una tabla exigente.

La tabla **plantilla** contiene a los empleados que están registrados actualmente.

id|nombre|edad
--|------|----
3|María|45
4|Vicente|40
6|Rodrigo|38
10|Alberto|46
15|Helena|50
18|Roberto|42

La tabla **nuevos** contiene a los empleados de nuevo ingreso.

id|nombre|edad
--|------|----
100|Juan|25
100|Juan|25
105|Guadalupe|30
110|José Luis|35

 Se desea crear otra tabla que contenga tanto a los existentes como a los nuevos.

 El siguiente código muestra como agrgar nuevos registros a una tabla

````sas
PROC SQL;
   CREATE TABLE PERSONAL AS 
   SELECT * FROM plantilla 
   UNION SELECT * FROM nuevos;
QUIT;
 ````

Se puede usar el operador `UNION` el cual especifica que se deben unir todas las observaciones *únicas* de las consultas, en este caso de la tabla **nuevos** y la tabla **plantilla**. Como ambas tablas tienen las mismas columnas, el dataset resultante no se altera.

El resultado es el siguiente:

id|nombre|edad
--|------|----
3|María|45
4|Vicente|40
6|Rodrigo|38
10|Alberto|46
15|Helena|50
18|Roberto|42
100|Juan|25
105|Guadalupe|30
110|José Luis|35

Note que a pesar de que en la tabla **nuevos**, el registro 1 y 2 estaban duplicados, únicamente se obtuvo un registro y se eliminó el duplicado.

Si se desea traer toda la información a pesar de que esté duplicada la información, se puede usar la palabra `ALL` despues del operador `UNION`.

````sas
PROC SQL;
   CREATE TABLE PERSONAL AS 
   SELECT * FROM plantilla 
   UNION ALL
   SELECT * FROM nuevos;
QUIT;
 ````

El resultado es el siguiente:

id|nombre|edad
--|------|----
3|María|45
4|Vicente|40
6|Rodrigo|38
10|Alberto|46
15|Helena|50
18|Roberto|42
100|Juan|25
100|Juan|25
105|Guadalupe|30
110|José Luis|35

Estos operadores son conocidos como **Operadores de conjunto** y forman parte de una **expresión de consulta**.

Una expresión de consulta está formada por una o más tablas unidas por un operador de conjunto. Por ejemplo se hizo la consulta `SELECT * FROM plantilla UNION SELECT * FROM nuevos`.

Para saber más acerca de los operadores de conjunto visite la [documentación](https://documentation.sas.com/doc/es/pgmsascdc/9.4_3.5/sqlproc/p1o6k7t8y56hobn1mup90vpf4ye6.htm).
