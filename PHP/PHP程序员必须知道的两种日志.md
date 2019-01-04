![](https://blog.fastrun.cn/wp-content/uploads/2018/07/74072834b3e92ffc915ddd9e2cd3a936.png)

# 前言
作为一名程序员，比码代码还重要那么一点点的东西就是日志的分析和查询。下面列出常见日志及设置方法。

# php-fpm 慢日志
php慢日志需要在php-fpm.conf设置，如果使用源码包安装默认请执行下面命令
```
cp php-fpm.conf.default php-fpm.conf
```
默认通过源码包编译安装php目录应在
```
/usr/local/php
```
目录下，如果你通过yum或者其他方式安装，不清楚或不知道php具体安装目录，可以使用
```
find / -name php-fpm.conf
```
or
```
php -i | grep Path
------------------------------------------
[root@xxxx etc]# php -i | grep Path
Configuration File (php.ini) Path => /usr/local/php/etc
XPath Support => enabled
Path to sendmail => /usr/sbin/sendmail -t -i
[root@xxxx etc]#
```
## 开启慢查询日志
旧的版本是在php-fpm.conf设置 （实际是我忘记了哪个版本），php7.x版本源码包编译后需要www.conf修改慢查询配置
```
vim /usr/local/php/etc/php-fpm.d/www.conf
```
不过配置项都一样的，如果你在php-fpm.conf找不到，就去他的同级目录php-fpm.d下面找下吧。
```
; The log file for slow requests
; Default Value: not set
; Note: slowlog is mandatory if request_slowlog_timeout is set
;slowlog = log/$pool.log.slow

; The timeout for serving a single request after which a PHP backtrace will be
; dumped to the 'slowlog' file. A value of '0s' means 'off'.
; Available units: s(econds)(default), m(inutes), h(ours), or d(ays)
; Default Value: 0
;request_slowlog_timeout = 0

```
- slowlog 设置慢查询日志的生成目录
- request_slowlog_timeout 设置慢查询的标准时间（打开此配置就相当于开启了慢查询日志），配置以秒为单位，一般设置3s。

# php-error 错误日志
在生产环境中是不允许php报错的，就算报错也是白屏或者500，所以在生产环境中的日志收集是非常重要的。
## 开启错误日志
一般情况下，php错误日志的配置都在php.ini文件中
```
/usr/local/php/etc/php.ini
---------------------------
error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT
display_errors = Off
log_errors = On
; Log errors to specified file. PHP's default behavior is to leave this value
; empty.
; http://php.net/error-log
; Example:
;error_log = php_errors.log
; Log errors to syslog (Event Log on Windows).
;error_log = syslog
```
- error_log 错误日志的生成目录
- error_reporting 生产环境错误级别应全开
- display_errors 在页面上不显示错误
- log_errors 开启错误日志

最终的结果是

```
error_log = /var/log/php_error.log
display_errors = Off
error_reporting = E_ALL
log_errors = On
```
# 致谢
感谢你看到这里，日志操作与分析的相关文章后面我还会写一些，希望可以帮助到你。谢谢

**代码多变,初心不变**
