![![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/12/4071558190-5c089723b0c8c_articlex.png)](https://resources.blog.fastrun.cn/wp-content/uploads/2018/12/4149909193-5c0898e0c4a99_articlex.png)



# 前言
从本章开始，我们继续造轮子，去完成一款类似于Laravel的现代化PHP框架，为什么说是现代化？因为他必须具备一下几点
- 遵守PSR-4编码规范
- 使用Composer进行包管理
- 标准的HTTP请求方式
- 优雅的使用设计模式
开始我们无需关心性能问题，先考虑框架具体需要实现哪些功能，这与实现业务就大不相同了，来!开始我的表演。

# 前期
做任何一件事情都要有个前期准备工作。
1. 作为PSR-4的规定，我们命名空间得有一个祖宗名字，这里我叫他神圣的 《z_framework》
2. 至少需要一个GITHUB库来存储这个项目 https://github.com/CrazyCodes/z_framework

3. 创建一个composer.json文件用于进行包管理,灰常简单，phpunit搞进来。通过psr-4加载个项目命名

```
{
      "name": "z framework",
      "require-dev": {
        "phpunit/phpunit": "^7.0"
      },
      "autoload": {
        "psr-4": {
          "Zero\\": "src/Zero",
        }
      },
      "autoload-dev": {
        "psr-4": {
          "Zero\\Tests\\": "tests/"
        }
      }
}
```

最后我们就需要考虑下目录的结构及其我们第一步要完成的功能，核心的结构(这里并非只的项目结构哦。是框架的核心结构)暂且是这样

- src
    - Zero
        - Config  // 可能存放一些配置文件的解析器
        - Container // 容器的解析器
        - Http  // 请求处理的一些工具
        - Routes  // 路由处理的一些功能
        - Bootstrap.php  // 这可能是一个启动脚本
        - Zero.php  // 可能是核心的入口文件
- tests // 测试目录
- .gitignore
- composer.json
- LICENSE
- README.md


# 路由
还记得第一次使用Laravel时我们第一步做的事情吗？是的，去研究路由，所以我们把路由作为框架的第一步。在研究路由前，我们要知道
```
http://www.domain.com/user/create
```
是如何实现的，php默认是必须请求index.php或者default.php的，上述链接实际隐藏了index.php或default.php ，这是Nginx等服务代理帮我们做到的优雅的链接，具体配置如下，实际与Laravel官方提供无差别
```
server {
  listen       80;
  server_name  www.zf.com;
  root          /mnt/app/z_framework/server/public;
  index         index.php index.html index.htm;

  location / {
     try_files $uri $uri/ /index.php?$query_string;
   }

  location ~ \.php$ {
     fastcgi_pass   php71:9000;
     fastcgi_index  index.php;
     fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
     include        fastcgi_params;
  }
}
```
通过
```
try_files $uri $uri/ /index.php?$query_string;
```
去解析请求，通过上述可以得出
```
http://www.domain.com/user/create
=======
http://www.domain.com/index.php?user/create
```
好了，明白了其中奥秘后，我们开始路由的编写,在src/Routes/Route.php
```
namespace Zero\Routes;
    
class Route 
{
}
```
# 实现
首先我们先创建一个简单的接口文件
src/Routes/RouteInterface.php
```
namespace Zero\Routes;
    
interface RouteInterface
{
    public function Get($url, $callFile);
    
    public function Post($url, $callFile);
    
    public function Put($url, $callFile);
    
    public function Delete($url, $callFile);
}
```
从Get请求开始
```
namespace Zero\Routes;
    
class Route implements RouteInterface
{
    public function Get($url, $callFile)
    {
    
    }
}
```
最后实现Get代码块
```
if (parent::isRequestMethod("GET")) { // 判读请求方式
                
    if (is_callable($callFile)) { // 判断是否是匿名函数
        return $callFile();
    }
    
    if ($breakUpString = parent::breakUpString($callFile)) { // 获取Get解析。既/user/create 
        header('HTTP/1.1 404 Not Found');
    }
    
    try {
        // 通过反射类获取对象 $breakUpString[0] = user
        $reflectionClass = new \ReflectionClass('App\\Controllers\\' . $breakUpString[0]);
        // 实例化对象
        $newInstance     = $reflectionClass->newInstance();
        // 获取对象中的指定方法，$breakUpString[1] = create
        call_user_func([
            $newInstance,
            $breakUpString[1],
        ], []);
    } catch (\ReflectionException $e) {
        header('HTTP/1.1 404 Not Found');
    }
} else {
    header('HTTP/1.1 404 Not Found');
}

return "";
```
如果你想测试上述代码，可使用phpunit,或者傻大粗的方式，这里便于理解使用傻大粗的方式

![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/12/4071558190-5c089723b0c8c_articlex.png)


创建一个目录，随后按照Laravel的目录形式创建几个目录，
```
<?php
    
namespace App\Controllers;

class UserController
{
    public function create()
    {
       var_dump(0);
    }
}
```
最后public/index.php文件中去调用路由
```
require_once "../../vendor/autoload.php";

Zero\Zero::Get("user", "UserController@create");
```
到这里我们就基本完成了路由的功能，下一章将完善路由的编码
# 致谢
感谢你看到这里，希望本篇可以帮到你。具体代码在 https://github.com/CrazyCodes/z_framework