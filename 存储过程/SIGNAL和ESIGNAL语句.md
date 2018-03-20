在本教程中，您将学习如何使用`SIGNAL`和`RESIGNAL`语句来引发存储过程中的错误条件。

## MySQL SIGNAL语句

使用`SIGNAL`语句在存储的程序(例如存储过程，[存储函数](http://www.yiibai.com/mysql/stored-function.html)，[触发器](http://www.yiibai.com/mysql/triggers.html)或[事件](http://www.yiibai.com/mysql/triggers-modifying-mysql-events.html))中向调用者返回错误或警告条件。 `SIGNAL`语句提供了对返回值(如值和消息`SQLSTATE`)的信息的控制。

以下说明`SIGNAL`语句的语法：

```sql
SIGNAL SQLSTATE | condition_name;
SET condition_information_item_name_1 = value_1,
    condition_information_item_name_1 = value_2, etc;
```

`SIGNAL`关键字是由`DECLARE CONDITION`语句声明的`SQLSTATE`值或条件名称。 请注意，`SIGNAL`语句必须始终指定使用`SQLSTATE`值定义的`SQLSTATE`值或命名条件。

要向调用者提供信息，请使用`SET`子句。如果要使用值返回多个条件信息项名称，则需要用逗号分隔每个名称/值对。

`condition_information_item_name`可以是`MESSAGE_TEXT`，`MYSQL_ERRORNO`，`CURSOR_NAME`等。

以下存储过程将订单行项目添加到现有销售订单中。 如果订单号码不存在，它会发出错误消息。

```sql
DELIMITER $$

CREATE PROCEDURE AddOrderItem(in orderNo int,
 in productCode varchar(45),
 in qty int,in price double, in lineNo int )

BEGIN
 DECLARE C INT;

 SELECT COUNT(orderNumber) INTO C
 FROM orders 
 WHERE orderNumber = orderNo;

 -- check if orderNumber exists
 IF(C != 1) THEN 
 SIGNAL SQLSTATE '45000'
 SET MESSAGE_TEXT = 'Order No not found in orders table';
 END IF;
 -- more code below
 -- ...
END $$
DELIMITER ;
```

*首先*，它使用传递给存储过程的输入订单号对订单进行计数。
*第二步*，如果订单数不是`1`，它会引发*SQLSTATE 45000*的错误以及`orders`表中不存在订单号的错误消息。

> 请注意，`45000`是一个通用`SQLSTATE`值，用于说明未处理的用户定义异常。

如果调用存储过程`AddOrderItem()`，但是传递不存在的订单号，那么将收到一条错误消息。

```sql
CALL AddOrderItem(10,'S10_1678',1,95.7,1);
```

执行上面代码，得到以下结果 -

```sql
mysql> CALL AddOrderItem(10,'S10_1678',1,95.7,1);
1644 - Order No not found in orders table
```

## MySQL RESIGNAL语句

除了`SIGNAL`语句，MySQL还提供了用于引发警告或错误条件的`RESIGNAL`语句。

`RESIGNAL`语句在功能和语法方面与`SIGNAL`语句相似，只是：

- 必须在错误或警告处理程序中使用`RESIGNAL`语句，否则您将收到一条错误消息，指出“`RESIGNAL when handler is not active`”。 请注意，您可以在存储过程中的任何位置使用`SIGNAL`语句。
- 可以省略`RESIGNAL`语句的所有属性，甚至可以省略`SQLSTATE`值。

如果单独使用`RESIGNAL`语句，则所有属性与传递给条件处理程序的属性相同。

以下存储过程在将发送给调用者之前更改错误消息。

```sql
DELIMITER $$

CREATE PROCEDURE Divide(IN numerator INT, IN denominator INT, OUT result double)
BEGIN
 DECLARE division_by_zero CONDITION FOR SQLSTATE '22012';

 DECLARE CONTINUE HANDLER FOR division_by_zero 
 RESIGNAL SET MESSAGE_TEXT = 'Division by zero / Denominator cannot be zero';
 -- 
 IF denominator = 0 THEN
 SIGNAL division_by_zero;
 ELSE
 SET result := numerator / denominator;
 END IF;
END $$
DELIMITER ;
```

下面我们来调用`Divide()`存储过程。

```sql
CALL Divide(10,0,@result);
```

执行上面语句，得到以下结果 -

```sql
mysql> CALL Divide(10,0,@result);
1644 - Division by zero / Denominator cannot be zero
```

在本教程中，我们向您展示了如何使用`SIGNAL`和`RESIGNAL`语句引发存储程序中的错误条件。