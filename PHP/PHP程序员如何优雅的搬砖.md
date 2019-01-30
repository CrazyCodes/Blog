![clipboard.png](https://resources.blog.fastrun.cn/wp-content/uploads/2019/01/2748557248-5c4ebc16818c1_articlex.png)


> 我一生的文章都会放在这里，我的博客，我希望每一行代码，每一段文字都能帮助你。https://github.com/CrazyCodes/Blog

# 前言
Hello , 各位Coder ！

在这里向各位工程师提前拜年 “新年快乐” ， 距离年三十已经没有几天了，可能有些朋友还坚持在一线战斗着，有些已经回到家乡陪伴家人。北京每到这个时候都似一座空城，城与城之间表现的那么凄凉。

这是年前的最后一篇文章，本章来聊一聊程序员如何优雅的搬砖

搬砖既 “为达到目的，不断重复某项工作的行为，其实与造轮子一样，不谋而合”

# 基础
![clipboard.png](https://resources.blog.fastrun.cn/wp-content/uploads/2019/01/3727715730-5c4ebc544468d_articlex.png)


这里的基础并非单指其技术能力，技术底蕴，更有意体现程序员在初期不断重复的工作而获得的感想与意识。想必大家都是这么过来的，第一年时根据需求不断创新，不断磨练。所有的功能都必须自己写，用其他人的不放心。但自己写的东西经常出问题，无论是思路或者代码都不够精炼。一层一层的技术债在完工后不断的涌现出来。当时你会不会有跑路的想法？

# 选择

![clipboard.png](https://resources.blog.fastrun.cn/wp-content/uploads/2019/01/1762861377-5c4ebc6becb08_articlex.png)

在不断的进步中，我们积攒了很多经验，这里指的变是开发经验，并非什么技术经验。开发经验大概意思是在看到某项需求时，可以快速的根据自己的知识与经验的储备选择其开发框架、语言、数据库及流程逻辑等。这里就是在做选择，你会对该需求给出自己的几项方案，而不是现查现写。

# 开源

![](https://resources.blog.fastrun.cn/wp-content/uploads/2019/01/2243375467-5c4ebcc1a16ff_articlex.png)

开源的目的是什么？建立一个更好的技术生态圈，Coder与Coder之间互相帮助，达到更好的效果（并不是结对编程哈），现如今PHP的生态圈非常健康的运转，无论是PHP7的发布或者Composer的诞生，都为贵圈提供了更好的技术与实践的支持。我们应更好的去接触、熟练的去运用各位大神费尽心血为我们准备的全新的PHP

# 本机


讲过很多初学者在本地开发时，对本地的开发环境毫无关心，随随便便拿一个集成开发工具便搭建了一整套的运行环境，对其本质毫无理解，我想大部分人都有过“全干工程师”的历程，对本地环境毫无在意的程序员，敢说在生产、测试环境中依旧无法出色的表现其技术能力。

# 建议
在这里，我给出一些常见业务需求的解决方案 （并非是一些高级的东西） 

## 后台
如果是从头做一个后台，然而又不想从0开始搭建后台的逻辑，在这里我强烈推荐laravel-admin,laravel虽然性能方面低于其他框架，但其作为后台的开发框架来说我认为还是第一名的。

- https://laravel.com/
- https://laravel-admin.org/
- https://laravel-admin.org/docs/zh

laravel-admin 安装比其他的开源程序要简单的多，这都寄托于能力极佳的composer与laravel
```
composer require encore/laravel-admin

```

## 接口
接口开发着重性能，相应速度，如果依旧喜欢laravel，可以选择lumen

- https://lumen.laravel.com/

或者使用C编写的框架 Phalcon

- https://phalconphp.com/zh/

实在感觉这些框架太过庞大，复杂也可以选择 Slim ，他一定精简到让你飞起

- http://www.slimframework.com/

不想使用框架？但从0写还嫌麻烦，这时你可以考虑 鸟哥的Yaf 或者 韩天峰的 Swoole
- https://www.swoole.co.uk/
- http://php.net/manual/en/book.yaf.php

## 其他
熟练去搜集、查找适合自己业务的包，熟练去运用其优势。让自己不需要再重复造轮子，无止尽的还技术债，这才是2019年程序员应该学习的。

- https://segmentfault.com/a/1190000016004756
- https://segmentfault.com/a/1190000017782596
- https://packagist.org/
- https://github.com/

# 致谢
感谢你看到这里，希望本篇文章可以帮助到你，谢谢。
