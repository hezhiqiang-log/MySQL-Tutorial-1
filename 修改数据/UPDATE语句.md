更新数据是使用数据库时最重要的任务之一。 在本教程中，您将学习如何使用MySQL `UPDATE`语句来更新表中的数据。

## 1. MySQL UPDATE语句简介

我们使用`UPDATE`语句来更新表中的现有数据。也可以使用`UPDATE`语句来更改表中单个行，一组行或所有行的列值。

下面说明了MySQL `UPDATE`语句的语法：

```sql
UPDATE [LOW_PRIORITY] [IGNORE] table_name 
SET 
    column_name1 = expr1,
    column_name2 = expr2,
    ...
WHERE
    condition;
```

在上面`UPDATE`语句中：

- 首先，在`UPDATE`关键字后面指定要更新数据的表名。
- 其次，`SET`子句指定要修改的列和新值。要更新多个列，请使用以逗号分隔的列表。以字面值，表达式或[子查询](http://www.yiibai.com/mysql/subquery.html)的形式在每列的赋值中来提供要设置的值。
- 第三，使用[WHERE子句](http://www.yiibai.com/mysql/where.html)中的条件指定要更新的行。`WHERE`子句是可选的。 如果省略`WHERE`子句，则`UPDATE`语句将更新表中的所有行。

请注意，`WHERE`子句非常重要，所以不应该忘记指定更新的条件。 有时，您可能只想改变一行; 但是，可能会忘记写上`WHERE`子句，导致意外更新表中的所有行。

MySQL在`UPDATE`语句中支持两个修饰符。

- `LOW_PRIORITY`修饰符指示`UPDATE`语句延迟更新，直到没有从表中读取数据的连接。 `LOW_PRIORITY`对仅使用表级[锁](http://www.yiibai.com/mysql/table-locking.html)定的[存储引擎](http://www.yiibai.com/understand-mysql-table-types-innodb-myisam.html)(例如*MyISAM*，*MERGE*，*MEMORY*)生效。
- 即使发生错误，*IGNORE*修饰符也可以使*UPDATE*语句继续更新行。导致错误(如重复键冲突)的行不会更新。

## 2. MySQL UPDATE示例

我们使用MySQL[示例数据库(yiibaidb)](http://www.yiibai.com/mysql/sample-database.html)中的一些表来练习使用`UPDATE`语句。

**2.1 MySQL UPDATE一个单列示例**

在这个例子中，我们将把 *Mary Patterson* 的电子邮件更新为新的电子邮件`mary.patterso@yiibai.com`。

首先，为了确保更新电子邮件成功，使用以下[SELECT语句](http://www.yiibai.com/mysql/select-statement-query-data.html)从`employees`表查询`Mary`的电子邮件：

```sql
SELECT 
    firstname, lastname, email
FROM
    employees
WHERE
    employeeNumber = 1056;
```

执行上面的查询语句，得到以下结果 -

```sql
+-----------+-----------+----------------------+
| firstname | lastname  | email                |
+-----------+-----------+----------------------+
| Mary      | Patterson | mpatterso@yiibai.com |
+-----------+-----------+----------------------+
1 row in set
```

**第二步**，使用`UPDATE`语句将`Mary`的电子邮件更新为新的电子邮件：`mary.new@yiibai.com`，如下查询所示：

```sql
UPDATE employees 
SET 
    email = 'mary.new@yiibai.com'
WHERE
    employeeNumber = 1056;
```

因为上面语句中，只想更新一行，所以使用`WHERE`子句来指定更新的是员工编号`1056`的行。`SET`子句将电子邮件列的值设置为新的电子邮件。

**第三**，再次执行`SELECT`语句来验证更改。

```sql
SELECT 
    firstname, lastname, email
FROM
    employees
WHERE
    employeeNumber = 1056;
```

再次执行上面的查询语句，得到以下结果 -

```sql
+-----------+-----------+---------------------+
| firstname | lastname  | email               |
+-----------+-----------+---------------------+
| Mary      | Patterson | mary.new@yiibai.com |
+-----------+-----------+---------------------+
1 row in set
```

**2.2 MySQL UPDATE多列**

要更新多列中的值，需要在`SET`子句中指定分配。例如，以下语句更新了员工编号`1056`的姓氏和电子邮件列：

```sql
UPDATE employees 
SET 
    lastname = 'Hill',
    email = 'mary.hill@yiibai.com'
WHERE
    employeeNumber = 1056;
```

在执行上面语句之后，查询员工编号为：`1056`的记录，如下所示 -

```sql
+-----------+----------+----------------------+
| firstname | lastname | email                |
+-----------+----------+----------------------+
| Mary      | Hill     | mary.hill@yiibai.com |
+-----------+----------+----------------------+
1 row in set
```

**2.3 使用SELECT语句的MySQL UPDATE示例**

可以使用`SELECT`语句查询来自其他表的数据来提供给`SET`子句的值。

例如，在`customers`表中，有些客户没有任何销售代表。 `salesRepEmployeeNumber`列的值为`NULL`，如下所示：

```sql
mysql> SELECT 
    customername, salesRepEmployeeNumber
FROM
    customers
WHERE
    salesRepEmployeeNumber IS NULL;
+--------------------------------+------------------------+
| customername                   | salesRepEmployeeNumber |
+--------------------------------+------------------------+
| Havel & Zbyszek Co             | NULL                   |
| Porto Imports Co.              | NULL                   |
| Asian Shopping Network, Co     | NULL                   |
| Natrlich Autos                 | NULL                   |
| ANG Resellers                  | NULL                   |
| Messner Shopping Network       | NULL                   |
| Franken Gifts, Co              | NULL                   |
| BG&E Collectables              | NULL                   |
| Schuyler Imports               | NULL                   |
| Der Hund Imports               | NULL                   |
| Cramer Spezialitten, Ltd       | NULL                   |
| Asian Treasures, Inc.          | NULL                   |
| SAR Distributors, Co           | NULL                   |
| Kommission Auto                | NULL                   |
| Lisboa Souveniers, Inc         | NULL                   |
| Stuttgart Collectable Exchange | NULL                   |
| Feuer Online Stores, Inc       | NULL                   |
| Warburg Exchange               | NULL                   |
| Anton Designs, Ltd.            | NULL                   |
| Mit Vergngen & Co.             | NULL                   |
| Kremlin Collectables, Co.      | NULL                   |
| Raanan Stores, Inc             | NULL                   |
+--------------------------------+------------------------+
22 rows in set
```

我们可以为这些客户提供销售代表和更新。

为此，需要从`employees`表中随机选择一个职位为`Sales Rep`的雇员，并将其更新到`employees`表中。
下面的查询语句是从`employees`表中随机选择一个其职位是`Sales Rep`的员工。

```sql
SELECT 
    employeeNumber
FROM
    employees
WHERE
    jobtitle = 'Sales Rep'
ORDER BY RAND()
LIMIT 1;
```

要更新`customers`表中的销售代表员工编号(`employeeNumber`)列，我们将上面的查询放在`UPDATE`语句的`SET`子句中，如下所示：

```sql
UPDATE customers 
SET 
    salesRepEmployeeNumber = (SELECT 
            employeeNumber
        FROM
            employees
        WHERE
            jobtitle = 'Sales Rep'
        LIMIT 1)
WHERE
    salesRepEmployeeNumber IS NULL;
```

如果在执行上面更新语句后，查询`customers`表中的数据，将看到每个客户都有一个销售代表。 换句话说，以下查询不返回任何行数据。

```sql
SELECT 
     salesRepEmployeeNumber
FROM
    customers
WHERE
    salesRepEmployeeNumber IS NULL;
```

在本教程中，您已经学会了如何使用MySQL `UPDATE`语句来更新数据库表中的数据。