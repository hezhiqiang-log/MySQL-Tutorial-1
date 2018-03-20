本教程将向您介绍MySQL `VARCHAR`数据类型，并讨论`VARCHAR`的一些重要功能。

## MySQL VARCHAR数据类型简介

MySQL `VARCHAR`是可变长度的字符串，其长度可以达到`65,535`个字符。 MySQL将`VARCHAR`值作为`1`字节或`2`字节长度前缀加上实际数据。

长度前缀指定值的字节数。 如果列需要少于`255`个字节，则长度前缀为`1`个字节。 如果列需要超过`255`个字节，长度前缀是两个长度字节。

但是，最大长度受到最大行大小(`65,535`字节)和所使用的[字符集](http://www.yiibai.com/mysql/character-set.html)的限制。 这意味着所有列的总长度应该小于`65,535`字节。

下面我们来看一个例子。

[创建一个新的表](http://www.yiibai.com/mysql/create-table.html)，它有两列`s1`和`s2`，长度分别为`32765`(长度前缀为`+2`)和`32766(+2)`。注意，`32765 + 2 + 32766 + 2 = 65535`，这是最大行大小。

```sql
USE testdb;

CREATE TABLE IF NOT EXISTS varchar_test (
    s1 VARCHAR(32765) NOT NULL,
    s2 VARCHAR(32766) NOT NULL
)  CHARACTER SET 'latin1' COLLATE LATIN1_DANISH_CI;
```

该语句成功创建了表。 但是，如果我们将`s1`列的长度增加`1`。

```sql
USE testdb;

CREATE TABLE IF NOT EXISTS varchar_test_2 (
    s1 VARCHAR(32766) NOT NULL, -- error
    s2 VARCHAR(32766) NOT NULL
)  CHARACTER SET 'latin1' COLLATE LATIN1_DANISH_CI;
```

MySQL将发出错误消息：

```sql
Error Code: 1118. Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs 0.000 sec
```

如上所示，行长度太大，所以创建语句失败。

如果插入长度大于`VARCHAR`列长度的字符串，MySQL将发出错误。 请考虑以下示例：

```sql
USE testdb;

CREATE TABLE items (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(3)
);

INSERT INTO items(title)
VALUES('ABCD');
```

在这个例子中，MySQL发出以下错误消息：

```sql
1406 - Data too long for column 'title' at row 1
```

## MySQL VARCHAR和空格

当MySQL存储`VARCHAR`值时，MySQL不会占用空间。 此外，MySQL在插入或选择`VARCHAR`值时保留尾随空格。请参阅以下示例：

```sql
INSERT INTO items(title)
VALUES('AB ');
```

查询插入字符串值的长度，如下所示 -

```sql
SELECT 
    id, title, length(title)
FROM
    items;

+----+-------+---------------+
| id | title | length(title) |
+----+-------+---------------+
|  1 | AB    |             3 |
+----+-------+---------------+
1 row in set
```

但是，当插入包含导致列长度超过的尾随空格的`VARCHAR`值时，MySQL将截断尾随空格。 此外，MySQL发出警告。我们来看下面的例子：

```sql
INSERT INTO items(title)
VALUES('ABC ');
```

此语句将长度为`4`的字符串插入标`title`列。 MySQL仍然插入字符串，但是在插入该值之前会截断尾随空格。

```sql
row(s) affected, 1 warning(s): 1265 Data truncated for column 'title' at row 1
```

可以使用以下查询来验证它：

```sql
SELECT 
    title, LENGTH(title)
FROM
    items;

+-------+---------------+
| title | LENGTH(title) |
+-------+---------------+
| AB    |             3 |
| ABC   |             3 |
+-------+---------------+
2 rows in set
```

在本教程中，您已经学习了如何使用MySQL `VARCHAR`数据类型来存储数据库中的可变长度字符串的值。