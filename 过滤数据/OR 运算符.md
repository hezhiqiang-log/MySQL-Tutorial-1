本教程将学习如何使用MySQL `OR`运算符组合布尔表达式来过滤数据。

## MySQL OR运算符介绍

MySQL `OR`运算符组合了两个或两个以上布尔表达式。当任一条件为真时，返回`true`。

下面说明了`OR`运算符的语法。

```sql
boolean_expression_1 OR boolean_expression_2
```

`boolean_expression_1`和`boolean_expression_2`是布尔表达式，它可能返回的结果是：`true`，`false`或`NULL`。

下表显示了`OR`运算符的结果。

| —     | TRUE | FALSE | NULL |
| ----- | ---- | ----- | ---- |
| TRUE  | TRUE | TRUE  | TRUE |
| FALSE | TRUE | FALSE | NULL |
| NULL  | TRUE | NULL  | NULL |

## MySQL OR短路求值

MySQL使用`OR`运算符进行短路评估(求值计算)。 换句话说，当MySQL可以确定结果时，MySQL会停止评估(求值计算)语句的其余部分。

请参见以下示例。

```sql
SELECT 1 = 1 OR 1 / 0;
```

执行上面代码，得到以下结果 -

```sql
mysql> SELECT 1 = 1 OR 1 / 0;
+----------------+
| 1 = 1 OR 1 / 0 |
+----------------+
|              1 |
+----------------+
1 row in set
```

因为表达式`1 = 1`总是返回`true`，MySQL不会对`1/0`进行求值。如果是这样，它会发出一个除以零错误的错误消息。

## 运算符优先级

当您在语句中使用多个逻辑运算符时，MySQL会在`AND`运算符之后再对`OR`运算符进行求值。 这称为运算符优先级。

运算符优先级决定运算符的求值顺序。 MySQL首先对优先级较高的运算符进行求值。

请参见以下示例。

```sql
SELECT true OR false AND false;
```

执行上面查询，得到如下结果 -

```sql
mysql> SELECT true OR false AND false;
+-------------------------+
| true OR false AND false |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set
```

上面得出的结果，运算的过程是怎么样呢？

- 首先，MySQL对`AND`运算符求值，因此，`false AND false`返回`false`。
- 其次，MySQL对`OR`运算符求值，根据返回的`false`值再执行`AND`运算，因此`true OR false`返回`true`。

要更改评估/求值的顺序，请使用括号，例如：

```sql
SELECT (true OR false) AND false;
```

执行上面查询，得到如下结果 -

```sql
mysql> SELECT (true OR false) AND false;
+---------------------------+
| (true OR false) AND false |
+---------------------------+
|                         0 |
+---------------------------+
1 row in set
```

上面得出的结果，运算的过程是怎么样呢？

- 首先，MySQL计算小括号中的表达式(`true OR false`)返回`true`
- 第二，MySQL评估求值语句的剩余部分，将上面第一步中计算出的表达式结果-`true`和剩余部分求值，即：`true AND false`返回`false`。

## MySQL OR运算符示例

