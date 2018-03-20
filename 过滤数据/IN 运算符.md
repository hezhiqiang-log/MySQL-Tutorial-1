在本教程中，您将学习如何使用 MySQL `IN`运算符来确定指定列的值是否匹配列表中的值或子查询中的任何值。

## MySQL IN 操作符介绍

IN运算符允许您确定指定的值是否与列表中的值或[子查询](http://www.yiibai.com/mysql/subquery.html)中的任何值匹配。 下面说明了`IN`操作符的语法。

```sql
SELECT 
    column1,column2,...
FROM
    table_name
WHERE 
 (expr|column_1) IN ('value1','value2',...);
```

下面我们更详细的来看看上面的查询：

- 可以在 [WHERE 子句](http://www.yiibai.com/mysql/where.html)中与`IN`运算符一起使用，可使用列或表达式(`expr`)。
- 列表中的值必须用逗号(`，`)分隔。
- `IN`操作符也可以用在其他语句(如[INSERT](http://www.yiibai.com/mysql/insert-statement.html)，[UPDATE](http://www.yiibai.com/mysql/update-data.html)，[DELETE](http://www.yiibai.com/mysql/delete-statement.html)等)的 WHERE 子句中。

如果`column_1`的值或`expr`表达式的结果等于列表中的任何值，则`IN`运算符返回`1`，否则返回`0`。

当列表中的值都是常量时：

- 首先，MySQL 根据`column_1`的类型或`expr`表达式的结果来计算值。
- 第二步，MySQL 排序值。
- 第三步，MySQL 使用二进制搜索算法搜索值。因此，使用具有常量列表的`IN`运算符的查询将执行得非常快。

> 请注意，如果列表中的`expr`或任何值为`NULL`，则`IN`运算符计算结果返回`NULL`。

可以将`IN`运算符与`NOT`运算符组合，以确定值是否与列表或子查询中的任何值不匹配。

## MySQL IN 示例

下面练习一些使用`IN`操作符的例子。首先来看看办事处表：`offices` 的结构

```sql
mysql> desc offices;
+--------------+-------------+------+-----+---------+-------+
| Field        | Type        | Null | Key | Default | Extra |
+--------------+-------------+------+-----+---------+-------+
| officeCode   | varchar(10) | NO   | PRI | NULL    |       |
| city         | varchar(50) | NO   |     | NULL    |       |
| phone        | varchar(50) | NO   |     | NULL    |       |
| addressLine1 | varchar(50) | NO   |     | NULL    |       |
| addressLine2 | varchar(50) | YES  |     | NULL    |       |
| state        | varchar(50) | YES  |     | NULL    |       |
| country      | varchar(50) | NO   |     | NULL    |       |
| postalCode   | varchar(15) | NO   |     | NULL    |       |
| territory    | varchar(10) | NO   |     | NULL    |       |
+--------------+-------------+------+-----+---------+-------+
9 rows in set
```

如果您想查找位于美国和法国的办事处，可以使用`IN`运算符作为以下查询：

```sql
SELECT 
    officeCode, city, phone, country
FROM
    offices
WHERE
    country IN ('USA' , 'France');
```

执行上面查询语句，得到以下结果

```sql
mysql> SELECT officeCode, city, phone, country FROM offices WHERE country IN ('USA' , 'France');
+------------+---------------+-----------------+---------+
| officeCode | city          | phone           | country |
+------------+---------------+-----------------+---------+
| 1          | San Francisco | +1 650 219 4782 | USA     |
| 2          | Boston        | +1 215 837 0825 | USA     |
| 3          | NYC           | +1 212 555 3000 | USA     |
| 4          | Paris         | +33 14 723 4404 | France  |
+------------+---------------+-----------------+---------+
4 rows in set
```

也可以使用`OR`运算符执行得到与上面查询相同的结果，如下所示：

```sql
SELECT 
    officeCode, city, phone
FROM
    offices
WHERE
    country = 'USA' OR country = 'France';
```

执行上面查询语句，得到以下结果

```sql
mysql> SELECT officeCode, city, phone FROM offices WHERE country = 'USA' OR country = 'France';
+------------+---------------+-----------------+
| officeCode | city          | phone           |
+------------+---------------+-----------------+
| 1          | San Francisco | +1 650 219 4782 |
| 2          | Boston        | +1 215 837 0825 |
| 3          | NYC           | +1 212 555 3000 |
| 4          | Paris         | +33 14 723 4404 |
+------------+---------------+-----------------+
4 rows in set
```

如果列表中有很多值，使用多个`OR`运算符则会构造一个非常长的语句。 因此，使用`IN`运算符则会缩短查询并使查询更易读。

要获得不在美国和法国的办事处，请在`WHERE`子句中使用`NOT IN`如下：

```sql
SELECT 
    officeCode, city, phone
FROM
    offices
WHERE
    country NOT IN ('USA' , 'France');
```

执行上面查询语句，得到以下结果

```sql
mysql> SELECT officeCode, city, phone FROM offices WHERE country NOT IN( 'USA', 'France');
+------------+---------+------------------+
| officeCode | city    | phone            |
+------------+---------+------------------+
| 5          | Beijing | +86 33 224 5000  |
| 6          | Sydney  | +61 2 9264 2451  |
| 7          | London  | +44 20 7877 2041 |
+------------+---------+------------------+
3 rows in set
```

## MySQL IN 与子查询

`IN`运算符通常用于子查询。子查询不提供常量值列表，而是提供值列表。

我们来看看两张表：`orders`和`orderDetails`表的结构以及它们之间的关系：

![img](images/8.png)

例如，如果要查找总金额大于`60000`的订单，则使用`IN`运算符查询如下所示：

```sql
SELECT 
    orderNumber, customerNumber, status, shippedDate
FROM
    orders
WHERE
    orderNumber IN (SELECT 
            orderNumber
        FROM
            orderDetails
        GROUP BY orderNumber
        HAVING SUM(quantityOrdered * priceEach) > 60000);
```

执行上面语句，得到以下结果

```sql
mysql> SELECT 
    orderNumber, customerNumber, status, shippedDate
FROM
    orders
WHERE
    orderNumber IN (SELECT 
            orderNumber
        FROM
            orderDetails
        GROUP BY orderNumber
        HAVING SUM(quantityOrdered * priceEach) > 60000);
+-------------+----------------+---------+-------------+
| orderNumber | customerNumber | status  | shippedDate |
+-------------+----------------+---------+-------------+
|       10165 |            148 | Shipped | 2013-12-26  |
|       10287 |            298 | Shipped | 2014-09-01  |
|       10310 |            259 | Shipped | 2014-10-18  |
+-------------+----------------+---------+-------------+
3 rows in set
```

上面的整个查询可以分为`2`个查询。

首先，子查询使用`orderDetails`表中的[GROUP BY](http://www.yiibai.com/mysql/group-by.html)和[HAVING子句](http://www.yiibai.com/mysql/having.html)返回总额大于`60000`的订单号列表。

```sql
SELECT 
    orderNumber
FROM
    orderDetails
GROUP BY orderNumber
HAVING SUM(quantityOrdered * priceEach) > 60000;
```

执行上面语句，得到以下结果

```sql
mysql> SELECT 
    orderNumber
FROM
    orderDetails
GROUP BY orderNumber
HAVING SUM(quantityOrdered * priceEach) > 60000;
+-------------+
| orderNumber |
+-------------+
|       10165 |
|       10287 |
|       10310 |
+-------------+
3 rows in set
```

其次，主查询从`orders`表中获取数据，并在`WHERE`子句中应用`IN`运算符。

```sql
SELECT 
    orderNumber, customerNumber, status, shippedDate
FROM
    orders
WHERE
    orderNumber IN (10165,10287,10310);
```

执行上面语句，得到以下结果

```sql
mysql> SELECT 
    orderNumber, customerNumber, status, shippedDate
FROM
    orders
WHERE
    orderNumber IN (10165,10287,10310);
+-------------+----------------+---------+-------------+
| orderNumber | customerNumber | status  | shippedDate |
+-------------+----------------+---------+-------------+
|       10165 |            148 | Shipped | 2013-12-26  |
|       10287 |            298 | Shipped | 2014-09-01  |
|       10310 |            259 | Shipped | 2014-10-18  |
+-------------+----------------+---------+-------------+
3 rows in set
```

在本教程中，我们向您展示了如何使用 MySQL `IN`运算符来确定值是否匹配列表或子查询中的任何值。