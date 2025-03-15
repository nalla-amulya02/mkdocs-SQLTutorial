#MySQL Basics

SQL (Structured Query Language) is the foundation of relational databases. It allows you to **store, retrieve, update, and manage data** efficiently. This section covers essential SQL commands, including **SELECT, INSERT, UPDATE, DELETE**, and more. Understanding these basics will help you interact with MySQL databases and build powerful data-driven applications.  

## Section 1. Querying data
### SELECT
`SELECT` statement doesnt always need a FROM clause. That means we can use the SELECT statement without referencing any table.

This can perform certain simple arithmetic operations or use any inbuild functons like string functions, date funcctions and math functions.

```sql
SELECT 1+1;
SELECT NOW();
SELECT CONCAT('Hello','World');
```

**Column alias:** 

A column alias in SQL is a temporary name given to a column in the result set. It makes output more readable, especially when using calculations or complex queries. Aliases are defined using the `AS` keyword. Here `AS` keyword is optional.

```sql
-- Renaming a single value
SELECT 100 AS Price;
        or
SELECT 100 Price;
```

### SELECT FROM
The `SELECT FROM` statement in MySQL is used to **retrieve data** from one or more tables. It allows us to fetch specific columns or all columns from a database table.
```SQL
SELECT select_list
FROM table_name;
```
Explanation:  
    •`SELECT` → Specifies the columns to retrieve.  
    •`select_list` → The list of columns or * (to select all columns).  
    •`FROM` → Indicates the table from which data should be fetched.  
    •`table_name` → The name of the table.  

**NOTE:** SQL is case-insensitive, meaning `select`, `SELECT`, and `SeLeCt` work the same way.
However, the convention is to write SQL keywords in uppercase (e.g., `SELECT`, `FROM`, `WHERE`) for better readability.
Table and column names are usually written in lowercase (unless the database is case-sensitive).  
When executing a `SELECT` statement, **MySQL evaluates the `FROM` clause before the `SELECT` clause**.   

```SQL
SELECT first_name fname FROM name;                  --selecting single col from a table
SELECT first_name fname, last_name lname FROM name; --selecting multiple col from a table
SELECT * FROM names;                                --selecting all col from a table
```  
Using `SELECT *` is fine for exploring data quickly, but in production, always select only the required columns for efficient and optimized queries.

<!-- --------------------------------------------------------------------------------------------------------------------------------- -->
## Section 2. Filtering data
### WHERE
Filters rows based on specified conditions in a query.  
```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```
 Explanation:  
• `SELECT column1, column2, ...` → Specifies the columns to retrieve.  
• `FROM table_name` → Specifies the table from which to fetch data.  
• `WHERE condition` → Filters the rows based on a given condition. 

The condition can be a combination of expressions using muliple logical operators(AND,OR,NOT etc) or comparator operators like (=,<>/!=,<,>,<=,>=). 
 MySQL evaluates `WHERE` **after** `FROM` and **before** `SELECT` and `ORDER BY`.
```sql
SELECT first_name, last_name  
FROM employees  
WHERE department = 'IT' AND city = 'Boston'
```
### SELECT DISTINCT
The `DISTINCT` clause removes **duplicate rows** from a result set in a `SELECT` statement. If one column is specified in SELECT, duplicates are removed based on that column.
If multiple columns are specified in SELECT, uniqueness is determined by their combination.  
MySQL evaluates the DISTINCT clause after the FROM, WHERE, and SELECT clause and before the ORDER BY clause.
```sql
SELECT DISTINCT column1, column2, ...
FROM table_name
WHERE condition
```

The DISTINCT clause treats all NULL values as the same and keeps only one NULL for multiple NULLs in the selected col.
### AND, OR
`AND` - Combines multiple conditions where **all** must be true.  
The logical AND operator returns 1 if both A and B are non-zero and not NULL. It returns 0 if either operand is zero; otherwise, it returns NULL. i.e 1 AND (1 is 1), (1 AND 0) or (0 AND 1) or (0 AND 0) or (0 AND NULL) is 0, (1 AND NULL) or (NULL AND NULL) is NULL.  

