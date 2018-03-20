在本教程中，将学习如何使用 MySQL `AND`运算符组合多个布尔表达式以形成多个条件来过滤数据。

## MySQL AND 运算符简介

`AND`运算符是组合两个或多个布尔表达式的逻辑运算符，只有当两个表达式求值为`true`时才返回`true`。如果两个表达式中的一个求值为`false`，则`AND`运算符返回`false`。

```sql
WHERE boolean_expression_1 AND boolean_expression_2
```

以下说明`AND`运算符组合`true`，`false`和`null`时的结果。

| -     | TRUE  | FALSE | NULL  |
| ----- | ----- | ----- | ----- |
| TRUE  | TRUE  | FALSE | NULL  |
| FALSE | FALSE | FALSE | FALSE |
| NULL  | NULL  | FALSE | NULL  |

`AND`运算符通常用在`SELECT`，`UPDATE`，`DELETE`语句的`WHERE`子句中以形成布尔表达式。`AND`运算符也用于[INNER JOIN](http://www.yiibai.com/mysql/inner-join.html)或[LEFT JOIN](http://www.yiibai.com/mysql/left-join.html)子句的连接条件。

当求值具有`AND`运算符的表达式时，MySQL 会计算表达式的其余部分，直到可以确定结果为止。该功能称为短路求值。请参见以下示例。

```sql
SELECT 1 = 0 AND 1 / 0 ;
```

执行上面查询时，得到以下结果 -

```sql
mysql> SELECT 1 = 0 AND 1 / 0 ;
+-----------------+
| 1 = 0 AND 1 / 0 |
+-----------------+
|               0 |
+-----------------+
1 row in set
```

> 请注意，在 MySQL 中，`0`被认为是`false`，非零被视为`true`。

MySQL 只计算表达式`1 = 0 AND 1/0`的第一部分`1 = 0`，因为表达式`1 = 0`返回`false`，所以 MySQL 得出结论：整个表达式的结果是`false`。 MySQL 不对表达式的剩余部分求值，即不对`1/0`进行求值; 如果对`1/0`进行求值，它将发出一个错误消息，因为除以零错误。

## MySQL AND 运算符示例

下面使用示例数据库中的`customers`表进行演示。`customers`表的结构如下所示 -

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

以下声明选择国家是`USA`和`CA`的客户。我们在`WHERE`子句中使用`AND`运算符。

```sql
SELECT customername, country, state FROM customers WHERE country = 'USA' AND state = 'CA';
```

执行上面查询得到以下结果 -

```sql
mysql> SELECT customername, country, state FROM customers WHERE country = 'USA' AND state = 'CA';
+------------------------------+---------+-------+
| customername                 | country | state |
+------------------------------+---------+-------+
| Mini Gifts Distributors Ltd. | USA     | CA    |
| Mini Wheels Co.              | USA     | CA    |
| Technics Stores Inc.         | USA     | CA    |
| Toys4GrownUps.com            | USA     | CA    |
| Boards & Toys Co.            | USA     | CA    |
| Collectable Mini Designs Co. | USA     | CA    |
| Corporate Gift Ideas Co.     | USA     | CA    |
| Men 'R' US Retailers, Ltd.   | USA     | CA    |
| The Sharp Gifts Warehouse    | USA     | CA    |
| West Coast Collectables Co.  | USA     | CA    |
| Signal Collectibles Ltd.     | USA     | CA    |
+------------------------------+---------+-------+
11 rows in set
```

使用`AND`运算符，可以组合两个以上的布尔表达式。例如，以下查询返回位于美国加州的客户，并且信用额度大于`100K`。

```sql
SELECT   customername,
         country,
         state,
         creditlimit
FROM customers
WHERE country = 'USA'
        AND state = 'CA'
        AND creditlimit > 100000;
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT customername, country, state, creditlimit FROM customers
WHERE country = 'USA' AND state = 'CA' AND creditlimit > 100000;
+------------------------------+---------+-------+-------------+
| customername                 | country | state | creditlimit |
+------------------------------+---------+-------+-------------+
| Mini Gifts Distributors Ltd. | USA     | CA    | 210500      |
| Collectable Mini Designs Co. | USA     | CA    | 105000      |
| Corporate Gift Ideas Co.     | USA     | CA    | 105000      |
+------------------------------+---------+-------+-------------+
3 rows in set
```

在本教程中，我们向您展示了如何使用 MySQL `AND`运算符组合两个或多个表达式以形成`WHERE`子句的复合条件语句。