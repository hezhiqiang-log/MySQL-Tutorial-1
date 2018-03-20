在本教程中，您将学习如何使用MySQL `TEXT`在数据库表中存储文本数据。

## MySQL TEXT数据类型简介

MySQL `TEXT`数据类型是除了[CHAR](http://www.yiibai.com/mysql/char-data-type.html)和[VARCHAR](http://www.yiibai.com/mysql/varchar.html)字符类型，MySQL为我们提供了具有`CHAR`和`VARCHAR`无法实现的更多功能的`TEXT`类型。

`TEXT`可用于存储可以从`1`字节到`4GB`长度的文本字符串。 我们经常在电子商务网站中找到用于在新闻站点存储物品的`TEXT`数据类型，如：产品详细描述。

与`CHAR`和`VARCHAR`不同，您不必在列使用`TEXT`类型时指定存储长度。 此外，当检索或插入文本数据(如`CHAR`和`VARCHAR`)时，MySQL不会删除或填充空格。

> 请注意，`TEXT`数据不存储在数据库服务器的内存中，因此，每当查询`TEXT`数据时，MySQL都必须从磁盘读取它，这与`CHAR`和`VARCHAR`相比要慢得多。

MySQL提供四种TEXT类型：`TINYTEXT`，`TEXT`，`MEDIUMTEXT`和`LONGTEXT`。

下面显示每个`TEXT`类型的大小，假设我们使用一个字符集，该字符集需要`1`个字节来存储字符。

## TINYTEXT - 1个字节(255个字符)

`TINYTEXT`可以存储的最大字符是`255`(`2 ^ 8 = 256`，`1`字节开销)。

需要少于`255`个字符的列应该使用`TINYTEXT`类型，长度不一致，不需要排序，例如：博文摘录，文章摘要等。

请参阅以下示例：

```sql
CREATE TABLE articles (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255),
    summary TINYTEXT
);
```

在本示例中，我们[创建](http://www.yiibai.com/mysql/create-table.html)了一个名为`articles`的新表，该表具有数据类型为`TINYTEXT`的`summary`列。

## TEXT - 64KB(65,535个字符)

`TEXT`数据类型最多可容纳`64KB`，相当于`65535`(`2 ^ 16 - 1`)个字符。 `TEXT`还需要`2`字节开销。

文本可以容纳文章的正文。 请考虑以下示例：

```sql
ALTER TABLE articles 
ADD COLUMN body TEXT NOT NULL
AFTER summary;
```

在本示例中，我们使用[ALTER TABLE](http://www.yiibai.com/mysql/alter-table.html)语句将具有`TEXT`数据类型的`body`列添加到`articles`表。

## MEDIUMTEXT - 16MB(16,777,215个字符)

`MEDIUMTEXT`最多可容纳*16MB*的文本数据，相当于`16,777,215`个字符。它需要`3`字节开销。
`MEDIUMTEXT`可用于存储相当大的文本数据，如书籍文本，白皮书等。例如：

```sql
USE testdb;

CREATE TABLE whitepapers (
    id INT AUTO_INCREMENT PRIMARY KEY,
    body MEDIUMTEXT NOT NULL,
    published_on DATE NOT NULL
);
```

## LONGTEXT - 4GB(4,294,967,295个字符)

`LONGTEXT`可以存储高达*4GB*的文本数据，这是非常巨大的。 它需要`4`字节开销。

在本教程中，您已经学会了如何使用各种MySQL `TEXT`数据类型来存储数据库表中的文本。