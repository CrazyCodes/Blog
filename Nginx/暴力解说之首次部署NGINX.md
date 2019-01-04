![](https://blog.fastrun.cn/wp-content/uploads/2018/08/1027215404-5b69549ba8084_articlex.png)

# 前言
> 本章基于Centos 7.x系统讲解

本章讲解下在项目上线部署的时候对NGINX的操作。有些童鞋在网上百度类似LNMP安装就跟着命令一条一条执行了，如果没报错还好，一旦报错就懵逼状态了。这是对自己、对代码的不负责任的表现。本章带大家"正经"的安装一次NGINX。

# 下载
下载NGINX的源码包切勿随意查找，好好的NGINX官网在那摆着，何必盲目搜寻呢？

源码包下载地址 : http://nginx.org/en/download.html


![](https://blog.fastrun.cn/wp-content/uploads/2018/08/2324010723-5b69453414d44_articlex.png)

- Mainline Version 主线版本,也是开发版本测试版本,跟自己没仇的最好别下载
- Stable version 稳定版本,一般下载就在稳定版本内找就可以了
- Legacy versions 以往的版本

实际对版本没有太多要求的，下载稳定版本的就可以，每个版本都分.gz的源码包和.exe的windows安装包，会下载游戏还不会下载个压缩包了吗?

很多人纠结安装包放哪比较好，现在告诉你
```
/usr/src
```
usr 历史上全称是user（用户目录）,只不过现在不是这个意思了，。总之放这地死不了人。

```
wget http://nginx.org/download/nginx-1.14.0.tar.gz
```
wget 比吃饭还简单的命令了解一下？

![](https://blog.fastrun.cn/wp-content/uploads/2018/08/741352409-5b69475ed5f94_articlex.png)

然后就开始下载了,下载懂不懂？没下载过游戏吗？

# 安装
下载完之后 ``` /usr/src ``` 目录下就有个 nginx-1.14.0.tar.gz 的压缩包，然后就是刷刷刷的命令，撸起袖子就是干

## 解压缩
tar 解压缩命令
| 参数 | 说明 |
| -- | -- |
| -z | 专门解压gzip压缩的,没看到压缩包最后是.gz嘛 |
| -x | 解压 |
| -v | 解压过程,就是解出来啥文件 |
| -f | 指向文件,一定得放最后 |
```
tar -zxvf nginx-1.14.0.tar.gz
```
随后就是咔咔咔的解压，然后出来一些看不懂的文件
```
nginx-1.14.0/
nginx-1.14.0/auto/
nginx-1.14.0/conf/
nginx-1.14.0/contrib/
nginx-1.14.0/src/
nginx-1.14.0/configure
nginx-1.14.0/LICENSE
nginx-1.14.0/README
nginx-1.14.0/html/
nginx-1.14.0/man/
nginx-1.14.0/CHANGES.ru
nginx-1.14.0/CHANGES
nginx-1.14.0/man/nginx.8
nginx-1.14.0/html/50x.html
nginx-1.14.0/html/index.html
nginx-1.14.0/src/core/
nginx-1.14.0/src/event/
nginx-1.14.0/src/http/
nginx-1.14.0/src/mail/
nginx-1.14.0/src/misc/
nginx-1.14.0/src/os/
nginx-1.14.0/src/stream/
nginx-1.14.0/src/stream/ngx_stream_geo_module.c
nginx-1.14.0/src/stream/ngx_stream.c
nginx-1.14.0/src/stream/ngx_stream.h
nginx-1.14.0/src/stream/ngx_stream_limit_conn_module.c
nginx-1.14.0/src/stream/ngx_stream_access_module.c
......
```
这个时候你所在的 ```/usr/src``` 目录下就有了一个 ```nginx-1.14.0``` 文件夹
```
nginx-1.14.0  nginx-1.14.0.tar.gz
```

## ./configure
目录有了现在就是安装了,不要百度不要谷歌,官网文档写的那么清楚干那多余的活有什么用。小学英语就能看懂。http://nginx.org/en/docs/configure.html , 滑动到最下面，官网给出了一个demo
```
./configure
    --sbin-path=/usr/local/nginx/nginx
    --conf-path=/usr/local/nginx/nginx.conf
    --pid-path=/usr/local/nginx/nginx.pid
    --with-http_ssl_module
    --with-pcre=../pcre-8.41
    --with-zlib=../zlib-1.2.11
```
> 对头就是这样安装,如果你不愿了解这些配置，完全就可以 
```
./configure
```
> 对没错，啥都不用加，9个字母结束战斗，为什么不需要加参数?是因为nginx很多参数都有默认值。

| 参数 | 默认 | 注释 |
| -- | -- | -- |
| --prefix=path | /usr/local/nginx  | nginx安装路径 |
| --sbin-path=path | prefix/sbin/nginx | nginx命令路径  |
| --modules-path=path | prefix/modules | nginx模块路径|
| --conf-path=path | prefix/conf/nginx.conf | nginx配置文件路径 |
| --error-log-path=path | prefix/logs/error.log | nginx默认日志目录 |

> 执行./configure
```
checking for OS
 + Linux 3.10.0-514.26.2.el7.x86_64 x86_64
checking for C compiler ... found
 + using GNU C compiler
 + gcc version: 4.8.5 20150623 (Red Hat 4.8.5-16) (GCC)
checking for gcc -pipe switch ... found
checking for -Wl,-E switch ... found
checking for gcc builtin atomic operations ... found
checking for C99 variadic macros ... found
checking for gcc variadic macros ... found
checking for gcc builtin 64 bit byteswap ... found
checking for unistd.h ... found
checking for inttypes.h ... found
checking for limits.h ... found
checking for sys/filio.h ... not found
checking for sys/param.h ... found
checking for sys/mount.h ... found
checking for sys/statvfs.h ... found
checking for crypt.h ... found
checking for Linux specific features
checking for epoll ... found
checking for EPOLLRDHUP ... found
checking for EPOLLEXCLUSIVE ... not found
checking for O_PATH ... found
checking for sendfile() ... found
checking for sendfile64() ... found
....
```

> configure是个检查工具，上面的直接结果很清楚了,各种的checking,检测下环境支持不？依赖都装了吗？检测成功后你才可以进入下一步,否则你进入下一步也依旧会提示xxx不存在,xxx不支持等等
```
make && make install
```
> make是一个编译工具,你就记住是个编译工具就行了,编译的过程输出
```
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I objs \
	-o objs/src/core/nginx.o \
	src/core/nginx.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I objs \
	-o objs/src/core/ngx_log.o \
	src/core/ngx_log.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I objs \
	-o objs/src/core/ngx_palloc.o \
	src/core/ngx_palloc.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I objs \
	-o objs/src/core/ngx_array.o \
	src/core/ngx_array.c
....
cp conf/nginx.conf '/usr/local/nginx/conf/nginx.conf.default'
test -d '/usr/local/nginx/logs' \
	|| mkdir -p '/usr/local/nginx/logs'
test -d '/usr/local/nginx/logs' \
	|| mkdir -p '/usr/local/nginx/logs'
test -d '/usr/local/nginx/html' \
	|| cp -R html '/usr/local/nginx'
test -d '/usr/local/nginx/logs' \
	|| mkdir -p '/usr/local/nginx/logs'
make[1]: 离开目录“/usr/src/nginx-1.14.0”
```
最后他copy了一些必须文件到指定目录里，这个时候nginx的安装就基本完成了。 


# 配置
如果是仅仅执行了这条命令
```
./configure
```
那nginx的目录就是
```
/usr/local/nginx
-----------
cert  client_body_temp  conf  fastcgi_temp  html  logs  proxy_temp  sbin  scgi_temp  uwsgi_temp
```
以下讲解的所有配置都在```nginx.conf```内进行

## 修改默认指向目录
取消默认指向的解析目录``` html ``` 直接注释掉就行了。一般我会把项目目录指向 ```/var/www``` 一般都在虚拟主机文件中指向
## 避免泛解析
总有些人会把域名解析错地址或者是恶意解析到你的服务器上。对于这种人必须严惩
```
server {
    listen       80  default_server;
    server_name  _;
    return       403;
}
```
分分钟屏蔽他
## 添加虚拟主机
虚拟主机的配置文件可千万别都写到nginx.conf中
一般我会在nginx.conf同级建立一个server目录存放
```
include /usr/local/nginx/conf/server/*.conf;
```
## 配置Gzip压缩
[http://nginx.org/en/docs/http/ngx_http_gzip_module.html](http://nginx.org/en/docs/http/ngx_http_gzip_module.html)
## 添加SSL支持
[https://blog.fastrun.cn/2018/08/01/1-35/#SSL](https://blog.fastrun.cn/2018/08/01/1-35/#SSL)
## 开启日志记录
[https://blog.fastrun.cn/2018/07/29/1-34/](https://blog.fastrun.cn/2018/07/29/1-34/)
## 开启负载均衡
[https://blog.fastrun.cn/2018/08/01/1-35/#i-8](https://blog.fastrun.cn/2018/08/01/1-35/#i-8)
## 开启反向代理
[https://blog.fastrun.cn/2018/08/01/1-35/#i-7](https://blog.fastrun.cn/2018/08/01/1-35/#i-7)
## 设置权限
最好设置nginx命令之允许root用户或者你们公司的运营执行，无缘无故的nginx -s stop可受不了。

# 官网文档
nginx官网是我感觉文档写的最简介最详细的文档。以下列出各部分详细地址
* 编译配置参数 [http://nginx.org/en/docs/configure.html](http://nginx.org/en/docs/configure.html)
* 虚拟主机配置 [http://nginx.org/en/docs/http/server_names.html](http://nginx.org/en/docs/http/server_names.html)
* 配置文件中的计量单位 [http://nginx.org/en/docs/syntax.html](http://nginx.org/en/docs/syntax.html)
* nginx命令参数 [http://nginx.org/en/docs/switches.html](http://nginx.org/en/docs/switches.html)
* nginx负载均衡配置 [http://nginx.org/en/docs/http/load_balancing.html](http://nginx.org/en/docs/http/load_balancing.html)
* nginx官方博客 [https://www.nginx.com/blog/](https://www.nginx.com/blog/)

# 致谢
感谢你看到这里，本篇文章我的语言过于偏激了，还希望见谅。希望本篇文章可以帮助到你，有什么问题可以在评论区留言。谢谢


**别害怕英语,小学英语水平就能看懂，一切源于坚持**