本教程将向您介绍如何存储和使用位值的MySQL `BIT`数据类型。

## MySQL BIT数据类型简介

MySQL提供了允许您存储位值的`BIT`类型。`BIT(m)`可以存储多达`m`位的值，`m`的范围在`1`到`64`之间。

如果省略，默认值为`1`。所以下列陈述是一样的：

```sql
column_name BIT(1);
```

以及，

```sql
column_name BIT;
```

要指定一个位值字面值，可使用`b'val'`或`0bval`来表示，该`val`是仅包含`0`和`1`的二进制值。

开头字符`b`可以写成`B`，例如，以下两种方式都一样：

```sql
b01
B11
```

上面书写方式都是有效的位字面量。

但是，前导`0b`是区分大小写的，所以不能使用`0B`。以下是无效的字面值：

```sql
0B'1000'
```

默认情况下，位值文字的字符集是二进制字符串，如下所示：

```sql
SELECT CHARSET(B''); -- binary
```

执行结果如下 -

```sql
mysql> SELECT CHARSET(B'');
+--------------+
| CHARSET(B'') |
+--------------+
| binary       |
+--------------+
1 row in set
```

## MySQL BIT示例

以下语句创建一个名为`working_calendar`的新表，其中的列类型和宽度指定为`BIT(7)`：

```sql
USE testdb;

CREATE TABLE working_calendar(
    y INT,
    w INT,
    days BIT(7),
    PRIMARY KEY(y,w)
);
```

`days`列中的值表示工作日或休息日，即`1`：表示工作日，`0`表示休息日。

假设`2017`年第一周的星期六和星期五不是工作日，可以在`working_calendar`表中[插入一行](http://www.yiibai.com/mysql/insert-statement.html)，如下所示：

```sql
INSERT INTO working_calendar(y,w,days)
VALUES(2017,1,B'1111100');
```

以下查询从`working_calendar`表检索数据，结果如下：

```sql
SELECT 
    y, w , days
FROM
    working_calendar;
```

执行上面查询语句，得到以下结果 -

```sql
+------+---+---------+
| y    | w | days    |
+------+---+---------+
| 2017 | 1 | 1111100 |
+------+---+---------+
1 row in set
```

如上所见，`days`列中的位值被转换成一个整数。要将其表示为位值，请使用`BIN`函数：

```sql
SELECT 
    y, w , bin(days)
FROM
    working_calendar;
```

执行上面查询语句，得到以下结果 -

```sql
+------+---+-----------+
| y    | w | bin(days) |
+------+---+-----------+
| 2017 | 1 | 1111100   |
+------+---+-----------+
1 row in set
```

如果将值插入到长度小于`m`位的`BIT(m)`列中，MySQL将在位值的左侧使用零填充。

假设第二周的第一天关闭，可以向`days`列中插入`01111100`值。 但是，`111100`的值也可以工作，因为MySQL将使用零填充左侧。

```sql
INSERT INTO working_calendar(y,w,days)
VALUES(2017,2,B'111100');
```

要查看数据，使用与上述相同的查询：

```sql
SELECT 
    y, w , bin(days)
FROM
    working_calendar;
```

执行上面查询语句，得到以下结果 -

```sql
+------+---+-----------+
| y    | w | bin(days) |
+------+---+-----------+
| 2017 | 1 | 1111100   |
| 2017 | 2 | 111100    |
+------+---+-----------+
2 rows in set
```

如您所见，MySQL返回结果之前删除了前导零。 要正确显示可以使用`LPAD`函数：

```sql
SELECT 
    y, w , lpad(bin(days),7,'0')
FROM
    working_calendar;
```

执行上面查询语句，得到以下结果 -

```sql
+------+---+-----------------------+
| y    | w | lpad(bin(days),7,'0') |
+------+---+-----------------------+
| 2017 | 1 | 1111100               |
| 2017 | 2 | 0111100               |
+------+---+-----------------------+
2 rows in set
```

现在工作正常了。

在本教程中，您已经学会了如何使用MySQL `BIT`类型来存储位值。