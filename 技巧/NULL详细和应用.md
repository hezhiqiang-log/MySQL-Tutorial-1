在本教程中，您将学习如何使用MySQL `NULL`值。 此外，您将学习一些有用的函数来有效地处理`NULL`值。

> 如果不能理解和使用数据库中`NULL`值，那么可以认为您的数据库学习最多算刚入门水平。

## MySQL NULL值简介

在MySQL中，`NULL`值表示一个未知值。 `NULL`值不同于`0`或空字符串`''`。

`NULL`值不等于它自身。如果将`NULL`值与另一个`NULL`值或任何其他值进行比较，则结果为`NULL`，因为一个不知道是什么的值(`NULL`值)与另一个不知道是什么的值(`NULL`值)比较，其值当然也是一个不知道是什么的值(`NULL`值)。

通常，使用`NULL`值来表示数据丢失，未知或不适用的情况。 例如，潜在客户的电话号码可能为`NULL`，并且可以稍后添加。

创建表时，可以通过使用`NOT NULL`约束来指定列是否接受`NULL`值。

例如，以下语句用于创建一张`leads`表：

```sql
USE testdb;
CREATE TABLE leads (
    id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    source VARCHAR(255) NOT NULL,
    email VARCHAR(100),
    phone VARCHAR(25)
);
```

