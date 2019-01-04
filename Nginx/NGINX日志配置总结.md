![](https://blog.fastrun.cn/wp-content/uploads/2018/07/1119748353-5b5d9b8753264_articlex.png)

# 前言
本来准备讲解nginx和apache的日志的，但是个人不太推荐apache(纯属个人爱好)，这里就不介绍apache的日志了。

作为一名程序员，比码代码还重要那么一点点的东西就是日志的分析和查询。下面列出常见日志及设置方法。

# 配置文件

> nginx分access_log和error_log两种日志

设置需要在nginx.conf中,默认通过源码包编译安装nginx目录应在
```
/usr/local/nginx
```
目录下，如果你通过yum或者其他方式安装，不清楚或不知道nginx具体安装目录，可以使用
```
find / -name nginx.conf
```
or
```
nginx -V | grep prefix
-------------
nginx version: nginx/1.13.9
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-16) (GCC)
built with OpenSSL 1.0.2k-fips  26 Jan 2017
TLS SNI support enabled
configure arguments: --prefix=/usr/local/nginx --with-http_ssl_module
```
# 开启访问日志
如果是你源码包默认安装的，打开路径如下
```
vim /usr/local/nginx/nginx.conf
```
找到如下内容
```
http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;
    
    ...
}
```
将log_format到access_log的注释打开即可,log_format可定义nginx的日志规格。

## log_format默认规格参数表
| 名称 | 注解 |
| -- | -- |
|$remote_addr | 客户端/用户的IP地址|
| $time_local | 访问时间 |
| $request|请求方式 + 请求地址 |
| $status | 请求状态码 与HTTP状态码一致 |
| $body_bytes_sent | 请求的地址大小 以bytes格式计算|
| $http_referer | 请求来源,从什么地方访问的 |
| $http_user_agent | 用户信息(浏览器信息) |
| $http_x_forwarded_for |转发IP地址 |
 
# 开启错误日志
如果是你源码包默认安装的，打开路径如下
```
vim /usr/local/nginx/nginx.conf
```
找到如下内容
```
error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
```
将注解删除即可，你可以将不同的错误类型分开存储如
```
error_log logs/error.log notice;
```
notice既为错误类型，不写则是全部。

# 致谢
感谢你看到这里，日志操作与分析的相关文章后面我还会写一些，希望可以帮助到你。谢谢

** 代码多变,初心不变 **
