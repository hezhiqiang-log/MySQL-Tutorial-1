在本教程中，您将了解MySQL `CHAR`数据类型以及如何将其应用于数据库表设计。

## MySQL CHAR数据类型简介

`CHAR`数据类型是MySQL中固定长度的字符类型。 我们经常声明`CHAR`类型，其长度指定要存储的最大字符数。 例如，`CHAR(20)`最多可以容纳`20`个字符。

如果要存储的数据是固定大小，则应使用`CHAR`数据类型。在这种情况下，与VARCHAR相比，您将获得更好的性能。

`CHAR`数据类型的长度可以是从`0`到`255`的任何值。当存储`CHAR`值时，MySQL将其值与空格填充到声明的长度。

当查询`CHAR`值时，MySQL会删除尾部的空格。

> 请注意，如果启用`PAD_CHAR_TO_FULL_LENGTH SQL`模式，MySQL将不会删除尾随空格。

以下语句创建具有`CHAR`列的表。

```sql
USE testdb;

CREATE TABLE mysql_char_test (
    status CHAR(3)
);
```

`status`列使用`CHAR`数据类型，并有指定最多可容纳`3`个字符。

现在，使用以下语句向`mysql_char_test`表中插入`2`行数据。

```sql
INSERT INTO mysql_char_test(status)
VALUES('Yes'),('No');
```

下面我们使用[length](http://www.yiibai.com/mysql/string-length.html)函数来获取每个`CHAR`值的长度。

```sql
SELECT 
    status, LENGTH(status)
FROM
    mysql_char_test;
```

执行上面查询语句，得到以下结果 -

```sql
+--------+----------------+
| status | LENGTH(status) |
+--------+----------------+
| Yes    |              3 |
| No     |              2 |
+--------+----------------+
2 rows in set
```

以下语句将插入带有前导和尾随空格的`CHAR`值。

```sql
INSERT INTO mysql_char_test(status)
VALUES(' Y ');
```

但是，当我们检索该值时，MySQL会删除尾随空格。

```sql
SELECT 
    status, LENGTH(status)
FROM
    mysql_char_test;
```

执行上面查询语句，得到以下结果 -

```sql
+--------+----------------+
| status | LENGTH(status) |
+--------+----------------+
| Yes    |              3 |
| No     |              2 |
|  Y     |              2 |
+--------+----------------+
3 rows in set
```

## 比较MySQL CHAR值

存储或比较`CHAR`值时，MySQL使用分配给列的字符集排序规则。

使用比较运算符比较`CHAR`值时，MySQL不会考虑尾随空格，例如：`=`，`<>`，`>`，`<`等等。

> 请注意，当您使用`CHAR`值进行模式匹配时，`LIKE`运算符会考虑尾随空格。

在前面的例子中，我们将值`Y`的前面和后面都加空格存储。 但是，当执行以下查询时：

```sql
mysql> SELECT 
    *
FROM
    mysql_char_test
WHERE
    status = 'Y';
Empty set
```

MySQL没有返回任何行记录，因为它不考虑尾随空格。 要与“`Y`”匹配，需要删除尾随空格：

```sql
SELECT 
    *
FROM
    mysql_char_test
WHERE
    status = ' Y';
```

执行上面查询语句，得到以下结果 -

```sql
+--------+
| status |
+--------+
|  Y     |
+--------+
1 row in set
```

## MySQL CHAR和UNIQUE索引

如果`CHAR`列具有`UNIQUE`索引，并且插入的值有多个尾随空格不同的值，则MySQL将拒绝因重复键错误而要求您进行的更改。

**请参见以下示例**

首先，为`mysql_char_test`表的`status`列[创建唯一的索引](http://www.yiibai.com/mysql/unique.html)。

```sql
CREATE UNIQUE INDEX uidx_status ON mysql_char_test(status);
```

其次，在`mysql_char_test`表中[插入一行](http://www.yiibai.com/mysql/insert-statement.html)。

```sql
INSERT INTO mysql_char_test(status)
VALUES('N ');

Error Code: 1062. Duplicate entry 'N' for key 'uidx_status'
```

在本教程中，我们向您介绍了MySQL `CHAR`数据类型及其功能。 现在，相信您应该很好地了解`CHAR`数据类型，以将其应用于数据库设计。