下面，我们将使用[示例数据库(yiibaidb)](http://www.yiibai.com/mysql/sample-database.html)中的`customers`表进行演示。`customers`表的结果如下所示 -

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

例如，要获得美国(`USA`)或者法国(`France`)的客户，请在WHERE子句中使用OR运算符，如下所示：

```sql
SELECT 
    customername, country
FROM
    customers
WHERE
    country = 'USA' OR country = 'France';
```

执行上面代码，得到如下结果 -

```sql
mysql> SELECT  customername, country FROM customers WHERE country = 'USA' OR country = 'France';
+------------------------------+---------+
| customername                 | country |
+------------------------------+---------+
| Atelier graphique            | France  |
| Signal Gift Stores           | USA     |
| La Rochelle Gifts            | France  |
| Mini Gifts Distributors Ltd. | USA     |
| Mini Wheels Co.              | USA     |
| Land of Toys Inc.            | USA     |
| Saveley & Henriot, Co.       | France  |
| Muscle Machine Inc           | USA     |
| Diecast Classics Inc.        | USA     |
| Technics Stores Inc.         | USA     |
... ...
+------------------------------+---------+
48 rows in set
```

以下声明返回位于美国(`USA`)或者法国(`France`)，并且信用额度大于`10000`的客户。

```sql
SELECT 
    customername, country, creditLimit
FROM
    customers
WHERE
    (country = 'USA' OR country = 'France')
    AND creditlimit > 100000;
```

执行上面代码，得到如下结果 -

```sql
mysql> SELECT  customername, country, creditLimit FROM customers WHERE (country = 'USA' OR country = 'France') AND creditlimit > 100000;
+------------------------------+---------+-------------+
| customername                 | country | creditLimit |
+------------------------------+---------+-------------+
| La Rochelle Gifts            | France  | 118200      |
| Mini Gifts Distributors Ltd. | USA     | 210500      |
| Land of Toys Inc.            | USA     | 114900      |
| Saveley & Henriot, Co.       | France  | 123900      |
| Muscle Machine Inc           | USA     | 138500      |
| Diecast Classics Inc.        | USA     | 100600      |
| Collectable Mini Designs Co. | USA     | 105000      |
| Marta's Replicas Co.         | USA     | 123700      |
| Mini Classics                | USA     | 102700      |
| Corporate Gift Ideas Co.     | USA     | 105000      |
| Online Diecast Creations Co. | USA     | 114200      |
+------------------------------+---------+-------------+
11 rows in set
```

请注意，如果不使用括号，查询将返回位于美国的客户或者位于法国并且信用额度大于`10000`的客户。

```sql
SELECT
    customername, country, creditLimit
FROM
    customers
WHERE
    country = 'USA' OR country = 'France' AND creditlimit > 100000;
```

执行上面代码，得到如下结果(共 `38` 行) -

```sql
mysql> SELECT customername, country, creditLimit FROM customers WHERE country = 'USA' OR country = 'France' AND creditlimit > 100000;
+------------------------------+---------+-------------+
| customername                 | country | creditLimit |
+------------------------------+---------+-------------+
| Signal Gift Stores           | USA     | 71800       |
| La Rochelle Gifts            | France  | 118200      |
| Mini Gifts Distributors Ltd. | USA     | 210500      |
| Mini Wheels Co.              | USA     | 64600       |
| Land of Toys Inc.            | USA     | 114900      |
| Saveley & Henriot, Co.       | France  | 123900      |
| Muscle Machine Inc           | USA     | 138500      |
| Diecast Classics Inc.        | USA     | 100600      |
| Technics Stores Inc.         | USA     | 84600       |
| American Souvenirs Inc       | USA     | 0           |
| Cambridge Collectables Co.   | USA     | 43400       |
| Gift Depot Inc.              | USA     | 84300       |
| Vitachrome Inc.              | USA     | 76400       |
| Auto-Moto Classics Inc.      | USA     | 23000       |
| Online Mini Collectables     | USA     | 68700       |
| Toys4GrownUps.com            | USA     | 90700       |
| Boards & Toys Co.            | USA     | 11000       |
| Collectable Mini Designs Co. | USA     | 105000      |
| Marta's Replicas Co.         | USA     | 123700      |
| Mini Classics                | USA     | 102700      |
| Mini Creations Ltd.          | USA     | 94500       |
| Corporate Gift Ideas Co.     | USA     | 105000      |
| Tekni Collectables Inc.      | USA     | 43000       |
| Classic Gift Ideas, Inc      | USA     | 81100       |
| Men 'R' US Retailers, Ltd.   | USA     | 57700       |
| Gifts4AllAges.com            | USA     | 41900       |
| Online Diecast Creations Co. | USA     | 114200      |
| Collectables For Less Inc.   | USA     | 70700       |
| Classic Legends Inc.         | USA     | 67500       |
| Gift Ideas Corp.             | USA     | 49700       |
| The Sharp Gifts Warehouse    | USA     | 77600       |
| Super Scale Inc.             | USA     | 95400       |
| Microscale Inc.              | USA     | 39800       |
| FunGiftIdeas.com             | USA     | 85800       |
| West Coast Collectables Co.  | USA     | 55400       |
| Motor Mint Distributors Inc. | USA     | 72600       |
| Signal Collectibles Ltd.     | USA     | 60300       |
| Diecast Collectables         | USA     | 85100       |
+------------------------------+---------+-------------+
38 rows in set
```

在本教程中，您已经学习了如何使用MySQL `OR`运算符来组合布尔表达式来过滤数据。需要注意的是：使用组合运算符时，`OR`运算符和`AND`运算符的求值顺序。