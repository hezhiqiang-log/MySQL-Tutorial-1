在本教程中，您将学习如何使用MySQL `INSERT IGNORE`语句将数据插入到表中。

## 1. MySQL INSERT IGNORE语句简介

当使用[INSERT语句](http://www.yiibai.com/mysql/insert-statement.html)向表中添加一些行数据并且在处理期间发生错误时，`INSERT`语句将被中止，并返回错误消息。因此，可能不会向表中没有插入任何行。

但是，如果使用`INSERT INGORE`语句，则会忽略导致错误的行，并将其余行插入到表中。

`INSERT INGORE`语句的语法如下：

```sql
INSERT IGNORE INTO table(column_list)
VALUES( value_list),
      ( value_list),
      ...
```

> 请注意，`IGNORE`子句是MySQL对SQL标准的扩展。

## 2. MySQL INSERT IGNORE示例

为了演示，我们将[创建](http://www.yiibai.com/mysql/create-table.html)一个名为订阅者(`subscribers`)的新表。

```sql
USE testdb;

CREATE TABLE IF NOT EXISTS subscribers (
    id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(50) NOT NULL UNIQUE
);
```

[UNIQUE约束](http://www.yiibai.com/mysql/unique-constraint.html)确保电子邮件列中不存在重复的电子邮件。

以下语句在`subscribers`表中插入一个新行：

```sql
INSERT INTO subscribers(email)
VALUES('yiibai.com@gmail.com');
```

上面语句它按预期那样工作。接下来，我们来执行另一个语句，将两行插入到`subscribers`者表中：

```sql
INSERT INTO subscribers(email)
VALUES('yiibai.com@gmail.com'), 
      ('jane.max@ibm.com');
```

它将返回一个错误：

```sql
1062 - Duplicate entry 'yiibai.com@gmail.com' for key 'email'
```

如错误消息中所示，电子邮件`yiibai.com@gmail.com`值重复而导致违反`UNIQUE`约束。

但是，如果您使用`INSERT IGNORE`语句，则其它语句将会继续执行 -

```sql
INSERT IGNORE INTO subscribers(email)
VALUES('yiibai.com@gmail.com'), 
      ('jane.max@ibm.com');
```

MySQL服务器返回一条消息，显示插入一行，另一行被忽略。

```sql
Query OK, 1 row affected
Records: 2  Duplicates: 1  Warnings: 1
```

如果使用`SHOW WARNINGS`语句，就会发现警告的详细信息：

```sql
mysql> SHOW WARNINGS;
+---------+------+--------------------------------------------------------+
| Level   | Code | Message                                                |
+---------+------+--------------------------------------------------------+
| Warning | 1062 | Duplicate entry 'yiibai.com@gmail.com' for key 'email' |
+---------+------+--------------------------------------------------------+
2 rows in set
```

所以当使用`INSERT IGNORE`语句来执行插入数据时，MySQL只发出警告而不是发出错误，以防发生错误退出其它数据无法插入。

如果查询`subscribers`表中的数据，就会发现实际只有一行数据插入，导致错误的行被忽略。

```sql
mysql> select * from subscribers;
+----+----------------------+
| id | email                |
+----+----------------------+
|  4 | jane.max@ibm.com     |
|  1 | yiibai.com@gmail.com |
+----+----------------------+
2 rows in set
```

## 3. MySQL INSERT IGNORE和STRICT模式

当`STRICT`模式打开时，如果您尝试将无效值插入到表中，MySQL将返回错误并中止`INSERT`语句。

但是，如果使用`INSERT IGNORE`语句，则MySQL将发出警告而不是错误。 此外，它将尝试调整值以使其在插入表之前有效。

请来看看以下示例。

首先，我们创建一个名为`tokens`的新表：

```sql
USE yiibaidb;

CREATE TABLE IF NOT EXISTS tokens (
    s VARCHAR(6)
);
```

在此表中，列只接受长度小于或等于`6`的字符串。接下来，如果将长度为`7`的字符串插入到`tokens`表中，那么会发生什么？

```sql
INSERT INTO tokens VALUES('abcdefg');
```

由于`STRICT`模式已打开，MySQL发出以下错误 -

```sql
mysql> INSERT INTO tokens VALUES('abcdefg');
1406 - Data too long for column 's' at row 1
```

现在，使用`INSERT IGNORE`语句重新插入相同的字符串。

```sql
INSERT IGNORE INTO tokens VALUES('abcdefg');
```

将MySQL截断的数据插入到`tokens`表中。 此外，它发出警告。使用 `SHOW WARNINGS` 查看错误提示 -

```sql
mysql> SHOW WARNINGS;
+---------+------+----------------------------------------+
| Level   | Code | Message                                |
+---------+------+----------------------------------------+
| Warning | 1265 | Data truncated for column 's' at row 1 |
+---------+------+----------------------------------------+
1 row in set
```

在本教程中，您学习了如何使用MySQL `INSERT IGNORE`语句将行插入到表中，并忽略导致错误的行的错误。