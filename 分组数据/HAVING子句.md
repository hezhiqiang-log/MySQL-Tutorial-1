在本教程中，您将学习如何使用MySQL `HAVING`子句为行分组或聚合组指定过滤条件。

## MySQL HAVING子句简介

在[SELECT语句](http://www.yiibai.com/mysql/select-statement-query-data.html)中使用`HAVING`子句来指定一组行或聚合的过滤条件。

`HAVING`子句通常与[GROUP BY](http://www.yiibai.com/mysql/group-by.html)子句一起使用，以根据指定的条件过滤分组。如果省略`GROUP BY`子句，则`HAVING`子句的行为与`WHERE`子句类似。

> 请注意，`HAVING`子句将过滤条件应用于每组分行，而`WHERE`子句将过滤条件应用于每个单独的行。

## MySQL HAVING子句示例

让我们举一些使用`HAVING`子句的例子来看看它是如何工作。 我们将使用[示例数据库(yiibaidb)](http://www.yiibai.com/mysql/sample-database.html)中的`orderdetails`表进行演示。

```sql
mysql> desc orderdetails;
+-----------------+---------------+------+-----+---------+-------+
| Field           | Type          | Null | Key | Default | Extra |
+-----------------+---------------+------+-----+---------+-------+
| orderNumber     | int(11)       | NO   | PRI | NULL    |       |
| productCode     | varchar(15)   | NO   | PRI | NULL    |       |
| quantityOrdered | int(11)       | NO   |     | NULL    |       |
| priceEach       | decimal(10,2) | NO   |     | NULL    |       |
| orderLineNumber | smallint(6)   | NO   |     | NULL    |       |
+-----------------+---------------+------+-----+---------+-------+
5 rows in set
```

可以使用`GROUP BY`子句来获取订单号，查看每个订单销售的商品数量和每个销售总额：

```sql
SELECT 
    ordernumber,
    SUM(quantityOrdered) AS itemsCount,
    SUM(priceeach*quantityOrdered) AS total
FROM
    orderdetails
GROUP BY ordernumber;
```

执行上面查询语句，得到以下结果(部分) -

```sql
+-------------+------------+----------+
| ordernumber | itemsCount | total    |
+-------------+------------+----------+
|       10100 | 151        | 10223.83 |
|       10101 | 142        | 10549.01 |
|       10102 | 80         | 5494.78  |
|       10103 | 541        | 50218.95 |
|       10104 | 443        | 40206.20 |
|       10105 | 545        | 53959.21 |
|       10106 | 675        | 52151.81 |
| ------- 这里省略了一大波数据 ---------|
| ........                ........... |
|       10421 | 75         | 7639.10  |
|       10422 | 76         | 5849.44  |
|       10423 | 111        | 8597.73  |
|       10424 | 269        | 29310.30 |
|       10425 | 427        | 41623.44 |
+-------------+------------+----------+
326 rows in set
```

现在，可以通过使用`HAVING`子句查询(过滤)哪些订单的总销售额大于`55000`，如下所示：

```sql
SELECT 
    ordernumber,
    SUM(quantityOrdered) AS itemsCount,
    SUM(priceeach*quantityOrdered) AS total
FROM
    orderdetails
GROUP BY ordernumber
HAVING total > 55000;
```

执行上面查询语句，得到以下结果 -

```sql
+-------------+------------+----------+
| ordernumber | itemsCount | total    |
+-------------+------------+----------+
|       10126 | 617        | 57131.92 |
|       10127 | 540        | 58841.35 |
|       10135 | 607        | 55601.84 |
|       10142 | 577        | 56052.56 |
|       10165 | 670        | 67392.85 |
|       10181 | 522        | 55069.55 |
|       10192 | 585        | 55425.77 |
|       10204 | 619        | 58793.53 |
|       10207 | 615        | 59265.14 |
|       10212 | 612        | 59830.55 |
|       10222 | 717        | 56822.65 |
|       10287 | 595        | 61402.00 |
|       10310 | 619        | 61234.67 |
|       10312 | 601        | 55639.66 |
|       10390 | 603        | 55902.50 |
+-------------+------------+----------+
```

可以使用逻辑运算符(如`OR`和`AND`)在`HAVING`子句中构造复杂过滤条件。 假设您想查找哪些订单的总销售额大于`50000`，并且包含超过`600`个项目，则可以使用以下查询：

```sql
SELECT 
    ordernumber,
    SUM(quantityOrdered) AS itemsCount,
    SUM(priceeach*quantityOrdered) AS total
FROM
    orderdetails
GROUP BY ordernumber
HAVING total > 50000 AND itemsCount > 600;
```

执行上面查询语句，得到以下结果 -

```sql
+-------------+------------+----------+
| ordernumber | itemsCount | total    |
+-------------+------------+----------+
|       10106 | 675        | 52151.81 |
|       10126 | 617        | 57131.92 |
|       10135 | 607        | 55601.84 |
|       10165 | 670        | 67392.85 |
|       10168 | 642        | 50743.65 |
|       10204 | 619        | 58793.53 |
|       10207 | 615        | 59265.14 |
|       10212 | 612        | 59830.55 |
|       10222 | 717        | 56822.65 |
|       10310 | 619        | 61234.67 |
|       10312 | 601        | 55639.66 |
|       10360 | 620        | 52166.00 |
|       10390 | 603        | 55902.50 |
|       10414 | 609        | 50806.85 |
+-------------+------------+----------+
```

假设您想查找所有已发货(`status='Shiped'`)的订单和总销售额大于`55000`的订单，可以使用[INNER JOIN](http://www.yiibai.com/mysql/inner-join.html)子句将`orders`表与`orderdetails`表一起使用，并在`status`列和总金额(`total`)列上应用条件，如以下查询所示：

执行上面查询，得到以下结果 -

```sql
+-------------+---------+----------+
| ordernumber | status  | total    |
+-------------+---------+----------+
|       10126 | Shipped | 57131.92 |
|       10127 | Shipped | 58841.35 |
|       10135 | Shipped | 55601.84 |
|       10142 | Shipped | 56052.56 |
|       10165 | Shipped | 67392.85 |
|       10181 | Shipped | 55069.55 |
|       10192 | Shipped | 55425.77 |
|       10204 | Shipped | 58793.53 |
|       10207 | Shipped | 59265.14 |
|       10212 | Shipped | 59830.55 |
|       10222 | Shipped | 56822.65 |
|       10287 | Shipped | 61402.00 |
|       10310 | Shipped | 61234.67 |
|       10312 | Shipped | 55639.66 |
|       10390 | Shipped | 55902.50 |
+-------------+---------+----------+
```

`HAVING`子句仅在使用`GROUP BY`子句生成高级报告的输出时才有用。 例如，您可以使用`HAVING`子句来回答统计问题，例如在本月，本季度或今年总销售额超过`10000`的订单。

在本教程中，您已经学习了如何使用具有`GROUP BY`子句的MySQL `HAVING`子句为行分组或聚合分组指定过滤器条件。