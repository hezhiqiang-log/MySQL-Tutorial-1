本教程将向您展示如何使用MySQL `BOOLEAN`数据类型来存储布尔值：`true`和`false`。

## MySQL BOOLEAN数据类型简介

MySQL没有内置的布尔类型。 但是它使用[TINYINT(1)](http://www.yiibai.com/mysql/int.html)。 为了更方便，MySQL提供`BOOLEAN`或`BOOL`作为`TINYINT(1)`的同义词。

在MySQL中，`0`被认为是`false`，非零值被认为是`true`。 要使用布尔文本，可以使用常量`TRUE`和`FALSE`来分别计算为`1`和`0`。 请参阅以下示例：

```sql
SELECT true, false, TRUE, FALSE, True, False;
-- 1 0 1 0 1 0
```

执行上面代码，得到以下结果 -

```sql
mysql> SELECT true, false, TRUE, FALSE, True, False;
+------+-------+------+-------+------+-------+
| TRUE | FALSE | TRUE | FALSE | TRUE | FALSE |
+------+-------+------+-------+------+-------+
|    1 |     0 |    1 |     0 |    1 |     0 |
+------+-------+------+-------+------+-------+
1 row in set
```

## MySQL BOOLEAN示例

MySQL将布尔值作为整数存储在表中。为了演示，让我们来看下面的`tasts`表：

```sql
USE testdb;

CREATE TABLE tasks (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    completed BOOLEAN
);
```

上面创建表语句中，即使将`completed`列指定为`BOOLEAN`类型，当[显示表定义](http://www.yiibai.com/mysql/show-tables.html)时，它是却是`TINYINT(1)`类型，如下所示：

```sql
DESCRIBE tasks;
```

以下语句向`tasts`表中插入`2`行数据：

```sql
INSERT INTO tasks(title,completed)
VALUES('Master MySQL Boolean type',true),
      ('Design database table',false);
```

在将数据保存到布尔列之前，MySQL将其转换为`1`或`0`，以下查询从`tasks`表中检索数据：

```sql
SELECT 
    id, title, completed
FROM
    tasks; 

+----+---------------------------+-----------+
| id | title                     | completed |
+----+---------------------------+-----------+
|  1 | Master MySQL Boolean type |         1 |
|  2 | Design database table     |         0 |
+----+---------------------------+-----------+
2 rows in set
```

如上所见， `true` 和 `false` 分别被转换为`1`和`0`。

因为`Boolean`类型是`TINYINT(1)`的同义词，所以可以在布尔列中插入`1`和`0`以外的值。如下示例：

```sql
INSERT INTO tasks(title,completed)
VALUES('Test Boolean with a number',2);
```

上面语句，工作正常~，查询`tasts`表中的数据，如下所示 -

```sql
mysql> SELECT 
    id, title, completed
FROM
    tasks; 
+----+----------------------------+-----------+
| id | title                      | completed |
+----+----------------------------+-----------+
|  1 | Master MySQL Boolean type  |         1 |
|  2 | Design database table      |         0 |
|  3 | Test Boolean with a number |         2 |
+----+----------------------------+-----------+
3 rows in set
```

如果要将结果输出为`true`和`false`，可以使用[IF](http://www.yiibai.com/mysql/if-function.html)函数，如下所示：

```sql
SELECT 
    id, 
    title, 
    IF(completed, 'true', 'false') completed
FROM
    tasks;
```

执行上面查询语句，得到结果如下所示 -

```sql
+----+----------------------------+-----------+
| id | title                      | completed |
+----+----------------------------+-----------+
|  1 | Master MySQL Boolean type  | true      |
|  2 | Design database table      | false     |
|  3 | Test Boolean with a number | true      |
+----+----------------------------+-----------+
3 rows in set
```

## MySQL BOOLEAN运算符

要在`tasts`表中获取所有完成的任务，可以执行以下查询：

```sql
SELECT 
    id, title, completed
FROM
    tasks
WHERE
    completed = TRUE;
```

执行上面查询语句，得到结果如下所示 -

```sql
+----+---------------------------+-----------+
| id | title                     | completed |
+----+---------------------------+-----------+
|  1 | Master MySQL Boolean type |         1 |
+----+---------------------------+-----------+
1 row in set
```

如您所见，它只返回`completed`列的值为`1`的任务。要解决它，必须使用`IS`运算符：

```sql
SELECT 
    id, title, completed
FROM
    tasks
WHERE
    completed IS TRUE;
```

执行上面查询语句，得到结果如下所示 -

```sql
+----+----------------------------+-----------+
| id | title                      | completed |
+----+----------------------------+-----------+
|  1 | Master MySQL Boolean type  |         1 |
|  3 | Test Boolean with a number |         2 |
+----+----------------------------+-----------+
2 rows in set
```

在这个例子中，我们使用`IS`运算符来测试一个与布尔值的值。

要获得待处理(未完成)的任务，请使用`IS FALSE`或`IS NOT TRUE`，如下所示：

```sql
SELECT 
    id, title, completed
FROM
    tasks
WHERE
    completed IS NOT TRUE;
```

执行上面查询语句，得到结果如下所示 -

```sql
+----+-----------------------+-----------+
| id | title                 | completed |
+----+-----------------------+-----------+
|  2 | Design database table |         0 |
+----+-----------------------+-----------+
1 row in set
```

在本教程中，您已经学习了如何使用MySQL `BOOLEAN`数据类型(它是`TINYINT(1)`的同义词)，以及如何操作布尔值。