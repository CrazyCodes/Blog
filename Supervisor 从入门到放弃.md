![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/09/963433467-5b9b1ea063ba7_articlex.png)

# 前言
> Supervisor是一个客户端/服务器系统，允许其用户在类UNIX操作系统上控制许多进程。(官方解释)

简单点来讲，就是一个监控脚本运行的工具，不过他可以统一化管理，laravel的队列文档上也有相关使用方式方法，例如

- 定时脚本的启动、重启、关闭和日志监控
- swoole的启动、重启、关闭和日志监控 (众所周知，swoole大部分的特性都只能在cli中运行)
- redis的启动、重启、关闭和日志监控 (redis自身未提供类似phpmyadmin的后台可视化工具)
- laravel中的队列、一些自动化的脚本、workman等等的脚本

一般都使用 ``` &test.sh ``` 让其来保证在后台运行，但在很多情况下，无法对脚本个人化的监控。这时你可能就需要 Supervisor 来帮助你。你大可把它当作你的unix系统的可视化管理后台。下面来让我们见证它的强大之处。

# 安装
Supervisor 有多种安装方式，我推荐其中最简单也是最容易安装的一种
```
apt-get -y install python-setuptools
easy_install supervisor
```
正如你所见，两条命令即完成安装
# 配置
Supervisor安装完成后，运行 ```echo_supervisord_conf```。这将打印一个示例的Supervisor配置文件到您的终端。只要你能看到打印的配置文件内容。

Supervisor 不会自动生成配置文件。

请使用命令  ```echo_supervisord_conf > /etc/supervisord.conf ``` 来生成配置文件。

## 部分配置文件信息表
| 名称 | 注释 | 栗子 |
| -- | -- | -- |
| inet_http_server[port] | 内置管理后台 |*:8888 |
| inet_http_server[username] | 管理后台用户名 | admin|
| inet_http_server[password] | 管理后台密码 | admin |
| include[files] | 设置进程配置文件格式 | /etc/supervisor/supervisor.d/*.ini |

# 运行
Supervisor 启动需加载配置文件
```
supervisord -c /etc/supervisor/supervisord.conf
```
停止命令是
```
supervisorctl shutdown
```
重新加载配置文件
```
supervisorctl reload
```

Supervisor 以 ```[program:[your_cli_name]] ``` 以每段进程配置文件的开头，your_cli_name 则是你的进程名称，名称会显示在Supervisor后台管理工具和Supervisor cli命令输出上。我们以运行php-fpm为例
```
[program:php7]
command=php-fpm
```
哦呦，就是酱紫简单。没有过多的废话。或者运行一段shell。
```
[program:echo]
command=sh echo.sh

--------------------------------

echo.sh

your_name="my name zhangsan" 
echo $your_name

```
当然laravel队列也是依旧简单
```
[program:laravel-worker]
command=php /home/forge/app.com/artisan queue:work sqs --sleep=3 --tries=3
```
当然这里只是简单的演示，让你可以快速上手，配置脚本内不仅仅只有command命令。
具体可见官方文档 http://www.supervisord.org/configuration.html#program-x-section-settings
# 后台
Supervisor提供的后台管理比较简单

![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/09/2731681913-5b9b1b03bc8d0_articlex.png)

大致功能有 重启、启动、停止进程，打印日志，清除日志等。基本上就这么几个简单的功能，当然也没有宕机报警，日志报警什么的。不过强大的Supervisor为我们提供了接口😄

# 接口
通过API能获取基本所有的信息，例如进程列表，某个进程的状态，进程日志。包括对进程的重启、停止、开启等操作，将Supervisor彻底集成到内部监控后台也是没有什么问题的。

具体内容请移步官网Api文档 http://www.supervisord.org/api.html

# 脚本
最后送给朋友们一个简单的脚本，方便用于学习Supervisor
```shell
#!/bin/bash
set -x

case $1 in
    'sp')
    		if [[ $2 == 'start' ]]; then
    			"supervisord -c /etc/supervisor/supervisord.conf"
    		elif [[ $2 == 'stop' ]]; then
    			"supervisorctl shutdown"
    		elif [[ $2 == 'restart' ]]; then
    			"supervisorctl shutdown"
    			"supervisord -c /etc/supervisor/supervisord.conf"
    		elif [[ $2 == 'reload' ]]; then
    			"supervisorctl reload"
    		fi
    	;;
esac
```
你可以使用这个简单的脚本快速启动、重启、关闭Supervisor
```
sh test.sh sp start // 启动
sh test.sh sp restart // 重启
```
# 致谢
感谢看到这里，希望本章可以帮到你。谢谢