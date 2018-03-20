在本教程中，您将学习如何将`NULL`值映射到其他有意义的值。

数据库关系模型创建者 - [E.F.Codd博士](http://en.wikipedia.org/wiki/Edgar_F._Codd)在关系数据库理论中引入了`NULL`概念。 根据*Dr.E.C.F.Codd*的表述，`NULL`表示未知值或缺少信息。

MySQL还支持`NULL`表示缺少或不适用信息的概念。

在数据库表中，您可能会存储包含`NULL`值的数据。但是如果以报表的形式向用户呈现数据时，显示`NULL`值是没有意义的。

要使报告更可读和可理解，必须显示`NULL`值作为其他有意义的值，如未知，缺失或不可用(*N/A*)。可以使用[IF函数](http://www.yiibai.com/mysql/if-function.html)做到这一点。

`IF`函数的语法如下：

```sql
IF(exp,exp_result1,exp_result2);
```

如果`exp`计算结果为`TRUE`(当`exp <> 0`和`exp <> NULL`)时，`IF`函数返回`exp_result1`的值，否则返回`exp_result2`的值。

`IF`函数的返回值可以是字符串或数字，具体取决于`exp_result1`和`exp_result2`表达式。

让我们练习一些例子以更好的理解。

假设要使用[示例数据库(yiibaidb)](http://www.yiibai.com/mysql/sample-database.html)中的`customers`表来作演示。

以下是`customers`表中包含：`customername`， `state` 和 `country` 的部分数据：

```sql
SELECT 
    customername, state, country
FROM
    customers
ORDER BY country;
```

执行上面代码，得到以下结果 -

```sql
+------------------------------------+---------------+--------------+
| customername                       | state         | country      |
+------------------------------------+---------------+--------------+
| Australian Collectors, Co.         | Victoria      | Australia    |
| Annas Decorations, Ltd             | NSW           | Australia    |
| Souveniers And Things Co.          | NSW           | Australia    |
| Australian Gift Network, Co        | Queensland    | Australia    |
|************** 此处省略了一大波数据 *********************************|
| Handji Gifts& Co                   | NULL          | Singapore    |
| Asian Shopping Network, Co         | NULL          | Singapore    |
| SAR Distributors, Co               | Pretoria      | South Africa |
| Euro+ Shopping Channel             | NULL          | Spain        |
| Diecast Collectables               | MA            | USA          |
+------------------------------------+---------------+--------------+
122 rows in set
```

从上面的结果集中，您将看到`state`列的值不适用于某些客户。可以使用`IF`函数将`NULL`值显示为`N/A`：

```sql
SELECT 
    customername, IF(state IS NULL, 'N/A', state) state, country
FROM
    customers
ORDER BY country;
```

执行上面查询代码，得到以下结果 -

```sql
+------------------------------------+---------------+--------------+
| customername                       | state         | country      |
+------------------------------------+---------------+--------------+
| Australian Collectors, Co.         | Victoria      | Australia    |
| Annas Decorations, Ltd             | NSW           | Australia    |
| Souveniers And Things Co.          | NSW           | Australia    |
| Australian Gift Network, Co        | Queensland    | Australia    |
| Australian Collectables, Ltd       | Victoria      | Australia    |
| Salzburg Collectables              | N/A           | Austria      |
| Mini Auto Werke                    | N/A           | Austria      |
| Petit Auto                         | N/A           | Belgium      |
| Royale Belge                       | N/A           | Belgium      |
|************** 此处省略了一大波数据 *********************************|
| Motor Mint Distributors Inc.       | PA            | USA          |
| Signal Collectibles Ltd.           | CA            | USA          |
| Diecast Collectables               | MA            | USA          |
+------------------------------------+---------------+--------------+
122 rows in set
```

除了`IF`函数外，MySQL提供了[IFNULL](http://www.mysqltutorial.org/mysql-ifnull.html)函数，可以直接处理`NULL`值。 以下是`IFNULL`函数的语法：

```sql
IFNULL(exp,exp_result);
```

重写上代码查询 -

```sql
SELECT customername, 
       IFNULL(state,"N/A") state, 
       country
FROM customers
ORDER BY country;
```

如果`exp`计算结果为`NULL`值，则`IFNULL`函数返回`exp_result`表达式的值，否则返回`exp`表达式的值。
以下查询使用`IFNULL`函数将`NULL`显示为未知，如下所示：

```sql
SELECT customername, 
       IFNULL(state,"N/A") state, 
       country
FROM customers
ORDER BY country;
```

执行上面查询代码，得到以下结果 -

```sql
+------------------------------------+---------------+--------------+
| customername                       | state         | country      |
+------------------------------------+---------------+--------------+
| Australian Collectors, Co.         | Victoria      | Australia    |
| Annas Decorations, Ltd            | NSW           | Australia    |
| Souveniers And Things Co.          | NSW           | Australia    |
| Australian Gift Network, Co        | Queensland    | Australia    |
| Australian Collectables, Ltd       | Victoria      | Australia    |
| Salzburg Collectables              | N/A           | Austria      |
| Mini Auto Werke                    | N/A           | Austria      |
| Petit Auto                         | N/A           | Belgium      |
| Royale Belge                       | N/A           | Belgium      |
|************** 此处省略了一大波数据 *********************************|
| Motor Mint Distributors Inc.       | PA            | USA          |
| Signal Collectibles Ltd.           | CA            | USA          |
| Diecast Collectables               | MA            | USA          |
+------------------------------------+---------------+--------------+
122 rows in set
```

在本教程中，您已经学习了如何使用`IF`和`IFNULL`函数将`NULL`值映射到其他更有意义的值，以便以可读方式呈现数据。