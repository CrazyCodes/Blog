![](https://resources.blog.fastrun.cn/wp-content/uploads/2019/03/1167427476-5c80ebf78f3b0_articlex.png)

# 前言

大家好，我是CrazyCodes，我没有消失，最近在准备考试，所以文章出的比较慢，请见谅

有些童鞋感觉对Swoole不从下手，也不知在什么业务上使用它，看它这么火却学不会也是挺让人捉急的一件事情。

> Swoole：面向生产环境的 PHP 异步网络通信引擎

啥是异步网络通信?

# 异步通信

![](https://resources.blog.fastrun.cn/wp-content/uploads/2019/03/1872214882-5c80e9751f0e7_articlex.png)

简单点来说，就是一个人和一群人的关系，一个人去做十件事，需要一件一件去做，一群人去做10件事，可以分配每个人做每件事。我们用Swoole可以做什么？

- 聊天室
- 并发的处理 （读大文件）
- 异步MySQL
- 异步Redis
- 等等

当然不去做，只在看，很难理解为何选择使用Swoole去做这些。从现在开始，我们暂时不关心上面的这些概念，啥话都不说，就是干

# EasySwoole
EasySwoole ? 名副其实，作者为了让开发者更便捷的使用Swoole 而封装的开发框架,地址在下方
> EasySwoole https://www.easyswoole.com/

使用EasySwoole你会发现有很多很难理解的概念及用法。没关系，跟着我，慢慢来~

# 安装
EasySwoole的环境要求
- 保证 PHP 版本大于等于 7.1
- 保证 Swoole 拓展版本大于等于 4.3.0
- 需要 pcntl 拓展的任意版本
- 使用 Linux / FreeBSD / MacOS 这三类操作系统
- 使用 Composer 作为依赖管理工具

如果你感觉以上要求太苛刻，你可以选择使用Docker快速部署一套开发环境或者使用更简单的 homestead

在使用EasySwoole之前我们要安装Swoole，Swoole是PHP扩展，我们可以通过
```
pecl install swoole
```
快速安装，或者使用源码编译的形式安装

安装完扩展后，接下来我们就使用万能composer来安装EasySwoole
```
composer require easyswoole/easyswoole=3.x
php vendor/bin/easyswoole install
```

# 服务管理
EasySwoole(Swoole)与其他框架不同，他不擅长开发Web,请将目标定位在后端服务上。以下内容为引用官方文档
```
php easyswoole start 
```
# Hello World
> 以下为官方文档内容 https://www.easyswoole.com/Manual/3.x/Cn/_book/Introduction/install.html

在项目根目录下创建如下的目录结构，这个目录是编写业务逻辑的应用目录，编辑 Index.php 文件，添加基础控制器的代码
```
project              项目部署目录
----------------------------------
├─App        应用目录
│  └─HttpController      应用的控制器目录
│     └─Index.php    默认控制器文件
----------------------------------
```
```
<?php
namespace App\HttpController;


use EasySwoole\Http\AbstractInterface\Controller;

class Index extends Controller
{

    function index()
    {
        // TODO: Implement index() method.
        $this->response()->write('hello world');
    }
}
```
然后编辑根目录下的 composer.json 文件，注册应用的命名空间
```
{
    "autoload": {
        "psr-4": {
            "App\\": "App/"
        }
    },
    "require": {
        "easyswoole/easyswoole": "3.x-dev"
    }
}
```
最后执行 ``` composer dumpautoload ``` 命令更新命名空间，框架已经可以自动加载 App 目录下的文件了，此时框架已经安装完毕，可以开始编写业务逻辑
```
# 更新命名空间映射
composer dumpautoload
# 启动框架
php easyswoole start
```
启动框架后，访问 http://localhost:9501即可看到 Hello World 。

# 组件
EasySwoole提供了很多实用的组件包括
- 控制台组件
- 定时器
- 触发器
- 日志处理等等...

# 致谢
从下一章开始，我们逐步使用EasySwoole的各项功能并开发一个简单的并发版爬虫系统，感谢你看到这里，希望本文可以帮到你，谢谢