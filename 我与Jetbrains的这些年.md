![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/3172380423-5bf6bb76b2c05_articlex.png)

# 前言
本章主要说一下如何去使用Jetbrains的各类工具，并且在上周参加了Jetbrains开发者日的大会，把参会的感受和体验在这里分享给各位。话不多说，我们进入正题。

# 使用
想必各位一定使用过Jetbrains的任意一款产品，就算没用过也应该听说过吧。Jetbrains从开始至今总共分为三大模块
1. Developer Ide (集成开发工具)
2. Language (Jetbrains的开发语言 Kotlin)
3. DevOps (任务管理，持续集成，持续部署的一些东西)

对于Developer Ide我推荐使用Jetbrains ToolBox，它是管理所有Jetbrains Ide的工具。

![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/2738462830-5bf763dbbaaa8_articlex.png)

Jetbrains平均一个月做一次小更新，安装了Jetbrains ToolBox就不必再去关心更新的事情了。并且Jetbrains ToolBox 还提供了项目列表的功能，如果这时候你需要打开一个项目，你无需去关心项目是哪种开发语言做的，只需要打开它并选择你需要开启的项目即可。

![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/3367914272-5bf764e45ef15_articlex.png)


这里要注意，使用Alfred的同学，Jetbrains ToolBox将Ide安装的目录不在是/Application而是~/Application，要记得加入到可搜索列表中，否则是找不到启动文件的。


> 以PhpStrom为例，来讲解一下Jetbrains Ide的一些有趣的配置。

## 快速编码
这在Jetbrains开发者日上范老师讲过的一个技巧，你需要打开

> Preferences -> Editor -> {General->Postfix Completion || Live Templates}
    
General->Postfix Completion 与 Live Templates 都是为了去提供编码速度的设置。首先我们先看下 Live Templates
   

![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/1027937040-5bf76881278a9_articlex.png)

当你在输入fore时按空格(当然也可以选择回车，Tab键，这是需要设置的)

![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/2201331946-5bf7691dc24c8_articlex.png)

就会出现完整的foreach语句，你可以把他当做填空题去完善这个结构
```
fore 
----> 
foreach ($ITERABLE$ as $VAR_VALUE$) {
    $END$
}
```
当然还有一些其他的例如 eco -> echo , prof -> 创建一个protected的类方法。Ide默认已经为我们准备了一些常用的简写方法，涵盖了至少你知道的所有语言。当然如果没有你想要的你也可以自行添加新的模板。


![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/579261965-5bf769e67327e_articlex.png)

真不愧叫 Live Templates,第二个来介绍下General->Postfix Completion，这个对于第一个来说是另外一种编辑，第一种是通过键入初始化命令来生成模板，例如eco -> echo ，第二种则是以对象形式去便捷的去生成模板。下面举个栗子


![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/1502191407-5bf76b0c98d65_articlex.png)

当你键入 $a.notnull 则生成 if ($a !== null) {}
```
$a.notnull
---->
if ($a !== null) {

}
```
在编码的时候不是每次都会想好整个流程结构在开始编码，所以Live Templates 就有一定的局限性了。这时候 General->Postfix Completion 就起到了很大作用。最后我们介绍 File and Code Templates , 在快捷生成编码及后续生成代码是在编码开始时的一些骚操作，为了将编码更快，我们还需要将常用的文件模板加入其中


![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/1027937040-5bf76881278a9_articlex.png)

例如你长期使用Laravel框架去开发应用，在使用laravel写一个功能的时候我们会经历下面几个步骤

> 声明一个路由 -> 创建一个Controller,Model,Action ... 文件 -> 调用Model操作数据 -> 返回给用户结果

如果你使用PhpStrom你大致这样写
1. 打开路由文件 router+空格 完成一个路由结构的自动生成 (当然你需要提前设置)
2. 创建一个 Php Controller,Model,Action 文件，文件结构已经设置好
3. 使用General->Postfix Completion设置好的写法分分钟解决所有操作

## Database
PhpStrom内置了Database，面板十分简洁易用。他面板的右上角
![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/4104122340-5bf771de5184e_articlex.png)
有兴趣你可以去试试

## Api
细心的朋友一定知道PhpStrom内置了接口测试工具,他在 Tools -> Http Client -> Test RestFul Web Service 下。

