在学习 MySQL 之前，首先需要了解数据库和 SQL 。 如果您已经知道数据库和 SQL，那么可以直接跳转到下一章节的学习。

## 数据库简介

当您想收听最喜欢的歌曲时，可以从智能手机上打开播放列表。在这种情况下，播放列表是数据库就是从数据库中读取出来的。

当您拍摄照片并将其上传到微博，朋友圈等，这样的社交网络中的帐户时，您的照片库就有可能存储在一个数据库中。

当您浏览电子商务网站购买鞋子，衣服等时，您使用购物车就是数据库应用。

数据库无处不在。 那么什么是数据库？ 根据定义，数据库只是一个结构化的数据集合。

数据本质上相互关联，例如，产品属于产品类别并与多个标签相关联。这就是为什么要使用关系数据库。

在关系数据库中，我们使用表对产品，类别，标签等数据进行建模。 表包含列和行。它就像一个电子表格(Excel)。

表可以涉及的使用有：一对一，一对多，多对一关系等关系。

因为我们要处理大量的数据，所以需要一种方法来定义数据库，表等，并更有效地处理数据。 另外，我们可以将数据转换成数据信息。

所以就需要SQL来处理了。

## SQL - 数据库的语言

SQL 代表结构化查询语言 (**Structured Query Language**)。SQL 是用于访问数据库的标准化语言。

> ANSI/SQL 定义了SQL 标准。当前版本的 SQL 是 *SQL: 2003* 。每当我们引用 SQL 标准时，指的就是当前的SQL 版本。

SQL 包含三个部分：

- 数据定义语言包含定义数据库及其对象的语句，例如表，[视图](http://www.yiibai.com/mysql/views.html)，[触发器](http://www.yiibai.com/mysql/triggers.html)，[存储过程](http://www.yiibai.com/mysql/stored-procedure.html)等。
- 数据操作语言包含允许您[更新](http://www.yiibai.com/mysql/update-data.html)和[查询数据](http://www.yiibai.com/mysql/select-statement-query-data.html)的语句。
- 数据控制语言允许[授予用户权限](http://www.yiibai.com/mysql/grant.html)访问数据库中特定数据的权限。

现在，您了解数据库和 SQL，现在是时候回答下一个问题了…

## MySQL 是什么？

*My* 是 MySQL 的联合创始人 *Monty Widenius* 的女儿的名字。MySQL 是 *My* 和 *SQL* 的组合，这就是 *MySQL* 命名的由来。

MySQL 的官方网址： <http://www.mysql.com/> ，MySQL 的社区版本下载地址为： <http://dev.mysql.com/downloads/mysql/> ，在写本文时，当前的 MySQL 最新版本是：*5.7.18* 。

MySQL 是一个数据库管理系统，也是一个关系数据库。它是由 Oracle 支持的开源软件。这意味着任何一个人都可以使用 MySQL 而不用支付一毛钱。 另外，如果需要，还可以更改其源代码或进行二次开发以满足您的需要。

即使 MySQL 是开源软件，但是可以从 Oracle 购买商业许可证版本，以获得高级支持服务(特殊企业用户需要)。

与其他数据库软件 (如 Oracle 数据库或 Microsoft SQL Server)相比，MySQL 非常容易学习和掌握。

MySQL 可以在各种平台上运行 UNIX，Linux，Windows 等。可以将其安装在服务器甚至桌面系统上。 此外，MySQL 是可靠，可扩展和快速的。

如果您开发网站或 Web 应用程序，MySQL 是一个不错的选择(强烈建议使用)。MySQL 是 LAMP 堆栈的重要组成部分，包括 Linux，Apache，MySQL 和 PHP。

