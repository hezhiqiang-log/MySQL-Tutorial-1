在本教程中，我们将给您一些技巧，以便您在存储过程中在什么情况分别选择`IF`和`CASE`语句。

MySQL提供`IF`和CA`SE`语句，使您能够根据某些条件(称为流控制)执行一个SQL代码块。那么您应该使用什么语句？ 对于大多数开发人员，在`IF`和`CASE`之间进行选择只是个人偏好的问题。但是，当您决定使用`IF`或`CASE`时，应该考虑以下几点：

- 当将单个表达式与唯一值的范围进行比较时，[简单CASE语句](http://www.yiibai.com/mysql/case-statement.html)比[IF语句](http://www.yiibai.com/mysql/if-statement.html)更易读。另外，简单`CASE`语句比`IF`语句更有效率。
- 当您根据多个值检查复杂表达式时，`IF`语句更容易理解。
- 如果您选择使用`CASE`语句，则必须确保至少有一个`CASE`条件匹配。否则，需要定义一个[错误处理](http://www.yiibai.com/mysql/error-handling-in-stored-procedures.html)程序来捕获错误。`IF`语句则不需要处理错误。
- 在大多数组织(公司)中，总是有一些所谓的开发指导文件，为开发人员提供了编程风格的命名约定和指导，那么您应参考本文档并遵循开发实践。
- 在某些情况下，`IF`和`CASE`混合使用反而使您的存储过程更加可读和高效。