![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/2809847463-5bf7727ca72a0_articlex.png)

临时的测试你不必打开万能的Postman，你完全可以通过ide内置的测试工具去完成api测试。当然还有另外一种更厉害的方式 -> 创建一个 test.http 文件
```
GET www.baidu.com
```

![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/558378001-5bf78e204e894_articlex.png)

像上述这样，点击运行你将获得Response
```
GET http://www.baidu.com

HTTP/1.1 200 OK
Date: Fri, 23 Nov 2018 05:19:42 GMT
Server: Apache
Last-Modified: Tue, 12 Jan 2010 13:48:00 GMT
ETag: "51-47cf7e6ee8400"
Accept-Ranges: bytes
Content-Length: 81
Cache-Control: max-age=86400
Expires: Sat, 24 Nov 2018 05:19:42 GMT
Connection: Keep-Alive
Content-Type: text/html

<html>
<meta http-equiv="refresh" content="0;url=http://www.baidu.com/">
</html>


Response code: 200 (OK); Time: 71ms; Content length: 81 bytes
```
当然对比Postman依旧不逊色，既然已经将软件改为编码形式，那在Postman内的功能，我们在.http文件中很方便的就可以实现。可以添加请求参数
```
GET http://www.baidu.com
Content-Type: application/json
{
"name":"zhangsan"
}

```
每个请求以下一个请求方式前结束。具体请移步官网查看。

## 主题
工欲善其事，必先利其器。上面说的Jetbrains提供的功能只是冰山一角，想要具体的学习如何使用还请移步官方。帅气的UI也是调整开发效率的一部分。看到自己的ide非常漂亮，编码的心情自己好的不得了。我使用的是 Material Theme UI


![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/2267589781-5bf7932089ee7_articlex.png)

关于字体的调整可以在 Editor -> Font 下进行

![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/3237674940-5bf793994dca3_articlex.png)

当然如果你不仅仅从事PHP相关的开发，好不容易配置出来一个自己满意的IDE，写Go的时候用Goland还要配置，大可不必这样。你可以通过 File -> Export Settings 来导出你的配置


![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/2425191417-5bf7944a87c56_articlex.png)

随后在通过 File -> Import Settings 来导入你的配置。这样就OK了。如果你购买了 Jetbrains 全家桶或者任意的Ide，也可以通过File -> Sync Settings To Jetbrains Account 来同步到你的Jetbrains账号，类似于云同步一样。

# 便捷
剩下的就是快捷键了，快捷键乃IDE编码之本，什么？不需要？ 那你咋不用Word编码去？快捷键我就不总结了，网络上太多了。下面贴出laravel-china一位大神的总结。

https://laravel-china.org/topics/5420/your-keyboard-shortcuts-please
# 大会
以上说了一部分大会的内容了。Jetbrains的开发者日也并不是全部商业宣传，至少它做的事情是值得让开发者认可的。PPT无法分享给各位，我也不知道怎么拿到。抱歉！大会主要将了以下几项内容，上午是主会场，下午分俩个会场 Kotlin & Java 和 Web，作为一个PHP程序员，我没得选。


----------
上午

1. 大会开始，说了很多关于他们的语言 Kotlin 的使用方法。
2. 随后说了Jetbrains内部的工作方式及管理方法
3. 开始ide -> Kotlin 显示 Kotlin特性


----------
下午

1. 简洁高效的PHP编程指南，推荐了一下测试、调试方法&类库及PhpStrom的使用方法
2. 在真实世界中进行 Go性能优化 讲的go pprof
3. 用TDD学习高效开发 开始这大佬写了一通的java测试用例，不咋能看懂。后面说了一些关于程序人生的事情
4. JetBrains的无痛DevOps解决方案 最后讲了JetBrainsDevOps的一些套件，例如TeamCity (与TravisCi类似) ，Upsource 等等。具体可参考 https://www.jetbrains.com/devops/?fromMenu

----------

总而言之，总体下来大多都是干货，至少我感觉没有任何商业宣传的性质。

# 致谢
到这里本章就结束了，感谢看到这里，不过我还要提醒一句给各位开发者

**编辑器不要汉化**
**编辑器不要汉化**
**编辑器不要汉化**

相信你可以明白。谢谢！
 