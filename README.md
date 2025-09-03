# Postgres-SQL-query-command

#### Hope to enjoy!

[SELECT](#select)         
[WHERE](#where)                                                       
[CRUD](#crud-create-read-update-delete)              
[Change Configuration](#change-configuration)                                  
[Aggregation & Grouping](#aggregation-and-grouping)                                
[Pattern matching](#pattern-matching)                         
[JOIN](#join)                    
[UNION](#union)            
[Constraint](#constraint)                       
[On delete](#on-delete)                                       
[Sub query](#sub-query)                         
[CTE](#common-table-expressions)                               
[INDEX](#index)                    
[View and Procedures](#view-and-stored-procedure)                         
[Function](#function)                         
[Trigger](#trigger)      
[Transferring Data](#transferring-data)           
[Vaccum](#vaccum)            
[Other](#other)       



# SELECT

The SELECT statement is used to select data from a database.The data returned is stored in a result table, called the result-set

+ return all rows from selected table:      

    ```sql  
    SELECT * FROM table_name;
    ```

+ return un-repeated rows:  

    ```sql   
    SELECT DISTINCT * FROM table_name;
    ```

+ return unique-combination:  

    ```sql   
    SELECT DISTINCT column1, column2 FROM table_name;
    ```

+ return all rows but skip first 10 rows:      

    ```sql  
    SELECT * FROM table_name OFFSET 10;
    ```

+ skip first 10 rows and then return 5 rows after that (always it run OFFSET then LIMIT in combination cases):      

    ```sql  
    SELECT * FROM table_name OFFSET 10 LIMIT 5;
    ```    

+ return column1 and greatest and put value in greatest-column according to the comparison result:  

    ```sql   
    SELECT column1, GREATEST(30, 2 * column1)  FROM table_name;
    ```

+ return column1 and least and put value in least-column according to the comparison result:  

    ```sql   
    SELECT column1, LEAST(30, 2 * column1)  FROM table_name;
    ```

+ **ORDER BY**: is used to sort the result-set in ascending or descending order. This keyword sorts the records in ascending order (ASC) by default. To sort the records in descending order, use the DESC keyword.

    In this example we passed double columns to the ORDER BY, This means when we have the same value on column1, then these rows should order based on the second column:

    ```sql
    SELECT column1, column2 FROM table_name ORDER BY column1 ASC, column2 DESC;
    ```
    
    In Postgres, you can return results distinct on a special column Provided that the result be ordered by that column:  

    ```sql   
    SELECT DISTINCT ON (column1) column1, column2 FROM table_name ORDER BY column1 LIMIT 10;
    ```

+ **Math Operation**: it is possible to do math operation on column values and return them as a new column. add(+), subtract(-), multiply(*), divide(/), exponent(^), square root(|/), absolute value(@) and remainder(%) are some candidate for this situations.             

    Note: when you make a new temporary column with math operation, this column has not any name, But you can define a name for it with "AS":

    ```sql
    SELECT column1, column2 * column3 AS total FROM table_name LIMIT 10;
    ```

+ **String Operation**: in addition to math operators, SQL offers us some operators and functions to work with strings. we can contact two or more strings with the || or concat() function. there are also some available functions like LENGTH(), LOWER(), and UPPER().     

    ```sql
    SELECT column1, city || ', ' || country AS location FROM table_name;
    SELECT CONCAT(LOWER(name), ' - ', UPPER(family)) AS full_name FROM table_name;
    ```

<br>

# WHERE

The SQL WHERE clause is used to specify a condition while fetching the data from a single table or by joining with multiple tables. If the given condition is satisfied, then only it returns a specific value from the table. You should use the WHERE clause to filter the records and fetching only the necessary records.

+ Example:

    example of working with **is** and **is not**:

    ```sql
    SELECT * FROM table_name WHERE column1 IS null;
    ```
    ```sql
    SELECT * FROM table_name WHERE column1 IS not null;
    ```

    example of working with **and**, **or**:
    ```sql
    SELECT * FROM table_name 
    WHERE (column1 > 100 AND column2='berlin') OR (column2='berlin' AND NOT column3=100);
    ```

    example of working with **between**:
    ```sql
    SELECT * FROM table_name WHERE column1 BETWEEN 10 AND 50;
    ```

    example of working with **in**:
    ```sql
    SELECT column1, column2 FROM table_name 
    WHERE column1 NOT IN (300, 400) and column2 != 'somethings';
    ```

<br>

# CRUD (create, read, update, delete)


+ **CREATE database**: 

    ```sql
    CREATE DATABASE database_name;
    ```

+ **CREATE table**:

    ```sql
    CREATE TABLE table_name (
        column1 datatype,
        column2 datatype,
    );
    ```

+ **Add row**:

    ```sql
    INSERT INTO table_name (column1, column2) VALUES (value1, value2);
    ```

   Note: To insert data into a table that has a foreign key column, we should insert the value that was in the referenced column or we can insert Null. It will lead to an error if we do not do this.

+ **Update row**:

    ```sql
    UPDATE table_name SET column1=value1, column2=value2 WHERE condition;
    ```

    ```sql
    UPDATE products SET price=9999 WHERE price is NULL;
    ```

    Note: To prevent updating unwanted columns, always use WHERE when updating rows.

+ **DELETE database**: 

    ```sql
    DROP DATABASE database_name;
    ```

+ **DELETE table**: 

    ```sql
    DROP TABLE table_name
    ```

+ **DELETE row**:

    ```sql
    DELETE FROM table_name WHERE condition;
    ```    
    Note: To prevent deleting unwanted columns, always use WHERE when deleting rows.


<br>

# Change Configuration
The ALTER TABLE statement is used to add, delete, or modify columns in an existing table. They also used to add and drop various constraints on an existing table:

+ Rename table:

    ```sql
    ALTER TABLE table_name RENAME TO new_table_name;
    ```

+ Add column:

    ```sql
    ALTER TABLE table_name ADD COLUMN column_name datatype;
    ```

+ Rename column:

    ```sql
    ALTER TABLE table_name RENAME column_name TO new_column_name;
    ```

+ Delete column:

    ```sql
    ALTER TABLE table_name DROP COLUMN column_name;
    ```

+ Change column datatype:

    ```sql
    ALTER TABLE table_name COLUMN column_name TYPE datatype;
    ```
    Sometimes it is not possible to cast automatically some datatype to another for example integer to boolean. In these cases can use:

    ```sql
    ALTER TABLE table_name ALTER COLUMN column_name TYPE BOOLEAN USING (column_name::BOOLEAN);
    ```

+ Change column constraint:

    ```sql
    ALTER TABLE table_name ALTER COLUMN column_name SET NOT NULL;
    ```
    ```sql
    ALTER TABLE table_name ALTER COLUMN column_name SET DEFAULT 1000;
    ```


<br>

# Aggregation and Grouping

An aggregate function in SQL performs a calculation on multiple values and returns a single value. SQL provides many aggregate functions that include avg, count, sum, min, max, etc. An aggregate function ignores NULL values when it performs the calculation, except for the count function. 

+ **Aggregation Example**:

    Note: We can not use aggregate and other column in same SELECT

    ```sql
    SELECT MAX(column1) FROM table_name
    SELECT MIN(column1) FROM table_name
    SELECT AVG(column1) FROM table_name
    SELECT SUM(column1) FROM table_name
    SELECT COUNT(*) FROM table_name;
    ```

+ **GROUP BY**: groups rows that have the same values into summary rows, like "find the number of customers in each country". It is often used with aggregate functions that learned about it.

    Note: when we group a table, just grouped column is directly accessible. For example in the below code we grouped our table based on column1 so we can just select column1 in SELECT - FROM:

    ```sql
    SELECT column1 FROM table_name WHERE total > 2000 GROUP BY column1;
    ```

    Note: After grouping a table based on a special column, other columns in this temporary table are reachable through Aggregate functions or we can access them directly if they was unique(single) for every group:     

    ```sql                 
    SELECT column1, MAX(column2) FROM table_name GROUP BY column1;
    SELECT column1, COUNT(*) FROM table_name GROUP BY column1;
    ```

    Example of counting un-repeated rows:       

    ```sql                 
    SELECT COUNT(DISTINCT country) FROM table_name GROUP BY country;
    ```

+ **Having**: group by will use to groups rows by a unique set of values but having is for filtering the set of groups.  It is not necessary to use having with the group by but if we want to do some filter on the group then we should use having:

    ```sql                 
    SELECT column1, COUNT(*) FROM table_name WHERE column1 > 2000 
    GROUP BY column1 HAVING COUNT(*) > 2;
    ```

    ```sql                 
    SELECT column1, SUM(column2 * column3) FROM table_name 
    GROUP BY column1 HAVING SUM(column2 * column3) > 1000;
    ```


<br>

# Pattern matching 

+ **SQL pattern matching**: enables you to use _ to match any single character and % to match an arbitrary number of characters (including zero characters).      
Some examples are shown here. Do not use = or <> when you use SQL patterns. Use the LIKE or NOT LIKE comparison operators instead.

     start whith A, continue with everything, end to e:

    ```sql
    SELECT * FROM table_name WHERE column1 LIKE 'A%e';
    ```

    start whith A, continue with everything, e, continue with everything,

    ```sql
    SELECT * FROM table_name WHERE column1 LIKE 'A%e%';
    ```

    start whith A, just 1 char between, f, everything:

    ```sql
    SELECT * FROM table_name WHERE column1 LIKE 'A_f%';
    ```

+ **Regex pattern matching**: the TILDE “~” operator brings us to the more complex pattern matching operator. It matches regular expressions in different ways: 

    | Operator      | Description |
    | ----------- | ----------- |
    | ~    | Matches regular expression, case sensitive    |
    | ~*   | Matches regular expression, case insensitive  |
    | !~   | Does not match regular expression, case sensitive   |
    | !~*  | Does not match regular expression, case insensitive  |
   
   
    ```sql
    SELECT * FROM table_name WHERE column1 ~ '^[A-Z][0-9].*$';
    ```
    
    You can use SQL regular expression pattern to extract a substring from a string instead of returning whole of it:

    ```sql
    SUBSTRING(string, matching_pattern);
    ```

    ```sql
    SELECT SUBSTRING (column1, '(.+@[^ ]+)') FROM table_name WHERE column1 ~ '^From';
    ```

    The PostgreSQL REGEXP_MATCHES() function matches a regular expression against a string and returns matched substrings.

    ```sql
    REGEXP_MATCHES(source_string, pattern [, flags])
    ```

    ```sql
    SELECT REGEXP_MATCHES(description, 'Cat | Dog') FROM film;
    ```

<br>

# JOIN

### Select column from different table:     
We use join to merge different tables for reaching something meaningful. In this section, we’ll show how to do that using different types of joins:

+ **INNER JOIN**: An inner join focuses on the commonality between two tables. When using an inner join, there must be at least some matching data between two (or more) tables that are being compared. An inner join searches tables for matching or overlapping data. Upon finding it, the inner join combines and returns the information into one new table. So you’ll use INNER JOIN when you want to return only records having pair on both sides. 

    ```sql
    SELECT column1, column2 FROM table_one INNER JOIN table_two 
    ON table_one.count=table_two.count;
    ```

    Note: if there is some column that have same name in two different tables, Then you can not Join like above code. Instead you should define alias or note the table name for columns. See this example: 

    ```sql
    SELECT table_one.column1, table_two.column2 FROM table_one
    INNER JOIN table_two on table_one.id=table_two.id;
    ```

    Note: if you want to return columns from different tables with the same title, it is best to set alias name for them to use as a temporary name in column header. See this example: 

    ```sql
    SELECT table_one.column1 AS name, table_two.column1 AS other_name FROM table_one
    INNER JOIN table_two on table_one.id=table_two.id;
    ```

    example of inner join between three different tables:

    ```sql
    SELECT table_one.first, table_one.last, table_two.start, table_two.end, table_three.outcome
    FROM table_one
    INNER JOIN table_two ON table_two.employee = table_one.id
    INNER JOIN table_three ON table_two.outcome = table_three.id
    ORDER BY table_two.start ASC;
    ```


+ **LEFT JOIN**: Writing queries that use LEFT JOINs doesn’t differ a lot when compared to writing queries using INNER JOINs. The result would, of course, be different (at least in cases when some records don’t have a pair in other tables).           
The result of LEFT JOIN shall be the same as the result of INNER JOIN + we’ll have rows, from the “left” table, without a pair in the “right” table.

    ```sql
    SELECT column1, column2 FROM table_name LEFT JOIN table_two 
    ON table_name.count=table_two.count;
    ```
+ **RIGHT JOIN**: The result of RIGHT JOIN shall be the same as the result of INNER JOIN + we’ll have rows, from the “right” table, without a pair in the “left” table. So it will return all from right but just put value if there is a pair in left

    ```sql
    SELECT column1, column2 FROM table_name RIGHT JOIN table_two 
    ON table_name.count=table_two.count;
    ```
+ **SELF JOIN**: A self join is a regular join, but the table is joined with itself (T1 and T2 are different table aliases for the same table):

    ```sql
    SELECT t1.column1, t2.column1
    FROM table_name t1,  table_name t2 
    WHERE t1.column1 > t2.column1;
    ```

<br>

# UNION

There are several options you can use to combine data from multiple data. One of those option is to decide whether to use Joins or Unions.  
In simple terms, joins combine data into new columns.  The query uses a “join condition” to match column together to form new rows. The new rows consist of column values from both tables. But **Unions** combine data into new rows.  Here the union takes the result as rows and appends them together row by row.


+ **Union**: join together the results of two queries and remove duplicated if use without **ALL** keyword  
    Note: union columns should be with same count and same datatype as well.

    ```sql
    SELECT column1 FROM table_one 
    UNION ALL
    SELECT column1 FROM table_two;
    ```

+ **Intersect**: return common result of two queries and remove duplicated if use without **ALL** keyword 

    ```sql
    SELECT column1 FROM table_one 
    INTERSECT ALL
    SELECT column1 FROM table_two;
    ```

+ **Except**: find the rows that are present in first query but not second query and remove duplicated if use without **ALL** keyword 

    ```sql
    SELECT column1 FROM table_one 
    EXCEPT ALL
    SELECT column1 FROM table_two;
    ```

# Constraint

#### Specify rules for data in a table:
Constraints are used to limit the type of data that can go into a table (specify rules for data). This ensures the accuracy and reliability of the data in the table. If there is any violation between the constraint and the data action, the action is aborted. They can be specified when the table is created with the **CREATE TABLE** statement, or after the table is created with the **ALTER TABLE** statement.              
Constraints can be column level or table level. Column level constraints apply to a column, and table level constraints apply to the whole table:

+ **Not null**: ensures that a column cannot have a NULL value

    ```sql
    CREATE TABLE table_one (
        column1 datatype NOT NULL,
    );
    ```

+ **Unique**: ensures that all values in a column are different

    ```sql
    CREATE TABLE table_one (
        column1 datatype UNIQUE,
    );
    ```

+ **Primary key**: a combination of a NOT NULL and UNIQUE. If we set a primary column datatype as **SERIAL** then it will fill automatically with inserting data to the table:

    ```sql
    CREATE TABLE table_one (
        column1 serial PRIMARY KEY,
    );
    ```

+ **Foreign key**: a foreign key column in a table points to a column with unique values in another table (often the primary key column) to create a way of cross-referencing the two tables. It is a constraint that links a column in one table (table_1.column_a) to a column in a different table (table_2.column_b) and ensures that a value can be added to column_a only if the same value already exists in column_b. Foreign key constraints are used to create relationships between tables.                 
The table with the foreign key is called the child table, and second table is called the referenced or parent table.           


    ```sql
    CREATE TABLE table_child (
        ...,
        column1 datatype,
        FOREIGN KEY (column1) REFERENCES parent_table (column1)
    );
    ```
    it is possible to directly define and assign a column as foreign key like this example:

    ```sql
    CREATE TABLE table_child (
        ...,
        column1 int REFERENCES parent_table (id)
    );
    ```


+ **Default**: sets a default value for a column if no value is specified

    ```sql
    CREATE TABLE table_one (
        column1 datatype DEFAULT 'jeff'
    );
    ```

+ **Check**: ensures that the values in a column satisfies a specific condition (we can not use sub-queries as condition here. just basic operators are acceptable)

    ```sql
    CREATE TABLE table_one (
        column1 INTEGER CHECK (column1 > 0)
    );
    ```
    it is possible to check between different columns:

    ```sql
    CREATE TABLE table_one (
        price INTEGER NOT NULL,
        discount INTEGER,
        CHECK (discount < price)
    );
    ```

<br>

# On delete

When you create a foreign key in your database, you can specify what happens upon delete of the parent row. There are usually four possibilities:

+ **ON DELETE SET NULL**: specifies that when a referenced row is deleted (in parent table), row(s) referencing (in child table) should be set to **null** automatically.
+ **ON DELETE CASCADE**: specifies that when a referenced row is deleted (in parent table), row(s) referencing (in child table) should be automatically deleted as well.   
+ **ON DELETE RESTRICT**: prevents deletion of a referenced row in the parent table. This means if we try to delete a row in the parent table that has been referenced in the child table we faced an error
+ **ON DELETE NO ACTION**: it's very similar to **ON DELETE RESTRICT** with a little differences. It means that if any referencing rows still exist when the constraint is checked, an error is raised; this is the default behavior if you do not specify anything

Note: In Some Databases there is no RESTRICT keyword. For example the only option in MySQL is NO ACTION and there is no difference between ON DELETE RESTRICT and ON DELETE NO ACTION.


```sql
CREATE TABLE parent (
    id SERIAL PRIMARY KEY,
    name varchar(20),
    ...
);
```

```sql
CREATE TABLE child (
    id SERIAL PRIMARY KEY,
    parent_id int REFERENCES parent(id) ON DELETE RESTRICT,
    ...
);
```

<br>

# Sub query
A subquery or Inner query or Nested query is a query within another PostgreSQL query and embedded within the WHERE clause. It is used to return data that will be used in the main query as a condition to further restrict the data to be retrieved.     
Subqueries can be used with the SELECT, INSERT, UPDATE and DELETE statements along with the operators like =, <, >, >=, <=, IN, etc.
Remember there are a few rules that subqueries must follow:

+ Subqueries must be enclosed within parentheses.
+ A subquery can have only one column in the SELECT clause, unless multiple columns are in the main query for the subquery to compare its selected columns.
+ An ORDER BY cannot be used in a subquery, although the main query can use an ORDER BY. The GROUP BY can be used to perform the same function as the ORDER BY in a subquery.
+ Subqueries that return more than one row can only be used with multiple value operators, such as the IN, EXISTS, NOT IN, ANY/SOME, ALL operator.
+ The BETWEEN operator cannot be used with a subquery; however, the BETWEEN can be used within the subquery.    

**Note**: sub-queries can be use in different locations and it can be a little confusing for beginners. 

+ Sub-query in WHERE: this sub queries are most frequently used against another kind of sub-queries:

    ```sql
    SELECT first_name, last_name FROM customers
    WHERE salary > (
            SELECT max(salary) FROM employees
    );
    ```

+ Sub-query inside of FROM: suppose a scenario that we want to find maximum average of price for different manufactures. As you know the aggregate functions can not be used in nested form. So we can not resolve this problem with them. In like the situation the solution is sub-queries:

    ```sql
    SELECT MAX(p.avg_price) AS max_average_price 
    FROM (
            SELECT AVG(price) AS avg_price 
            FROM phones 
            GROUP BY manufactures
    ) AS p;
    ```

+ Sub-query in SELECT:

    ```sql
    SELECT name, price, price / (SELECT MAX(price) FROM phones) AS price_ratio FROM phones;
    ```    

<br>

# Common Table Expressions

+ **Simple CTE:** In PostgreSQL, the WITH query provides a way to write auxiliary statements for use in a larger query. It helps in breaking down complicated and large queries into simpler forms, which are easily readable. These statements often referred to as Common Table Expressions or CTEs, can be thought of as defining temporary tables that exist just for one query.

    The WITH query being CTE query, is particularly useful when subquery is executed multiple times. It is equally helpful in place of temporary tables. It computes the aggregation once and allows us to reference it by its name (may be multiple times) in the queries. Example:
    ```sql
    WITH CTE AS (SELECT ID, NAME, AGE, ADDRESS FROM COMPANY)
    Select * From CTE;
    ```

    **Note**: The WITH clause must be defined before it is used in the query.

<br>

# INDEX 

Indexes are a common way to enhance database performance.  An index allows the database server to find and retrieve specific rows much faster than it could do without an index. But indexes also add overhead to the database system as a whole, so they should be used sensibly. 

Indexing will add some data to the original database and increase the size of data which can lead to extra costs for data storage. The INSERT and UPDATE statements take more time on tables having indexes, whereas the SELECT statements become fast on those tables. The reason is that while doing INSERT or UPDATE, a database needs to insert or update the index values as well. So for situations that works too many with UPDATE and INSERT the indexing may have more downside against benefits.

The main structure for defining index is:

+ **[USING method]**: btree, hash, gist, spgist, gin, or brin. PostgreSQL uses btree by default.     
+ **[ASC | DESC]** : specify the sort order. ASC is the default. 
+ **[NULLS {FIRST | LAST }]**: specifies nulls sort before or after non-nulls. The NULLS FIRST is the default when DESC is specified and NULLS LAST is the default when DESC is not specified.

    ```sql
    CREATE INDEX index_name ON table_name [USING method]
    (
        column_name [ASC | DESC] [NULLS {FIRST | LAST }],
        ...
    );
    ```


+ Example:

    + **Basic B-tree index**:
        ```sql
        CREATE INDEX index_name ON table_name (column_name);
        ```

    + **Hash index**:
        In some cases, such as when indexing a column with TEXT datatype and full of data, we can index the hash of the data instead of the actual data to speed up the process. For accomplish this goal can define separate column that store the hash of data_column and index it or do it directly:

        ```sql
        CREATE INDEX index_name ON table_name USING hash (column_name);
        ```

    + **Unique indexes**: are used not only for performance, but also for data integrity. A unique index does not allow any duplicate values to be inserted into the table:

        ```sql
        CREATE UNIQUE INDEX index_name ON table_name (column1_name);
        ```

    + **Multi-column Indexes**: defined on more than one column of a table

        ```sql
        CREATE UNIQUE INDEX index_name
        ON table_name (column1_name, column2_name);
        ```
 
    + **Partial Indexes**: A partial index is an index built over a subset of a table; the subset is defined by a conditional expression (called the predicate of the partial index). The index contains entries only for those table rows that satisfy the predicate. example with gist method: 

        ```sql
        CREATE INDEX index_name
        on table_name USING gist (conditional_expression);
        ```

    + **DROP INDEX**:          
        ```sql
        DROP INDEX index_name;
        ```


    **Note**: In Postgres after setting the index, it will update automatically after inserting new data into the table.   

    **Note**: With creating a column as primary-key or unique, Postgres automatically will create an index for these column. So you should never create a manual index for this kind of column because the index exists.

<br>

# View And Stored Procedure

Views and stored procedures can both simplify and optimize the database design and performance. Through these tools, you can encapsulate complex queries and logic in reusable and maintainable code, reduce the amount of data transferred between the database and the application, implement security and access control, enhance the consistency and accuracy of the data by enforcing business rules and validations, and modify the logic and structure of the views and stored procedures without affecting dependent objects or applications.

When should I use a view and when should I use a stored procedure?  
Most simply, a view is used when only a SELECT statement is needed. Views should be used to store commonly-used JOIN queries and specific columns to build virtual tables of an exact set of data we want to see. Stored procedures hold the more complex logic, such as INSERT, DELETE, and UPDATE statements to automate large SQL workflows. Stored Procedure also accept parameters but views Does NOT.


+ **View:**     

    Views are virtual tables that return the result of a query every time they are accessed. When you create a view, you basically create a query and assign a name to the query:

    ```sql
    -- standard view definition:
    CREATE OR REPLACE VIEW my_view AS
    SELECT name, rating
    FROM authors
    WHERE rating > 6;

    -- call a view:
    SELECT name, rating from my_view;

    -- drop a view:
    DROP VIEW IF EXISTS my_view;
    ```

    **Note**: Views do not store any data except the materialized views. In PostgreSQL, you can create special views called materialized views that store data physically and periodically refresh data from the base tables. The materialized views are handy in many scenarios, such as faster data access to a remote server and caching. So materialized views are used if data from complex queries needs to be accessed quickly.

    Materialized views don't always have the most recent data. Since the result of a query is stored in a materialized view like in a cache, you need to make sure to refresh it periodically.

    In conclusion Regular-View execute in calling time so they always return recent data, But Materialized-View act as caching mechanism so they are faster than Regular-View but they need to be refresh periodically:
   
    ```sql
    -- materialized view definition:
    CREATE MATERIALIZED VIEW my_view AS
    SELECT name, rating
    FROM authors
    WHERE rating > 6;

    -- call a view:
    SELECT name, rating from my_view;

    -- refresh a materialize view
    REFRESH MATERIALIZED VIEW popular_active_authors;
    ```
    **Note**: Materialized views can be refreshed when their underlying source data changes using Postgres triggers.   


<br>

+ **Stored Procedure:**     

    A stored procedure is a set of SQL statements that perform a specific task. You can use stored procedures to encapsulate business logic, enforce security rules, or automate repetitive operations. Stored procedures are stored in the database, and are executed by the database engine. This means that stored procedures can be faster and more efficient than views, but also that they may become outdated or inconsistent with the data.

    Example of a procedure that give 2 variable and run a query in transactional way:

    ```SQL
    -- procedure definition with PLPGSQL language
    CREATE OR REPLACE PROCEDURE my_procedure(
        due_id int,
        name_value varchar
    ) 
    AS
    $body$
    DECLARE
    BEGIN
        UPDATE users
        SET username = name_value
        WHERE id = due_id;
        COMMIT;
    END;
    $body$
    LANGUAGE PLPGSQL;

    -- calling a procedure
    CALL my_procedure(678, 'jeff')
    ```

<br>

# Function
Postgres functions are similar to views but allow more procedural computations. Function can take arguments and return value, things we have not in View. Notice that Functions can not run transactional queries but Procedures can do it:

```sql
-- define a FUNCTION with SQL language that return a int value
CREATE OR REPLACE FUNCTION my_function() 
RETURNS int 
as
$body$
    SELECT COUNT(*) 
    FROM my_table; 
$body$
LANGUAGE SQL

-- define a FUNCTION with SQL language that accept parameters and return a table
CREATE OR REPLACE FUNCTION my_function(loc varchar) 
RETURNS SETOF my_table 
as
$body$
	SELECT *
	FROM my_table
    WHERE state = loc 
	ORDER BY username DESC;
$body$
LANGUAGE SQL

-- call a function
SELECT * FROM my_function();
```

<br>

# Trigger
Postgres triggers are used to invoke previously defined Postgres functions before or after a specific database event (e.g. INSERT) occurs.

+ **Syntax**:
    ```sql
    CREATE TRIGGER trigger_name [BEFORE|AFTER|INSTEAD OF] event_name
    ON table_name
    [
    -- Trigger logic goes here....
    ];
    ```

+ Example: we have a function that updates the value of the column named updated_at and want to trigger it every time the table_name is updated. Using this method, we can auto-fill our custom column in the selected table:

    ```sql
    CREATE OR REPLACE FUNCTION trigger_set_timestamp()
    RETURNS TRIGGER AS $$
    BEGIN
        NEW.updated_at = NOW();
        RETURN NEW;
    END;
    $$ LANGUAGE plpgsql;
    ```

    ```sql
    CREATE TRIGGER set_timestamp BEFORE UPDATE 
    ON table_name
    FOR EACH ROW
    EXECUTE PROCEDURE trigger_set_timestamp();
    ```

<br>

# Transferring Data


+ **Transfer data from file to the table**:  
Below see how we can copy data from CSV file to the table in postgres. If the file has not header, remove HEADER from the WITH STATEMENT:

    ```sql
    \copy target_table (column1, column2) FROM 'source.csv'   
                WITH (FORMAT csv, DELIMITER ',', HEADER);
    ```

    Note: The file should be in destination that postgres has permission to access it, for example:
    ```bash
    sudo mv source.csv /var/lib/postgres/
    ```


+ **Transferring Data Between tables**:     
    It is possible to transfer data between tables. With insert, a new row will be added to the table, and with update, present row values will be changed in selected columns:

    ```sql
    INSERT INTO target_table SELECT * FROM source_table WHERE condition;
    ```
    ```sql
    UPDATE target_table SET target_column = 
    (SELECT source_column FROM source_table WHERE condition);
    ```

    As an example here we added data from 'artists' table to the artist column in 'album' table. Then we updated the track column based on track table with defined condition:

    ```sql
    INSERT INTO album (artist) SELECT name FROM artist;
    UPDATE album SET track = (SELECT track.name FROM track 
    WHERE album.artist = track.artist);
    ```


<br>

# Vaccum    

In normal PostgreSQL operation, tuples that are deleted or obsoleted by an update are not physically removed from their table; they remain present until a VACUUM is done. Therefore it's necessary to do VACUUM periodically, especially on frequently-updated tables:

1. **Plain VACUUM**: simply reclaims space and makes it available for re-use. This form of the command can operate in parallel with normal reading and writing of the table, as an exclusive lock is not obtained. However, extra space is not returned to the operating system (in most cases); it's just kept available for re-use within the same table. It also allows us to leverage multiple CPUs in order to process indexes. This feature is known as parallel vacuum.

1. **VACUUM FULL**: rewrites the entire contents of the table into a new disk file with no extra space, allowing unused space to be returned to the operating system. This form is much slower and requires an ACCESS EXCLUSIVE lock on each table while it is being processed.
          
Example:  

```sql
# check database usage size before operation
SELECT pg_size_pretty(pg_database_size('database_name')) AS database_size;

# plain vaccum on custom table
VACUUM (VERBOSE, ANALYZE) table_name;

# plain vaccum on database
VACUUM (VERBOSE, ANALYZE);

# full vaccum on database
VACUUM (VERBOSE, ANALYZE, FULL);
```
    
    
<br>

# Other

+ **Text generation**:           
here is an example of creating a table with a single column and filling it with random data:

    ```sql
    CREATE TABLE bigtext (content TEXT);
    INSERT INTO bigtext SELECT concat('content', generate_series(100000, 199999)); 
    ```



+ **Concurrency and Transaction**:    
    Lock a special section in the table to prevent conflict when working online and different results because of concurrency issues, and unlock it once the work is finished:

    ```sql
    BEGIN;
    UPDATE accounts SET balance = balance - 100.00
        WHERE name = 'Alice';
    -- etc
    COMMIT;
    ```
    
    Double phase locking (Exclusive lock + Shared lock):

    ```sql
    BEGIN;
    SELECT * FROM account WHERE name = 'Alice' FOR UPDATE;
    -- etc
    UPDATE accounts SET balance = balance - 100.00
        WHERE name = 'Alice';
    COMMIT;
    ```


+ **Datatypes**: https://www.w3schools.com/sql/sql_datatypes.asp
