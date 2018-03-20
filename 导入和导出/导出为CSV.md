在本教程中，您将学习如何将MySQL表导出到CSV文件的各种方法/技术。

CSV代表逗号分隔值。 您经常使用CSV文件格式在*Microsoft Excel*，*Open Office*，*Google Docs*等应用程序之间交换数据。

以CSV文件格式从MySQL数据库中获取数据将非常有用，因为您可以按照所需的方式分析和格式化数据。

MySQL提供了一种将查询结果导出到位于数据库服务器中的CSV文件的简单方法。

在导出数据之前，必须确保：

- MySQL服务器的进程对包含目标CSV文件的目标文件夹具有写访问权限。
- 要导出的目标CSV文件不能存在。

以下查询从`orders`表中查询选择已取消的订单：

```sql
SELECT 
    orderNumber, status, orderDate, requiredDate, comments
FROM
    orders
WHERE
    status = 'Cancelled';
```

要将此结果集导出为CSV文件，请按如下方式向上述查询添加一些子句：

```sql
SELECT 
    orderNumber, status, orderDate, requiredDate, comments
FROM
    orders
WHERE
    status = 'Cancelled' 
INTO OUTFILE 'F:/worksp/mysql/cancelled_orders.csv' 
FIELDS ENCLOSED BY '"' 
TERMINATED BY ';' 
ESCAPED BY '"' 
LINES TERMINATED BY '\r\n';
```

该语句在`F:/worksp/mysql/`目录下创建一个包含结果集，名称为`cancelled_orders.csv`的CSV文件。

CSV文件包含结果集中的行集合。每行由一个回车序列和由`LINES TERMINATED BY '\r\n'`子句指定的换行字符终止。文件中的每行包含表的结果集的每一行记录。

每个值由`FIELDS ENCLOSED BY '"'`子句指示的双引号括起来。 这样可以防止可能包含逗号(`，`)的值被解释为字段分隔符。 当用双引号括住这些值时，该值中的逗号不会被识别为字段分隔符。

## 将数据导出到文件名包含时间戳的CSV文件

我们经常需要将数据导出到CSV文件中，该文件的名称包含创建文件的时间戳。 为此，您需要使用[MySQL准备语句](http://www.yiibai.com/mysql/prepared-statement.html)。

以下命令将整个`orders`表导出为将时间戳作为文件名的一部分的CSV文件。

```sql
SET @TS = DATE_FORMAT(NOW(),'_%Y%m%d_%H%i%s');

SET @FOLDER = 'F:/worksp/mysql/';
SET @PREFIX = 'orders';
SET @EXT    = '.csv';

SET @CMD = CONCAT("SELECT * FROM orders INTO OUTFILE '",@FOLDER,@PREFIX,@TS,@EXT,
    "' FIELDS ENCLOSED BY '\"' TERMINATED BY ';' ESCAPED BY '\"'",
    "  LINES TERMINATED BY '\r\n';");

PREPARE statement FROM @CMD;

EXECUTE statement;
```

下面，让我们来详细讲解上面的命令。

- **首先**，构造了一个具有当前时间戳的查询作为文件名的一部分。
- **其次**，使用`PREPARE`语句`FROM`命令准备执行语句。
- **第三**，使用`EXECUTE`命令执行语句。

可以通过[事件](http://www.yiibai.com/mysql/triggers/working-mysql-scheduled-event.html)包装命令，并根据需要定期安排事件的运行。

## 使用列标题导出数据

如果CSV文件包含第一行作为列标题，那么该文件更容易理解，这是非常方便的。

要添加列标题，需要使用[UNION](http://www.yiibai.com/sql-union-mysql.html)语句如下：

```sql
(SELECT 'Order Number','Order Date','Status')
UNION 
(SELECT orderNumber,orderDate, status
FROM orders
INTO OUTFILE 'F:/worksp/mysql/orders_union_title.csv'
FIELDS ENCLOSED BY '"' TERMINATED BY ';' ESCAPED BY '"'
LINES TERMINATED BY '\r\n');
```

如查询所示，需要包括每列的列标题。

## 处理NULL值

如果结果集中的值包含[NULL](http://www.yiibai.com/mysql/null.html)值，则目标文件将使用“`N/A`”来代替数据中的`NULL`值。要解决此问题，您需要将`NULL`值替换为另一个值，例如不适用(`N/A`)，方法是使用[IFNULL函数](http://www.yiibai.com/mysql/ifnull.html)，如下：

```sql
SELECT 
    orderNumber, orderDate, IFNULL(shippedDate, 'N/A')
FROM
    orders INTO OUTFILE 'F:/worksp/mysql/orders_null2na.csv' 
    FIELDS ENCLOSED BY '"' 
    TERMINATED BY ';' 
    ESCAPED BY '"' LINES 
    TERMINATED BY '\r\n';
```

我们用`N/A`字符串替换了`shippingDate`列中的`NULL`值。 CSV文件将显示`N/A`而不是`NULL`值。