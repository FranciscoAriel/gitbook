---
description: SQL nivel básico
---

# Consultas básicas

En este capítulo se mostrarán las consultas más básicas de SQL.

Como se mencionó anteriormente, se usará el procedimiento SQL de SAS, aunque no hay gran diferencia en otros lenguajes.

Para usar el lenguaje SQL en SAS, se debe llamar el procedimiento y se termina con la sentencia QUIT.

````sas
PROC SQL;
    <sentencias sql>
QUIT;
````

## Sentencia SELECT

La sentencia básica es SELECT.

Esta sentencia permite elegir diversas variables y permite agregar otras cláusulas como condicionales para recuperar datos de una tabla, el origen de los datos, entre otras.

La sentencia SELECT más simple es la siguiente:

````sas
PROC SQL;
    SELECT * 
    FROM table-name;
QUIT;
````
donde `table-name` representa el nombre de la tabla.

Por ejemplo el siguiente código seleccionaria todas las columnas y observaciones de la tabla `sashelp.class`.

````sas
PROC SQL;
    SELECT * 
    FROM sashelp.class;
QUIT;
````
