在本教程中，您将学习如何使用MySQL `GROUP BY`根据指定列或表达式的值将行进行分组到子组。

## MySQL GROUP BY子句简介

`GROUP BY`子句通过列或表达式的值将一组行分组为一个小分组的汇总行记录。 `GROUP BY`子句为每个分组返回一行。换句话说，它减少了结果集中的行数。

经常使用`GROUP BY`子句与[聚合函数](http://www.yiibai.com/mysql/aggregate-functions.html)一起使用，如[SUM](http://www.yiibai.com/mysql/sum.html)，[AVG](http://www.yiibai.com/mysql/avg.html)，[MAX](http://www.yiibai.com/mysql/max-function.html)，[MIN](http://www.yiibai.com/mysql/min.html)和[COUNT](http://www.yiibai.com/mysql/count.html)。SELECT子句中使用聚合函数来计算有关每个分组的信息。

`GROUP BY`子句是[SELECT语句](http://www.yiibai.com/mysql/select-statement-query-data.html)的可选子句。 下面是`GROUP BY`子句语法：

```sql
SELECT 
    c1, c2,..., cn, aggregate_function(ci)
FROM
    table
WHERE
    where_conditions
GROUP BY c1 , c2,...,cn;
```

`GROUP BY`子句必须出现在`FROM`和`WHERE`子句之后。 在`GROUP BY`关键字之后是一个以逗号分隔的列或表达式的列表，这些是要用作为条件来对行进行分组。

## MySQL GROUP BY示例

### 简单的MySQL GROUP BY示例

我们来看看[示例数据库(yiibaidb)](http://www.yiibai.com/mysql/sample-database.html)中的`orders`表，其结构如下所示 -

```sql
mysql> desc orders;
+----------------+-------------+------+-----+---------+-------+
| Field          | Type        | Null | Key | Default | Extra |
+----------------+-------------+------+-----+---------+-------+
| orderNumber    | int(11)     | NO   | PRI | NULL    |       |
| orderDate      | date        | NO   |     | NULL    |       |
| requiredDate   | date        | NO   |     | NULL    |       |
| shippedDate    | date        | YES  |     | NULL    |       |
| status         | varchar(15) | NO   |     | NULL    |       |
| comments       | text        | YES  |     | NULL    |       |
| customerNumber | int(11)     | NO   | MUL | NULL    |       |
+----------------+-------------+------+-----+---------+-------+
7 rows in set
```

假设要将订单状态的值分组到子组中，则要使用`GROUP BY`子句并指定按`status`列来执行分组，如下查询：

```sql
SELECT 
    status
FROM
    orders
GROUP BY status;
```

执行上面查询语句，得到以下结果 -

```sql
+------------+
| status     |
+------------+
| Cancelled  |
| Disputed   |
| In Process |
| On Hold    |
| Resolved   |
| Shipped    |
+------------+
```

可以看到，`GROUP BY`子句返回状态(`status`)值是唯一的。它像[DISTINCT](http://www.yiibai.com/mysql/distinct.html)运算符一样工作，如下面的查询所示：

```sql
SELECT DISTINCT
    status
FROM
    orders;
```

执行上面查询语句，得到以下结果 -

```sql
+------------+
| status     |
+------------+
| Shipped    |
| Resolved   |
| Cancelled  |
| On Hold    |
| Disputed   |
| In Process |
+------------+
```

### MySQL GROUP BY与聚合函数

可使用[聚合函数](http://www.yiibai.com/mysql/aggregate-functions.html)来执行一组行的计算并返回单个值。 `GROUP BY`子句通常与聚合函数一起使用以执行计算每个分组并返回单个值。

例如，如果想知道每个状态中的订单数，可以使用`COUNT`函数与`GROUP BY`子句查询语句，如下所示：

```sql
SELECT 
    status, COUNT(*) AS total_number
FROM
    orders
GROUP BY status;
```

执行上面查询语句，得到以下结果 -

```sql
+------------+--------------+
| status     | total_number |
+------------+--------------+
| Cancelled  |            6 |
| Disputed   |            3 |
| In Process |            6 |
| On Hold    |            4 |
| Resolved   |            4 |
| Shipped    |          303 |
+------------+--------------+
6 rows in set
```

请参阅以下订单(`orders`)和订单详细(`orderdetails`)表，它们的*ER*图如下所示 -

![img](../images/955090748_38158.png)

要按状态获取所有订单的总金额，可以使用`orderdetails`表[连接](http://www.yiibai.com/mysql/inner-join.html)`orders`表，并使用`SUM`函数计算总金额。请参阅以下查询：

```sql
SELECT 
    status, SUM(quantityOrdered * priceEach) AS amount
FROM
    orders
        INNER JOIN
    orderdetails USING (orderNumber)
GROUP BY status;
```

执行上面查询，得到以下结果 -

```sql
+------------+------------+
| status     | amount     |
+------------+------------+
| Cancelled  | 238854.18  |
| Disputed   | 61158.78   |
| In Process | 135271.52  |
| On Hold    | 169575.61  |
| Resolved   | 134235.88  |
| Shipped    | 8865094.64 |
+------------+------------+
```

类似地，以下查询返回订单号和每个订单的总金额。

```sql
SELECT 
    orderNumber,
    SUM(quantityOrdered * priceEach) AS total
FROM
    orderdetails
GROUP BY orderNumber;
```

执行上面查询，得到以下结果 -

```sql
+-------------+----------+
| orderNumber | total    |
+-------------+----------+
|       10100 | 10223.83 |
|       10101 | 10549.01 |
|       10102 | 5494.78  |
|       10103 | 50218.95 |
|       10104 | 40206.20 |
|       10105 | 53959.21 |
|... ...
|... 这里省略了一大波数据 ...
|... ...
|       10423 | 8597.73  |
|       10424 | 29310.30 |
|       10425 | 41623.44 |
+-------------+----------+
326 rows in set
```

### MySQL GROUP BY用表达式示例

除了列之外，可以按表达式对行进行分组。以下查询获取每年的总销售额。

```sql
SELECT 
    YEAR(orderDate) AS year,
    SUM(quantityOrdered * priceEach) AS total
FROM
    orders
        INNER JOIN
    orderdetails USING (orderNumber)
WHERE
    status = 'Shipped'
GROUP BY YEAR(orderDate);
```

执行上面查询，得到以下结

```sql
+------+------------+
| year | total      |
+------+------------+
| 2013 | 3223095.80 |
| 2014 | 4300602.99 |
| 2015 | 1341395.85 |
+------+------------+
```

在这个例子中，我们使用[YEAR](http://www.yiibai.com/mysql/year.html)函数从订单日期(`orderDate`)中提取年份数据。只包括已发货(`Shipped`)状态的订单。 请注意，`SELECT`子句中出现的表达式必须与`GROUP BY`子句中的相同。

## MySQL GROUP BY与HAVING子句

可使用[HAVING](http://www.yiibai.com/mysql/having.html)子句过滤`GROUP BY`子句返回的分组。以下查询使用`HAVING`子句来选择`2013`年以后的年销售总额。

```sql
SELECT 
    YEAR(orderDate) AS year,
    SUM(quantityOrdered * priceEach) AS total
FROM
    orders
        INNER JOIN
    orderdetails USING (orderNumber)
WHERE
    status = 'Shipped'
GROUP BY year
HAVING year > 2013;
```

执行上面查询，得到以下结果 -

```sql
+------+------------+
| year | total      |
+------+------------+
| 2014 | 4300602.99 |
| 2015 | 1341395.85 |
+------+------------+
```

## GROUP BY子句：MySQL与标准SQL

标准SQL不允许使用`GROUP BY`子句中的别名，但MySQL支持此选项。以下查询从订单日期提取年份，并对每年的订单进行计数。该`year`用作表达式`YEAR(orderDate)`的别名，它也用作`GROUP BY`子句中的别名，此查询在标准SQL中无效。
参考以下查询 -

```sql
SELECT 
    YEAR(orderDate) AS year, COUNT(orderNumber)
FROM
    orders
GROUP BY year;
```

执行上面查询，得到以下结果 -

```sql
+------+--------------------+
| year | COUNT(orderNumber) |
+------+--------------------+
| 2013 |                111 |
| 2014 |                151 |
| 2015 |                 64 |
+------+--------------------+
```

MySQL还允许您以升序或降序(标准SQL不能提供)对组进行排序。默认顺序是升序。例如，如果要按状态获取订单数量并按降序对状态进行排序，则可以使用带有`DESC`的`GROUP BY`子句，如下查询语句：

```sql
SELECT 
    status, COUNT(*)
FROM
    orders
GROUP BY status DESC;
```

执行上面查询，得到以下结果 -

```sql
+------------+----------+
| status     | COUNT(*) |
+------------+----------+
| Shipped    |      303 |
| Resolved   |        4 |
| On Hold    |        4 |
| In Process |        6 |
| Disputed   |        3 |
| Cancelled  |        6 |
+------------+----------+
```

请注意，在`GROUP BY`子句中使用`DESC`以降序对状态进行排序。我们还可以在`GROUP BY`子句中明确指定`ASC`，按状态对分组进行升序排序。

在本教程中，我们向您演示了如何使用MySQL `GROUP BY`子句根据列或表达式的值将行分组到子组中。