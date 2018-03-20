在本教程中，您将学习如何使用MySQL `ENUM`数据类型定义存储枚举值的列。

## MySQL ENUM数据类型简介

在MySQL中，`ENUM`是一个字符串对象，其值是从列创建时定义的允许值列表中选择的。

`ENUM`数据类型提供以下优点：

- 紧凑型数据存储，MySQL `ENUM`使用数字索引(1，2，3，…)来表示字符串值。
- 可读查询和输出。

要定义`ENUM`列，请使用以下语法：

```sql
CREATE TABLE table_name (
    ...
    col ENUM ('value1','value2','value3'),
    ...
);
```

在这种语法中，可以有三个以上的枚举值。但是，将枚举值的数量保持在`20`以下是一个很好的做法。

下面来看看看下面的例子。

假设我们必须存储优先级为：`low`, `medium` 和 `high` 的票据信息。 要将`priority`列分配给`ENUM`类型，请使用以下[CREATE TABLE](http://www.yiibai.com/mysql/create-table.html)语句：

```sql
USE testdb;

CREATE TABLE tickets (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    priority ENUM('Low', 'Medium', 'High') NOT NULL
);
```

`priority`列只接受三个`Low`, `Medium`, `High`值。 在后台，MySQL将每个枚举成员映射到数字索引。在这种情况下，`Low`，`Medium`和`High`分别映射到`1`,`2`和`3`(注意：与数组不同，这不是从`0`开始的)。

## 插入MySQL ENUM值

要将数据[插入](http://www.yiibai.com/mysql/insert-statement.html)到`ENUM`列中，可以使用预定义列表中的枚举值。 例如，以下语句在`tickets`表中插入一个新行。

```sql
INSERT INTO tickets(title, priority)
VALUES('Scan virus for computer A', 'High');
```

除了枚举值之外，还可以使用枚举成员的数字索引将数据插入`ENUM`列。 例如，以下语句插入优先级为`Low`的新机票数据：

```sql
INSERT INTO tickets(title, priority)
VALUES('Upgrade Windows OS for all computers', 1);
```

在这个例子中，我们使用的值为：`1`，而不是使用`Low`枚举值，因为`Low`被映射到`1`，这是可以接受的。

我们再向`ticketa`表添加一些行数据：

```sql
INSERT INTO tickets(title, priority)
VALUES('Install Google Chrome for Mr. John', 'Medium'),
      ('Create a new user for the new employee David', 'High');
```

因为我们将优先级定义为`NOT NULL`列，当插入新行而不指定优先级列的值时，MySQL将使用第一个枚举成员作为默认值。

参见下列语句声明：

```sql
INSERT INTO tickets(title)
VALUES('Refresh the computer of Ms. Lily');
```

在非严格的SQL模式下，如果在`ENUM`列中插入无效值，MySQL将使用空字符串`''`，插入数字索引为`0`。 如果启用了严格的SQL模式，尝试插入无效的`ENUM`值将导致错误。

> 请注意，如果`ENUM`列定义为可空列，则可以接受`NULL`值。

## 过滤MySQL ENUM值

以下语句查询获得所有高优先级机票：

```sql
SELECT 
    *
FROM
    tickets
WHERE
    priority = 'High';
```

执行上面查询语句，得到以下结果 -

```sql
+----+----------------------------------------------+----------+
| id | title                                        | priority |
+----+----------------------------------------------+----------+
|  1 | Scan virus for computer A                    | High     |
|  4 | Create a new user for the new employee David | High     |
+----+----------------------------------------------+----------+
2 rows in set
```

由于枚举成员’`High`‘映射到`3`，因此以下查询返回相同的结果集：

```sql
SELECT 
    *
FROM
    tickets
WHERE
    priority = 3;
```

也可以使用比较运算符来查询，比如 -

```sql
SELECT 
    *
FROM
    tickets
WHERE
    priority >= 2;
```

## 排序MySQL ENUM值

MySQL根据索引号[排序](http://www.yiibai.com/mysql/order-by.html)`ENUM`值。 因此，成员的顺序取决于它们在枚举列表中的定义。

以下查询选择门票并按优先级从高到低进行排序：

```sql
SELECT 
    title, priority
FROM
    tickets
ORDER BY priority DESC;
```

执行上面查询语句，得到以下结果 -

```sql
+----------------------------------------------+----------+
| title                                        | priority |
+----------------------------------------------+----------+
| Scan virus for computer A                    | High     |
| Create a new user for the new employee David | High     |
| Install Google Chrome for Mr. John           | Medium   |
| Upgrade Windows OS for all computers         | Low      |
| Refresh the computer of Ms. Lily             | Low      |
+----------------------------------------------+----------+
5 rows in set
```

在创建列时，按顺序定义枚举值是一个很好的做法。

## MySQL ENUM的缺点

MySQL `ENUM`有以下缺点：

- 更改枚举成员需要使用[ALTER TABLE](http://www.yiibai.com/mysql/alter-table.html)语句重建整个表，这在资源和时间方面是昂贵的。

- 获取完整的枚举列表很复杂，因为需要访问

  ```sql
  information_schema
  ```

  数据库：

  ```sql
  SELECT 
    column_type
  FROM
    information_schema.COLUMNS
  WHERE
    TABLE_NAME = 'tickets'
        AND COLUMN_NAME = 'priority';
  ```

- 迁移到其他RDBMS可能是一个问题，因为`ENUM`不是SQL标准的，并且数据库系统不支持它。

- 向枚举列表添加更多属性是不可能的。假设您要为每个优先级添加服务协议，例如`High(24h)`，`Medium(1-2天)`，`Low(1周)`，则不可以使用`ENUM`类型的。 在这种情况下，需要有一个单独的表来存储优先级列表，例如`priority(id，name，sort_order，description)`，并且通过引用了`priority`表的`id`字段的`priority_id`来替换`tickets`表中的`priority`字段。

- 与查找表(`priorities`)相比，枚举列表不可重用。 例如，如果要[创建](http://www.yiibai.com/mysql/create-table.html)一个名为`tasks`并且要重用优先级列表的新表，则是不可能的。

在本教程中，我们向您介绍了MySQL `ENUM`数据类型以及如何使用它来定义存储枚举值的列。