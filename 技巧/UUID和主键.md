本教程将向您介绍MySQL UUID，并演示如何将其用作表的主键(PK)，并讨论将其用作主键的优缺点。

## MySQL UUID简介

`UUID`代表通用唯一标识符。`UUID`是基于”[RFC 4122](http://tools.ietf.org/html/rfc4122)“通用唯一标识符(`UUID`)URN命名空间”)定义的。

`UUID`被设计为在空间和时间全球独一无二的数字。 预期两个UUID值是不同的，即使它们在两个独立的服务器上生成。

在MySQL中，`UUID`值是一个`128`位的数字，表示为以下格式的十五进制数字的`utf8`字符串：

```sql
aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee
```

要生成`UUID`值，请使用`UUID()`函数，如下所示：

```sql
UUID()
```

`UUID()`函数返回符合`RFC 4122`中描述的`UUID`版本1的`UUID`值。

例如，以下语句使用`UUID()`函数来生成`UUID`值：

```sql
mysql> SELECT UUID();
+--------------------------------------+
| UUID()                               |
+--------------------------------------+
| 9d6212cf-72fc-11e7-bdf0-f0def1e6646c |
+--------------------------------------+
1 row in set
```

## MySQL UUID与自动递增INT作为主键

**优点**

使用`UUID`作为[主键](http://www.yiibai.com/mysql/uuid.html)具有以下优点：

- UUID值在表，数据库甚至在服务器上都是唯一的，允许您从不同数据库合并行或跨服务器分发数据库。
- UUID值不会公开有关数据的信息，因此它们在URL中使用更安全。例如，如果`ID`为`10`的客户通过URL： <http://www.example.com/customers/10/> 访问他的帐户，那么很容易猜到有一个客户`11`,`12`等，这可能是攻击的目标。
- 可以在避免往返数据库服务器的任何地方生成`UUID`值。它也简化了应用程序中的逻辑。 例如，要将数据插入到父表和子表中，必须首先插入父表，获取生成的`id`，然后将数据插入到子表中。通过使用`UUID`，可以生成父表的主键值，并在事务中同时在父表和子表中插入行。

**缺点**

除了优势之外，UUID值也有一些缺点：

- 存储UUID值(16字节)比整数(4字节)或甚至大整数(8字节)占用更多的存储空间。
- 调试似乎更加困难，想像一下`WHERE id ='9d6212cf-72fc-11e7-bdf0-f0def1e6646c'`和`WHERE id = 10`那个舒服一点？
- 使用UUID值可能会导致性能问题，因为它们的大小和没有被排序。

## MySQL UUID解决方案

在MySQL中，可以以紧凑格式(`BINARY`)存储UUID值，并通过以下功能显示人机可读格式([VARCHAR](http://www.yiibai.com/mysql/varchar.html))：

- *UUID_TO_BIN*
- *BIN_TO_UUID*
- *IS_UUID*

> 请注意，`UUID_TO_BIN()`，`BIN_TO_UUID()`和`IS_UUID()`函数仅在*MySQL 8.0*或更高版本中可用。

`UUID_TO_BIN()`函数将UUID从人类可读格式(`VARCHAR`)转换成用于存储的紧凑格式(`BINARY`)格式，并且`BIN_TO_UUID()`函数将UUID从紧凑格式(`BINARY`)转换为人类可读格式(`VARCHAR`)。

如果参数是有效的字符串格式`UUID`，`IS_UUID()`函数将返回`1`。 如果参数不是有效的字符串格式`UUID`，则`IS_UUID`函数返回`0`，如果参数为`NULL`，则`IS_UUID()`函数返回`NULL`。

以下是MySQL中有效的字符串格式`UUID`：

```sql
aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee
aaaaaaaabbbbccccddddeeeeeeeeeeee
{aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee}
```

## MySQL UUID示例

我们来看一下使用`UUID`作为主键的例子。

以下语句[创建](http://www.yiibai.com/mysql/create-table.html)一个名为`customers`的新表：

```sql
USE testdb;
CREATE TABLE customers (
    id BINARY(16) PRIMARY KEY,
    name VARCHAR(255)
);
```

要将`UUID`值[插入](http://www.yiibai.com/mysql/insert-statement.html)到`id`列中，可以使用`UUID()`和`UUID_TO_BIN()`函数，如下所示：

```sql
INSERT INTO customers(id, name)
VALUES(UUID_TO_BIN(UUID()),'John Doe'),
      (UUID_TO_BIN(UUID()),'Will Minsu'),
      (UUID_TO_BIN(UUID()),'Mary Jane');
```

要从UUID列查询数据，可以使用`BIN_TO_UUID()`函数将二进制格式转换为可读取的格式：

```sql
SELECT 
    BIN_TO_UUID(id) id, 
    name
FROM
    customers;
```

在本教程中，您已经了解了MySQL UUID以及如何将其用于主键列。