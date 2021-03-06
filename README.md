# Lion-SQL
Package responsible for generating and executing MySQL processes.

[![Latest Stable Version](http://poser.pugx.org/lion-framework/lion-sql/v)](https://packagist.org/packages/lion-framework/lion-sql) [![Total Downloads](http://poser.pugx.org/lion-framework/lion-sql/downloads)](https://packagist.org/packages/lion-framework/lion-sql) [![License](http://poser.pugx.org/lion-framework/lion-sql/license)](https://packagist.org/packages/lion-framework/lion-sql) [![PHP Version Require](http://poser.pugx.org/lion-framework/lion-sql/require/php)](https://packagist.org/packages/lion-framework/lion-sql)

## Install
```shell
composer require lion-framework/lion-sql
```

## Table of content.
| # | Description | # | Description |
| :---: | :---: | :---: | :---: |
| 1 | [CONNECTION](https://github.com/Sleon4/Lion-SQL/#1-connection) | 15 | [COUNT](https://github.com/Sleon4/Lion-SQL/#15-count) |
| 2 | [INSERT](https://github.com/Sleon4/Lion-SQL/#2-insert) | 16 | [AVG](https://github.com/Sleon4/Lion-SQL/#16-avg) |
| 3 | [SELECT](https://github.com/Sleon4/Lion-SQL/#3-select) | 17 | [SUM](https://github.com/Sleon4/Lion-SQL/#17-sum) |
| 4 | [WHERE](https://github.com/Sleon4/Lion-SQL/#4-where) | 18 | [ORDER BY](https://github.com/Sleon4/Lion-SQL/#18-order-by) |
| 5 | [AND](https://github.com/Sleon4/Lion-SQL/#5-and) | 19 | [GROUP BY](https://github.com/Sleon4/Lion-SQL/#19-group-by) |
| 6 | [OR](https://github.com/Sleon4/Lion-SQL/#6-or) | 20 | [LIMIT](https://github.com/Sleon4/Lion-SQL/#20-limit) |
| 7 | [BETWEEN](https://github.com/Sleon4/Lion-SQL/#7-between) | 21 | [HAVING](https://github.com/Sleon4/Lion-SQL/#21-having) |
| 8 | [LIKE](https://github.com/Sleon4/Lion-SQL/#8-like) |
| 9 | [JOIN](https://github.com/Sleon4/Lion-SQL/#9-join) |
| 10 | [UPDATE](https://github.com/Sleon4/Lion-SQL/#10-update) |
| 11 | [DELETE](https://github.com/Sleon4/Lion-SQL/#11-delete) |
| 12 | [CALL](https://github.com/Sleon4/Lion-SQL/#12-call) |
| 13 | [MIN](https://github.com/Sleon4/Lion-SQL/#13-min) |
| 14 | [MAX](https://github.com/Sleon4/Lion-SQL/#14-max) |

## Usage
### 1. CONNECTION
The connection is established using an array containing data about your configuration to connect, currently only available with MySQL.
```php
require_once("vendor/autoload.php");

use LionSQL\Drivers\MySQLDriver as Builder;

Builder::init([
    'host' => 'localhost',
    'port' => 3306,
    'db_name' => 'example',
    'charset' => 'utf8',
    'user' => 'root',
    'password' => ''
]);
```

The configuration it handles is an array of elements with parameters set by default.
```php
[
    PDO::ATTR_EMULATE_PREPARES => false,
    PDO::MYSQL_ATTR_INIT_COMMAND => "SET NAMES 'utf8'",
    PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
    PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_OBJ,
    PDO::ATTR_TIMEOUT => 5
]
```

The configuration can be overridden and parameterized as required.
```php
Builder::init([
    'host' => 'localhost',
    'port' => 3306,
    'db_name' => 'example',
    'charset' => 'utf8',
    'user' => 'root',
    'password' => '',
    'config' => [
        PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
        PDO::ATTR_TIMEOUT => 5
    ]
]);
```

### 2. INSERT
The first parameter is defined by the **name of the table** to insert data, The second parameter is set by the **number of columns** separated by `(,)` without spaces, The third parameter is an **array that contains the arrays of data** to insert.
```php
$list = Builder::insert('table', 'column,column,column', [
    1, 'example_name', '1999-09-30'
]);

var_dump($list);
```

### 3. SELECT
The first parameter to define the type of method to use `(fetch, fetchAll)`, The second parameter is defined for the name of the table, The third parameter is defined by the alias `(AS)`, The fourth parameter is defined by the `columns` you want to bring.
```sql
/* prepared sentence PHP */
/* alias */
SELECT alias.column, alias.column, alias.column, alias.column FROM table AS alias

/* prepared sentence PHP */
/* no aliases */
SELECT column, column, column, column FROM table

/* prepared sentence PHP */
/* all data */
SELECT * FROM table
```
equivalent to
```php
// alias
$list = Builder::select(Builder::FETCH_ALL, 'table', 'alias', 'alias.column,alias.column,alias.column,alias.column');
var_dump($list);

// no aliases
$list = Builder::select(Builder::FETCH_ALL, 'table', null, 'table.column,table.column,column,column');
var_dump($list);

// all data
$list = Builder::select(Builder::FETCH_ALL, 'table');
var_dump($list);
```

### 3.1 FIND COLUMN
findColumn is a function prepared to search for a specified column based on specified parameters. Note that the number of columns entered for the condition must match the number of values submitted. <br>
Example #1.
```sql
/* prepared sentence PHP */
SELECT id FROM table WHERE column=?
```
```php
$list = Builder::findColumn('table', 'id', 'column', [$value]);
var_dump($list);
```

Example #2.
```sql
/* prepared sentence PHP */
SELECT id FROM table WHERE column=? AND column=?
```
```php
$list = Builder::findColumn('table', 'id', 'column,column', [
    $value, $value2
]);

var_dump($list);
```

Example #3.
```sql
/* prepared sentence PHP */
SELECT id FROM table WHERE column=? AND column=? AND column=?
```
```php
$list = Builder::findColumn('table', 'id', 'column,column,column', [
    $value, $value2, $value3
]);

var_dump($list);
```

### 4. WHERE
The fifth parameter of `(SELECT)` is defined by an array containing the data to be prepared in the query.
The use of `(where)` is valid for the `(fetch)` function. <br>
Example #1.
```sql
/* prepared sentence PHP */
SELECT alias.column, alias.columns FROM table AS alias WHERE alias.id=?
```
```php
// alias
$list = Builder::select(Builder::FETCH, 'table', 'alias', 'alias.column,alias.columns', [
    Builder::where('alias.id', '=' /* '>', '<', '<>' */)
], [$id]);

var_dump($list);
```

Example #2.
```sql
/* prepared sentence PHP */
SELECT column, columns FROM table WHERE id=?
```
```php
// no aliases
$list = Builder::select(Builder::FETCH, 'table', null, 'column,columns', [
    Builder::where('id', '=' /* '>', '<', '<>' */)
], [$id]);

var_dump($list);
```

Example #3.
```sql
/* prepared sentence PHP */
SELECT * FROM table WHERE id=?
```
```php
// all data
$list = Builder::select(Builder::FETCH, 'table', null, '*', [
    Builder::where('id', '=' /* '>', '<', '<>' */)
], [$id]);

var_dump($list);
```

### 5. AND
Example #1
```sql
/* prepared sentence PHP */
SELECT * FROM table WHERE id=? AND date=?
```
```php
// all data
$list = Builder::select(Builder::FETCH, 'table', null, '*', [
    Builder::where('id', '=' /* '>', '<', '<>' */),
    Builder::and('date', '=' /* '>', '<', '<>' */)
], [$id, $date]);

var_dump($list);
```

Example #2
```sql
/* prepared sentence PHP */
SELECT * FROM table AS alias WHERE alias.id=? AND alias.date=?
```
```php
// all data
$list = Builder::select(Builder::FETCH, 'table', 'alias', '*', [
    Builder::where('alias.id', '=' /* '>', '<', '<>' */),
    Builder::and('alias.date', '=' /* '>', '<', '<>' */)
], [$id, $date]);

var_dump($list);
```

### 6. OR
Example #1
```sql
/* prepared sentence PHP */
SELECT * FROM table WHERE id=? OR date=?
```
```php
// all data
$list = Builder::select(Builder::FETCH, 'table', null, '*', [
    Builder::where('id', '=' /* '>', '<', '<>' */),
    Builder::or('date', '=' /* '>', '<', '<>' */)
], [$id, $date]);

var_dump($list);
```

Example #2
```sql
/* prepared sentence PHP */
SELECT * FROM table AS alias WHERE alias.id=? OR alias.date=?
```
```php
// all data
$list = Builder::select(Builder::FETCH, 'table', 'alias', null, [
    Builder::where('alias.id', '=' /* '>', '<', '<>' */),
    Builder::or('alias.date', '=' /* '>', '<', '<>' */)
], [$id, $date]);

var_dump($list);
```

### 7. BETWEEN
Example #1
```sql
/* prepared sentence PHP */
SELECT * FROM table WHERE date BETWEEN ? AND ?
```
```php
// all data
$list = Builder::select(Builder::FETCH, 'table', null, null, [
    Builder::between('date')
], [$date1, $date2]);

var_dump($list);
```

Example #2
```sql
/* prepared sentence PHP */
SELECT * FROM table AS alias WHERE alias.date BETWEEN ? AND ?
```
```php
// all data
$list = Builder::select(Builder::FETCH, 'table', 'alias', null, [
    Builder::between('alias.date')
], [$date1, $date2]);

var_dump($list);
```

### 8. LIKE
```php
$list = Builder::select(Builder::FETCH_ALL, 'table', null, 'column,column', [
    Builder::where('column'),
    Builder::like()
], ['%example%']);

var_dump($list);
```

### 9. JOIN
Implementation of join `(INNER, LEFT AND RIGHT)`. <br>
Example #1
```sql
/* prepared sentence PHP */
SELECT alias.name, alias1.name, alias2.name, alias3.name FROM table1 AS alias
  INNER JOIN table2 AS alias1 ON alias.id_a=alias1.id_a
  LEFT JOIN table3 AS alias2 ON alias.id_b=alias2.id_b
  RIGHT JOIN table4 AS alias3 ON alias.id_c=alias3.id_c
```
```php
$list = Builder::select(Builder::FETCH_ALL, 'table1', 'alias', 'alias.name,alias1.name,alias2.name,alias3.name', [
    Builder::innerJoin('table2', 'alias1', "alias.id_a=alias1.id_a"),
    Builder::leftJoin('table3', 'alias2', "alias.id_b=alias2.id_b"),
    Builder::rightJoin('table4', 'alias3', "alias.id_c=alias3.id_c")
]);

var_dump($list);
```

Example #2
```sql
/* prepared sentence PHP */
SELECT alias.name, alias1.name, alias2.name, alias3.name FROM table1 AS alias
  INNER JOIN table2 AS alias1 ON alias.id_a=alias1.id_a
  LEFT JOIN table3 AS alias2 ON alias.id_b=alias2.id_b
  RIGHT JOIN table4 AS alias3 ON alias.id_c=alias3.id_c
WHERE alias.id=?
```
```php
$list = Builder::select(Builder::FETCH, 'table1', 'alias', 'alias.name,alias1.name,alias2.name,alias3.name', [
    Builder::innerJoin('table2', 'alias1', "alias.id_a=alias1.id_a"),
    Builder::leftJoin('table3', 'alias2', "alias.id_b=alias2.id_b"),
    Builder::rightJoin('table4', 'alias3', "alias.id_c=alias3.id_c"),
    Builder::where('alias.id', '=')
], [$id]);

var_dump($list);
```

Example #3
```sql
/* prepared sentence PHP */
SELECT alias.name, alias1.name, alias2.name, alias3.name FROM table1 AS alias
  INNER JOIN table2 AS alias1 ON alias.id_a=alias1.id_a
  LEFT JOIN table3 AS alias2 ON alias.id_b=alias2.id_b
  RIGHT JOIN table4 AS alias3 ON alias.id_c=alias3.id_c
WHERE alias.id
BETWEEN ? AND ?
```
```php
$list = Builder::select(Builder::FETCH, 'table1', 'alias', 'alias.name,alias1.name,alias2.name,alias3.name', [
    Builder::innerJoin('table2', 'alias1', "alias.id_a=alias1.id_a"),
    Builder::leftJoin('table3', 'alias2', "alias.id_b=alias2.id_b"),
    Builder::join('table4', 'alias3', "alias.id_c=alias3.id_c"),
    Builder::rightJoin('alias.date'),
    Builder::between()
], [$date1, $date2]);

var_dump($list);
```

### 10. UPDATE
Update queries take the name of the table as their first parameter. The second parameter carries the columns separated by `(,)`, the condition parameter is separated by `(:)` at the end of the columns. The third parameter receives an array with the parameters to update.
```sql
/* prepared sentence PHP */
UPDATE table SET name=?, date=?, phone=? WHERE id=?
```
```php
$list = Builder::update('table', 'name,date,phone:id', [
    $name, $date, $phone, $id
]);

var_dump($list);
```

### 11. DELETE
The first parameter receives the name of the table, The second parameter receives the name of the column that is referenced, The third parameter receives an array with the respective value to eliminate.

```sql
/* prepared sentence PHP */
DELETE FROM table WHERE id=?
```
```php
$list = Builder::delete('table', 'id', $id);
var_dump($list);
```

### 12. CALL
Stored procedures have their name as their first parameter. The second parameter has an array with the number of elements required.
```sql
/* prepared sentence PHP */
CALL name_procedure(?,?,?)
```
```php
$list = Builder::call('name_procedure', [
    $name, $date, $id
]);

var_dump($list);
```

### 13. MIN
The first parameter references the name of the column. The second parameter is `optional` and is used to give an alias. <br>
Example #1
```sql
/* prepared sentence PHP */
SELECT MIN(id) FROM table
```
```php
$list = Builder::select(Builder::FETCH, 'table', null, Builder::min('id'));
var_dump($list);
```

Example #2
```sql
/* prepared sentence PHP */
SELECT MIN(id) AS alias FROM table
```
```php
$list = Builder::select(Builder::FETCH, 'table', null, Builder::min('id', 'alias'));
var_dump($list);
```
### 14. MAX
The first parameter references the name of the column. The second parameter is `optional` and is used to give an alias. <br>
Example #1
```sql
/* prepared sentence PHP */
SELECT MAX(id) FROM table
```
```php
$list = Builder::select(Builder::FETCH, 'table', null, Builder::max('id'));
var_dump($list);
```

Example #2
```sql
/* prepared sentence PHP */
SELECT MAX(id) AS alias FROM table
```
```php
$list = Builder::select(Builder::FETCH, 'table', null, Builder::max('id', 'alias'));
var_dump($list);
```

### 15. COUNT
The first parameter references the name of the column. The second parameter is `optional` and is used to give an alias. <br>
Example #1
```sql
/* prepared sentence PHP */
SELECT COUNT(*) FROM table
```
```php
$list = Builder::select(Builder::FETCH, 'table', null, Builder::count());
var_dump($list);
```

Example #2
```sql
/* prepared sentence PHP */
SELECT COUNT(*) AS alias FROM table
```
```php
$list = Builder::select(Builder::FETCH, 'table', null, Builder::count(null, 'alias'));
var_dump($list);
```

Example #3
```sql
/* prepared sentence PHP */
SELECT COUNT(column) AS alias FROM table
```
```php
$list = Builder::select(Builder::FETCH, 'table', null, Builder::count('column', 'alias'));
var_dump($list);
```

### 16. AVG
The first parameter references the name of the column. The second parameter is `optional` and is used to give an alias. <br>
Example #1
```sql
/* prepared sentence PHP */
SELECT AVG(column) FROM table
```
```php
$list = Builder::select(Builder::FETCH, 'table', null, Builder::avg('column'));
var_dump($list);
```

Example #2
```sql
/* prepared sentence PHP */
SELECT AVG(column) AS alias FROM table
```
```php
$list = Builder::select(Builder::FETCH, 'table', null, Builder::avg('column', 'alias'));
var_dump($list);
```

### 17. SUM
The first parameter references the name of the column. The second parameter is `optional` and is used to give an alias. <br>
Example #1
```sql
/* prepared sentence PHP */
SELECT SUM(column) FROM table
```
```php
$list = Builder::select(Builder::FETCH, 'table', null, Builder::sum('column'));
var_dump($list);
```

Example #2
```sql
/* prepared sentence PHP */
SELECT SUM(column) AS alias FROM table
```
```php
$list = Builder::select(Builder::FETCH, 'table', null, Builder::sum('column', 'alias'));
var_dump($list);
```

### 18. ORDER BY
The first parameter refers to the name of the column, The second parameter is `optional` and is used to give a type of order `(ASC, DESC)`. <br>
Example #1
```sql
/* prepared sentence PHP */
SELECT * FROM table ORDER BY column;
```
```php
$list = Builder::select(Builder::FETCH_ALL, 'table', null, [
    Builder::orderBy('column')
]);

var_dump($list);
```

Example #2
```sql
/* prepared sentence PHP */
SELECT * FROM table ORDER BY column ASC;
```
```php
$list = Builder::select(Builder::FETCH_ALL, 'table', null, [
    Builder::orderBy('column', 'ASC')
]);

var_dump($list);
```

Example #3
```sql
/* prepared sentence PHP */
SELECT * FROM table ORDER BY column ASC, column DESC;
```
```php
$list = Builder::select(Builder::FETCH_ALL, 'table', null, [
    Builder::orderBy('column ASC, column DESC')
]);

var_dump($list);
```

### 19. GROUP BY
The first parameter refers to the name of the column, The second parameter is `optional` and is used to give a type of order `(ASC, DESC)`. <br>
Example #1
```sql
/* prepared sentence PHP */
SELECT * FROM table GROUP BY column
```
```php
$list = Builder::select(Builder::FETCH_ALL, 'table', null, [
    Builder::groupBy('column')
]);

var_dump($list);
```

Example #2
```sql
/* prepared sentence PHP */
SELECT * FROM table GROUP BY column DESC
```
```php
$list = Builder::select(Builder::FETCH_ALL, 'table', null, [
    Builder::groupBy('column', 'DESC')
]);

var_dump($list);
```

### 20. LIMIT
The first parameter is `boolean`, true for 2 parameters and false for a single parameter in the query, Note that the default parameter is initialized to true.<br>
Example #1
```sql
/* prepared sentence PHP */
SELECT * FROM table LIMIT ?
```
```php
$list = Builder::select(Builder::FETCH_ALL, 'table', null, null, [
    Builder::limit(false)
], [$id]);

var_dump($list);
```

Example #2
```sql
/* prepared sentence PHP */
SELECT * FROM table LIMIT ?, ?
```
```php
$list = Builder::select(Builder::FETCH_ALL, 'table', null, null, [
    Builder::limit(true)
], [$first, $second]);

var_dump($list);
```

Example #3
```sql
/* prepared sentence PHP */
SELECT * FROM table LIMIT ?, ?
```
```php
$list = Builder::select(Builder::FETCH_ALL, 'table', null, null, [
    Builder::limit()
], [$first, $second]);

var_dump($list);
```

### 21. HAVING
The first parameter is the `column` that is referenced, The second parameter is the operator with which the condition is handled, this parameter is optional. <br>
Example #1
```sql
/* prepared sentence PHP */
SELECT * FROM table HAVING column = ?
```
```php
$list = Builder::select(Builder::FETCH, 'table', null, null, [
    Builder::having('column', '=' /* '>', '<', '<>' */)
], [$value]);

var_dump($list);
```

Example #2
```sql
/* prepared sentence PHP */
SELECT * FROM table HAVING column
```
```php
$list = Builder::select(Builder::FETCH_ALL, 'table', null, null, [
    Builder::having('column')
]);

var_dump($list);
```

## License
Copyright ?? 2022 [MIT License](https://github.com/Sleon4/Lion-SQL/blob/main/LICENSE)