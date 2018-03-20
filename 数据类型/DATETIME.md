在本教程中，您将了解MySQL `DATETIME`数据类型以及如何使用一些方便的函数来有效地操作`DATETIME`类型数据。

## MySQL DATETIME数据类型简介

MySQL `DATETIME`存储包含[日期](http://www.yiibai.com/mysql/date.html)和[时间](http://www.yiibai.com/mysql/time.html)的值。 当您从`DATETIME`列[查询数据](http://www.yiibai.com/mysql/select-statement-query-data.html)时，MySQL会以以下格式显示`DATETIME`值：

```sql
YYYY-MM-DD HH:MM:SS
```

默认情况下，`DATETIME`的值范围为`1000-01-01 00:00:00`至`9999-12-31 23:59:59`。

`DATETIME`值使用`5`个字节进行存储。另外，`DATETIME`值可以包括格式为`YYYY-MM-DD HH:MM:SS [.fraction]`例如：`2017-12-20 10:01:00.999999`的尾数有小数秒。 当包含小数秒精度时，`DATETIME`值需要更多存储，如下表所示：

| 分数秒精度 | 存储(字节) |
| ---------- | ---------- |
| 0          | 0          |
| 1，2       | 1          |
| 3，4       | 2          |
| 5，6       | 3          |

例如，`2017-12-20 10:01:00.999999`需要`8`个字节，`2015-12-20 10:01:00`需要`5`个字节，`3`个字节为`.999999`，而`2017-12-20 10:01:00.9`只需要`6`个字节，小数秒精度为`1`字节。

> 请注意，在*MySQL 5.6.4*之前，`DATETIME`值需要`8`字节存储而不是`5`个字节。

## MySQL DATETIME与TIMESTAMP类型

MySQL提供了另一种类似于`DATETIME`，叫作[TIMESTAMP](http://www.yiibai.com/mysql/timestamp.html)的时间数据类型。

`TIMESTAMP`需要`4`个字节，而`DATETIME`需要`5`个字节。 `TIMESTAMP`和`DATETIME`都需要额外的字节，用于分数秒精度。

`TIMESTAMP`值范围从`1970-01-01 00:00:01 UTC`到`2038-01-19 03:14:07 UTC`。 如果要存储超过`2038`的时间值，则应使用`DATETIME`而不是`TIMESTAMP`。

MySQL将`TIMESTAMP`存储在`UTC`(有时区)值中。 但是，MySQL存储`DATETIME`值是没有时区的。下面来看看看下面的例子。

**首先**，将当前连接的时区设置为`+00:00`。

接下来，创建一个名为`timestamp_n_datetime`的表，它由两列组成：`ts`和`dt`，这两列分别使用`TIMESTAMP`和`DATETIME`类型，如以下语句 -

```sql
USE testdb;

CREATE TABLE timestamp_n_datetime (
    id INT AUTO_INCREMENT PRIMARY KEY,
    ts TIMESTAMP,
    dt DATETIME
);
```

然后，将当前日期和时间[插入](http://www.yiibai.com/mysql/insert-statement.html)到`timestamp_n_datetime`表的`ts`和`dt`列中，如下语句所示 -

```sql
INSERT INTO timestamp_n_datetime(ts,dt)
VALUES(NOW(),NOW());
```

之后，从`timestamp_n_datetime`表[查询数据](http://www.yiibai.com/mysql/select-statement-query-data.html)，如下语句所示 -

```sql
SELECT 
    ts, 
    dt
FROM
    timestamp_n_datetime;
```

执行上面查询语句，得到以下结果 -

```sql
+---------------------+---------------------+
| ts                  | dt                  |
+---------------------+---------------------+
| 2017-07-26 00:38:14 | 2017-07-26 00:38:14 |
+---------------------+---------------------+
1 row in set
```

`DATETIME`和`TIMESTAMP`列中的两个值相同。

最后，将连接的时区设置为`+03:00`，再次从`timestamp_n_datetime`表查询数据。

```sql
SET time_zone = '+03:00';

SELECT 
    ts, 
    dt
FROM
    timestamp_n_datetime;
```

执行上面查询语句，得到以下结果 -

```sql
+---------------------+---------------------+
| ts                  | dt                  |
+---------------------+---------------------+
| 2017-07-25 19:38:14 | 2017-07-26 00:38:14 |
+---------------------+---------------------+
1 row in set
```

可以看到，`ts`列为`TIMESTAMP`数据类型的值变了。这是因为在更改时区时，`TIMESTAMP`列以`UTC`为单位存储日期和时间值，根据新时区调整`TIMESTAMP`列的值。

这意味着如果使用`TIMESTAMP`数据来存储日期和时间值，则在将数据库移动到位于不同时区的服务器时时间的值可能不一样，所以应该认真考虑这个问题。

**MySQL DATETIME函数**

以下语句使用`NOW()`函数将变量`@dt`设置为当前日期和时间。

```sql
SET @dt =  NOW();
```

要查询`@dt`变量的值，请使用以下`SELECT`语句：

```sql
SELECT @dt;
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT @dt;
+---------------------+
| @dt                 |
+---------------------+
| 2017-07-25 19:41:14 |
+---------------------+
1 row in set
```

**MySQL DATE函数**

要从`DATETIME`值提取日期部分，请使用`DATE`函数，如下所示：

```sql
mysql> SELECT DATE(@dt);
+------------+
| DATE(@dt)  |
+------------+
| 2017-07-25 |
+------------+
1 row in set
```

如果希望根据日期查询数据，但是列中存储的数据是基于日期和时间，则此功能非常有用。

下面来看看看下面的例子。

```sql
USE testdb;

CREATE TABLE test_dt (
    id INT AUTO_INCREMENT PRIMARY KEY,
    created_at DATETIME
);

INSERT INTO test_dt(created_at)
VALUES('2017-11-05 20:29:36');
```

假设您想知道在`2017-11-05`当天创建的行，请使用以下查询：

```sql
SELECT 
    *
FROM
    test_dt
WHERE
    created_at = '2017-11-05';
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT 
    *
FROM
    test_dt
WHERE
    created_at = '2017-11-05';
Empty set
```

它不返回任何行记录。

这是因为`created_at`列不仅包含日期，还包含时间。要纠正它，请使用`DATE`函数，如下所示：

```sql
SELECT 
    *
FROM
    test_dt
WHERE
    DATE(created_at) = '2017-11-05';
```

执行上面查询语句，得到以下结果 -

```sql
+----+---------------------+
| id | created_at          |
+----+---------------------+
|  1 | 2017-11-05 20:29:36 |
+----+---------------------+
1 row in set
```

它按预期返回一行。 如果表有多行，MySQL必须执行全表扫描以查找与条件匹配的行。

**MySQL TIME函数**

要从`DATETIME`值中提取时间部分，可以使用`TIME`函数，如以下语句所示：

```sql
SELECT TIME(@dt);
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT TIME(@dt);
+-----------+
| TIME(@dt) |
+-----------+
| 19:41:14  |
+-----------+
1 row in set
```

**MySQL YEAR, QUARTER, MONTH, WEEK, DAY, HOUR，MINUTE和SECOND函数**

要从`DATETIME`值获取年，季，月，周，日，小时，分和秒，可以使用以下语句中所示的函数：

```sql
SET @dt =  NOW();
SELECT 
    HOUR(@dt),
    MINUTE(@dt),
    SECOND(@dt),
    DAY(@dt),
    WEEK(@dt),
    MONTH(@dt),
    QUARTER(@dt),
    YEAR(@dt);
```

执行上面查询语句，得到以下结果 -

```sql
+-----------+-------------+-------------+----------+-----------+------------+--------------+-----------+
| HOUR(@dt) | MINUTE(@dt) | SECOND(@dt) | DAY(@dt) | WEEK(@dt) | MONTH(@dt) | QUARTER(@dt) | YEAR(@dt) |
+-----------+-------------+-------------+----------+-----------+------------+--------------+-----------+
|        19 |          42 |          56 |       25 |        30 |          7 |            3 |      2017 |
+-----------+-------------+-------------+----------+-----------+------------+--------------+-----------+
1 row in set
```

**MySQL DATE_FORMAT函数**

要格式化`DATETIME`值，可以使用`DATE_FORMAT`函数。 例如，以下语句基于`%H:%i:%s - %W%M%Y`格式来格式化`DATETIME`值：

```sql
SET @dt =  NOW();
SELECT DATE_FORMAT(@dt, '%H:%i:%s - %W %M %Y');
```

执行上面查询语句，得到以下结果 -

```sql
+-----------------------------------------+
| DATE_FORMAT(@dt, '%H:%i:%s - %W %M %Y') |
+-----------------------------------------+
| 19:43:10 - Tuesday July 2017            |
+-----------------------------------------+
1 row in set
```

**MySQL DATE_ADD函数**

要将[间隔](http://www.yiibai.com/mysql/interval.html)添加到`DATETIME`值，请使用[DATE_ADD](http://www.yiibai.com/mysql/date_add.html)函数，如下所示：

```sql
SET @dt =  NOW();
SELECT @dt start, 
       DATE_ADD(@dt, INTERVAL 1 SECOND) '1 second later',
       DATE_ADD(@dt, INTERVAL 1 MINUTE) '1 minute later',
       DATE_ADD(@dt, INTERVAL 1 HOUR) '1 hour later',
       DATE_ADD(@dt, INTERVAL 1 DAY) '1 day later',
       DATE_ADD(@dt, INTERVAL 1 WEEK) '1 week later',
       DATE_ADD(@dt, INTERVAL 1 MONTH) '1 month later',
       DATE_ADD(@dt, INTERVAL 1 YEAR) '1 year later';
```

执行上面查询语句，得到以下结果 -

```sql
+---------------------+---------------------+---------------------+---------------------+---------------------+---------------------+---------------------+---------------------+
| start               | 1 second later      | 1 minute later      | 1 hour later        | 1 day later         | 1 week later        | 1 month later       | 1 year later        |
+---------------------+---------------------+---------------------+---------------------+---------------------+---------------------+---------------------+---------------------+
| 2017-07-25 19:43:22 | 2017-07-25 19:43:23 | 2017-07-25 19:44:22 | 2017-07-25 20:43:22 | 2017-07-26 19:43:22 | 2017-08-01 19:43:22 | 2017-08-25 19:43:22 | 2018-07-25 19:43:22 |
+---------------------+---------------------+---------------------+---------------------+---------------------+---------------------+---------------------+---------------------+
1 row in set
```

**MySQL DATE_SUB函数**

要从`DATETIME`值中减去一个间隔值，可以使用[DATE_SUB](http://www.yiibai.com/mysql/date_sub.html)函数，如下所示：

```sql
SET @dt =  NOW();
SELECT @dt start, 
       DATE_SUB(@dt, INTERVAL 1 SECOND) '1 second before',
       DATE_SUB(@dt, INTERVAL 1 MINUTE) '1 minute before',
       DATE_SUB(@dt, INTERVAL 1 HOUR) '1 hour before',
       DATE_SUB(@dt, INTERVAL 1 DAY) '1 day before',
       DATE_SUB(@dt, INTERVAL 1 WEEK) '1 week before',
       DATE_SUB(@dt, INTERVAL 1 MONTH) '1 month before',
       DATE_SUB(@dt, INTERVAL 1 YEAR) '1 year before';
```

执行上面查询语句，得到以下结果 -

```sql
+---------------------+---------------------+---------------------+---------------------+---------------------+---------------------+---------------------+---------------------+
| start               | 1 second before     | 1 minute before     | 1 hour before       | 1 day before        | 1 week before       | 1 month before      | 1 year before       |
+
```

**MySQL DATE_DIFF函数**

要计算两个`DATETIME`值之间的差值，可以使用[DATEDIFF](http://www.yiibai.com/mysql/datediff.html)函数。 请注意，`DATEDIFF`函数仅在计算中考虑`DATETIME`值的日期部分。

请参见以下示例。

**首先**，创建一个名为`datediff_test`的表，其中只有一个`dt`列，其数据类型为`DATETIME`。

```sql
USE testdb;
CREATE TABLE datediff_test (
    dt DATETIME
);
```

**其次**，将一些行插入到`datediff_test`表中。

```sql
INSERT INTO datediff_test(dt)
VALUES('2017-04-30 07:27:39'),
 ('2017-05-17 22:52:21'),
 ('2017-05-18 01:19:10'),
 ('2017-05-22 14:17:16'),
 ('2017-05-26 03:26:56'),
 ('2017-06-10 04:44:38'),
 ('2017-06-13 13:55:53');
```

**第三**，使用`DATEDIFF`函数将当前日期时间与`datediff_test`表的每一行中的值进行比较。

```sql
SELECT 
    dt, 
    DATEDIFF(NOW(), dt)
FROM
    datediff_test;
```

执行上面查询语句，得到以下结果 -

```sql
+---------------------+---------------------+
| dt                  | DATEDIFF(NOW(), dt) |
+---------------------+---------------------+
| 2017-04-30 07:27:39 |                  86 |
| 2017-05-17 22:52:21 |                  69 |
| 2017-05-18 01:19:10 |                  68 |
| 2017-05-22 14:17:16 |                  64 |
| 2017-05-26 03:26:56 |                  60 |
| 2017-06-10 04:44:38 |                  45 |
| 2017-06-13 13:55:53 |                  42 |
+---------------------+---------------------+
7 rows in set
```

在本教程中，您已经了解了MySQL `DATETIME`数据类型和一些有用的`DATETIME`函数。