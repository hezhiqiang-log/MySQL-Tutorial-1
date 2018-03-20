在本教程中，我们将向您介绍MySQL `DATE`数据类型，并演示一些有用的日期函数来有效处理日期数据。

## MySQL DATE数据类型简介

MySQL `DATE`是用于管理日期值的五种时间[数据类型](http://www.yiibai.com/mysql/data-types.html)之一。 MySQL使用`yyyy-mm-dd`格式存储日期值。此格式是固定的，不可能更改它。

例如，您可能更喜欢使用`mm-dd-yyyy`格式，但是遗憾，不能直接使用。 一个代替的办法：遵循标准日期格式，并使用[DATE_FORMAT](http://www.yiibai.com/mysql/date_format.html)函数按所需格式来格式化日期。

MySQL使用`3`个字节来存储`DATE`值。`DATE`值的范围为`1000-01-01`到`9999-12-31`。 如果要存储超出此范围的日期值，则需要使用非时间数据类型，例如整数，例如使用三列，分别存储年，月和日的数据。还需要创建[存储函数](http://www.yiibai.com/mysql/stored-function.html)来模拟MySQL提供的内置日期函数，这是不推荐的。

当严格模式被禁用时，MySQL将任何无效日期(例如`2015-02-30`)转换为零日期值`0000-00-00`。

## MySQL日期值为两位数年份

MySQL使用四位数字存储日期值的年份。 如果您使用两位数的年份值，MySQL仍会接受以下规则：

- 年份值在`00-69`范围内转换为`2000-2069`。
- `70-99`的年值被转换为`1970 - 1999`年。

但是，具有两位数字的日期值是不明确的，因此您应避免使用它。

现在，让我们来看下面的例子。

首先，创建一个名为`people`表，其生日(`birth_date`)列使用`DATE`数据类型。

```sql
USE testdb;

CREATE TABLE people (
    id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    birth_date DATE NOT NULL
);
```

接下来，将一行[插入](http://www.yiibai.com/mysql/insert-statement.html)到`people`表中。

```sql
INSERT INTO people(first_name,last_name,birth_date)
VALUES('Max','Su','1992-10-11');
```

然后，查询`people`表中的数据，如下所示 -

```sql
SELECT 
    first_name, 
    last_name, 
    birth_date
FROM
    people;
```

执行上面查询语句，得到以下结果 -

```sql
+------------+-----------+------------+
| first_name | last_name | birth_date |
+------------+-----------+------------+
| Max        | Su        | 1992-10-11 |
+------------+-----------+------------+
1 row in set
```

之后，使用两位数的年份格式将数据插入到`people`表中。

```sql
INSERT INTO people(first_name,last_name,birth_date)
VALUES('Jack','Daniel','01-09-01'),
      ('Lily','Bush','80-09-01');
```

在第一行，我们使用`01`(范围在`00-69`)作为年份，所以MySQL将其转换为`2001`年。在第二行，我们使用`80`(范围`70-99`)作为年份，MySQL将其转换为`1980`年。

最后，从`people`表查询数据，以检查数据是否根据转换规则进行转换。

```sql
SELECT 
    first_name, 
    last_name, 
    birth_date
FROM
    people;
```

执行上面查询语句，得到以下结果 -

```sql
+------------+-----------+------------+
| first_name | last_name | birth_date |
+------------+-----------+------------+
| Max        | Su        | 1992-10-11 |
| Jack       | Daniel    | 2001-09-01 |
| Lily       | Bush      | 1980-09-01 |
+------------+-----------+------------+
3 rows in set
```

## MySQL DATE函数

MySQL提供了许多有用的日期功能，可以有效地操作日期。

要获取当前日期和时间，请使用[NOW()](http://www.yiibai.com/mysql/now.html)函数。

```sql
SELECT NOW() as cur_datetime;
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT NOW() as cur_datetime;
+---------------------+
| cur_datetime        |
+---------------------+
| 2017-07-25 21:51:54 |
+---------------------+
1 row in set
```

要获取[DATETIME](http://www.yiibai.com/mysql/datetime.html)值的日期部分，可以使用`DATE()`函数。

```sql
SELECT DATE(NOW());
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT DATE(NOW());
+-------------+
| DATE(NOW()) |
+-------------+
| 2017-07-25  |
+-------------+
1 row in set
```

要获取当前的系统日期，可以使用[CURDATE()](http://www.yiibai.com/mysql/curdate.html)函数，如下所示：

```sql
SELECT CURDATE();
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT CURDATE();
+------------+
| CURDATE()  |
+------------+
| 2017-07-25 |
+------------+
1 row in set
```

要格式化日期值，可以使用[DATE_FORMAT](http://www.yiibai.com/mysql/date_format.html)函数。以下语句使用日期格式模式`％m/％d/％Y`，格式化日期为：`mm/dd/yyyy`：

```sql
SELECT DATE_FORMAT(CURDATE(), '%m/%d/%Y') today;
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT DATE_FORMAT(CURDATE(), '%m/%d/%Y') today;
+------------+
| today      |
+------------+
| 07/25/2017 |
+------------+
1 row in set
```

要计算两个日期值之间的天数，可以使用[DATEDIFF](http://www.yiibai.com/mysql/datediff.html)函数，如下所示：

```sql
SELECT DATEDIFF('2015-11-04','2014-11-04') days;
```

执行上面查询语句，得到以下结果 -

```

```

要添加几天，几周，几个月，几年等到一个日期值，可以使用[DATE_ADD](http://www.yiibai.com/mysql/date_add.html)函数：

```sql
SELECT 
    '2018-01-01' start,
    DATE_ADD('2018-01-01', INTERVAL 1 DAY) 'one day later',
    DATE_ADD('2018-01-01', INTERVAL 1 WEEK) 'one week later',
    DATE_ADD('2018-01-01', INTERVAL 1 MONTH) 'one month later',
    DATE_ADD('2018-01-01', INTERVAL 1 YEAR) 'one year later';
```

执行上面查询语句，得到以下结果 -

```sql
+------------+---------------+----------------+-----------------+----------------+
| start      | one day later | one week later | one month later | one year later |
+------------+---------------+----------------+-----------------+----------------+
| 2018-01-01 | 2018-01-02    | 2018-01-08     | 2018-02-01      | 2019-01-01     |
+------------+---------------+----------------+-----------------+----------------+
1 row in set
```

类似地，可以使用[DATE_SUB](http://www.yiibai.com/mysql/date_sub.html)函数从日期中减去[间隔](http://www.yiibai.com/mysql/interval.html)值：

```sql
SELECT 
    '2018-01-01' start,
    DATE_SUB('2018-01-01', INTERVAL 1 DAY) 'one day before',
    DATE_SUB('2018-01-01', INTERVAL 1 WEEK) 'one week before',
    DATE_SUB('2018-01-01', INTERVAL 1 MONTH) 'one month before',
    DATE_SUB('2018-01-01', INTERVAL 1 YEAR) 'one year before';
```

执行上面查询语句，得到以下结果 -

```sql
+------------+----------------+-----------------+------------------+-----------------+
| start      | one day before | one week before | one month before | one year before |
+------------+----------------+-----------------+------------------+-----------------+
| 2018-01-01 | 2017-12-31     | 2017-12-25      | 2017-12-01       | 2017-01-01      |
+------------+----------------+-----------------+------------------+-----------------+
1 row in set
```

如果要获取日期值的日期，月份，季度和年份，可以使用相应的函数：[DAY](http://www.yiibai.com/mysql/day.html)，[MONTH](http://www.yiibai.com/mysql/month.html)，`QUARTER`和[YEAR](http://www.yiibai.com/mysql/year.html)，如下所示：

```sql
SELECT DAY('2018-12-31') day, 
       MONTH('2018-12-31') month, 
       QUARTER('2018-12-31') quarter, 
       YEAR('2018-12-31') year;
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT DAY('2018-12-31') day, 
       MONTH('2018-12-31') month, 
       QUARTER('2018-12-31') quarter, 
       YEAR('2018-12-31') year;
+-----+-------+---------+------+
| day | month | quarter | year |
+-----+-------+---------+------+
|  31 |    12 |       4 | 2018 |
+-----+-------+---------+------+
1 row in set
```

获得周信息周相关功能。例如，[WEEK](http://www.yiibai.com/mysql/week.html)函数返回周数，`WEEKDAY`函数返回工作日索引，`WEEKOFYEAR`函数返回周日历。

```sql
SELECT 
    WEEKDAY('2018-12-31') weekday,
    WEEK('2018-12-31') week,
    WEEKOFYEAR('2018-12-31') weekofyear;
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT 
    WEEKDAY('2018-12-31') weekday,
    WEEK('2018-12-31') week,
    WEEKOFYEAR('2018-12-31') weekofyear;
+---------+------+------------+
| weekday | week | weekofyear |
+---------+------+------------+
|       0 |   52 |          1 |
+---------+------+------------+
1 row in set
```

如果没有传递第二个参数，或者如果传递参数为`0`，则`week`函数将返回带有零的索引的周数。如果传递参数为`1`，则将返回`1`索引的周数。

```sql
SELECT 
    WEEKDAY('2018-12-31') weekday,
    WEEK('2018-12-31',1) week,
    WEEKOFYEAR('2018-12-31') weekofyear;
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT 
    WEEKDAY('2018-12-31') weekday,
    WEEK('2018-12-31',1) week,
    WEEKOFYEAR('2018-12-31') weekofyear;
+---------+------+------------+
| weekday | week | weekofyear |
+---------+------+------------+
|       0 |   53 |          1 |
+---------+------+------------+
1 row in set
```

在本教程中，您已经了解并学习了MySQL `DATE`数据类型以及如何使用一些有用的日期函数来操作日期值。