## MySQL SELECT 语句简介

使用`SELECT`语句从表或[视图](http://www.yiibai.com/mysql/views.html)获取数据。表由行和列组成，如电子表格。 通常，我们只希望看到子集行，列的子集或两者的组合。`SELECT`语句的结果称为结果集，它是行列表，每行由相同数量的列组成。

请参阅示例数据库(`yiibaidb`)中的以下`employees`表的结构。它有`8`列：员工人数，姓氏，名字，分机，电子邮件，办公室代码，报告，职位等。

![img](../images/6.png)

`SELECT`语句控制要查看哪些列和行。例如，如果只对所有员工的名字，姓氏和职位感兴趣，或者您只想查看其职位是销售代表的每位员工的信息，则`SELECT`语句可帮助您执行这些操作。

我们来看一下`SELECT`语句的语法：

```sql
SELECT 
    column_1, column_2, ...
FROM
    table_1
[INNER | LEFT |RIGHT] JOIN table_2 ON conditions
WHERE
    conditions
GROUP BY column_1
HAVING group_conditions
ORDER BY column_1
LIMIT offset, length;
```

`SELECT`语句由以下列表中所述的几个子句组成：

- `SELECT`之后是逗号分隔列或星号(`*`)的列表，表示要返回所有列。
- `FROM`指定要查询数据的表或视图。
- `JOIN`根据某些连接条件从其他表中获取数据。
- `WHERE`过滤结果集中的行。
- `GROUP BY`将一组行组合成小分组，并对每个小分组应用聚合函数。
- `HAVING`过滤器基于`GROUP BY`子句定义的小分组。
- `ORDER BY`指定用于排序的列的列表。
- `LIMIT`限制返回行的数量。

语句中的`SELECT`和`FROM`语句是必须的，其他部分是可选的。

> 在随后的教程中将更详细地了解每个子句。在本教程中，我们将重点介绍`SELECT`语句的简单形式用法。

## MySQL SELECT 语句示例

`SELECT`语句允许通过在`SELECT`子句中指定逗号分隔列的列表来查询表的部分数据。 例如，如果要仅查看员工的名字，姓氏和职位，请使用以下查询：

```sql
SELECT 
    lastname, firstname, jobtitle
FROM
    employees;
```

即使员工表中有很多列，`SELECT`语句只返回表中所有行的三列数据，如下图所示：

```sql
mysql> SELECT lastname, firstname, jobtitle FROM employees;
+-----------+-----------+----------------------+
| lastname  | firstname | jobtitle             |
+-----------+-----------+----------------------+
| Murphy    | Diane     | President            |
| Patterson | Mary      | VP Sales             |
| Firrelli  | Jeff      | VP Marketing         |
| Patterson | William   | Sales Manager (APAC) |
| Bondur    | Gerard    | Sale Manager (EMEA)  |
| Bow       | Anthony   | Sales Manager (NA)   |
| Jennings  | Leslie    | Sales Rep            |
| Thompson  | Leslie    | Sales Rep            |
| Firrelli  | Julie     | Sales Rep            |
| Patterson | Steve     | Sales Rep            |
| Tseng     | Foon Yue  | Sales Rep            |
| Vanauf    | George    | Sales Rep            |
| Bondur    | Loui      | Sales Rep            |
| Hernandez | Gerard    | Sales Rep            |
| Castillo  | Pamela    | Sales Rep            |
| Bott      | Larry     | Sales Rep            |
| Jones     | Barry     | Sales Rep            |
| Fixter    | Andy      | Sales Rep            |
| Marsh     | Peter     | Sales Rep            |
| King      | Tom       | Sales Rep            |
| Nishi     | Mami      | Sales Rep            |
| Kato      | Yoshimi   | Sales Rep            |
| Gerard    | Martin    | Sales Rep            |
+-----------+-----------+----------------------+
23 rows in set
```

注意比较以下两个语句返回列有什么区别 -

*语句-1*

```sql
SELECT lastname, firstname, jobtitle FROM employees;
```

*语句-2*

```sql
SELECT * FROM employees;
```

如果要获取`employees`表中所有列的数据，可以列出`SELECT`子句中的所有列名，或者只需使用星号(`*`)表示您想要从表的所有列获取数据，如下查询：

```sql
mysql> SELECT * FROM employees;
+----------------+-----------+-----------+-----------+-----------------------+------------+-----------+----------------------+
| employeeNumber | lastName  | firstName | extension | email                 | officeCode | reportsTo | jobTitle             |
+----------------+-----------+-----------+-----------+-----------------------+------------+-----------+----------------------+
|           1002 | Murphy    | Diane     | x5800     | dmurphy@yiibai.com    | 1          | NULL      | President            |
|           1056 | Patterson | Mary      | x4611     | mpatterso@yiibai.com  | 1          |      1002 | VP Sales             |
|           1076 | Firrelli  | Jeff      | x9273     | jfirrelli@yiibai.com  | 1          |      1002 | VP Marketing         |
|           1088 | Patterson | William   | x4871     | wpatterson@yiibai.com | 6          |      1056 | Sales Manager (APAC) |
|           1102 | Bondur    | Gerard    | x5408     | gbondur@gmail.com     | 4          |      1056 | Sale Manager (EMEA)  |
|           1143 | Bow       | Anthony   | x5428     | abow@gmail.com        | 1          |      1056 | Sales Manager (NA)   |
|           1165 | Jennings  | Leslie    | x3291     | ljennings@yiibai.com  | 1          |      1143 | Sales Rep            |
|           1166 | Thompson  | Leslie    | x4065     | lthompson@yiibai.com  | 1          |      1143 | Sales Rep            |
|           1188 | Firrelli  | Julie     | x2173     | jfirrelli@yiibai.com  | 2          |      1143 | Sales Rep            |
|           1216 | Patterson | Steve     | x4334     | spatterson@yiibai.com | 2          |      1143 | Sales Rep            |
|           1286 | Tseng     | Foon Yue  | x2248     | ftseng@yiibai.com     | 3          |      1143 | Sales Rep            |
|           1323 | Vanauf    | George    | x4102     | gvanauf@yiibai.com    | 3          |      1143 | Sales Rep            |
|           1337 | Bondur    | Loui      | x6493     | lbondur@yiibai.com    | 4          |      1102 | Sales Rep            |
|           1370 | Hernandez | Gerard    | x2028     | ghernande@gmail.com   | 4          |      1102 | Sales Rep            |
|           1401 | Castillo  | Pamela    | x2759     | pcastillo@gmail.com   | 4          |      1102 | Sales Rep            |
|           1501 | Bott      | Larry     | x2311     | lbott@yiibai.com      | 7          |      1102 | Sales Rep            |
|           1504 | Jones     | Barry     | x102      | bjones@gmail.com      | 7          |      1102 | Sales Rep            |
|           1611 | Fixter    | Andy      | x101      | afixter@yiibai.com    | 6          |      1088 | Sales Rep            |
|           1612 | Marsh     | Peter     | x102      | pmarsh@yiibai.com     | 6          |      1088 | Sales Rep            |
|           1619 | King      | Tom       | x103      | tking@gmail.com       | 6          |      1088 | Sales Rep            |
|           1621 | Nishi     | Mami      | x101      | mnishi@gmail.com      | 5          |      1056 | Sales Rep            |
|           1625 | Kato      | Yoshimi   | x102      | ykato@gmail.com       | 5          |      1621 | Sales Rep            |
|           1702 | Gerard    | Martin    | x2312     | mgerard@gmail.com     | 4          |      1102 | Sales Rep            |
+----------------+-----------+-----------+-----------+-----------------------+------------+-----------+----------------------+
23 rows in set
```

它返回`employees`表中的所有列和行。应该使用星号(`*`)进行测试。建议显式获取数据的列，原因如下：

- 使用星号(`*`)可能会返回不使用的列的数据。 它在MySQL数据库服务器和应用程序之间产生不必要的*I/O*磁盘和网络流量。
- 如果明确指定列，则结果集更可预测并且更易于管理。 想象一下，当您使用星号(`*`)并且有人通过添加更多列来更改表格数据时，将会得到一个与预期不同的结果集。
- 使用星号(`*`)可能会将敏感信息暴露给未经授权的用户。

在本教程中，您已经了解并熟悉了 MySQL `SELECT`语句的用法，并通过`SELECT`语句从 MySQL 表中查询数据。