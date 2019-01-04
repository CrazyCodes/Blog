![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/09/2932010282-5b978902533da_articlex.png)

# 前言
MySQL服务器可以在不同的SQL模式下运行，并且可以针对不同的客户端以不同的方式应用这些模式，具体取决于sql_mode系统变量的值。DBA可以设置全局SQL模式以匹配站点服务器操作要求，并且每个应用程序可以将其会话SQL模式设置为其自己的要求。

模式会影响MySQL支持的SQL语法以及它执行的数据验证检查。这使得在不同环境中使用MySQL以及将MySQL与其他数据库服务器一起使用变得更加容易。

# 设置SQL模式
要在运行时更改SQL模式，请sql_mode使用以下SET 语句设置全局或会话 系统变量
```
SET GLOBAL sql_mode = 'modes';
SET SESSION sql_mode = 'modes';
```

# 模式列表
| 模式 | 注释 |
| -- | -- |
| ALLOW_INVALID_DATES | 无效日期会生成错误 |
| ERROR_FOR_DIVISION_BY_ZERO | 除0错误 |
| NO_BACKSLASH_ESCAPES | 禁止使用反斜杠字符（\\）作为字符串中的转义字符。启用此模式后，反斜杠就像其他任何一个普通字符一样。|
| NO_UNSIGNED_SUBTRACTION | 在整数值之间减去（其中一个是类型） UNSIGNED，默认情况下会产生无符号结果。如果结果否则为负，则会导致错误 |
| NO_ZERO_IN_DATE | '0000-00-00' 则允许并且插入产生警告|
| ONLY_FULL_GROUP_BY | select 内指定字段必须出现在 groupby 中，否则错误 |
| STRICT_TRANS_TABLES | 为事务存储引擎启用严格的SQL模式，并在可能的情况下为非事务性存储引擎启用。 |
| STRICT_ALL_TABLES | 为所有存储引擎启用严格SQL模式。无效的数据值被拒绝。 |

详情请参考 https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html#sql-mode-important


# 严格SQL模式

> MySQL服务器可以在不同的SQL模式下运行，并且可以针对不同的客户端以不同的方式应用这些模式，具体取决于sql_mode系统变量的值。在严格SQL模式下，服务器会将某些警告升级为错误。

严格SQL模式适用于以下语句
- ALTER TABLE
- CREATE TABLE
- CREATE TABLE ... SELECT
- DELETE
- INSERT
- LOAD DATA
- LOAD XML
- SELECT SLEEP()
- UPDATE

> 在存储的程序中，如果在严格模式生效时定义了程序，则列出的类型的单个语句将以严格的SQL模式执行。


> 严格的SQL模式适用于以下错误，表示输入值无效或缺失的一类错误。如果值具有错误的列数据类型或可能超出范围，则该值无效。如果要插入的新行不包含其定义中NOT NULL没有显式DEFAULT子句的列的值，则缺少值。


- ER_BAD_NULL_ERROR
- ER_CUT_VALUE_GROUP_CONCAT
- ER_DATA_TOO_LONG
- ER_DATETIME_FUNCTION_OVERFLOW
- ER_DIVISION_BY_ZERO
- ER_INVALID_ARGUMENT_FOR_LOGARITHM
- ER_NO_DEFAULT_FOR_FIELD
- ER_NO_DEFAULT_FOR_VIEW_FIELD
- ER_TOO_LONG_KEY
- ER_TRUNCATED_WRONG_VALUE
- ER_TRUNCATED_WRONG_VALUE_FOR_FIELD
- ER_WARN_DATA_OUT_OF_RANGE
- ER_WARN_NULL_TO_NOTNULL
- ER_WARN_TOO_FEW_RECORDS
- ER_WRONG_ARGUMENTS
- ER_WRONG_VALUE_FOR_TYPE
- WARN_DATA_TRUNCATED




# 致谢
感谢你看到这里，希望本篇文章可以帮到你，谢谢。🙏