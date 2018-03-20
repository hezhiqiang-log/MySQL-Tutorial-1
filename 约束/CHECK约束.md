在本教程中，您将学习如何使用带有`check`选项的触发器或视图来模拟MySQL `CHECK`约束。

> 注意： 要更好学习和理解本教程，您需要对[触发器](http://www.yiibai.com/mysql/triggers.html)，视图和[存储过程](http://www.yiibai.com/mysql/stored-procedure-tutorial.html)有很好的了解。

## SQL CHECK约束简介

标准SQL提供的检查(`CHECK`)约束指定某列中的值必须满足布尔表达式。 例如，您可以添加一个`CHECK`约束来强制成本(`cost`)列为正值，如下所示：

```sql
USE testdb;
CREATE TABLE IF NOT EXISTS parts (
    part_no VARCHAR(18) PRIMARY KEY,
    description VARCHAR(40),
    cost DECIMAL(10 , 2 ) NOT NULL CHECK(cost > 0),
    price DECIMAL (10,2) NOT NULL
);
```

SQL允许您将一个或多个CHECK约束应用于一列或跨多个列。 例如，为了确保价格(`price`)列总是大于或等于成本(`cost`)列，可使用`CHECK`约束如下：

```sql
USE testdb;
CREATE TABLE IF NOT EXISTS parts (
    part_no VARCHAR(18) PRIMARY KEY,
    description VARCHAR(40),
    cost DECIMAL(10 , 2 ) NOT NULL CHECK (cost > 0),
    price DECIMAL(10 , 2 ) NOT NULL CHECK (price > 0),
    CHECK (price >= cost)
);
```

当`CHECK`约束设置完成，每当插入或更新导致布尔表达式的值计算为`false`时，则视为违反检查约束，并且数据库系统拒绝插入或更改数据。

不幸的是，MySQL不支持`CHECK`约束。 实际上，MySQL在[CREATE TABLE](http://www.yiibai.com/mysql/create-table.html)语句中接受`CHECK`子句，但是它会以静默方式忽略它。

## MySQL使用触发器CHECK约束

在MySQL中模拟`CHECK`约束的第一种方法是使用两个[触发器](http://www.yiibai.com/mysql/triggers.html)：`BEFORE INSERT`和`BEFORE UPDATE`。

**首先**，为了演示目的，我们先创建一个`parts`表，如下语句 -

```sql
USE testdb;
CREATE TABLE IF NOT EXISTS parts (
    part_no VARCHAR(18) PRIMARY KEY,
    description VARCHAR(40),
    cost DECIMAL(10 , 2 ) NOT NULL,
    price DECIMAL(10,2) NOT NULL
);
```

**其次**，创建一个[存储过程](http://www.yiibai.com/mysql/stored-procedure-tutorial.html)来检查`cost`和`price`列中的值。

```sql
DELIMITER $$

CREATE PROCEDURE `check_parts`(IN cost DECIMAL(10,2), IN price DECIMAL(10,2))
BEGIN
    IF cost < 0 THEN
        SIGNAL SQLSTATE '45000'
            SET MESSAGE_TEXT = 'check constraint on parts.cost failed';
    END IF;

    IF price < 0 THEN
 SIGNAL SQLSTATE '45001'
 SET MESSAGE_TEXT = 'check constraint on parts.price failed';
    END IF;

    IF price < cost THEN
 SIGNAL SQLSTATE '45002'
            SET MESSAGE_TEXT = 'check constraint on parts.price & parts.cost failed';
    END IF;

END$$

DELIMITER ;
```

**第三**，创建`BEFORE INSERT`和`BEFORE UPDATE`触发器。 在触发器中，调用`check_parts()`存储过程。

```sql
-- before insert
DELIMITER $$
CREATE TRIGGER `parts_before_insert` BEFORE INSERT ON `parts`
FOR EACH ROW
BEGIN
    CALL check_parts(new.cost,new.price);
END$$   
DELIMITER ;

-- before update
DELIMITER $$
CREATE TRIGGER `parts_before_update` BEFORE UPDATE ON `parts`
FOR EACH ROW
BEGIN
    CALL check_parts(new.cost,new.price);
END$$   
DELIMITER ;
```

**第四**，插入满足以下所有条件的新行：

- `cost > 0`
- `price > 0`
- `price >= cost`

执行以下插入语句 -

```sql
INSERT INTO parts(part_no, description,cost,price)
VALUES('A-001','Cooler',100,120);
```

`INSERT`语句调用`BEFORE INSERT`触发器并接受值。

**演示-1**
以下`INSERT`语句执行将会失败，因为它违反了条件：`cost> 0`。

```sql
INSERT INTO parts(part_no, description,cost,price)
VALUES('A-002','Heater',-100,120);
```

执行上面插入语句，得到以下错误提示信息 -

```sql
Error Code: 1644. check constraint on parts.cost failed
```

**演示-2**
以下`INSERT`语句执行将会失败，因为它违反了条件：`price> 0`。

```sql
INSERT INTO parts(part_no, description,cost,price)
VALUES('A-002','Heater',100,-120);
```

执行上面插入语句，得到以下错误提示信息 -

```sql
Error Code: 1644. check constraint on parts.price failed
```

**演示-3**
以下`INSERT`语句执行将会失败，因为它违反了条件：`price > cost`。

```sql
INSERT INTO parts(part_no, description,cost,price)
VALUES('A-003','wiper',120,100);
```

执行上面插入语句，得到以下错误提示信息 -

```sql
1644 - check constraint on parts.price & parts.cost failed
```

现在，让我们来看看在`parts`表中的数据。

```sql
SELECT * FROM parts;
```

执行上面查询语句，得到以下结果 -

```sql
+---------+-------------+------+-------+
| part_no | description | cost | price |
+---------+-------------+------+-------+
| A-001   | Cooler      | 100  | 120   |
+---------+-------------+------+-------+
1 row in set
```

我们试图更新`cost`列的值，使其低于价格(`price`)列：

```sql
UPDATE parts
SET price = 10
WHERE part_no = 'A-001';
```

执行上面更新语句，得到以下错误提示信息 -

```sql
Error Code: 1644. check constraint on parts.price & parts.cost failed
```

上面更新语句被拒绝执行了。

如上示例中所示，我们通过使用两个触发器：`BEFORE INSERT`和`BEFORE UPDATE`，来模拟MySQL中的`CHECK`约束。

## MySQL CHECK约束使用可更新视图与check选项

这个方法是使用基于表的`check`选项来创建一个视图。 在视图的[SELECT](http://www.yiibai.com/mysql/select-statement-query-data.html)语句中，我们仅选择满足`CHECK`条件的有效行。对视图的任何插入或更新都将被拒绝，这样使新的行记录不会出现在视图中。

首先，删除`parts`表以删除所有相关的触发器，并创建一个新的表，与`parts`表具有相同的结构，但使用了不同的名称：`parts_data`：

```sql
DROP TABLE IF EXISTS parts;

CREATE TABLE IF NOT EXISTS parts_data (
    part_no VARCHAR(18) PRIMARY KEY,
    description VARCHAR(40),
    cost DECIMAL(10 , 2 ) NOT NULL,
    price DECIMAL(10,2) NOT NULL
);
```

其次，根据`parts_data`表创建名为`parts`的视图。 通过这样做，我们可以保持使用`parts`表的应用程序的代码保持不变。 此外，旧零件表的所有权限保持不变。

```sql
CREATE VIEW vparts AS
    SELECT 
        part_no, description, cost, price
    FROM
        parts_data
    WHERE
        cost > 0 AND price > 0 AND price >= cost 
WITH CHECK OPTION;
```

**第三**，通过`parts`视图向`parts_data`表中插入一个新行：

```sql
INSERT INTO vparts(part_no, description,cost,price)
VALUES('A-001','Cooler',100,120);
```

上面的新行可被接受，因为新行有效，可以出现在视图中。

但是，以下语句失败，因为新行不会出现在视图中。

```sql
INSERT INTO vparts(part_no, description,cost,price)
VALUES('A-002','Heater',-100,120);
```

执行上面语句，MySQL会发出以下错误 -

```sql
Error Code: 1369. CHECK OPTION failed 'testdb.parts_checked'
```

在本教程中，我们向您介绍了标准SQL `CHECK`约束和两种在MySQL中模拟`CHECK`约束的方法用法。