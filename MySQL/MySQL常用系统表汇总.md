![](https://blog.fastrun.cn/wp-content/uploads/2018/07/timg-1.jpeg)

# 概述
本篇文章虽大部分内容为参考原文作者的相关内容，但对原文对于文章的逻辑与排版上进行了大范围修改，方便阅读与理解。原文链接在底部

# MySQL5.7 默认模式

|  库名 |  表数量 | 视图数量 |
| ------------ | ------------ | ----|
|  information_schema |  61 | 0|
|  mysql |  32 | 0|
| performance_schema | 87 | 0|
| sys | 1 | 100|

## Information_schema
Information_schema数据库是MySQL自带的，它提供了访问数据库元数据的方式。

### 什么是元数据呢？
元数据是关于数据的数据，如数据库名或表名，列的数据类型，或访问权限等。有些时候用于表述该信息的其他术语包括“数据词典”和“系统目录”。

在MySQL中，把 information_schema 看作是一个数据库，确切说是信息数据库。其中保存着关于MySQL服务器所维护的所有其他数据库的信息。如数据库名，数据库的表，表栏的数据类型与访问权限等。在INFORMATION_SCHEMA中，有数个只读表。它们实际上是视图，而不是基本表，因此，你将无法看到与之相关的任何文件

### information_schema 数据库部分表说明
| 表名  | 注释  |
| ------------ | ------------ |
|  SCHEMATA |  提供了当前mysql实例中所有数据库的信息。是show databases的结果取之此表 |
| TABLES  | 提供了关于数据库中的表的信息（包括视图）。详细表述了某个表属于哪个schema、表类型、表引擎、创建时间等信息。是show tables from schemaname的结果取之此表  |
| COLUMNS | 提供了表中的列信息。详细表述了某张表的所有列以及每个列的信息。是show columns from schemaname.tablename的结果取之此表 |
| STATISTICS |提供了关于表索引的信息。是show index from schemaname.tablename的结果取之此表 |
|  USER_PRIVILEGES | 用户权限表:给出了关于全程权限的信息。该信息源自mysql.user授权表。是非标准表|
| SCHEMA_PRIVILEGES | 方案权限表:给出了关于方案（数据库）权限的信息。该信息来自mysql.db授权表。是非标准表 |
| TABLE_PRIVILEGES | 表权限表:给出了关于表权限的信息。该信息源自mysql.tables_priv授权表。是非标准表 |
| COLUMN_PRIVILEGES |列权限表:给出了关于列权限的信息。该信息源自mysql.columns_priv授权表。是非标准表 |
| CHARACTER_SETS | 字符集表:提供了mysql实例可用字符集的信息。是SHOW CHARACTER SET结果集取之此表|
| COLLATIONS | 提供了关于各字符集的对照信息|
| COLLATION_CHARACTER_SET_APPLICABILITY |指明了可用于校对的字符集。这些列等效于SHOW COLLATION的前两个显示字段。  |
| TABLE_CONSTRAINTS | 描述了存在约束的表。以及表的约束类型|
|  KEY_COLUMN_USAGE |描述了具有约束的键列 |
| ROUTINES | 提供了关于存储子程序（存储程序和函数）的信息。此时，ROUTINES表不包含自定义函数（UDF）。名为“mysql.proc name”的列指明了对应于INFORMATION_SCHEMA.ROUTINES表的mysql.proc表列|
| VIEWS |给出了关于数据库中的视图的信息。需要有show views权限，否则无法查看视图信息 |
| TRIGGERS | 提供了关于触发程序的信息。必须有super权限才能查看该表 |

## performance_schema
PERFORMANCE_SCHEMA这个功能默认是关闭的。需要设置参数： performance_schema 才可以启动该功能，这个参数是静态参数，只能写在my.cnf 中 不能动态修改。 
### performance_schema数据库部分表说明 
|  表名 |  注释 |
| ------------ | ------------ |
|  setup_table | 设置表，配置监控选项  |
|  current_events_table | 记录当前那些thread 正在发生什么事情  |
| history_table | 发生的各种事件的历史记录表 |
| summary_table | 对各种事件的统计表  |
| setup_consumers\setup_instruments|描述各种事件, 设置哪些事件能够被收集  |
| setup_instruments | 描述这个数据库下的表名以及是否开启监控 | 
| setup_timers | 描述监控选项已经采样频率的时间间隔|
| threads |监控服务器所有连接 |
| performance_timers | 设置一些监控信息, 指定mysql服务可用的监控周期，CYCLE表示按每秒检测2603393034次, 目前 performance-schema 只支持'wait'时间的监控，代码树上 wait/ 下的函数都可以监控到|

## mysql
在mysql数据库中，有mysql_install_db脚本初始化权限表，存储权限的表
### mysql数据库部分表说明 
|  表名  | 注释  |
| ------------ | ------------ |
|  user |  用户列、权限列、安全列、资源控制列 |
| db  |  用户列、权限列  |
| host | |
| table_priv | |
| columns_priv | |
| proc_priv | |



##  sys
sys_config ： 这是在这个系统库上存在的唯一一个表
### sys数据库表说明
```
CREATE TABLE `sys_config` (
  `variable` varchar(128) NOT NULL,
  `value` varchar(128) DEFAULT NULL,
  `set_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  `set_by` varchar(128) DEFAULT NULL,
  PRIMARY KEY (`variable`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```
- variable ： 配置选项名称 
- value    ： 配置选项值 
- set_time ： 该行配置修改的时间 
- set_by   ： 该行配置信息修改者，如果从被安装没有修改过，那么这个数据应该为NULL 

![](https://img-blog.csdn.net/20160701140113431)

以上值的会话变量为```@sys.```+表中variable字段,如：
```
@sys.statement_truncate_len 
```

可以
```
set @sys.statement_truncate_len = 32 
```
临时改变值，在会话中会一直使用这个值，如果想要恢复使用表的默认值，只需要将这个会话值设置为null
```
set @sys.statement_truncate_len = null; 
```
#### diagnostics.allow_i_s_tables
默认为OFF ，如果开启表示允许diagnostics() 存储过程执行扫描information_schema.tables 表，如果表很多，那么可能会很耗性能

#### diagnostics.include_raw 
默认为OFF,开启将会从metrics 视图输出未加工处理的数据
#### statement_performance_analyzer.limit
视图在没有加limit限制时，返回的最大行数 
#### statement_truncate_len 
通过format_statement()函数返回值的最大长度

#### debug
这个表非默认选项还有一个@sys.debug参数,可以手动加入
```
INSERT INTO sys_config (variable, value) VALUES('debug', 'ON');
UPDATE sys_config SET value = 'OFF' WHERE variable = 'debug';
SET @sys.debug = NULL;
```

### 关于此表有两个触发器 
#### sys_config_insert_set_user触发器 
如果加入新行通过insert语句，那么这个触发器会把set_by列设置为当前操作者 
#### sys_config_update_set_user触发器 
如果加入新行通过update语句，那么这个触发器会把set_by列设置为当前操作者

# MYSQL SHOW 命令
|  命令  | 注释   |
| ------------ | ------------ |
|  desc [table_name] | 表信息  |
|  show columns from [table_name]  | 表字段  |
|  describe [table_name] | 表信息 |
| show create table [table_name] | 表创建语句 |
| show create database [database_name] | 显示数据库信息 |
| show table status from [database_name] |数据库状态|
|  show tables |显示当前数据库中所有表的名称  |
| show tables from [database_name] | 显示当前数据库中所有表的名称(同上)  |
| show databases | 显示mysql中所有数据库的名称 |
| show processlist | 显示系统中正在运行的所有进程，也就是当前正在执行的查询。|
| show table status | 显示当前使用或者指定的database中的每个表的信息。信息包括表类型和表的最新更新时间  |
|  show columns from [table_name] from [database_name] | 显示表中列名称 |
| show grants for user_name@localhost | 显示一个用户的权限，显示结果类似于grant 命令 |
| show index from [table_name] |显示表的索引  |
| show status | 显示一些系统特定资源的信息，例如，正在运行的线程数量 |
| show variables |显示系统变量的名称和值 |
| show privileges |  显示服务器所支持的不同权限 | 
| show create database [database_name] |显示create database 语句是否能够创建指定的数据库 |
| show create table [table_name] |显示create database 语句是否能够创建指定的数据库  |
| show engies | 显示安装以后可用的存储引擎和默认引擎|
| show innodb status | 显示innoDB存储引擎的状态 | 
| show logs | 显示BDB存储引擎的日志  |
| show warnings | 显示最后一个执行的语句所产生的错误、警告和通知 |
| show errors | 只显示最后一个执行语句所产生的错误|

整理转载自:
https://blog.csdn.net/xlxxcc/article/details/51754524

# 致谢
感谢你看完这篇文章，作者原文的知识点概括的很全面，但是文章的排版与表达比较凌乱，不适宜新手来查阅，所以我就做了重构文章的一件事。希望可以帮到你。
