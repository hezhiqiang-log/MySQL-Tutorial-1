在本教程中，您将学习如何使用 MySQL `IS NULL`运算符来测试值是否为一个`NULL`值。

## MySQL IS NULL 操作符简介

要测试值是否为`NULL`值，需要使用`IS NULL`运算符。 以下显示`IS NULL`运算符的语法：

```sql
value IS NULL
```

如果值为`NULL`，该表达式将返回`true`。 否则返回`false`。

请注意，MySQL 没有内置的[BOOLEAN类型](http://www.yiibai.com/mysql/boolean.html)。 它使用[TINYINT(1)](http://www.yiibai.com/mysql/int.html)来表示`BOOLEAN`值，即`1`表示`true`，`0`表示`false`。

因为`IS NULL`是一个比较运算符，所以您可以在任何使用运算符的地方使用它，例如在 [SELECT](http://www.yiibai.com/mysql/select-statement-query-data.html) 或 [WHERE](http://www.yiibai.com/mysql/where/) 子句中。如下面的例子：

```sql
SELECT 1 IS NULL,  # -- 0
       0 IS NULL,  # -- 0
       NULL IS NULL; # -- 1;
```

要检查值是否不为`NULL`，请使用`IS NOT NULL`运算符，如下所示：

```sql
value IS NOT NULL
```

如果该值不为`NULL`，则此表达式返回`true`(也就是`1`)。 否则返回`false`(也就是`0`)。 请考虑以下示例：

```sql
SELECT 1 IS NOT NULL, #-- 1
       0 IS NOT NULL, #-- 1
       NULL IS NOT NULL; #-- 0;
```

上面查询语句，执行后得到以下结果 -

```sql
mysql> SELECT 1 IS NOT NULL, #-- 1
       0 IS NOT NULL, #-- 1
       NULL IS NOT NULL; #-- 0
+---------------+---------------+------------------+
| 1 IS NOT NULL | 0 IS NOT NULL | NULL IS NOT NULL |
+---------------+---------------+------------------+
|             1 |             1 |                0 |
+---------------+---------------+------------------+
1 row in set
```

## MySQL IS NULL 示例

我们将使用[示例数据库(yiibaidb)](http://www.yiibai.com/mysql/sample-database.html)中的`customers`表进行演示，`customers`表的结构如下所示 -

```sql
mysql> desc customers;
+------------------------+---------------+------+-----+---------+-------+
| Field                  | Type          | Null | Key | Default | Extra |
+------------------------+---------------+------+-----+---------+-------+
| customerNumber         | int(11)       | NO   | PRI | NULL    |       |
| customerName           | varchar(50)   | NO   |     | NULL    |       |
| contactLastName        | varchar(50)   | NO   |     | NULL    |       |
| contactFirstName       | varchar(50)   | NO   |     | NULL    |       |
| phone                  | varchar(50)   | NO   |     | NULL    |       |
| addressLine1           | varchar(50)   | NO   |     | NULL    |       |
| addressLine2           | varchar(50)   | YES  |     | NULL    |       |
| city                   | varchar(50)   | NO   |     | NULL    |       |
| state                  | varchar(50)   | YES  |     | NULL    |       |
| postalCode             | varchar(15)   | YES  |     | NULL    |       |
| country                | varchar(50)   | NO   |     | NULL    |       |
| salesRepEmployeeNumber | int(11)       | YES  | MUL | NULL    |       |
| creditLimit            | decimal(10,2) | YES  |     | NULL    |       |
+------------------------+---------------+------+-----+---------+-------+
13 rows in set
```

要查询没有销售代表的客户，请使用`IS NULL`运算符，如下所示：

```sql
SELECT 
    customerName, 
    country, 
    salesrepemployeenumber
FROM
    customers
WHERE
    salesrepemployeenumber IS NULL
ORDER BY customerName;
```

执行上面查询，得到以下结果 -

```sql
mysql> SELECT 
    customerName, 
    country, 
    salesrepemployeenumber
FROM
    customers
WHERE
    salesrepemployeenumber IS NULL
ORDER BY customerName;
+--------------------------------+--------------+------------------------+
| customerName                   | country      | salesrepemployeenumber |
+--------------------------------+--------------+------------------------+
| ANG Resellers                  | Spain        | NULL                   |
| Anton Designs, Ltd.            | Spain        | NULL                   |
| Asian Shopping Network, Co     | Singapore    | NULL                   |
| Asian Treasures, Inc.          | Ireland      | NULL                   |
| BG&E Collectables              | Switzerland  | NULL                   |
| Cramer Spezialitten, Ltd       | Germany      | NULL                   |
| Der Hund Imports               | Germany      | NULL                   |
| Schuyler Imports               | Netherlands  | NULL                   |
| Stuttgart Collectable Exchange | Germany      | NULL                   |
| Warburg Exchange               | Germany      | NULL                   |
... ...
+--------------------------------+--------------+------------------------+
22 rows in set
```

要查询有销售代表的客户，请使用`IS NOT NULL`运算符，如下查询语句 -

```sql
SELECT 
    customerName, 
    country, 
    salesrepemployeenumber
FROM
    customers
WHERE
    salesrepemployeenumber IS NOT NULL
ORDER BY customerName;
```

执行上面查询，得到以下结果 -

```sql
mysql> SELECT 
    customerName, 
    country, 
    salesrepemployeenumber
FROM
    customers
WHERE
    salesrepemployeenumber IS NOT NULL
ORDER BY customerName;
+------------------------------------+-------------+------------------------+
| customerName                       | country     | salesrepemployeenumber |
+------------------------------------+-------------+------------------------+
| Alpha Cognac                       | France      |                   1370 |
| American Souvenirs Inc             | USA         |                   1286 |
| Amica Models & Co.                 | Italy       |                   1401 |
| Anna's Decorations, Ltd            | Australia   |                   1611 |
| Atelier graphique                  | France      |                   1370 |
| Australian Collectables, Ltd       | Australia   |                   1611 |
| Australian Collectors, Co.         | Australia   |                   1611 |
| Australian Gift Network, Co        | Australia   |                   1611 |
| Auto Associs & Cie.                | France      |                   1370 |
| Auto Canal+ Petit                  | France      |                   1337 |
| Auto-Moto Classics Inc.            | USA         |                   1216 |
| AV Stores, Co.                     | UK          |                   1501 |
| Baane Mini Imports                 | Norway      |                   1504 |
| Bavarian Collectables Imports, Co. | Germany     |                   1504 |
... ...
+------------------------------------+-------------+------------------------+
100 rows in set
```

## MySQL IS NULL 的专用功能

为了兼容 ODBC 程序，MySQL 支持`IS NULL`运算符的一些专门功能。

(1). 如果具有`NOT NULL`约束的`DATE`或`DATETIME`列包含特殊日期’`0000-00-00`‘，则可以使用`IS NULL`运算符来查找这些行。如下示例 -

```sql
CREATE TABLE IF NOT EXISTS projects (
    id INT AUTO_INCREMENT,
    title VARCHAR(255),
    begin_date DATE NOT NULL,
    complete_date DATE NOT NULL,
    PRIMARY KEY(id)
);

INSERT INTO projects(title,begin_date, complete_date)
VALUES('New CRM','2020-01-01','0000-00-00'),
      ('ERP Future','2020-01-01','0000-00-00'),
      ('VR','2020-01-01','2030-01-01');


SELECT 
    *
FROM
    projects
WHERE
    complete_date IS NULL;
```

在这个例子中，创建了一个 `projects` 新表，并将一些数据[插入到表中](http://www.yiibai.com/mysql/insert-statement.html)。最后一个查询使用`IS NULL`来获取`complete_date`列中的值为“`0000-00-00`”的行。

(2). 如果变量`@@sql_auto_is_null`设置为`1`，则可以使用`IS NULL`运算符在执行`INSERT`语句后获取生成列的值。请注意，默认情况下，变量`@@sql_auto_is_null`为`0`。请参见以下示例。

首先，将变量`@@sql_auto_is_null`设置为`1`。

```sql
SET @@sql_auto_is_null = 1;
```

*第二步*，在`projects`表中插入一个新行：

```sql
INSERT INTO projects(title,begin_date, complete_date)
VALUES('MRP III','2010-01-01','2020-12-31');
```

*第三步*，使用`IS NULL`运算符来获取`id`列的生成值：

```sql
SELECT 
    id
FROM
    projects
WHERE
    id IS NULL;
```

## MySQL IS NULL优化

MySQL 对于`IS NULL`运算符执行相同的优化方式与等于(`=`)运算符相同。

例如，MySQL 在使用`IS NULL`运算符搜索`NULL`时使用索引，如以下查询所示：

```sql
SELECT 
    customerNumber, 
    salesRepEmployeeNumber
FROM
    customers
WHERE
    salesRepEmployeeNumber IS NULL;
```

查看`EXPLAIN`查询过程：

```sql
EXPLAIN SELECT 
    customerNumber, 
    salesRepEmployeeNumber
FROM
    customers
WHERE
    salesRepEmployeeNumber IS NULL;
```

执行上面查询语句，输出以下结果 -

```sql
mysql> EXPLAIN SELECT 
    customerNumber, 
    salesRepEmployeeNumber
FROM
    customers
WHERE
    salesRepEmployeeNumber IS NULL;
+----+-------------+-----------+------------+------+------------------------+------------------------+---------+-------+------+----------+--------------------------+
| id | select_type | table     | partitions | type | possible_keys          | key                    | key_len | ref   | rows | filtered | Extra                    |
+----+-------------+-----------+------------+------+------------------------+------------------------+---------+-------+------+----------+--------------------------+
|  1 | SIMPLE      | customers | NULL       | ref  | salesRepEmployeeNumber | salesRepEmployeeNumber | 5       | const |   22 |      100 | Using where; Using index |
+----+-------------+-----------+------------+------+------------------------+------------------------+---------+-------+------+----------+--------------------------+
1 row in set
```

MySQL 也可以优化组合`col = value OR col IS NULL`。 请参阅以下示例：

```sql
EXPLAIN SELECT 
    customerNumber,
    salesRepEmployeeNumber
FROM
    customers
WHERE
    salesRepEmployeeNumber = 1370 OR
    salesRepEmployeeNumber IS NULL;
```

执行上面查询语句，得到以下结果 -

```sql
mysql> EXPLAIN SELECT 
    customerNumber,
    salesRepEmployeeNumber
FROM
    customers
WHERE
    salesRepEmployeeNumber = 1370 OR
    salesRepEmployeeNumber IS NULL;
+----+-------------+-----------+------------+-------------+------------------------+------------------------+---------+-------+------+----------+--------------------------+
| id | select_type | table     | partitions | type        | possible_keys          | key                    | key_len | ref   | rows | filtered | Extra                    |
+----+-------------+-----------+------------+-------------+------------------------+------------------------+---------+-------+------+----------+--------------------------+
|  1 | SIMPLE      | customers | NULL       | ref_or_null | salesRepEmployeeNumber | salesRepEmployeeNumber | 5       | const |   29 |      100 | Using where; Using index |
+----+-------------+-----------+------------+-------------+------------------------+------------------------+---------+-------+------+----------+--------------------------+
1 row in set
```

在这个例子中，当应用优化时，`EXPLAIN`会显示`ref_or_null`。

如果您有一个列的组合键，MySQL可以对任何关键部分执行优化。假设在表`t1`的列`c1`和`c2`上有一个索引，以下查询被优化：

```sql
SELECT 
    *
FROM
    t1
WHERE
    c1 IS NULL;
```

在本教程中，您已经学习了如何使用 MySQL `IS NULL`运算符来测试值是否为`NULL`。