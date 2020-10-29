![](https://resources.blog.fastrun.cn/wp-content/uploads/2020/10/2663373599-5e6c5b38ccd6e_articlex.png)
#前言
大家好，我是CrazyCodes,今天聊聊为什么在MAC上，我不安装类似XAMPP或者Laravel的Homestead的集成开发环境

#对比
我也是卸载了这些环境不到一年的时间，为何要去卸载他呢，原因其实很简单，扩展性差，这里这个扩展性指的不是集成环境这个软件本身的扩展性，当初安装的目的其实不就是**省事**嘛~，也不会过多的查看文档，而是在需要扩展的时候，学习成本会逐渐提高，这里的扩展性=学习成本。

#开始
MAC自带了Apache和PHP的版本，这时候直接安装一个MySQL其实就可以进入开发了。可以选择使用brew安装
```
brew install mysql
```
当然如果希望使用其他版本的PHP或者是将Apache替换为Nginx也是很简单的时候，只要部署过单机服务器，其实在Mac上的步骤是差不多的。

#php
首先还是使用brew安装一个你心仪的PHP版本，可以通过
```
brew search php7
```
来搜索下现有的php7+都有哪些版本，这里在php@7.3上打了✔️，意思是我已经安装了这个版本，现在使用7.4版本做一些讲解。
![](https://resources.blog.fastrun.cn/wp-content/uploads/2020/10/466241470-5e6c555346980_articlex.png)
```
brew install php@7.4
```
![](https://resources.blog.fastrun.cn/wp-content/uploads/2020/10/2672533940-5e6c55a27aaaf_articlex.png)
经过一顿猛如虎的操作后，正常状况下会看到下面这样
![](https://resources.blog.fastrun.cn/wp-content/uploads/2020/10/545534636-5e6c56cb4b177_articlex.png)
多么人性化的提示，还告诉了咱们如何配置。当然这不是主要的，看要最下面这段
```
The php.ini and php-fpm.ini file can be found in:
    /usr/local/etc/php/7.4/

To have launchd start php now and restart at login:
  brew services start php
Or, if you don't want/need a background service you can just run:
  php-fpm
```
安装完成后，他会说明php安装到什么位置了，可以使用命令```brew services start php```或者```php-fpm```去启动和重启PHP,这个时候就可以在指定目录下看到熟知的php相关文件
![](https://resources.blog.fastrun.cn/wp-content/uploads/2020/10/2171069275-5e6c579c8338c_articlex.png)

#nginx
依旧是使用brew安装一个你心仪的nginx版本
![](https://resources.blog.fastrun.cn/wp-content/uploads/2020/10/3431562296-5e6c57fdd1c38.png)
这里我已经安装过nginx了。就不截图演示了。具体操作如下
```
brew install nginx
```
一顿操作猛如虎后，与PHP安装完成后的提示一样，会告诉我们安装到哪个目录下了，一般默认为
```
/usr/local/etc/nginx
```
![](https://resources.blog.fastrun.cn/wp-content/uploads/2020/10/2415719393-5e6c5856a46e0_articlex.png)
依旧也是我们熟知的NGINX相关的目录以及配置文件，servers目录是我新建的，用于存放server配置

#mysql
依旧依旧是使用brew安装一个你心仪的mysql版本，通过使用命令
```
brew search mysql
```
![](https://resources.blog.fastrun.cn/wp-content/uploads/2020/10/642420240-5e6c58d047385_articlex.png)
这时要看清楚，有些并不是mysql本体，可能会是一些链接库。要脑子清楚的选择安装，mysql8我已经安装，以5.6为例
```
brew install mysql@5.6
```
一顿操作猛如虎后，mysql也如期安装完成，正常情况下不会报错的

#补充
全部完成后，按照正常步骤

1.启动Nginx
2.启动PHP
3.启动MySQL

访问链接 http://localhost
会看到nginx友爱的欢迎界面。

nginx server配置与单机配置是一致的。如果感觉配置host每次都很麻烦，可以使用一个ihost去统一管理
![](https://resources.blog.fastrun.cn/wp-content/uploads/2020/10/2072146744-5e6c5a82cea9f_articlex.png)
![](https://resources.blog.fastrun.cn/wp-content/uploads/2020/10/780687878-5e6c5a63f1390_articlex.png)
所有源码都与源码包安装一致，相信你一定会按需安装需要的扩展了把~

#致谢
感谢你看到这里，希望本篇文章可以帮到你。