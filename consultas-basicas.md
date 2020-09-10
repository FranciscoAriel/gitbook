---
description: SQL nivel básico
---

## Consultas básicas

En esta sección se mostrarán las consultas más básicas de SQL.

Como se mencionó anteriormente, se usará el procedimiento SQL de SAS, aunque no hay gran diferencia en otros lenguajes.

Para usar el lenguaje SQL en SAS, se debe llamar el procedimiento y se termina con la sentencia QUIT.


> `PROC SQL;`
>
>    `sentencias sql`
>
> `QUIT;`


### Sentencia SELECT

La sentencia básica es SELECT.

Esta sentencia permite elegir diversas variables y permite agregar otras cláusulas como condicionales para recuperar datos de una tabla, el origen de los datos, entre otras.

Para elegir una o más variables, se deben nombrar las columnas de la tabla que se desa consultar, los nombres deben estár separados por una coma. También es posible elegir todas las columnas de la tabla con `*`.

La sentencia SELECT más simple es la siguiente:


> `PROC SQL;`
>
> `SELECT objeto-1 <, objeto-2,...> FROM tabla;`
>
> `QUIT;`


donde `obejeto-1` representa el nombre de una variable o el símbolo `*` y `tabla` representa el nombre de la tabla de donde se hará la consulta.

Por ejemplo el siguiente código seleccionaria todas las columnas y observaciones de la tabla `sashelp.class`.

````sas
PROC SQL;
    SELECT * 
    FROM sashelp.class;
QUIT;
````

Mientras que si solo se desea obtener la variable **name** y **sex**, se usa el siguiente código.

````sas
PROC SQL;
    SELECT name,sex 
    FROM sashelp.class;
QUIT;
````

### Almacenando una consulta

La sentecia `SELECT` realiza únicamente una consulta, la cual se mostrará en la ventana de resultados, si se desea guardar esa consulta en una tabla, se debe anteponer la cláusula `CREATE TABLE` seguido de un nombre válido y la palabra clave `AS`.

````sas
PROC SQL;
    CREATE TABLE clase AS
    SELECT * 
    FROM sashelp.class;
QUIT;
````
El código anteror realizaría una copia exacta de la tabla `sashelp.class` y la almacenaría en la librería `WORK`.

La cláusula `CREATE TABLE` tambien permite copiar la estructura de una tabla existente usando la palabra clave `LIKE`.

> `PROC SQL;`
>
> `CREATE TABLA tabla1 LIKE tabla2;`
>
> `QUIT;`

donde `tabla1` es el nombre de la tabla a crear y `tabla2` es la tabla de la cual se copiará la estructura.

Por ejemplo, es siguiente código creará una tabla vacía copiando la estructura del dataset `sashelp.class`.

````sas
PROC SQL;
	CREATE TABLE clase LIKE sashelp.class;
QUIT;
````

Si se desea crear una tabla completamente nueva, se pueden definir los nombres, tipos y otros metadatos de la tabla.Los nombres de variables deben dentro de un paréntesis y separados por comas.

````sas
PROC SQL;
    CREATE TABLE tabla
    (nombre char(16),
    edad num, 
    fecha num informat = date. format = ddmmyy.);
QUIT;
````

Opcionalmente pueden insertarse nuevos registros en dicha tabla al usar la sentencia `INSERT INTO`, por ejemplo.

````sas
PROC SQL;
    INSERT INTO tabla (nombre,edad,fecha)
    VALUES ("Francisco",25,"1jan12"d)
    VALUES ("Jerónimo",29,"11jun15"d)
    VALUES ("Fernanda",33,"25dec19"d);
QUIT;

PROC SQL;
	INSERT INTO clase
	SET NAME = "Daniel",SEX = "M",AGE = 12,HEIGHT=1.35,WEIGHT = 40.5
	SET NAME = "Alejandra",SEX = "F",AGE=11,HEIGHT=1.45,WEIGHT = 45.0
;
QUIT;
````
