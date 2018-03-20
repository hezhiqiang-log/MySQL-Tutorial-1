使用UNION，如果想从几个表选择行一前一后的所有作为一个单一的结果集，或几个集合行在单一的表中。

UNION是从MySQL4.0开始使用。本节说明如何使用它。

假设有两个表，列出潜在和实际的客户，第三个表，列出供应商购买耗材，并且希望通过从所有三个表合并名称和地址，以创建一个单一的邮件列表。UNION提供了一种方法来做到这一点。假设这三个表具有以下内容：

```sql
mysql> SELECT * FROM prospect;
+---------+-------+------------------------+
| fname   | lname | addr                   |
+---------+-------+------------------------+
| Peter   | Jones | 482 Rush St., Apt. 402 |
| Bernice | Smith | 916 Maple Dr.          |
+---------+-------+------------------------+
mysql> SELECT * FROM customer;
+-----------+------------+---------------------+
| last_name | first_name | address             |
+-----------+------------+---------------------+
| Peterson  | Grace      | 16055 Seminole Ave. |
| Smith     | Bernice    | 916 Maple Dr.       |
| Brown     | Walter     | 8602 1st St.        |
+-----------+------------+---------------------+
mysql> SELECT * FROM vendor;
+-------------------+---------------------+
| company           | street              |
+-------------------+---------------------+
| ReddyParts, Inc.  | 38 Industrial Blvd. |
| Parts-to-go, Ltd. | 213B Commerce Park. |
+-------------------+---------------------+
```

这不要紧，如果所有的三个表都是不同的列名称。 以下查询说明如何从三个表一次全部选择名称和地址：

```sql
mysql> SELECT fname, lname, addr FROM prospect
-> UNION
-> SELECT first_name, last_name, address FROM customer
-> UNION
-> SELECT company, '', street FROM vendor;
+-------------------+----------+------------------------+
| fname             | lname    | addr                   |
+-------------------+----------+------------------------+
| Peter             | Jones    | 482 Rush St., Apt. 402 |
| Bernice           | Smith    | 916 Maple Dr.          |
| Grace             | Peterson | 16055 Seminole Ave.    |
| Walter            | Brown    | 8602 1st St.           |
| ReddyParts, Inc.  |          | 38 Industrial Blvd.    |
| Parts-to-go, Ltd. |          | 213B Commerce Park.    |
+-------------------+----------+------------------------+
```

如果想选择所有记录，包括重复的记录，请使用UNION关键字后面接一个 ALL 关键字：

```sql
mysql> SELECT fname, lname, addr FROM prospect
-> UNION ALL
-> SELECT first_name, last_name, address FROM customer
-> UNION
-> SELECT company, '', street FROM vendor;
+-------------------+----------+------------------------+
| fname             | lname    | addr                   |
+-------------------+----------+------------------------+
| Peter             | Jones    | 482 Rush St., Apt. 402 |
| Bernice           | Smith    | 916 Maple Dr.          |
| Grace             | Peterson | 16055 Seminole Ave.    |
| Bernice           | Smith    | 916 Maple Dr.          |
| Walter            | Brown    | 8602 1st St.           |
| ReddyParts, Inc.  |          | 38 Industrial Blvd.    |
| Parts-to-go, Ltd. |          | 213B Commerce Park.    |
+-------------------+----------+------------------------+
```