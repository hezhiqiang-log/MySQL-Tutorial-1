在本教程中，您将了解如何使用 MySQL `LIKE`运算符根据模式查询选择数据。

`LIKE`操作符通常用于基于模式查询选择数据。以正确的方式使用`LIKE`运算符对于增加/减少查询性能至关重要。

`LIKE`操作符允许您根据指定的模式从表中查询选择数据。 因此，`LIKE`运算符通常用在[SELECT语句](http://www.yiibai.com/mysql/select-statement-query-data.html)的[WHERE子句](http://www.yiibai.com/mysql/where.html)中。

MySQL 提供两个通配符，用于与`LIKE`运算符一起使用，它们分别是：百分比符号 - `%`和下划线 - `_`。

- 百分比(`%`)通配符允许匹配任何字符串的零个或多个字符。
- 下划线(`_`)通配符允许匹配任何单个字符。

## MySQL LIKE 运行符示例

下面让我们来学习一些使用`LIKE`操作符的例子。请参阅以下`employees`表。

```sql
mysql> desc employees;
+----------------+--------------+------+-----+---------+-------+
| Field          | Type         | Null | Key | Default | Extra |
+----------------+--------------+------+-----+---------+-------+
| employeeNumber | int(11)      | NO   | PRI | NULL    |       |
| lastName       | varchar(50)  | NO   |     | NULL    |       |
| firstName      | varchar(50)  | NO   |     | NULL    |       |
| extension      | varchar(10)  | NO   |     | NULL    |       |
| email          | varchar(100) | NO   |     | NULL    |       |
| officeCode     | varchar(10)  | NO   | MUL | NULL    |       |
| reportsTo      | int(11)      | YES  | MUL | NULL    |       |
| jobTitle       | varchar(50)  | NO   |     | NULL    |       |
+----------------+--------------+------+-----+---------+-------+
8 rows in set
```

### MySQL LIKE使用百分比(％)通配符

假设要搜索名字以字符`a`开头的员工信息，可以在模式末尾使用百分比通配符(`％`)，如下所示：

```sql
SELECT 
    employeeNumber, lastName, firstName
FROM
    employees
WHERE
    firstName LIKE 'a%';
```

执行上面查询，得到以下结果 -

```sql
mysql> SELECT 
    employeeNumber, lastName, firstName
FROM
    employees
WHERE
    firstName LIKE 'a%';
+----------------+----------+-----------+
| employeeNumber | lastName | firstName |
+----------------+----------+-----------+
|           1143 | Bow      | Anthony   |
|           1611 | Fixter   | Andy      |
+----------------+----------+-----------+
2 rows in set
```

### MySQL将扫描整个`employees`表以找到每个其名字以字符`a`开头，后跟任意数量的字符的员工信息。

要搜索员工以`on`字符结尾的姓氏，例如，`Patterson`，`Thompson`，可以使用模式开头的`％`通配符，如下查询：

```sql
SELECT 
    employeeNumber, lastName, firstName
FROM
    employees
WHERE
    lastName LIKE '%on';
```

执行上面查询，得到以下结果 -

```sql
mysql> SELECT 
    employeeNumber, lastName, firstName
FROM
    employees
WHERE
    lastName LIKE '%on';
+----------------+-----------+-----------+
| employeeNumber | lastName  | firstName |
+----------------+-----------+-----------+
|           1056 | Patterson | Mary      |
|           1088 | Patterson | William   |
|           1166 | Thompson  | Leslie    |
|           1216 | Patterson | Steve     |
+----------------+-----------+-----------+
4 rows in set
```

如果知道要搜索包含指定字符串，则可以在模式的开头和结尾使用百分比(`%`)通配符。

例如，要查找 `lastname` 字段值中包含`on`字符串的所有员工，可使用带有`%on%`条件，如下所示

```sql
SELECT 
    employeeNumber, lastName, firstName
FROM
    employees
WHERE
    lastname LIKE '%on%';
```

执行上面查询，得到以下结果 -

```sql
mysql> SELECT 
    employeeNumber, lastName, firstName
FROM
    employees
WHERE
    lastname LIKE '%on%';
+----------------+-----------+-----------+
| employeeNumber | lastName  | firstName |
+----------------+-----------+-----------+
|           1056 | Patterson | Mary      |
|           1088 | Patterson | William   |
|           1102 | Bondur    | Gerard    |
|           1166 | Thompson  | Leslie    |
|           1216 | Patterson | Steve     |
|           1337 | Bondur    | Loui      |
|           1504 | Jones     | Barry     |
+----------------+-----------+-----------+
7 rows in set
```

### MySQL LIKE 带下划线 (_) 通配符

要查找名字以`T`开头的员工，以`m`结尾，并且包含例如`Tom`，`Tim`之间的任何单个字符，可以使用下划线通配符来构建模式，如下所示：

```sql
SELECT 
    employeeNumber, lastName, firstName
FROM
    employees
WHERE
    firstname LIKE 'T_m';
```

执行上面查询，得到以下结果 -

```sql
mysql> SELECT 
    employeeNumber, lastName, firstName
FROM
    employees
WHERE
    firstname LIKE 'T_m';
+----------------+----------+-----------+
| employeeNumber | lastName | firstName |
+----------------+----------+-----------+
|           1619 | King     | Tom       |
+----------------+----------+-----------+
1 row in set
```

### 具有 NOT 运算符的 MySQL LIKE 运算符

MySQL 允许将`NOT`运算符与`LIKE`运算符组合，以找到不匹配特定模式的字符串。

假设要搜索姓氏(`lastname`)不以字符`B`开头的员工，则可以使用`NOT LIKE`作为以下查询：

```sql
SELECT 
    employeeNumber, lastName, firstName
FROM
    employees
WHERE
    lastName NOT LIKE 'B%';
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT 
    employeeNumber, lastName, firstName
FROM
    employees
WHERE
    lastName NOT LIKE 'B%';
+----------------+-----------+-----------+
| employeeNumber | lastName  | firstName |
+----------------+-----------+-----------+
|           1002 | Murphy    | Diane     |
|           1056 | Patterson | Mary      |
|           1076 | Firrelli  | Jeff      |
|           1088 | Patterson | William   |
|           1165 | Jennings  | Leslie    |
|           1166 | Thompson  | Leslie    |
|           1188 | Firrelli  | Julie     |
|           1216 | Patterson | Steve     |
|           1286 | Tseng     | Foon Yue  |
|           1323 | Vanauf    | George    |
|           1370 | Hernandez | Gerard    |
|           1401 | Castillo  | Pamela    |
|           1504 | Jones     | Barry     |
|           1611 | Fixter    | Andy      |
|           1612 | Marsh     | Peter     |
|           1619 | King      | Tom       |
|           1621 | Nishi     | Mami      |
|           1625 | Kato      | Yoshimi   |
|           1702 | Gerard    | Martin    |
+----------------+-----------+-----------+
19 rows in set
```

> 请注意，使用`LIKE`运算符，该模式不区分大小写，因此，`b%`和`B%`模式产生相同的结果。

## MySQL LIKE 与 ESCAPE 子句

有时想要匹配的模式包含通配符，例如`10%`，`_20`等这样的字符串时。在这种情况下，您可以使用`ESCAPE`子句指定转义字符，以便MySQL将通配符解释为文字字符。如果未明确指定转义字符，则反斜杠字符`\`是默认转义字符。

如下语句，将查询`productCode`字段中包含`_20`字符串的值。

```sql
SELECT 
    productCode, productName
FROM
    products
WHERE
    productCode LIKE '%\_20%';
```

执行上面语句，得到以下结果 -

```sql
mysql> SELECT 
    productCode, productName
FROM
    products
WHERE
    productCode LIKE '%\_20%';
+-------------+-------------------------------------------+
| productCode | productName                               |
+-------------+-------------------------------------------+
| S10_2016    | 1996 Moto Guzzi 1100i                     |
| S24_2000    | 1960 BSA Gold Star DBD34                  |
| S24_2011    | 18th century schooner                     |
| S24_2022    | 1938 Cadillac V-16 Presidential Limousine |
| S700_2047   | HMS Bounty                                |
+-------------+-------------------------------------------+
5 rows in set
```

或者，也可以使用`ESCAPE`子句指定一个不同的转义字符，例如`$`：

```sql
SELECT 
    productCode, productName
FROM
    products
WHERE
    productCode LIKE '%$_20%' ESCAPE '$';
```

以上语句查询结果与上一个语句得到的结果相同。

模式`%$_20%`匹配任何包含`_20`字符串的字符串。

`LIKE`操作符强制 MySQL 扫描整个表以找到匹配的行记录，因此，它不允许数据库引擎使用索引进行快速搜索。因此，当要从具有大量行的表查询数据时，使用`LIKE`运算符来查询数据的性能会大幅降低。

在本教程中，您已经学习了如何使用`LIKE`运算符根据模式查询数据，这比使用比较运算符更灵活。