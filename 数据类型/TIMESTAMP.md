在本教程中，您将了解MySQL `TIMESTAMP`和`TIMESTAMP`列的功能，如使用时间戳自动初始化和更新。

## MySQL TIMESTAMP简介

MySQL `TIMESTAMP`是一种保存[日期](http://www.yiibai.com/mysql/date.html)和[时间](http://www.yiibai.com/mysql/time.html)组合的时间[数据类型](http://www.yiibai.com/mysql/data-types.html)。 `TIMESTAMP`列的格式为`YYYY-MM-DD HH:MM:SS`，固定为`19`个字符。

`TIMESTAMP`值的范围从`'1970-01-01 00:00:01'` UTC到`'2038-01-19 03:14:07'` UTC。

当您将`TIMESTAMP`值[插入](http://www.yiibai.com/mysql/insert-statement.html)到表中时，MySQL会将其从连接的时区转换为UTC后进行存储。 当您[查询](http://www.yiibai.com/mysql/select-statement-query-data.html)`TIMESTAMP`值时，MySQL会将UTC值转换回连接的时区。请注意，对于其他时间数据类型(如[DATETIME](http://www.yiibai.com/mysql/datetime.html))，不会进行此转换。

当检索由不同时区中的客户端插入`TIMESTAMP`值时，将获得存储数据库中不同的值。 只要不更改时区，就可以获得与存储的相同的`TIMESTAMP`值。

**MySQL TIMESTAMP时区示例**

我们来看一个例子来看看MySQL如何处理`TIMESTAMP`值。

**首先**，创建一个名为`test_timestamp`的新表，该表具有一列：`t1`，其数据类型为`TIMESTAMP`;

```sql
USE testdb;
CREATE TABLE IF NOT EXISTS test_timestamp (
    t1  TIMESTAMP
);
```

**其次**，使用`SET time_zone`语句将时区设置为`"+00：00"`UTC。

```sql
SET time_zone='+00:00';
```

**第三**，将`TIMESTAMP`值插入到`test_timestamp`表中。

```sql
INSERT INTO test_timestamp 
VALUES('2018-01-01 00:00:01');
```

**第四**，从`test_timestamp`表中查询选择`TIMESTAMP`值。

```sql
SELECT 
    t1
FROM
    test_timestamp;

+---------------------+
| t1                  |
+---------------------+
| 2018-01-01 00:00:01 |
+---------------------+
1 row in set
```

**第五**，将会话时区设置为不同的时区，以查看从数据库服务器返回的值：

```sql
SET time_zone ='+03:00';

SELECT t1
FROM test_timestamp;
```

执行上面查询语句，得到以下结果 -

```sql
+---------------------+
| t1                  |
+---------------------+
| 2018-01-01 03:00:01 |
+---------------------+
1 row in set
```

如上面所见，查询结果集中为调整到新时区的不同时间值。

## 将TIMESTAMP列的自动初始化和更新

下面，我们从一个例子开始。以下语句创建一个名为`categories`的表：

```sql
USE testdb;

CREATE TABLE categories (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

在类别表中，`created_at`列是一个`TIMESTAMP`列，其默认值设置为`CURRENT_TIMESTAMP`。
以下语句向`categories`表中插入一个新行，而不指定`created_at`列的值：

```sql
INSERT INTO categories(name) 
VALUES ('A');

SELECT 
    *
FROM
    categories;
```

执行上面查询语句，得到以下结果 -

```sql
+----+------+---------------------+
| id | name | created_at          |
+----+------+---------------------+
|  1 | A    | 2017-07-25 21:52:46 |
+----+------+---------------------+
1 row in set
```

可以看到，MySQL使用时间戳(在插入行时)为`created_at`列初始化。

因此，`TIMESTAMP`列可以自动初始化为指定列插入行的当前时间戳作为一个值。此功能称为自动初始化。

我们将添加一个名为`updated_at`，数据类型为`TIMESTAMP`的新列到`categories`表中。

```sql
ALTER TABLE categories
ADD COLUMN updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP;
```

`updated_at`列的默认值为`CURRENT_TIMESTAMP`。 但是，在`DEFAULT CURRENT_TIMESTAMP`子句之后又有一个子句`ON UPDATE CURRENT_TIMESTAMP`。下面我们来看看它表示什么意思。

以下语句将一个新行插入到`categories`表中。

```sql
INSERT INTO categories(name)
VALUES('B');

SELECT * FROM categories;
```

执行上面查询语句，得到以下结果 -

```sql
+----+------+---------------------+---------------------+
| id | name | created_at          | updated_at          |
+----+------+---------------------+---------------------+
|  1 | A    | 2017-07-25 21:52:46 | 2017-07-25 21:53:16 |
|  2 | B    | 2017-07-25 21:53:25 | 2017-07-25 21:53:25 |
+----+------+---------------------+---------------------+
2 rows in set
```

`created_at`列的默认值是插入行的时间戳。

现在，我们更新`id=2`的行的`name`列中的值，并从`categories`表更新查询数据。

```sql
UPDATE categories 
SET 
    name = 'B+'
WHERE
    id = 2;
```

查询更新结果 -

```sql
SELECT 
    *
FROM
    categories
WHERE
    id = 2;
```

执行上面查询语句，得到以下结果 -

```sql
+----+------+---------------------+---------------------+
| id | name | created_at          | updated_at          |
+----+------+---------------------+---------------------+
|  2 | B    | 2017-07-25 21:53:25 | 2017-07-25 21:53:25 |
+----+------+---------------------+---------------------+
1 row in set
```

请注意，`updated_at`列中的值在更新行时自动更改了时间戳。

当行中任何其他列中的值从其当前值更改时，`TIMESTAMP`列的功能将自动更新为当前时间戳，这种行为称为**自动更新**。

`updated_at`列被称为自动更新列。

> 请注意，如果执行`UPDATE`语句以更新`name`列的相同值，则`updated_at`列将不会更新。

```sql
UPDATE categories 
SET 
    name = 'B+'
WHERE
    id = 2;
```

`updated_at`列的值保持不变。

有关自动初始化和更新的更多信息，请查看MySQL网站上的[时间初始化](http://dev.mysql.com/doc/refman/5.7/en/timestamp-initialization.html)。

> 从*MySQL 5.6.5*开始，`DATETIME`列还具有自动初始化和更新功能。此外，`DEFAULT_CURRENT_TIMESTAMP`和`ON UPDATE CURRENT TIMESTAMP`可以应用于多个列。

在本教程中，我们向您介绍了MySQL `TIMESTAMP`数据类型，并向您展示了如何使用`TIMESTAMP`列的自动初始化和更新功能。