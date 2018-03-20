在本教程中，您将学习如何使用MySQL `LIMIT`子句来限制`SELECT`语句返回记录的行数。

## MySQL LIMIT子句简介

在`SELECT`语句中使用`LIMIT`子句来约束结果集中的行数。`LIMIT`子句接受一个或两个参数。两个参数的值必须为零或正[整数](http://www.yiibai.com/mysql/int.html)。

下面说明了两个参数的`LIMIT`子句语法：

```sql
SELECT 
    column1,column2,...
FROM
    table
LIMIT offset , count;
```

我们来查看`LIMIT`子句参数：

- `offset`参数指定要返回的第一行的偏移量。第一行的偏移量为`0`，而不是`1`。
- `count`指定要返回的最大行数。

![img](images/9.jpg)

当您使用带有一个参数的`LIMIT`子句时，此参数将用于确定从结果集的开头返回的最大行数。

```sql
SELECT 
    column1,column2,...
FROM
    table
LIMIT count;
```

上面的查询等同于下面接受两个参数的`LIMIT`子句的查询：

```sql
SELECT 
    column1,column2,...
FROM
    table
LIMIT 0 , count;
```

## 使用MySQL LIMIT获取前N行

可以使用`LIMIT`子句来选择表中的前`N`行记录，如下所示：

```sql
SELECT 
    column1,column2,...
FROM
    table
LIMIT N;
```

例如，要查询`employees`表中前`5`个客户，请使用以下查询：

```sql
SELECT customernumber, customername, creditlimit FROM customers LIMIT 5;
```

或者 -

```sql
SELECT customernumber, customername, creditlimit FROM customers LIMIT 0,5;
```

执行上面语句，得到以下结果 -

```sql
mysql> SELECT customernumber, customername, creditlimit FROM customers LIMIT 5;
+----------------+----------------------------+-------------+
| customernumber | customername               | creditlimit |
+----------------+----------------------------+-------------+
|            103 | Atelier graphique          | 21000       |
|            112 | Signal Gift Stores         | 71800       |
|            114 | Australian Collectors, Co. | 117300      |
|            119 | La Rochelle Gifts          | 118200      |
|            121 | Baane Mini Imports         | 81700       |
+----------------+----------------------------+-------------+
5 rows in set
```

## 使用MySQL LIMIT获得最高和最低的值

`LIMIT`子句经常与[ORDER BY子句](http://www.yiibai.com/mysql/order-by.html)一起使用。首先，使用`ORDER BY`子句根据特定条件对结果集进行排序，然后使用`LIMIT`子句来查找最小或最大值。

> 注意：`ORDER BY`子句按指定字段排序的使用。

请参见[示例数据库(yiibaidb)](http://www.yiibai.com/mysql/sample-database.html)中的以下`customers`表，其表结构如下所示 -

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

例如，要查询信用额度最高的前五名客户，请使用以下查询：

```sql
SELECT customernumber, customername, creditlimit
FROM customers
ORDER BY creditlimit DESC
LIMIT 5;
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT customernumber, customername, creditlimit
FROM customers
ORDER BY creditlimit DESC
LIMIT 5;
+----------------+------------------------------+-------------+
| customernumber | customername                 | creditlimit |
+----------------+------------------------------+-------------+
|            141 | Euro+ Shopping Channel       | 227600      |
|            124 | Mini Gifts Distributors Ltd. | 210500      |
|            298 | Vida Sport, Ltd              | 141300      |
|            151 | Muscle Machine Inc           | 138500      |
|            187 | AV Stores, Co.               | 136800      |
+----------------+------------------------------+-------------+
5 rows in set
```

以下查询将返回信用限额最低的五位客户：

```sql
SELECT customernumber, customername, creditlimit
FROM customers
ORDER BY
 creditlimit ASC
LIMIT 5;
```

## 使用MySQL LIMIT获得第n个最高值

MySQL中最棘手的问题之一是：如何获得结果集中的第`n`个最高值，例如查询第二(或第`n`)贵的产品是哪个，显然不能使用[MAX](http://www.yiibai.com/mysql/max-function.html)或[MIN](http://www.yiibai.com/mysql/min.html)这样的函数来查询获得。 但是，我们可以使用MySQL `LIMIT`来解决这样的问题。

- **首先**，按照降序[对结果集进行排序](http://www.yiibai.com/mysql/order-by.html)。
- **第二步**，使用`LIMIT`子句获得第`n`贵的产品。

通用查询如下：

```sql
SELECT 
    column1, column2,...
FROM
    table
ORDER BY column1 DESC
LIMIT nth-1, count;
```

下面我们来看看一个例子，将使用[示例数据库(yiibaidb)](http://www.yiibai.com/mysql/sample-database.html)中的产品(`products`)表来进行演示。`products`表的结构如下所示 -

```sql
mysql> desc products;
+--------------------+---------------+------+-----+---------+-------+
| Field              | Type          | Null | Key | Default | Extra |
+--------------------+---------------+------+-----+---------+-------+
| productCode        | varchar(15)   | NO   | PRI | NULL    |       |
| productName        | varchar(70)   | NO   |     | NULL    |       |
| productLine        | varchar(50)   | NO   | MUL | NULL    |       |
| productScale       | varchar(10)   | NO   |     | NULL    |       |
| productVendor      | varchar(50)   | NO   |     | NULL    |       |
| productDescription | text          | NO   |     | NULL    |       |
| quantityInStock    | smallint(6)   | NO   |     | NULL    |       |
| buyPrice           | decimal(10,2) | NO   |     | NULL    |       |
| MSRP               | decimal(10,2) | NO   |     | NULL    |       |
+--------------------+---------------+------+-----+---------+-------+
9 rows in set
```

查看以下产品表中的行记录：

```sql
mysql> SELECT productCode, productName, buyprice
FROM products
ORDER BY
 buyprice DESC;
+-------------+--------------------------------------+----------+
| productCode | productName                          | buyprice |
+-------------+--------------------------------------+----------+
| S10_4962    | 1962 LanciaA Delta 16V               | 103.42   |
| S18_2238    | 1998 Chrysler Plymouth Prowler       | 101.51   |
| S10_1949    | 1952 Alpine Renault 1300             | 98.58    |
| S24_3856    | 1956 Porsche 356A Coupe              | 98.3     |
| S12_1108    | 2001 Ferrari Enzo                    | 95.59    |
| S12_1099    | 1968 Ford Mustang                    | 95.34    |
... ....
+-------------+--------------------------------------+----------+
110 rows in set
```

我们的任务找出结果集中价格第二高的产品。可以使用`LIMIT`子句来选择第二行，如以下查询(注意：偏移量从`0`开始，所以要指定从`1`开始，然后取一行记录)：

```sql
SELECT productCode, productName, buyprice FROM  products
ORDER BY buyprice DESC
LIMIT 1, 1;
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT productCode, productName, buyprice FROM  products
ORDER BY buyprice DESC
LIMIT 1, 1;
+-------------+--------------------------------+----------+
| productCode | productName                    | buyprice |
+-------------+--------------------------------+----------+
| S18_2238    | 1998 Chrysler Plymouth Prowler | 101.51   |
+-------------+--------------------------------+----------+
1 row in set
```

类似的，获取售价第三高、第四高的产品信息为：`LIMIT 2, 1` 和 `LIMIT 3, 1`。

在本教程中，我们向您展示了如何使用MySQL `LIMIT`子句来限制`SELECT`语句返回的行数。通过本教程的学习，相信您应该对MySQL `LIMIT`子句的使用有一定的理解了。