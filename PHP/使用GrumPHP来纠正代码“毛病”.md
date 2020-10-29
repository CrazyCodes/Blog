![clipboard.png](https://resources.blog.fastrun.cn/wp-content/uploads/2019/07/1636353612-5ceddce4547bb_articlex.png)


# 前言
> 我一生的文章都会放在这里，我的博客，我希望每一行代码，每一段文字都能帮助你。https://github.com/CrazyCodes/Blog

嗨，我是CrazyCodes,小时候做错事，长辈有没有训斥过你呢？今天让我们看看PHP的监护者，愤怒的老头- - grumphp

# GrumPHP
GrumPHP 是通过挂在git hook上的一款PHP代码检测工具，他可以通过编码人员提交git时进行检查，检查通过则提交成功，检查失败则终止提交。

# 安装
通过composer直接安装即可，不推荐其他安装方式
```
composer require --dev phpro/grumphp
```
这类工具一定要放在dev内，在生产环境使用毫无意义，所以带参 --dev。
# 配置
安装结束后会自动在项目根目录建立grumphp.yml,官方给出的demo如下
```
# grumphp.yml
parameters:
    bin_dir: "./vendor/bin"
    git_dir: "."
    hooks_dir: ~
    hooks_preset: local
    git_hook_variables:
         VAGRANT_HOST_DIR: .
         VAGRANT_PROJECT_DIR: /var/www
         EXEC_GRUMPHP_COMMAND: exec
    stop_on_failure: false
    ignore_unstaged_changes: false
    hide_circumvention_tip: false
    process_async_limit: 10
    process_async_wait: 1000
    process_timeout: 60
    ascii:
        failed: grumphp-grumpy.txt
        succeeded: grumphp-happy.txt
    tasks:
        ant: ~
        atoum: ~
        behat: ~
        brunch: ~
        clover_coverage: ~
        codeception: ~
        composer: ~
        composer_normalize: ~
        composer_require_checker: ~
        composer_script: ~
        deptrac: ~
        doctrine_orm: ~
        file_size: ~
        gherkin: ~
        git_blacklist: ~
        git_branch_name: ~
        git_commit_message: ~
        grunt: ~
        gulp: ~
        infection: ~
        jsonlint: ~
        kahlan: ~
        make: ~
        npm_script: ~
        paratest: ~
        phan: ~        
        phing: ~
        php7cc: ~
        phpcpd: ~
        phpcs: ~
        phpcsfixer: ~
        phpcsfixer2: ~
        phplint: ~
        phpmd: ~
        phpmnd: ~
        phpparser: ~
        phpspec: ~
        phpstan: ~
        phpunit: ~
        phpunitbridge: ~
        phpversion: ~
        progpilot: ~
        psalm: ~
        robo: ~
        securitychecker: ~
        shell: ~
        twigcs: ~
        xmllint: ~
        yamllint: ~
    testsuites: []
    extensions: []
```
## 参数说明
| 参数名 | 默认值 | 注释 |
| -- | -- | -- |
| bin_dir | ./vendor/bin | 方便找到phpcs这类检测工具的外部命令，一般不需要修改 |
| git_dir | . | 默认git目录，正常都在根目录的吧 |
| hooks_dir | null | 设置钩子文件夹，默认会直接找 resources/hooks |
| tasks |  | 用于加载代码检测的库 |

# 实战
是不是太多了，忽略上面，咱一步一步看。下面是安装完成后自动生成的配置文件
```
parameters:
    git_dir: .
    bin_dir: vendor/bin
    tasks: {  }
```
## 准备
现在目录是酱紫的

![clipboard.png](https://resources.blog.fastrun.cn/wp-content/uploads/2019/07/4032416285-5cede1e0a908e_articlex.png)

1. 首先在github建立一个库，用于测试。https://github.com/CrazyCodes/grumphp_test

2. 默认的配置不会检测任何代码。我们为 tasks 加入一个新成员 https://github.com/phpro/grumphp/blob/master/doc/tasks/phpcs.md

3. 重新设置配置文件
## 骚操作
```
parameters:
    git_dir: .
    bin_dir: vendor/bin
    tasks: {
       phpcs : ~
    }
```

新建一个文件easy.php,代码如下,多一个分号，老头是一定不会放过你的。
```
echo '召唤愤怒的老头';;
```
老头是酱紫说的,额，其实他是一个红色的
```
GrumPHP detected a pre-commit command.
GrumPHP is sniffing your code!
Running task 1/1: Phpcs... ✘
             ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄
           ▄▄▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▌
         ▐▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▄
        ▐▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▌
       ▐▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▌
  ▄███▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▌
 █▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▌
 ▐█▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▌
   ▀█▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▌
     ▀▀▓▓▓▓▓▓▓▓▓▓▓▓█▀▀▀▀▀▀▀▀▀▀▀▀▀▀████████████▄
      ▄███████                       ██████████
     ███████▀  ▀▀▀▀▀▄      ▄▀▀▀▀▀     █████ ▀
      ▐████      ▐██        ▐██        ████▌
      ████▌                            ███
       ▌██▌           ▄▄ ▄▄           ▐███
        ███       ▄▄▄▄▄▄▄▄▄▄▄▄       ▐███
         ██▄ ▐███████████████████████████
        █▀███████████▀     ▀▀███████████
          ██████████▄███████▄███████████
         ▐█████████████████████████████
          █████████████████████████████
           ██ █████████████████████▐██▀
            ▀ ▐███████████████████▌ ▐▀
                ████▀████████▀▐███
                 ▀█▌  ▐█████  ██▌
                        ██▀   ▐▀

       ██████████████████████████████████
       █░░░░░░▀█▀░░░░░░▀█░░░░░░▀█▀░░░░░▀█
       █░░▐█▌░░█░░░██░░░█░░██░░░█░░░██░░█
       █░░▐█▌░░█░░░██░░░█░░██░░░█░░░██░░█
       █░░▐█▌░░█░░░██░░░█░░░░░░▄█░░▄▄▄▄▄█
       █░░▐█▌░░█░░░██░░░█░░░░████░░░░░░░█
       █░░░█░░░█▄░░░░░░▄█░░░░████▄░░░░░▄█
       ██████████████████████████████████

FILE: /Users/crazy/http/app/yield_test/easy.php
----------------------------------------------------------------------
FOUND 1 ERROR AFFECTING 1 LINE
----------------------------------------------------------------------
 2 | ERROR | Missing file doc comment
----------------------------------------------------------------------

Time: 37ms; Memory: 4MB

```
不仅仅有愤怒的老头，GrumPHP还提示了一些其他内容。
- FILE 检测文件路径
- FOUND 1 ERROR AFFECTING 1 LINE 有几个错误，在哪一行
- 2 | ERROR | Missing file doc comment 错误的问题是什么

这次检测是说我没写注释。是不是很有意思？GrumPHP不仅仅就这点能力哦，想深入请自行挖掘。

- 参数列表 https://github.com/phpro/grumphp/blob/master/doc/parameters.md
- 质量检测包 https://github.com/phpro/grumphp/blob/master/doc/tasks.md


# 其他
有人问，你为什么要这么折磨自己呢？其实像类型GrumPHP代码质量工具，不是仅仅自己拿来玩的，在开发人员略多的技术团队，可以通过使用它来达到代码规范一致，如果每个人代码都不一样，后果不堪设想。

![clipboard.png](https://resources.blog.fastrun.cn/wp-content/uploads/2019/07/30655536-5cedef42825c0_articlex.png)


# 致谢
感谢你看到这里，希望本篇文章可以帮到你。

