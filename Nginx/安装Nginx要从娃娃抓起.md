本文翻译与2017年8月26日

使用configure命令配置构建。它定义了系统的各个方面，包括允许使用nginx进行连接处理的方法。最后它创建一个Makefile。该configure命令支持以下参数：

 - **--prefix=path**  定义一个nginx存储的系统目录，这个目录将存储nginx的代码和配置文件。如果不设置将默认安装到 /usr/local/nginx 目录中。   
   
 - **--sbin-path=path** 设置一个nginx可执行文件的路径。默认情况下路径为 prefix/sbin/nginx

 - **--conf-path=path** 设置默认nginx配置文件的所在目录,你可以通过不同的配置文件启动nginx，在启动的时候加上参数 -c fileprefix/conf/nginx.conf 即可

 - **--pid-path=path** 设置将存储主进程ID的nginx.pid文件的路径。默认情况下文件被安装到 prefix/logs/nginx.pid。

 - **--error-log-path=path** 设置错误、警告、提醒等错误信息的存放文件及目录，默认会安装到
prefix/logs/error.log 内。安装后可以在nginx.conf配置文件中修改error_log选项来更改。

 - **--http-log-path=path**  设置服务器请求日志文件的名称。安装后可通过nginx.conf内access_log选项修改，默认命名为 prefix/logs/access.log

 - **--build=name** 设置一个可选的nginx构建名称。

 - **--user=name**  设置nginx的所属用户，正常情况下我都会建立一个nginx的用户用于指定，默认的用户是nobody。

 - **--group=name**  设置nginx的所属群组，正常情况下我都会建立一个nginx的群组用于指定，默认为nobody组

 - **--without-http_gzip_module**  不编译压缩的HTTP服务器的响应模块。编译并运行此模块需要zlib库。

 - **--without-http_rewrite_module**  不编译重写模块。编译并运行此模块需要PCRE库支持。

 - **--without-http_proxy_module**   禁用构建HTTP服务器代理模块。

 - **--with-http_ssl_module**  编译https模块，让服务器可以支持https协议。

 - **--with-pcre=path**  指定PCRE的路径

 - **--with-pcre-jit**  使用“即时编译”支持构建PCRE库（1.1.12， pcre_jit指令）。

 - **--with-zlib=path**  指定zlib的路径


----------
下面的这几项配置我也不咋懂，无法合理的去解释，了解的朋友可以私信我

 - **--with-select_module** --without-select_module  启用或禁用构建允许服务器使用该select()方法的模块 。如果平台似乎不支持更合适的方法（如kqueue，epoll或/ dev / poll），则会自动构建该模块。

 - **--with-poll_module** --without-poll_module 启用或禁用构建允许服务器使用该poll()方法的模块 。如果平台似乎不支持更合适的方法（如kqueue，epoll或/ dev / poll），则会自动构建该模块。

 - **--with-cc-opt=parameters**  设置将添加到CFLAGS变量的其他参数。当在FreeBSD下使用系统PCRE库时， --with-cc-opt="-I /usr/local/include" 应该指定。如果支持的文件数量select()需要增加，也可以在这里指定，如： --with-cc-opt="-D FD_SETSIZE=2048"。

 - **--with-ld-opt=parameters**  设置链接期间将使用的其他参数。当在FreeBSD下使用系统PCRE库时， --with-ld-opt="-L /usr/local/lib" 应该指定。

参数使用示例（所有这些都需要输入一行）：

```
./configure 
    --sbin-path = /usr/local/nginx/nginx 
    --conf-path = /usr/local/nginx/nginx.conf 
    --pid-path = /usr/local/nginx/nginx.pid 
    --with-http_ssl_module 
    --with-pcre = ../pcre-8.41 
    --with-zlib = ../zlib-1.2.11
```

配置完成后，使用make进行编译和安装。