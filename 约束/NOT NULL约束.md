本教程将向您介绍MySQL `NOT NULL`约束，帮助我们来保持数据的一致性。

## MySQL NOT NULL约束简介

`NOT NULL`约束是一个列约束，仅将列的值强制为非`NULL`值。

`NOT NULL`约束的语法如下：

```sql
column_name data_type NOT NULL;
```

列可能只包含一个`NOT NULL`约束，它指定列不能包含任何`NULL`值。

以下[CREATE TABLE](http://www.yiibai.com/mysql/create-table.html)语句创建`tasts`表：

```sql
USE testdb;
CREATE TABLE tasks (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    start_date DATE NOT NULL,
    end_date DATE
);
```

`title`和`start_date`列明确地显示`NOT NULL`约束。 `id`列具有[PRIMARY KEY](http://www.yiibai.com/mysql/primary-key.html)约束，因此它隐含地包含一个`NOT NULL`约束。

`end_date`列可以具有`NULL`值。这是因为当添加新任务记录时，我们可能还不知道任务的结束日期。

在表的每一列中都有`NOT NULL`约束是最好的做法，除非你有很好的理由不使用`NOT NULL`约束。

通常，`NULL`值使查询更加复杂。在这种情况下，可以使用`NOT NULL`约束并为列提供默认值。请参阅以下示例：

```sql
use testdb;
CREATE TABLE inventory (
    material_no VARCHAR(18),
    warehouse_no VARCHAR(10),
    quantity DECIMAL(19 , 2 ) NOT NULL DEFAULT 0,
    base_unit VARCHAR(10) NOT NULL,
    PRIMARY KEY (material_no , warehouse_no)
);
```

在此示例中，数量(`quantity`)列的默认值为`0`，因为当向`inventory`表添加一行数据时，数量(`quantity`)列的值应该为`0`，而不是`NULL`。

## 向现有列添加NOT NULL约束

通常，在创建表时向列添加`NOT NULL`约束。 但是，有时希望将`NOT NULL`约束添加到现有表的`NULL`列。 在这种情况下，可以使用以下步骤：

- 检查列的当前值。
- 将所有`NULL`值更新为非`NULL`值。
- 添加`NOT NULL`约束

下面来看一个例子。

我们将[数据](http://www.yiibai.com/mysql/insert-statement.html)插入到`tasks`表中以更好地演示。

```sql
INSERT INTO tasks(title ,start_date, end_date)
VALUES('Learn MySQL NOT NULL constraint', '2017-02-01','2017-02-02'),
      ('Check and update NOT NULL constraint to your database', '2017-02-01',NULL);
```

现在，假设要强制用户在创建新任务时给出估计的结束日期。为此，您需要将`NOT NULL`约束添加到`tasts`表的`end_date`列上。

首先检查`end_date`列的值，使用`IS NULL`运算符来检查列中的值是否有`NULL`值：

```sql
SELECT 
    *
FROM
    tasks
WHERE
    end_date IS NULL;
```

执行上面的查询语句，得到以下结果 -

```sql
+----+-------------------------------------------------------+------------+----------+
| id | title                                                 | start_date | end_date |
+----+-------------------------------------------------------+------------+----------+
|  2 | Check and update NOT NULL constraint to your database | 2017-02-01 | NULL     |
+----+-------------------------------------------------------+------------+----------+
1 row in set
```

查询返回`end_date`值为`NULL`的一行记录。

**其次**，将`NULL`值[更新](http://www.yiibai.com/mysql/update-data.html)为非`NULL`值。在这种情况下，我们可以创建一个规则，如果`end_date`为`NULL`，则在开始日期后一周结束日期。

```sql
UPDATE tasks 
SET 
    end_date = start_date + 7
WHERE
    end_date IS NULL;
```

我们来查看一下这个修改后的变化：

```sql
SELECT 
    *
FROM
    tasks;
```

执行上面的查询语句，得到以下结果 -

```sql
+----+-------------------------------------------------------+------------+------------+
| id | title                                                 | start_date | end_date   |
+----+-------------------------------------------------------+------------+------------+
|  1 | Learn MySQL NOT NULL constraint                       | 2017-02-01 | 2017-02-02 |
|  2 | Check and update NOT NULL constraint to your database | 2017-02-01 | 2017-02-08 |
+----+-------------------------------------------------------+------------+------------+
2 rows in set
```

**第三**，将`NOT NULL`约束添加到`end_date`列。要执行此操作，请使用以下[ALTER TABLE](http://www.yiibai.com/mysql/alter-table.html)语句：

```sql
ALTER TABLE table_name
CHANGE old_column_name new_column_name new_column_definition;
```

在上面例子中，除了具有`NOT NULL`约束的列定义之外，旧的列名称和新的列名称必须相同：

```sql
ALTER TABLE tasks 
CHANGE end_date end_date DATE NOT NULL;
```

使用[DESCRIBE](http://www.yiibai.com/mysql/show-columns.html)或`DESC`语句来验证更改：

```sql
mysql> DESC tasks;
+------------+--------------+------+-----+---------+----------------+
| Field      | Type         | Null | Key | Default | Extra          |
+------------+--------------+------+-----+---------+----------------+
| id         | int(11)      | NO   | PRI | NULL    | auto_increment |
| title      | varchar(255) | NO   |     | NULL    |                |
| start_date | date         | NO   |     | NULL    |                |
| end_date   | date         | NO   |     | NULL    |                |
+------------+--------------+------+-----+---------+----------------+
4 rows in set
```

如上所示，`NOT NULL`约束被添加到`end_date`列。

在本教程中，您已经学习了如何为列定义`NOT NULL`约束，并将`NOT NULL`约束添加到现有列。