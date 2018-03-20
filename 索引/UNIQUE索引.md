在本教程中，您将学习如何使用MySQL `UNIQUE`索引来防止表中一个或多个列中拥有重复的值。

## MySQL UNIQUE索引简介

要强制执行一个或多个列的唯一性值，我们经常使用[PRIMARY KEY](http://www.yiibai.com/mysql/primary-key.html)约束。

但是，每个表只有一个主键。 如果要使用多个列或一组具有唯一值的列，则不能使用主键约束。

幸运的是，MySQL提供了另一种称为`UNIQUE`索引的[索引](http://www.yiibai.com/mysql/create-drop-index.html)，它允许您在一个或多个列中强制实现值的唯一性。 与`PRIMARY KEY`索引不同，每个表可以有多个`UNIQUE`索引。

要创建一个`UNIQUE`索引，请使用[CREATE UNIQUE INDEX](http://www.yiibai.com/mysql/create-drop-index.html)语句如下：

```sql
CREATE UNIQUE INDEX index_name
ON table_name(index_column_1,index_column_2,...);
```

在一个或多个列中强制实现值的唯一性的另一种方法是使用唯一约束。创建唯一约束时，MySQL会在幕后创建一个`UNIQUE`的索引。

以下语句说明了如何在[创建表](http://www.yiibai.com/mysql/create-table/)时创建唯一约束。

```sql
CREATE TABLE table_name(
...
   UNIQUE KEY(index_column_,index_column_2,...) 
);
```

也可以使用`UNIQUE INDEX`而不是`UNIQUE KEY`。 它们被称为相同的东西。

如果要向现有表添加唯一约束，可以使用[ALTER TABLE语句](http://www.yiibai.com/mysql/alter-table.html)，如下所示：

```sql
ALTER TABLE table_name
ADD CONSTRAINT constraint_name UNIQUE KEY(column_1,column_2,...);
```

## MySQL UNIQUE索引和NULL

与其他数据库系统不同，MySQL将[NULL](http://www.yiibai.com/mysql/null.html)值视为[不同的值](http://www.yiibai.com/mysql/distinct.html)。 因此，您可以在`UNIQUE`索引中具有多个`NULL`值。

这就是MySQL的设计方式。 这不是一个错误，即使它被报告为一个错误。

另一重要的一点是`UNIQUE`约束不适用于除了BDB[存储引擎](http://www.yiibai.com/understand-mysql-table-types-innodb-myisam.html)之外的`NULL`值。

**MySQL UNIQUE索引示例**

假设想在应用程序中管理联系人。还希望`contacts`表中的`email`列必须是唯一的。

要执行此规则，请在[CREATE TABLE](http://www.yiibai.com/mysql/create-table.html)语句中创建唯一的约束，如下所示：

```sql
USE testdb;

CREATE TABLE IF NOT EXISTS contacts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    phone VARCHAR(15) NOT NULL,
    email VARCHAR(100) NOT NULL,
    UNIQUE KEY unique_email (email)
);
```

如果使用`SHOW INDEXES`语句，将看到MySQL为`email`列创建了一个`UNIQUE`索引。

```sql
SHOW INDEXES FROM contacts;

+----------+------------+--------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table    | Non_unique | Key_name     | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+----------+------------+--------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| contacts |          0 | PRIMARY      |            1 | id          | A         |           0 | NULL     | NULL   |      | BTREE      |         |               |
| contacts |          0 | unique_email |            1 | email       | A         |           0 | NULL     | NULL   |      | BTREE      |         |               |
+----------+------------+--------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
2 rows in set
```

接下来，向`contacts`表中插入一行。

```sql
INSERT INTO contacts(first_name,last_name,phone,email)
VALUES('Max','Su','(+86)-999-9988','max.su@yiibai.com');
```

现在，如果您尝试插入电子邮件是`max.su@yiibai.com`的行数据，您将收到一条错误消息。

```sql
INSERT INTO contacts(first_name,last_name,phone,email)
VALUES('Max','Su','(+86)-999-9988','max.su@yiibai.com');
```

执行上面语句后，应该会看到以下结果 -

```sql
1062 - Duplicate entry 'max.su@yiibai.com' for key 'unique_email'
```

假设您不仅希望电子邮件是唯一的，而且`first_name`，`last_name`和`phone`的组合也是唯一的。 在这种情况下，可以使用`CREATE INDEX`语句为这些列创建一个`UNIQUE`索引，如下所示：

```sql
CREATE UNIQUE INDEX idx_name_phone
ON contacts(first_name,last_name,phone);
```

将以下行添加到`contacts`表中会导致错误，因为`first_name`，`last_name`和`phone`的组合已存在。

```sql
INSERT INTO contacts(first_name,last_name,phone,email)
VALUES('Max','Su','(+86)-999-9988','john.d@yiibai.com');
```

执行上面语句后，应该会看到以下结果 -

```sql
1062 - Duplicate entry 'Max-Su-(+86)-999-9988' for key 'idx_name_phone'
```

可以看到，不可以将重复电话号码插入到表中。

在本教程中，您已经学习了如何使用MySQL `UNIQUE`索引来防止数据库中的重复值。