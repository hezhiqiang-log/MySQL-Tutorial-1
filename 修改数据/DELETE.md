在本教程中，您将学习如何使用MySQL `DELETE`语句从单个表中删除数据。

## 1. MySQL DELETE语句介绍

要从表中删除数据，请使用MySQL `DELETE`语句。下面说明了`DELETE`语句的语法：

```sql
DELETE FROM table_name
WHERE condition;
```

在上面查询语句中 -

- 首先，指定删除数据的表(`table_name`)。
- 其次，使用条件来指定要在`WHERE`子句中删除的行记录。如果行匹配条件，这些行记录将被删除。

> 请注意，`WHERE`子句是可选的。如果省略`WHERE`子句，`DELETE`语句将删除表中的所有行。

除了从表中删除数据外，`DELETE`语句返回删除的行数。

要使用单个`DELETE`语句从多个表中删除数据，请阅读下一个教程中将介绍的[DELETE JOIN语句](http://www.yiibai.com/mysql/delete-join.html)。

要删除表中的所有行，而不需要知道删除了多少行，那么应该使用[TRUNCATE TABLE](http://www.yiibai.com/mysql/truncate-table.html)语句来获得更好的执行性能。

对于具有[外键约束](http://www.yiibai.com/mysql/foreign-key.html)的表，当从父表中删除行记录时，子表中的行记录将通过使用[ON DELETE CASCADE](http://www.yiibai.com/mysql/on-delete-cascade.html)选项自动删除。

## 2. MySQL DELETE的例子

我们将使用[示例数据库(yiibaidb)](http://www.yiibai.com/mysql/sample-database.html)中的`employees`表进行演示。

```sql
+-------------+--------------+------+-----+---------+----------------+
| Field       | Type         | Null | Key | Default | Extra          |
+-------------+--------------+------+-----+---------+----------------+
| emp_id      | int(11)      | NO   | PRI | NULL    | auto_increment |
| emp_name    | varchar(255) | NO   |     | NULL    |                |
| performance | int(11)      | YES  | MUL | NULL    |                |
| salary      | float        | YES  |     | NULL    |                |
+-------------+--------------+------+-----+---------+----------------+
4 rows in set
```

> 请注意，一旦删除数据，它就会永远消失。 因此，在执行`DELETE`语句之前，应该先[备份数据库](http://www.yiibai.com/mysql/how-to-backup-database-using-mysqldump.html)，以防万一要找回删除过的数据。

假设要删除`officeNumber`为`4`的员工，则使用`DELETE`语句与`WHERE`子句作为以下查询：

```sql
DELETE FROM employees 
WHERE
    officeCode = 4;
```

要删除`employees`表中的所有行，请使用不带[WHERE](http://www.yiibai.com/mysql/where.html)子句的`DELETE`语句，如下所示：

```sql
DELETE FROM employees;
```

在执行上面查询语句后，`employees`表中的所有行都被删除。

## MySQL DELETE和LIMIT子句

如果要限制要删除的行数，则使用[LIMIT](http://www.yiibai.com/mysql/limit.html)子句，如下所示：

```sql
DELETE FROM table
LIMIT row_count;
```

请注意，表中的行顺序未指定，因此，当您使用`LIMIT`子句时，应始终使用`ORDER BY`子句，不然删除的记录可能不是你所预期的那样。

```sql
DELETE FROM table_name
ORDER BY c1, c2, ...
LIMIT row_count;
```

考虑在[示例数据库(yiibaidb)](http://www.yiibai.com/mysql/sample-database.html)中的`customers`表，其表结构如下：

```sql
mysql> desc customers;
+------------------------+---------------+------+-----+---------+-------+
| Field                  | Type          | Null | Key | Default | Extra |
+------------------------+---------------+------+-----+---------+-------+
| customerNumber         | int(11)       | NO   | PRI | NULL    |       |
| customerName           | varchar(50)   | NO   |     | NULL    |       |
| contactLastName        | varchar(50)   | NO   |     | NULL    |       |
| contactFirstName       | varchar(50)   | NO   |     | NULL    |       |
| phone                  | varchar(50)   | NO   |     | NULL    |       |
| addressLine1           | varchar(50)   | NO   |     | NULL    |       |
| addressLine2           | varchar(50)   | YES  |     | NULL    |       |
| city                   | varchar(50)   | NO   |     | NULL    |       |
| state                  | varchar(50)   | YES  |     | NULL    |       |
| postalCode             | varchar(15)   | YES  |     | NULL    |       |
| country                | varchar(50)   | NO   |     | NULL    |       |
| salesRepEmployeeNumber | int(11)       | YES  | MUL | NULL    |       |
| creditLimit            | decimal(10,2) | YES  |     | NULL    |       |
+------------------------+---------------+------+-----+---------+-------+
13 rows in set
```

例如，以下语句按客户名称按字母排序客户，并删除前`10`个客户：

```sql
DELETE FROM customers
ORDER BY customerName
LIMIT 10;
```

类似地，以下`DELETE`语句选择法国(*France*)的客户，按升序按信用额度(`creditLimit`)进行排序，并删除前`5`个客户：

```sql
DELETE FROM customers
WHERE country = 'France'
ORDER BY creditLimit
LIMIT 5;
```

类似地，以下`DELETE`语句选择法国(*France*)的客户，按升序按信用额度(`creditLimit`)进行排序，并删除前`5`个客户：

```sql
DELETE FROM customers
WHERE country = 'France'
ORDER BY creditLimit
LIMIT 5;
```

在本教程中，您已经学会了如何使用MySQL `DELETE`语句从表中删除数据。