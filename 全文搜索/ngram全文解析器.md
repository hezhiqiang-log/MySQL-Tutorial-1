本教程将向您展示如何使用MySQL `ngram`全文解析器来支持中文，日文，韩文等表意语言的全文搜索。

## MySQL ngram全文解析器简介

MySQL内置的全文解析器使用空格确定单词的开始和结束。当涉及汉语，日语或韩语等表意语言语言时，这是一个限制，因为这些语言不使用分词符。

为了解决这个问题，MySQL提供了`ngram`全文解析器。自*MySQL5.7.6*版起，MySQL将`ngram`全文解析器作为内置的服务器插件，这意味着当MySQL数据库服务器启动时，MySQL会自动加载该插件。 MySQL支持用于*InnoDB*和*MyISAM*存储引擎的`ngram`全文解析器。

根据定义，`ngram`是来自文本序列的多个字符的连续序列。 `ngram`全文解析器的主要功能是将文本序列标记为`n`个字符的连续序列。

以下说明了`ngram`全文解析器如何标记不同值`n`的文本序列：

```sql
n = 1: 'm','y','s','q','l'
n = 2: 'my', 'ys', 'sq','ql' 
n = 3: 'mys', 'ysq', 'sql'
n = 4: 'mysq', 'ysql'
n = 5: 'mysql'
```

## 使用 ngram 解析器创建FULLTEXT索引

