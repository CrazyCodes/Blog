![](https://resources.blog.fastrun.cn/wp-content/uploads/2019/01/42289522-5c2d6e7147769_articlex.png)

# 前言
首先祝各位朋友新年快乐，工作顺利，事业有成，永无BUG。

有些朋友一直疑惑Travis CI是个什么东西，网络上搜索后得知的答案是自动测试，自动发布。自动xx 这个名词貌似非常流行，这也是DevOps的一部分，什么?DevOps是什么？我们下一章讲讲这个。

先来解释下“自动”这个词，自动顾名思义是自动完成一些事情，上述的自动测试，并不是各位认知中的人肉测试，而是通过我们自己的规则去测试，例如跑一遍tests内的所有测试，自动发布也是通过脚本对现有项目发布到生产环境中或预发布环境中。

万事开头难，咱们只玩简单的。
# 使用
想玩起来TravisCi不需要安装任何软件,它的网址是 https://travis-ci.org/ ， 你可以选择通过GitHub账号登录他。


![](https://resources.blog.fastrun.cn/wp-content/uploads/2019/01/3771889775-5c2d718bc8b9c_articlex.png)

随后我们可以建立一个github库，就叫它travis_ci_test吧，测试使用就随意点好了。之后点击项目管理 https://travis-ci.org/account/repositories ，会列出你所有的GitHub库

![](https://resources.blog.fastrun.cn/wp-content/uploads/2019/01/600778679-5c2d724a087cd_articlex.png)


通过点击单选按钮将库添加到TravisCi内。添加完成后并没有完事，这时候我们就该认真看看自动测试、集成、发布的脚本怎么写了。

# 配置文件
TravisCi为我们准备了超棒的配置文件，你可以在配置文件内随心所欲，例如打开某个目录，执行某条命令，他与dockerfile文件或者shell脚本很类似。只不过运行的容器在travisCi上，并非你本机

开发文档:https://docs.travis-ci.com/user/tutorial/


![](https://resources.blog.fastrun.cn/wp-content/uploads/2019/01/2274073549-5c2d7345d4dbb_articlex.png)

从简单开始,在根目录建立文件 .travis.yml , 下面是具体的配置项
```
language: php
php:
- 7.1
before_script:
- composer install
```
没错，五行配置就足够了，之后我们回到 https://travis-ci.com/dashboard，点击 trigger a build


![](https://resources.blog.fastrun.cn/wp-content/uploads/2019/01/3109802672-5c2d755158bc1_articlex.png)


点击当前项目看看详情。 https://travis-ci.org/CrazyCodes/travis_ci_test


![](https://resources.blog.fastrun.cn/wp-content/uploads/2019/01/2226091892-5c2d75c04e8ec_articlex.png)
 
TravisCi 做了几个简单的事情
1. 开机 
2. 克隆你的GITHUB项目
3. composer install
4. phpunit

通过测试了就显示success(大绿色) 失败就error喽。下面来看看这个详情页面上都有什么？


![](https://resources.blog.fastrun.cn/wp-content/uploads/2019/01/4190928006-5c2d767e71d0a_articlex.png)

上图大概展示了以下几点
- 分支名称
- 提交版本
- 执行时间
- 提交时间
- 开发语言
- 作者名称
- 等....

下面则是跑的命令行了。具体的配置文件还需要自己去研究，希望我这个头开的还不错吧。

# 感言
当你理解了Travis CI后会感觉这是一个多么优秀的产品，程序员要写出好代码是要求，而这些产品扩展。作为一名优秀的程序员，你其实可以这样做

 - 写测试用例
 - 实现业务
 - 提交分支 (TravisCi会自动检测提交并测试)
 - 自动部署
 - 结束任务

看似很复杂，不妨试试？


# 致谢
感谢你看到这里，希望本文可以帮到你。谢谢