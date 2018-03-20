在本教程中，您将学习如何在MySQL中模拟`row_number`函数。 我们将向您展示如何向每行或每组行添加行号。

## row_number函数简介

`row_number`是一个排序函数，返回一行的顺序号，从第一行的`1`开始。经常想使用`row_number`函数来生成特定的报告。

MySQL不提供像SQL Server，Oracle或[PostgreSQL](http://www.yiibai.com/postgresql/)中那样的`row_number`函数。 幸运的是，MySQL提供了可用于模拟`row_number`函数的会话变量。

## MySQL row_number - 为每行添加行号

要模拟`row_number`函数，必须在查询中使用会话变量。

以下语句从`employees`表中获取`5`名员工，并从`1`开始为每行添加行号。

```sql
SET @row_number = 0;

SELECT 
    (@row_number:=@row_number + 1) AS num, firstName, lastName
FROM
    employees
LIMIT 5;
```

执行上面语句，得到以下结果 -

```sql
+-----+-----------+-----------+
| num | firstName | lastName  |
+-----+-----------+-----------+
|   1 | Diane     | Murphy    |
|   2 | Mary      | Hill      |
|   3 | Jeff      | Firrelli  |
|   4 | William   | Patterson |
|   5 | Gerard    | Bondur    |
+-----+-----------+-----------+
5 rows in set
```

在上述查询语句中：

- 在第一个语句中，定义了一个名为ro`w_number`的变量，并将其值设置为`0`。 `row_number`是由`@`前缀指示的会话变量。
- 在第二个语句中，从`employees`表中查询选择了数据，并将`row_number`变量的值增加到每行的`1`。`LIMIT`子句用于约束一些返回的行，在这种情况下，它被设置为`5`。

另一种技术是使用会话变量作为派生表，并将其与主表连接。 请参阅以下查询：

```sql
SELECT 
    (@row_number:=@row_number + 1) AS num, firstName, lastName
FROM
    employees,(SELECT @row_number:=0) AS t
LIMIT 5;
```

> 请注意，派生表必须具有自己的[别名](http://www.yiibai.com/mysql/alias.html)，以使查询语法正确。

## MySQL row_number - 为每个组添加行号

如果要为每个组添加一个行号，并且为每个新组重置它。

我们来看看`payments`表：

```sql
SELECT
    customerNumber, paymentDate, amount
FROM
    payments
ORDER BY customerNumber;
```

执行上面查询语句，得到类似以下结果 -

```sql
+----------------+-------------+-----------+
| customerNumber | paymentDate | amount    |
+----------------+-------------+-----------+
|            103 | 2014-10-19  | 6066.78   |
|            103 | 2013-06-05  | 14571.44  |
|            103 | 2014-12-18  | 1676.14   |
|            112 | 2014-12-17  | 14191.12  |
|*********** 省略部分数据 ******************|
|            496 | 2015-05-25  | 30253.75  |
|            496 | 2013-07-16  | 32077.44  |
|            496 | 2014-12-31  | 52166     |
+----------------+-------------+-----------+
273 rows in set
```

假设每个客户要添加一个行号，并且每当客户号码更改时，行号都会被重置。

要实现这一点，可使用两个会话变量，一个用于行号，另一个用于存储旧客户编号，以将其与当前的客户编号进行比较，如下查询语句：

```sql
SELECT 
    @row_number:=CASE
        WHEN @customer_no = customerNumber THEN @row_number + 1
        ELSE 1
    END AS num,
    @customer_no:=customerNumber as CustomerNumber,
    paymentDate,
    amount
FROM
    payments
ORDER BY customerNumber;
```

在查询中使用了[CASE语句](http://www.yiibai.com/mysql/case-statement.html)。 如果客户号码保持不变，我们增加了`row_number`变量的值，否则将`row_number`变量重置为`1`。查询结果如下所示。

```sql
+-----+----------------+-------------+-----------+
| num | CustomerNumber | paymentDate | amount    |
+-----+----------------+-------------+-----------+
|   1 |            103 | 2014-10-19  | 6066.78   |
|   2 |            103 | 2013-06-05  | 14571.44  |
|   3 |            103 | 2014-12-18  | 1676.14   |
|   1 |            112 | 2014-12-17  | 14191.12  |
|   2 |            112 | 2013-06-06  | 32641.98  |
|   3 |            112 | 2014-08-20  | 33347.88  |
|   1 |            114 | 2013-05-20  | 45864.03  |
|   2 |            114 | 2014-12-15  | 82261.22  |
|   3 |            114 | 2013-05-31  | 7565.08   |
|   4 |            114 | 2014-03-10  | 44894.74  |
|   1 |            119 | 2014-11-14  | 19501.82  |
|   2 |            119 | 2014-08-08  | 47924.19  |
|   3 |            119 | 2015-02-22  | 49523.67  |
|   1 |            121 | 2013-02-16  | 50218.95  |
|************* 此处省略了一大波数据 ***************|
|   1 |            496 | 2015-05-25  | 30253.75  |
|   2 |            496 | 2013-07-16  | 32077.44  |
|   3 |            496 | 2014-12-31  | 52166     |
+-----+----------------+-------------+-----------+
273 rows in set
```

像`row_number`的行号一样，可以使用派生表和交叉连接技术来产生相同的结果。