要创建使用`ngram`全文解析器的`FULLTEXT`索引，可以在[CREATE TABLE](http://www.yiibai.com/mysql/create-table.html)，[ALTER TABLE](http://www.yiibai.com/mysql/alter-table.html)或`CREATE INDEX`语句中添加`WITH PARSER ngram`。

例如，以下语句创建新的帖子表，并将标题和正文列添加到使用`ngram`全文解析器的`FULLTEXT`索引。

```sql
USE testdb;
CREATE TABLE posts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255),
    body TEXT,
    FULLTEXT ( title , body ) WITH PARSER NGRAM
)  ENGINE=INNODB CHARACTER SET UTF8;
```

以下[INSERT](http://www.yiibai.com/mysql/insert-statement.html)语句赂`posts`表中插入一个新行：

```sql
SET NAMES utf8;

INSERT INTO posts(title,body)
VALUES('MySQL全文搜索','MySQL提供了具有许多好的功能的内置全文搜索'),
      ('MySQL教程','学习MySQL快速，简单和有趣');
```

请注意，`SET NAMES`语句设置客户端和服务器将用于发送和接收数据的字符集; 在本示例中，它使用的是`utf8`。

要查看`ngram`如何标记文本，请使用以下语句：

```sql
SET GLOBAL innodb_ft_aux_table="testdb/posts";

SELECT 
    *
FROM
    information_schema.innodb_ft_index_cache
ORDER BY doc_id , position;
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT 
    *
FROM
    information_schema.innodb_ft_index_cache
ORDER BY doc_id , position;
+------+--------------+-------------+-----------+--------+----------+
| WORD | FIRST_DOC_ID | LAST_DOC_ID | DOC_COUNT | DOC_ID | POSITION |
+------+--------------+-------------+-----------+--------+----------+
| my   |            2 |           3 |         2 |      2 |        0 |
| ys   |            2 |           3 |         2 |      2 |        1 |
| sq   |            2 |           3 |         2 |      2 |        2 |
| ql   |            2 |           3 |         2 |      2 |        3 |
| l全  |            2 |           2 |         1 |      2 |        4 |
| 全文 |            2 |           2 |         1 |      2 |        5 |
| 文搜 |            2 |           2 |         1 |      2 |        8 |
| 搜索 |            2 |           2 |         1 |      2 |       11 |
| ql   |            2 |           3 |         2 |      2 |       18 |
| my   |            2 |           3 |         2 |      2 |       18 |
| ys   |            2 |           3 |         2 |      2 |       18 |
| sq   |            2 |           3 |         2 |      2 |       18 |
| l提  |            2 |           2 |         1 |      2 |       22 |
| 提供 |            2 |           2 |         1 |      2 |       23 |
| 供了 |            2 |           2 |         1 |      2 |       26 |
| 了具 |            2 |           2 |         1 |      2 |       29 |
| 具有 |            2 |           2 |         1 |      2 |       32 |
| 有许 |            2 |           2 |         1 |      2 |       35 |
| 许多 |            2 |           2 |         1 |      2 |       38 |
| 多好 |            2 |           2 |         1 |      2 |       41 |
| 好的 |            2 |           2 |         1 |      2 |       44 |
| 的功 |            2 |           2 |         1 |      2 |       47 |
| 功能 |            2 |           2 |         1 |      2 |       50 |
| 能的 |            2 |           2 |         1 |      2 |       53 |
| 的内 |            2 |           2 |         1 |      2 |       56 |
| 内置 |            2 |           2 |         1 |      2 |       59 |
| 搜索 |            2 |           2 |         1 |      2 |       60 |
| 文搜 |            2 |           2 |         1 |      2 |       60 |
| 全文 |            2 |           2 |         1 |      2 |       60 |
| 置全 |            2 |           2 |         1 |      2 |       62 |
| my   |            2 |           3 |         2 |      3 |        0 |
| ys   |            2 |           3 |         2 |      3 |        1 |
| sq   |            2 |           3 |         2 |      3 |        2 |
| ql   |            2 |           3 |         2 |      3 |        3 |
| l教  |            3 |           3 |         1 |      3 |        4 |
| 教程 |            3 |           3 |         1 |      3 |        5 |
| 学习 |            3 |           3 |         1 |      3 |       12 |
| 习m  |            3 |           3 |         1 |      3 |       15 |
| sq   |            2 |           3 |         2 |      3 |       18 |
| ql   |            2 |           3 |         2 |      3 |       18 |
| my   |            2 |           3 |         2 |      3 |       18 |
| ys   |            2 |           3 |         2 |      3 |       18 |
| l快  |            3 |           3 |         1 |      3 |       22 |
| 快速 |            3 |           3 |         1 |      3 |       23 |
| 速， |            3 |           3 |         1 |      3 |       26 |
| ，简 |            3 |           3 |         1 |      3 |       29 |
| 简单 |            3 |           3 |         1 |      3 |       32 |
| 单和 |            3 |           3 |         1 |      3 |       35 |
| 和有 |            3 |           3 |         1 |      3 |       38 |
| 有趣 |            3 |           3 |         1 |      3 |       41 |
+------+--------------+-------------+-----------+--------+----------+
50 rows in set
```

此查询对于故障排除目的很有用。例如，如果一个单词不包括在搜索结果中，则该单词可能没有被编入索引，因为它是一个停止词或者可能是其它原因。

## 设置ngram令牌大小

在前面的示例可以看到，默认情况下，`ngram`中的令牌大小(`n`)为`2`，要更改令牌大小，请使用`ngram_token_size`配置选项，值的范围是：`1`到`10`。

请注意，较小的令牌大小可使较小的全文搜索索引更快地进行搜索。

因为`ngram_token_size`是只读变量，因此您只能使用两个选项设置其值：

*第一种方式*，在启动字符串中：

```powershell
mysqld --ngram_token_size=1
```

*第二种方式* - 在配置文件中：

```ini
[mysqld]
ngram_token_size=1
```

## ngram解析器短语搜索

MySQL将短语搜索转换成`ngram`短语搜索。 例如，`abc`被转换为`ab bc`，它返回包含`ab bc`和`abc`的文档。

以下示例显示在`posts`表中搜索短语：`搜索`：

```sql
SELECT 
    id, title, body
FROM
    posts
WHERE
    MATCH (title , body) AGAINST ('搜索' );
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT 
    id, title, body
FROM
    posts
WHERE
    MATCH (title , body) AGAINST ('搜索' );
+----+---------------+-------------------------------------------+
| id | title         | body                                      |
+----+---------------+-------------------------------------------+
|  1 | MySQL全文搜索 | MySQL提供了具有许多好的功能的内置全文搜索 |
+----+---------------+-------------------------------------------+
1 row in set
```

## 用ngram处理搜索结果

**自然语言模式**

在自然语言模式搜索中，搜索项被转换为`ngram`值的并集。 假设令牌大小为`2`或者二进制，则搜索项`mysql`被转换为我的`my` `ys` `sq`和`ql`。

```sql
SELECT 
    *
FROM
    posts
WHERE
    MATCH (title , body) AGAINST ('简单和有趣' IN natural language MODE);
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT 
    *
FROM
    posts
WHERE
    MATCH (title , body) AGAINST ('简单和有趣' IN natural language MODE);
+----+-----------+---------------------------+
| id | title     | body                      |
+----+-----------+---------------------------+
|  2 | MySQL教程 | 学习MySQL快速，简单和有趣 |
+----+-----------+---------------------------+
1 row in set
```

**布尔模式**

在`BOOLEAN MODE`搜索中，搜索项被转换成`ngram`短语搜索。 例如：

```sql
SELECT 
    *
FROM
    posts
WHERE
    MATCH (title , body) AGAINST ('简单和有趣' IN BOOLEAN MODE);
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT 
    *
FROM
    posts
WHERE
    MATCH (title , body) AGAINST ('简单和有趣' IN BOOLEAN MODE);
+----+-----------+---------------------------+
| id | title     | body                      |
+----+-----------+---------------------------+
|  2 | MySQL教程 | 学习MySQL快速，简单和有趣 |
+----+-----------+---------------------------+
1 row in set
```

## ngram通配符搜索

ngram `FULLTEXT`索引仅包含`ngram`，因此它不知道短语的开始。执行通配符搜索时，可能会返回意外的结果。

以下规则将应用于使用ngram `FULLTEXT`搜索索引的通配符搜索：

如果通配符中的前缀短语短于`ngram`令牌大小，则查询返回所有包含以前缀项为起始的`ngram`令牌的文档。 例如：

```sql
SELECT 
    id, title, body
FROM
    posts
WHERE
    MATCH (title , body) AGAINST ('my*' );
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT 
    id, title, body
FROM
    posts
WHERE
    MATCH (title , body) AGAINST ('my*' );
+----+---------------+-------------------------------------------+
| id | title         | body                                      |
+----+---------------+-------------------------------------------+
|  1 | MySQL全文搜索 | MySQL提供了具有许多好的功能的内置全文搜索 |
|  2 | MySQL教程     | 学习MySQL快速，简单和有趣                 |
+----+---------------+-------------------------------------------+
2 rows in set
```

如果通配符中的前缀短语长于`ngram`令牌大小，则MySQL将将前缀术语转换为`ngram`短语，并忽略通配符运算符。 请参阅以下示例：

```sql
SELECT 
    id, title, body
FROM
    posts
WHERE
    MATCH (title , body) AGAINST ('mysqld*' );
```

执行上面查询语句，得到以下结果 -

```sql
mysql> SELECT 
    id, title, body
FROM
    posts
WHERE
    MATCH (title , body) AGAINST ('mysqld*' );
+----+---------------+-------------------------------------------+
| id | title         | body                                      |
+----+---------------+-------------------------------------------+
|  1 | MySQL全文搜索 | MySQL提供了具有许多好的功能的内置全文搜索 |
|  2 | MySQL教程     | 学习MySQL快速，简单和有趣                 |
+----+---------------+-------------------------------------------+
2 rows in set
```

在这个例子中，短语“`mysqld`”被转换为`ngram`短语：`my` `ys` `sq` `ql` `ld`，因此返回包含其中一个短语的所有文档。

## 处理停止词

ngram解析器不包括在停止词列表中包含停止词的令牌。例如，假设`ngram_token_size`为`2`，文档包含`abc`。 `ngram`解析器将文档标记为`ab`和`bc`。 如果`b`是一个停用词，则`ngram`将包含`ab`和`bc`，因为它们包含`b`。

请注意，如果语言不是英语，则必须定义自己的词条列表。 此外，长度大于`ngram_token_size`的停止词将被忽略。

在本教程中，您已经学会了如何使用MySQL `ngram`全文解析器来处理表意语言的全文搜索。