因此，`id`是[主键](http://www.yiibai.com/mysql/primary-key.html)列，它不接受任何`NULL`值。

`first_name`，`last_name`和`source`列使用`NOT NULL`约束，因此，不能在这些列中插入任何`NULL`值，而`email`和`phone`列则可接受`NULL`值。

您可以在[INSERT](http://www.yiibai.com/mysql/insert-statement.html)语句中使用`NULL`值来指定数据丢失。 例如，以下语句将一行插入到线索表中。 因为电话号码丢失，所以使用`NULL`值。

```sql
INSERT INTO leads(first_name,last_name,source,email,phone)
VALUE('John','Doe','Web Search','john.doe@yiibai.com',NULL);
```

因为`email`列的默认值为`NULL`，可以按照以下方式在`INSERT`语句中省略电子邮件：

```sql
INSERT INTO leads(first_name,last_name,source,phone)
VALUES('Lily','Bush','Cold Calling','(408)-555-1234'),
('David','William','Web Search','(408)-888-6789');
```

## UPDATE语句中的MySQL SET NULL值

要将列的值设置为`NULL`，可以使用赋值运算符(`=`)。 例如，要将`David William`的手机(`phone`)更新为`NULL`，请使用以下`UPDATE`语句：

```sql
UPDATE leads 
SET 
    phone = NULL
WHERE
    id = 3;
```

## MySQL ORDER BY为NULL

如果使用[ORDER BY](http://www.yiibai.com/mysql/order-by.html)子句按升序对结果集进行排序，则MySQL认为`NULL`值低于其他值，因此，它会首先显示`NULL`值。

以下查询语句按照电话号码(`phone`)升序排列。如下所示 -

```sql
SELECT 
    *
FROM
    leads
ORDER BY phone;
```

执行上面查询语句，结果如下 -

```sql
+----+------------+-----------+--------------+---------------------+----------------+
| id | first_name | last_name | source       | email               | phone          |
+----+------------+-----------+--------------+---------------------+----------------+
|  1 | John       | Doe       | Web Search   | john.doe@yiibai.com | NULL           |
|  3 | David      | William   | Web Search   | NULL                | NULL           |
|  2 | Lily       | Bush      | Cold Calling | NULL                | (408)-555-1234 |
+----+------------+-----------+--------------+---------------------+----------------+
```

如果使用`ORDER BY DESC`，`NULL`值将显示在结果集的最后。 请参阅以下示例：

```sql
SELECT 
    *
FROM
    leads
ORDER BY phone DESC;
```

执行上面查询语句，结果如下 -

```sql
+----+------------+-----------+--------------+---------------------+----------------+
| id | first_name | last_name | source       | email               | phone          |
+----+------------+-----------+--------------+---------------------+----------------+
|  2 | Lily       | Bush      | Cold Calling | NULL                | (408)-555-1234 |
|  1 | John       | Doe       | Web Search   | john.doe@yiibai.com | NULL           |
|  3 | David      | William   | Web Search   | NULL                | NULL           |
+----+------------+-----------+--------------+---------------------+----------------+
3 rows in set
```

要在查询中测试`NULL`，可以在[WHERE](http://www.yiibai.com/mysql/where.html)子句中使用`IS NULL`或`IS NOT NULL`运算符。

例如，要获得尚未提供电话号码的潜在客户，请使用`IS NULL`运算符，如下所示：

```sql
SELECT 
    *
FROM
    leads
WHERE
    phone IS NULL;
```

执行上面查询语句，结果如下 -

```sql
+----+------------+-----------+------------+---------------------+-------+
| id | first_name | last_name | source     | email               | phone |
+----+------------+-----------+------------+---------------------+-------+
|  1 | John       | Doe       | Web Search | john.doe@yiibai.com | NULL  |
|  3 | David      | William   | Web Search | NULL                | NULL  |
+----+------------+-----------+------------+---------------------+-------+
2 rows in set
```

可以使用`IS NOT`运算符来获取所有提供电子邮件地址的潜在客户。

```sql
SELECT 
    *
FROM
    leads
WHERE
    email IS NOT NULL;
```

执行上面查询语句，结果如下 -

```sql
+----+------------+-----------+------------+---------------------+-------+
| id | first_name | last_name | source     | email               | phone |
+----+------------+-----------+------------+---------------------+-------+
|  1 | John       | Doe       | Web Search | john.doe@yiibai.com | NULL  |
+----+------------+-----------+------------+---------------------+-------+
1 row in set
```

即使`NULL`不等于`NULL`，`GROUP BY`子句中视两个`NULL`值相等。

```sql
SELECT 
    email, count(*)
FROM
    leads
GROUP BY email;
```

该查询只返回两行，因为其邮箱(`email`)列为`NULL`的行被分组为一行，结果如下所示 -

```sql
+---------------------+----------+
| email               | count(*) |
+---------------------+----------+
| NULL                |        2 |
| john.doe@yiibai.com |        1 |
+---------------------+----------+
2 rows in set
```

## MySQL NULL和UNIQUE索引

在列上使用唯一约束或`UNIQUE`索引时，可以在该列中[插入](http://www.yiibai.com/mysql/insert-statement.html)多个`NULL`值。这是非常好的，因为在这种情况下，MySQL认为`NULL`值是不同的。

我们通过为`phone`列创建一个`UNIQUE`索引来验证这一点。

```sql
CREATE UNIQUE INDEX idx_phone ON leads(phone);
```

请注意，如果使用BDB[存储引擎](http://www.yiibai.com/understand-mysql-table-types-innodb-myisam.html)，MySQL认为`NULL`值相等，因此您不能将多个`NULL`值插入到具有唯一约束的列中。

## MySQL NULL函数

MySQL提供了几个有用的功能，很好地处理空值：[IFNULL](http://www.yiibai.com/mysql/ifnull.html)，[COALESCE](http://www.yiibai.com/mysql/coalesce.html)和[NULLIF](http://www.yiibai.com/mysql/nullif.html)。

`IFNULL`函数接受两个参数。 如果`IFNULL`函数不为`NULL`，则返回第一个参数，否则返回第二个参数。

例如，如果不是`NULL`，则以下语句返回电话号码(`phone`)，否则返回`N/A`，而不是`NULL`。

```sql
SELECT 
    id, first_name, last_name, IFNULL(phone, 'N/A') phone
FROM
    leads;
```

执行上面查询语句，得到以下结果 -

```sql
+----+------------+-----------+----------------+
| id | first_name | last_name | phone          |
+----+------------+-----------+----------------+
|  1 | John       | Doe       | N/A            |
|  2 | Lily       | Bush      | (408)-555-1234 |
|  3 | David      | William   | N/A            |
+----+------------+-----------+----------------+
3 rows in set
```

`COALESCE`函数接受参数列表，并返回第一个非`NULL`参数。 例如，可以使用`COALESCE`函数根据信息的优先级按照以下顺序显示线索的联系信息：`phone`, `email`和`N/A`。

```sql
SELECT 
    id,
    first_name,
    last_name,
    COALESCE(phone, email, 'N/A') contact
FROM
    leads;
```

执行上面查询语句，得到以下代码 -

```sql
+----+------------+-----------+---------------------+
| id | first_name | last_name | contact             |
+----+------------+-----------+---------------------+
|  1 | John       | Doe       | john.doe@yiibai.com |
|  2 | Lily       | Bush      | (408)-555-1234      |
|  3 | David      | William   | N/A                 |
+----+------------+-----------+---------------------+
3 rows in set
```

`NULLIF`函数接受两个参数。如果两个参数相等，则`NULLIF`函数返回`NULL`。 否则，它返回第一个参数。

在列中同时具有`NULL`和空字符串值时，`NULLIF`函数很有用。 例如，错误地，您将以下行插入到`leads`表中：

```sql
INSERT INTO leads(first_name,last_name,source,email,phone)
VALUE('Thierry','Henry','Web Search','thierry.henry@yiibai.com','');
```

`phone`是一个空字符串:`''`，而不是`NULL`。

如果您想获得潜在客户的联系信息，则最终得到空`phone`，而不是电子邮件，如下所示：

```sql
SELECT 
    id,
    first_name,
    last_name,
    COALESCE(phone, email, 'N/A') contact
FROM
    leads;
```

执行上面查询语句，得到以下代码 -

```sql
+----+------------+-----------+---------------------+
| id | first_name | last_name | contact             |
+----+------------+-----------+---------------------+
|  1 | John       | Doe       | john.doe@yiibai.com |
|  2 | Lily       | Bush      | (408)-555-1234      |
|  3 | David      | William   | N/A                 |
|  4 | Thierry    | Henry     |                     |
+----+------------+-----------+---------------------+
```

要解决这个问题，您可以使用`NULLIF`函数将电话与空字符串(`''`)进行比较，如果相等，则返回`NULL`，否则返回电话号码。

```sql
SELECT 
    id,
    first_name,
    last_name,
    COALESCE(NULLIF(phone, ''), email, 'N/A') contact
FROM
    leads;
```

执行上面查询语句，得到以下代码 -

```sql
+----+------------+-----------+--------------------------+
| id | first_name | last_name | contact                  |
+----+------------+-----------+--------------------------+
|  1 | John       | Doe       | john.doe@yiibai.com      |
|  2 | Lily       | Bush      | (408)-555-1234           |
|  3 | David      | William   | N/A                      |
|  4 | Thierry    | Henry     | thierry.henry@yiibai.com |
+----+------------+-----------+--------------------------+
4 rows in set
```

在本教程中，您已经学习了如何使用MySQL `NULL`值，以及如何使用一些方便的函数来处理查询中的`NULL`。