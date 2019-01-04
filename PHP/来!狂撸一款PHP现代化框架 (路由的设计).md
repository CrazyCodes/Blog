![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/12/4149909193-5c0898e0c4a99_articlex.png)

# 前言
上一篇的标题改了一下，以一、二、三为章节对读者来说是种困扰，现在的标题是依照项目进度来编写的。上篇文章地址为 https://segmentfault.com/a/1190000017278828

这一系列文章并不准备写太多章节，大概规划的只有4~5章左右，具体实现代码还请移步Github
https://github.com/CrazyCodes/z_framework

本章详细讲解一下Route(路由的实现)，Come on Up Image


![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/12/563696082-5c1318a81c42a_articlex.png)

上图大概说明了实现路由要经过两个步骤
- 将所有路由信息存储到超全局变量中
- 用户请求时从全局变量中查找路由映射的服务脚本并实例化

OK,大概流程就是酱紫，下面开始“撸”

# 目录
路由的代码暂分为以下几个文件（这并不是确定的，详细可查看Github）

| 文件名 | 注释 |
| -- | -- |
| Route | 转发文件:为实现 Route::get 效果 |
| RouteCollection | 路由信息处理存储 |
| RouteInterface | 无需解释 |
| RouteModel | 路由模型,将每个路由信息以结构体方式存储到$_SERVER |
| Router | 路由的核心类 |

莫急,我们一个一个文件来看。先从RouteInterface开始

# RouteInterface
参照RESTful规定设定接口方法分别为 GET、POST、PATCH、PUT、DELETE、OPTIONS，当然Laravel也是规范了以上标准请求。

GitHub : https://github.com/CrazyCodes/z_framework/blob/master/src/Zero/ZeroInterface.php
```
interface RouteInterface
{
    /**
     * @param      $uri
     * @param null $action
     *
     * @return mixed
     */
    public function get($uri, $action = null);

    /**
     * @param      $uri
     * @param null $action
     *
     * @return mixed
     */
    public function post($uri, $action = null);

    /**
     * @param      $uri
     * @param null $action
     *
     * @return mixed
     */
    public function patch($uri, $action = null);

    /**
     * @param      $uri
     * @param null $action
     *
     * @return mixed
     */
    public function put($uri, $action = null);

    /**
     * @param      $uri
     * @param null $action
     *
     * @return mixed
     */
    public function delete($uri, $action = null);

    /**
     * @param      $uri
     * @param null $action
     *
     * @return mixed
     */
    public function options($uri, $action = null);
}
```
# Router
先写一个栗子
```
public function get($uri, $action = null)
{
    return $this->addRoute("GET", $uri, $action);
}
```
用户调用下方代码会指向上述方法,方法既调用addRoute方法将路由信息存储到$_SERVER中
```
Route::get('/','Controller')
```
以下为addRoute部分的代码
```
public function addRoute($methods, $uri, $action)
{
    // 这里判断请求方式是否合规,既是否存在  GET、POST、PATCH、PUT、DELETE、OPTIONS其中之一
    if ($this->verify($methods) == false) {
        return false;
    }
    
    // 之后我们去往RouteCollection路由信息的处理类中
    return $this->routes->add($uri, $this->createRoute($methods, $action));
}
```

# RouteCollection
最终达到 add 方法，将路由信息存储到$_SERVER中
```
public function add($uri, RouteModel $model)
{
    if (empty($_SERVER["routes"][$uri])) {
        $_SERVER["routes"][$uri] = $model;
    }
}
```
第二个参数RouteModel开始我们说过这是路由模型,将每个路由以结构体的方式存储到变量中,存储后的结果
```
'routes' =>
  array(6) {
    'test/get' =>
    class Zero\Routing\RouteModel#13 (2) {
      public $method =>
      string(3) "GET"
      public $action =>
      string(19) "testController@test"
    }
    'test/post' =>
    class Zero\Routing\RouteModel#14 (2) {
      public $method =>
      string(4) "POST"
      public $action =>
      string(19) "testController@test"
    }
    'test/put' =>
    class Zero\Routing\RouteModel#15 (2) {
      public $method =>
      string(3) "PUT"
      public $action =>
      string(18) "testController@put"
    }
    'test/del' =>
    class Zero\Routing\RouteModel#16 (2) {
      public $method =>
      string(6) "DELETE"
      public $action =>
      string(18) "testController@del"
    }
    'test/patch' =>
    class Zero\Routing\RouteModel#17 (2) {
      public $method =>
      string(5) "PATCH"
      public $action =>
      string(20) "testController@patch"
    }
    'test/opt' =>
    class Zero\Routing\RouteModel#18 (2) {
      public $method =>
      string(7) "OPTIONS"
      public $action =>
      string(18) "testController@opt"
    }
  }
```
# Route
最后通过__callStatic将代码重定向到核心类中
```
public static function __callStatic($name, $arguments)
{
    $router = new Router;
    
    return $router->{$name}($arguments[0], $arguments[1]);
}
```

上述套路部分是Laravel的设计思想，通过这款简单的框架可对Laravel核心设计有丁点的理解。
# 测试
测试上次做的有点糙，从本章到系列结束，我们都以PHPunit来测试。
```
/**
 * @content tests all methods storage -> $_SERVER["routes"]
 */
public function testAllMethodsStorage()
{
    $this->routes->get($methodGet = "test/get", "testController@test");
    $this->assertArrayHasKey($methodGet, $_SERVER[$this->methodsDataKey]);
    
    $this->routes->post($methodPost = "test/post", "testController@test");
    $this->assertArrayHasKey($methodPost, $_SERVER[$this->methodsDataKey]);
    
    $this->routes->put($methodPut = "test/put", "testController@put");
    $this->assertArrayHasKey($methodPut, $_SERVER[$this->methodsDataKey]);
    
    $this->routes->delete($methodDel = "test/del", "testController@del");
    $this->assertArrayHasKey($methodDel, $_SERVER[$this->methodsDataKey]);
    
    $this->routes->patch($methodPatch = "test/patch", "testController@patch");
    $this->assertArrayHasKey($methodPatch, $_SERVER[$this->methodsDataKey]);
    
    $this->routes->options($methodOpt = "test/opt", "testController@opt");
    $this->assertArrayHasKey($methodOpt, $_SERVER[$this->methodsDataKey]);
}
```
上述贴出部分代码，以过程化的方法去测试。查看存储是否符合预期。

```
/**
 * @content RouteModel Success
 */
public function testCreateRoute()
{
    $response = $this->routes->createRoute("GET", "TestController@Get");
    
    $this->assertInstanceOf(RouteModel::class, $response);
}
```
包括测试对路由创建后是否为RouteModel的实现。具体可查看Github
https://github.com/CrazyCodes/z_framework/tree/master/tests/Routing
# 致谢
上述已完成了路由的基本设计，下一章将讲解从启动到请求路由映射到服务脚本的过程。

希望本章可以帮到你，谢谢。
