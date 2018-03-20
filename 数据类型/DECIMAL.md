在本教程中，我们将向您介绍MySQL `DECIMAL`数据类型以及如何在数据库表中有效地使用它。

## MySQL DECIMAL数据类型简介

MySQL `DECIMAL`数据类型用于在数据库中存储精确的数值。我们经常将`DECIMAL`数据类型用于保留准确精确度的列，例如会计系统中的货币数据。

要定义数据类型为`DECIMAL`的列，请使用以下语法：

```sql
column_name  DECIMAL(P,D);
```

在上面的语法中：

- `P`是表示有效数字数的精度。 `P`范围为`1〜65`。
- `D`是表示小数点后的位数。 `D`的范围是`0`~`30`。MySQL要求`D`小于或等于(`<=`)`P`。

`DECIMAL(P，D)`表示列可以存储`D`位小数的`P`位数。十进制列的实际范围取决于精度和刻度。

与[INT数据类型](http://www.yiibai.com/mysql/int.html)一样，`DECIMAL`类型也具有`UNSIGNED`和`ZEROFILL`属性。 如果使用`UNSIGNED`属性，则`DECIMAL UNSIGNED`的列将不接受负值。

如果使用`ZEROFILL`，MySQL将把显示值填充到`0`以显示由列定义指定的宽度。 另外，如果我们对`DECIMAL`列使用`ZERO FILL`，MySQL将自动将`UNSIGNED`属性添加到列。

以下示例使用`DECIMAL`数据类型定义的一个叫作`amount`的列。

```sql
amount DECIMAL(6,2);
```

在此示例中，`amount`列最多可以存储`6`位数字，小数位数为`2`位; 因此，`amount`列的范围是从`-9999.99`到`9999.99`。

MySQL允许使用以下语法：

```sql
column_name DECIMAL(P);
```

这相当于：

```sql
column_name DECIMAL(P,0);
```

在这种情况下，列不包含小数部分或小数点。

此外，我们甚至可以使用以下语法。

```sql
column_name DECIMAL;
```

在这种情况下，`P`的默认值为`10`。

## MySQL DECIMAL存储

MySQL分别为整数和小数部分分配存储空间。 MySQL使用二进制格式存储`DECIMAL`值。它将`9`位数字包装成`4`个字节。

对于每个部分，需要`4`个字节来存储`9`位数的每个倍数。剩余数字所需的存储如下表所示：

| 剩余数字 | 位   |
| -------- | ---- |
| 0        | 0    |
| 1–2      | 1    |
| 3–4      | 2    |
| 5–6      | 3    |
| 7-9      | 4    |

例如，`DECIMAL(19,9)`对于小数部分具有`9`位数字，对于整数部分具有`19`位= `10`位数字，小数部分需要`4`个字节。 整数部分对于前`9`位数字需要`4`个字节，`1`个剩余字节需要`1`个字节。`DECIMAL(19,9)`列总共需要`9`个字节。

## MySQL DECIMAL数据类型和货币数据

经常使用`DECIMAL`数据类型的货币数据，如价格，工资，账户余额等。如果要设计一个处理货币数据的数据库，则可参考以下语法 -

```sql
amount DECIMAL(19,2);
```

但是，如果您要遵守公认会计原则(GAAP)规则，则货币栏必须至少包含`4`位小数，以确保舍入值不超过`$0.01`。 在这种情况下，应该定义具有`4`位小数的列，如下所示：

```sql
amount DECIMAL(19,4);
```

## MySQL DECIMAL数据类型示例

**首先**，创建一个名为`material`的新表，其中包含三列：`id`，`description`和`cost`。

```sql
CREATE TABLE materials (
    id INT AUTO_INCREMENT PRIMARY KEY,
    description VARCHAR(255),
    cost DECIMAL(19 , 4 ) NOT NULL
);
```

**第二步**，将资料[插入](http://www.yiibai.com/mysql/insert-statement.html)`materials`表。

```sql
INSERT INTO materials(description,cost)
VALUES('Bicycle', 500.34),('Seat',10.23),('Break',5.21);
```

**第三步**，从`materials`表查询数据。

```sql
SELECT 
    *
FROM
    materials;
```

**第四步**，更改`cost`列以包含`ZEROFILL`属性。

```sql
ALTER TABLE materials
MODIFY cost DECIMAL(19,4) zerofill;
```

**第五步**，再次查询`materials`表。

```sql
SELECT 
    *
FROM
    materials;
```

如上所见，在输出值中填充了许多零。

在本教程中，我们向您提供了有关MySQL `DECIMAL`数据类型的详细信息，并向您展示了如何将其应用于存储精确数字数据(例如财务数据)的列。