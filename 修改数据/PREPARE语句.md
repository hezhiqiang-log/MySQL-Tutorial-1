在本教程中，您将学习如何使用MySQL准备(`Prepared`)语句来使您的查询执行得更快更安全。

## MySQL Prepared语句简介

之前的MySQL版本4.1，查询以文本格式发送到MySQL服务器。 之后，MySQL服务器使用文本协议将数据返回给客户端。MySQL必须完全解析查询，并将结果集转换为字符串，然后再将其返回给客户端。

文本协议具有严重的性能问题。为了解决这个问题，MySQL自版本*4.1*以来添加了一个名为`prepare`语句的来实现一些新功能。

`prepare`语句利用客户端/服务器二进制协议。 它将包含占位符(`?`)的查询传递给MySQL服务器，如下例所示：

```sql
SELECT * 
FROM products 
WHERE productCode = ?;
```

当MySQL使用不同的`productcode`值执行此查询时，不必完全解析查询。 因此，这有助于MySQL更快地执行查询，特别是当MySQL多次执行查询时。 因为`prepare`语句使用占位符(`?`)，这有助于避免SQL注入的问题，从而使您的应用程序更安全一些。

## MySQL准备语句用法

为了使用MySQL准备语句，您需要使用其他三个MySQL语句如下：

- *PREPARE* - 准备执行的声明。
- *EXECUTE* - 执行由`PREPARE`语句定义的语句。
- *DEALLOCATE PREPARE* - 发布`PREPARE`语句。

下图说明了如何使用`PREPARE`语句：

![img](../images/953160741_94092.png)

## MySQL PREPARE语句示例

我们来看一下使用MySQL PREPARE语句的例子。

```sql
PREPARE stmt1 FROM 'SELECT productCode, productName
                    FROM products
                    WHERE productCode = ?';

SET @pc = 'S10_1678';
EXECUTE stmt1 USING @pc;

DEALLOCATE PREPARE stmt1;
```

首先，使用`PREPARE`语句准备执行语句。我们使用`SELECT`语句根据指定的产品代码从`products`表查询产品数据。然后再使用问号(`?`)作为产品代码的占位符。

接下来，声明了一个产品代码变量`@pc`，并将其值设置为`S10_1678`。

然后，使用`EXECUTE`语句来执行产品代码变量`@pc`的准备语句。

最后，我们使用`DEALLOCATE PREPARE`来发布`PREPARE`语句。

在本教程中，我们向您展示了如何使用MySQL `PREPARE`语句来执行带占位符的查询，以提高查询的速度，并使您的查询更安全。