![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/09/3276665162-5ba45dfac43fc_articlex.png)

# 前言
我的解析文章并非深层次多领域的解析攻略。但是参考着开发文档看此类文章会让你在日常开发中更上一层楼。

废话不多说，我们开始本章的讲解。

# 入口
Laravel启动后，会先加载服务提供者、中间件等组件，在查找路由之前因为我们使用的是门面，所以先要查到Route的实体类。


# 注册
第一步当然还是通过服务提供者，因为这是laravel启动的关键，在 ``` RouteServiceProvider ``` 内加载路由文件。
```
protected function mapApiRoutes()
{
    Route::prefix('api')
         ->middleware('api')
         ->namespace($this->namespace)  // 设置所处命名空间
         ->group(base_path('routes/api.php'));  //所得路由文件绝对路径
}
```

首先require是不可缺少的。因路由文件中没有命名空间。 ``` Illuminate\Routing\Router``` 下方法
```
protected function loadRoutes($routes)
{
    if ($routes instanceof Closure) {
        $routes($this);
    } else {
        $router = $this;

        require $routes;
    }
}
```
随后通过路由找到指定方法,依旧是```  Illuminate\Routing\Router ``` 内有你所使用的所有路由相关方法，例如get、post、put、patch等等，他们都调用了统一的方法 ``` addRoute ```
```
public function addRoute($methods, $uri, $action)
{
    return $this->routes->add($this->createRoute($methods, $uri, $action));
}

```
之后通过 ``` Illuminate\Routing\RouteCollection ``` addToCollections 方法添加到集合中
```
protected function addToCollections($route)
{
    $domainAndUri = $route->getDomain().$route->uri();

    foreach ($route->methods() as $method) {
        $this->routes[$method][$domainAndUri] = $route;
    }

    $this->allRoutes[$method.$domainAndUri] = $route;
}
```
添加后的结果如下图所示
![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/09/2988532948-5bac40d3bd9b4_articlex.png)
# 调用

通过 ``` Illuminate\Routing\Router ``` 方法开始运行路由实例化的逻辑
```
protected function runRoute(Request $request, Route $route)
{
    $request->setRouteResolver(function () use ($route) {
    	
        return $route;
    });
    $this->events->dispatch(new Events\RouteMatched($route, $request));

    return $this->prepareResponse($request,
        $this->runRouteWithinStack($route, $request)
    );
}
....
protected function runRouteWithinStack(Route $route, Request $request)
{
    $shouldSkipMiddleware = $this->container->bound('middleware.disable') &&
                            $this->container->make('middleware.disable') === true;

    $middleware = $shouldSkipMiddleware ? [] : $this->gatherRouteMiddleware($route);

    return (new Pipeline($this->container))
                    ->send($request)
                    ->through($middleware)
                    ->then(function ($request) use ($route) {
                        return $this->prepareResponse(
                            $request, $route->run() // 此处调用run方法
                        );
                    });
}
```
在 ``` Illuminate\Routing\Route ``` 下 run 方用于执行控制器的方法
```
public function run()
{
    $this->container = $this->container ?: new Container;

    try {
        if ($this->isControllerAction()) { 
            return $this->runController(); //运行一个路由并作出响应
        }
			
        return $this->runCallable();
    } catch (HttpResponseException $e) {
        return $e->getResponse();
    }
}
```
从上述方法内可以看出 runController 是运行路由的关键,方法内运行了一个调度程序，将控制器 ``` $this->getController() ``` 和控制器方法 ``` $this->getControllerMethod() ``` 传入到 ``` dispatch ``` 调度方法内 
```
protected function runController()
{
	
    return $this->controllerDispatcher()->dispatch(
        $this, $this->getController(), $this->getControllerMethod()
    );
}
```
这里注意 ```getController()``` 才是真正的将控制器实例化的方法
```
public function getController()
{
	
    if (! $this->controller) {
        $class = $this->parseControllerCallback()[0]; // 0=>控制器 xxController 1=>方法名 index
        $this->controller = $this->container->make(ltrim($class, '\\')); // 交给容器进行反射
    }

    return $this->controller;
}
```


# 实例化
依旧通过反射加载路由指定的控制器,这个时候build的参数$concrete =  ```  App\Api\Controllers\XxxController ```
```
public function build($concrete)
{
    // If the concrete type is actually a Closure, we will just execute it and
    // hand back the results of the functions, which allows functions to be
    // used as resolvers for more fine-tuned resolution of these objects.
    if ($concrete instanceof Closure) {
        return $concrete($this, $this->getLastParameterOverride());
    }
    
    $reflector = new ReflectionClass($concrete);
    // If the type is not instantiable, the developer is attempting to resolve
    // an abstract type such as an Interface of Abstract Class and there is
    // no binding registered for the abstractions so we need to bail out.
    if (! $reflector->isInstantiable()) {
        return $this->notInstantiable($concrete);
    }
    
		
    $this->buildStack[] = $concrete;

    $constructor = $reflector->getConstructor();
    // If there are no constructors, that means there are no dependencies then
    // we can just resolve the instances of the objects right away, without
    // resolving any other types or dependencies out of these containers.
    if (is_null($constructor)) {
	
			array_pop($this->buildStack);
	
			return new $concrete;
    }

    $dependencies = $constructor->getParameters();
    // Once we have all the constructor's parameters we can create each of the
    // dependency instances and then use the reflection instances to make a
    // new instance of this class, injecting the created dependencies in.
    $instances = $this->resolveDependencies(
        $dependencies
    );

    array_pop($this->buildStack);
    
    return $reflector->newInstanceArgs($instances);
}
```
这时将返回控制器的实例，下面将通过url访问指定方法，一般控制器都会继承父类 ``` Illuminate\Routing\Controller ```,laravel为其设置了别名 BaseController
```
public function dispatch(Route $route, $controller, $method)
{
	
    $parameters = $this->resolveClassMethodDependencies(
        $route->parametersWithoutNulls(), $controller, $method
    );

    if (method_exists($controller, 'callAction')) {

			return $controller->callAction($method, $parameters);
    }
		
    return $controller->{$method}(...array_values($parameters));
}
```
Laravel通过controller继承的callAction去调用子类的指定方法，也就是我们希望调用的自定义方法。
```
public function callAction($method, $parameters)
{
    return call_user_func_array([$this, $method], $parameters);
}
```

# 致谢
感谢你看到这里，本篇文章源码解析靠个人理解。如有出入请拍砖。

希望本篇文章可以帮到你。谢谢