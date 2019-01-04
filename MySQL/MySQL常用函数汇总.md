![](https://blog.fastrun.cn/wp-content/uploads/2018/07/timg-1.jpeg)

# 概述
MySQL常用函数汇总,希望可以帮到你，没事看看当复习
# 字符串函数
| 函数 | 功能 |
| --  | -- |
| CONCAT(s1,s2,....) | 字符串连接 |
| INSERT(str,x,y,instr) | 将指定开始标记到结束的字符串替换为指定字符串 |
| LOWER(str) |  将字符串所有字符转为小写|
| UPPER(str) | 将字符串所有字符串转为大写|
| LEFT(str,x) | 返回字符串str最左边的x个字符|
| RIGHT(str,x) | 返回字符串str最右边的x个字符 |
| LPAD(str,n,pad)| 在str最左边填充n个pad|
| RPAD(str,n,pad)| 在str最右边填充n个pad|
| LTRIM(str) | 去掉字符串str左侧的空格|
| RTRIM(str) | 去掉字符串str右侧的空格|
| REPEAT(str,x)| 返回str重复x次的结果|
| STRCMP(s1,s2)| 比较字符串s1和s2|
| REPLACE(str,a,b)|用字符串b替换字符串str中所有出现的字符串a|
| TRIM(str) |去掉字符串行尾和行头的空格 |
| SUBSTRING(str,x,y) | 返回从字符串str x位置起y个字符长度的字串| 
# 数学函数
| 函数 | 功能 |
| -- | -- |
| ABS(x) | 返回x的绝对值|
| CEIL(x) | 返回大于x的最小整数值 |
| FLOOR(x) | 返回小于x的最大整数值|
| MOD(x,y) | 返回x/y的模|
| RAND()| 返回 0～1内的随机值|
| ROUND(x,y) | 返回参数x的四舍五入的有y位小数的值|
| TRUNCATE(x,y)| 返回数字x截断位y位小数的结果|
# 日期和时间函数
| 函数|功能|
| -- | -- |
| CURDATE() | 返回当前日期|
| CURTIME() | 返回当前时间|
| NOW() |  返回当前的日期和时间|
| UNIX_TIMESTAMP(date) | 返回日期date的UNIX时间戳|
| FROM_UNIXTIME| 返回UNIX时间戳的日期值|
| WEEK(date) | 返回日期date为一年中的第几周 |
| YEAR(date) | 返回日期date的年份|
| HOUR(time) | 返回time的小时值|
| MINUTE(time) | 返回time的分钟值|
| MONTHNAME(date) | 返回date的月份名|
| DATE_FORMAT(date,fmt) | 返回按字符串fmt格式日期date值|
| DATE_ADD(date,interval expr type)| 返回一个日期或时间值加上一个时间间隔的时间值|
| DATEDIFF(expr,expr2) | 返回起始时间expr和结束时间expr2之间的天数|
# 流程函数
| 函数 | 功能|
| -- | -- |
| IF(value,t f) | 如果value是真，返回t；否则返回f|
| IFNULL(value1,value2)| 如果value1不为空，返回value1，否则返回value2|
| CASE WHEN [value1] THEN[result1]...ELSE[default]END|如果value1是真，返回result1，否则返回result|
| CASE[expr] WHEN [value1]THEN[result1]...ELSE[default]END|如果expr等于value1，返回result1，否则返回default|
# 其他常用函数
| 函数| 功能|
| -- | --|
| DATEBASE() | 返回当前数据库名|
| VERSION()| 返回当前数据库版本|
| USER() | 返回当前登录用户名|
| INET_ATON(ip) | 返回ip地址的数字表示|
| INET_NTOA(num) | 返回数字代表的ip地址|
| PASSWORD(str) | 返回字符串str的加密版本|
| MD5() | 返回字符串str的md5值|

# 其他文章
我整理的另一篇文章
MySQL常用系统表汇总 : https://blog.fastrun.cn/2016/07/10/1-27/

# 致谢
感谢你看完这篇文章，我相信初中级码农每天都在接触SQL，熟记mysql函数就跟熟记php函数一样重要。谢谢🙏


  [1]: /img/bVbdCe2