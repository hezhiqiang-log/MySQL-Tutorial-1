下表说明了MariaDB和MySQL之间的主要区别：

| ~                                               | MySQL                                            | MariaDB                                                      |
| ----------------------------------------------- | ------------------------------------------------ | ------------------------------------------------------------ |
| **开发商**                                      | Oracle公司                                       | `MariaDB Corporation AB (MariaDB Enterprise)`,`MariaDB基金会(社区MariaDB Server)` |
| **协议**                                        | MySQL                                            | MySQL + MariaDB                                              |
| **源代码**                                      | 开源+专有                                        | 开源                                                         |
| **开发**                                        | 关闭                                             | 开放                                                         |
| **合作**                                        | 有限                                             | 广泛                                                         |
| **存储引擎**                                    | InnoDB,MyISAM,BLACKHOLE,CSV,MEMORY,ARCHIVE,MERGE | InnoDB,MyISAM,BLACKHOLE,CSV,MEMORY,ARCHIVE,MERGE,ColumnStore,MyRocks,Aria,SphinxSE,TokuDB,CONNECT,SEQUENCE,Spider,Cassandra |
| **检查约束**                                    | No                                               | Yes                                                          |
| **DEFAULT表达式**                               | No                                               | Yes,此外，还支持`BLOB`和[TEXT](http://www.yiibai.com/mysql/text.html)列的DEFAULT值 |
| **虚拟列**                                      | Yes                                              | Yes                                                          |
| **动态列**                                      | No                                               | Yes                                                          |
| **角色**                                        | Yes                                              | Yes                                                          |
| **DELETE … RETURNING**                          | Yes                                              | No                                                           |
| **GIS支持**                                     | Yes                                              | Yes                                                          |
| **ALTER TABLE和LOAD DATA INFILE语句的进度报告** | No                                               | Yes                                                          |
| **表消除**                                      | No                                               | Yes                                                          |
| **SQL管理**                                     | MySQL Workbench                                  | SQLyog                                                       |
| **监控**                                        | MySQL Enterprise Monitor                         | Monyog                                                       |
| **备份**                                        | MySQL Enterprise Backup                          | MariaDB Backup                                               |
| **SQL公用表表达式(CTE)**                        | Yes(MySQL8.0+)                                   | Yes                                                          |
| **SQL窗口函数**                                 | Yes(MySQL8.0+)                                   | Yes                                                          |
| **JSON支持**                                    | Yes                                              | Yes                                                          |
| **数据屏蔽**                                    | No                                               | 是(MariaDB MaxScale)                                         |
| **加密**                                        | MySQL Enterprise Encryption                      | MariaDB Encryption                                           |
| **数据库防火墙**                                | MySQL Enterprise Firewall                        | MaxScale Firewall                                            |
| **审计**                                        | MySQL Enterprise Audit                           | MariaDB Audit                                                |
| **Analytics(分析)**                             | No                                               | MariaDB ColumnStore                                          |
| **分区**                                        | MySQL Partitioning                               | MariaDB Partitioning                                         |
| **路由**                                        | MySQL Router                                     | MariaDB MaxScale                                             |
| **复制**                                        | MySQL Replication                                | MariaDB Replication                                          |