|        | TRUE  | FALSE | NULL  |
|--------|-------|-------|-------|
| **TRUE**  | TRUE  | FALSE | NULL  |
| **FALSE** | FALSE | FALSE | FALSE |
| **NULL**  | NULL  | FALSE | NULL  |

When evaluating an expression with the **AND** operator, MySQL short-circuits the evaluation and stops processing further once the final result is determined.
```SQL
SELECT * FROM table_name  
WHERE condition1 AND condition2;
```
`OR` - Combines multiple conditions where **at least one** must be true.  
If both A and B are not NULL, the OR operator returns 1 (true) if either A or B is non-zero.

|        | TRUE  | FALSE | NULL  |
|--------|-------|-------|-------|
| **TRUE**  | TRUE  | TRUE | TRUE  |
| **FALSE** | TRUE | FALSE | NULL |
| **NULL**  | TRUE  | NULL | NULL  |

```SQL
SELECT * FROM table_name  
WHERE condition1 OR condition2;
```
### IN,  NOT IN
`IN` - Checks if a value exists within a specified set of values.Returns 1 is exists and 0 if don't.
```SQL
SELECT col1,col2..
FROM table_name
WHERE value_v IN (value1,value2,..)
```
It returns NULL is 2 cases -when the value on the left side of the operator is NULL and when the value doesn’t equal any value in the list and one of the values in the list is NULL.
```SQL
--returns NULL
SELECT NULL IN (1,2,3);
SELECT 3 IN (1,2,NULL);
```
`NOT IN` - Ensures a value **does not** exist in a specified set.

### BETWEEN
Filters data within a specified range (inclusive)
```SQL
SELECT column_name  
FROM table_name  
WHERE column_name BETWEEN value1 AND value2;
```
To check if a value is between a date range, you should explicitly cast the value to the DATE type using CAST().
Similarly, negate to `NOT BETWEEN'.
### LIKE
Performs pattern matching using **wildcards** (`%` and `_`).The percentage ( % ) wildcard matches any string of zero or more characters.
The underscore ( _ ) wildcard matches any single character.  
When a pattern includes a wildcard character (% or _) and you want to treat it as a regular character, use the ESCAPE clause or use '\' before escape character in pattern.
```SQL
SELECT column_name  
FROM table_name  
WHERE column_name LIKE 'pattern' ESCAPE 'escape_character';     --pattern can be '%s%'
```
Use the NOT operator to negate the LIKE operator.
### LIMIT
Restricts the number of rows returned by a query. 
```SQL
SELECT column_name(s)  
FROM table_name  
LIMIT [offset,] row_count;
```
Explanation:  
• LIMIT row_count → Retrieves a maximum of row_count rows.  
• LIMIT offset, row_count → Skips offset rows and then retrieves row_count rows.  

1) When using the `LIMIT` clause without `ORDER BY`, the rows retrieved are not guaranteed to be in a specific order because databases store and retrieve data in an unpredictable manner. `LIMIT` is executed after the select clause.  
2) Using LIMIT works well unless there are duplicate values in the column you are ordering by. If multiple rows have the same value, LIMIT may return incomplete results. Hence, use DENSE_RANK() window function.  
3) Using LIMIT with DISTINCT can speed up queries by reducing the number of rows scanned as MySQL stops searching once the required number of distinct values is found, making it more efficient.
### IS NULL
Checks if a value is **NULL** or not.

```SQL
SELECT 1 IS NOT NULL, -- 1
       0 IS NOT NULL, -- 1
       NULL IS NOT NULL; -- 0
```
**NOTE**: MySQL doesn't have a built-in boolen datatype. It uses TINYINT(). That means TRUE is 1 and FALSE is 0.