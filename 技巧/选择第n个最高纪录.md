在本教程中，您将学习如何使用各种技术选择数据库表中第`n`个最高记录。

使用[MAX](http://www.yiibai.com/mysql/max-function.html)或[MIN](http://www.yiibai.com/mysql/min.html)函数可以轻松选择数据库表中最高或最低的记录。但是，选择第`n`个最高纪录有点棘手。 例如，从`products`表中获得第二高价格的产品。

要选择第`n`个最高记录，需要执行以下步骤：

- 首先，得到`n`个最高记录，并按升序排列。第`n`个最高记录是结果集中的最后一个记录。
- 然后，按顺序对结果集进行排序，并获得第一个结果集。

以下是以升序获得第`n`个最高记录的查询：

```sql
SELECT 
    *
FROM
    table_name
ORDER BY column_name ASC
LIMIT N;
```

获得第`n`个最高记录的查询如下：

```sql
SELECT 
    *
FROM
    (SELECT 
        *
    FROM
        table_name
    ORDER BY column_name ASC
    LIMIT N) AS tbl
ORDER BY column_name DESC
LIMIT 1;
```

幸运的是，MySQL为我们提供了限制返回结果集中的行数的[LIMIT子句](http://www.mysqltutorial.org/mysql-limit.aspx)。可以重写上述查询如下：

```sql
SELECT 
    *
FROM
    table_name
ORDER BY column_name DESC
LIMIT n - 1, 1;
```

查询返回`n-1`行之后的第一行，以便获得第`n`个最高记录。

## 获得第n个最高纪录的例子

**第一种方法**

例如，如果要在`products`表中获得第二个最昂贵的产品(`n = 2`)，则使用以下查询：

```sql
SELECT 
    productCode, productName, buyPrice
FROM
    products
ORDER BY buyPrice DESC
LIMIT 1 , 1;
```

执行上面查询语句，结果如下：

```sql
+-------------+--------------------------------+----------+
| productCode | productName                    | buyPrice |
+-------------+--------------------------------+----------+
| S18_2238    | 1998 Chrysler Plymouth Prowler | 101.51   |
+-------------+--------------------------------+----------+
1 row in set
```

**第二种方法**

获得第`n`个最高记录的第二种技术是使用[MySQL子查询](http://www.mysqltutorial.org/mysql-subquery.html)：

```sql
SELECT *
FROM table_name AS a 
WHERE n - 1 = (
 SELECT COUNT(primary_key_column) 
 FROM products b 
 WHERE  b.column_name > a. column_name)
```

可以使用第一种技术获得相同的结果，以获得第二高价产品作为以下查询：

```sql
SELECT 
    productCode, productName, buyPrice
FROM
    products a
WHERE
    1 = (SELECT 
            COUNT(productCode)
        FROM
            products b
        WHERE
            b.buyPrice > a.buyPrice);
```

执行上面查询语句，结果如下：

```sql
+-------------+--------------------------------+----------+
| productCode | productName                    | buyPrice |
+-------------+--------------------------------+----------+
| S18_2238    | 1998 Chrysler Plymouth Prowler | 101.51   |
+-------------+--------------------------------+----------+
1 row in set
```

在本教程中，我们向您展示了如何使用MySQL中的`LIMIT`子句在数据库表中选择第`n`条记录。