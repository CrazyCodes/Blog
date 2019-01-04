![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/09/3276665162-5ba45dfac43fc_articlex.png)

# 前言
提升能力的方法并非使用更多工具，而是解刨自己所使用的工具。今天我们从Laravel启动的第一步开始讲起。
# 入口文件
laravel是单入口框架，所有请求必将经过index.php
```
define('LARAVEL_START', microtime(true)); // 获取启动时间
```
使用composer是现代PHP的标志
```
require __DIR__.'/../vendor/autoload.php'; // 加载composer -> autoload.php
```
加载启动文件
```
$app = require_once __DIR__.'/../bootstrap/app.php';
```
获取```$app```是laravel启动的关键，也可以说$app是用于启动laravel内核的钥匙🔑。随后就是加载内核，载入服务提供者、门面所映射的实体类，中间件，最后到接收http请求并返回结果。
```
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class); // 加载核心类

$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
);

$response->send();

$kernel->terminate($request, $response);
```
看似短短的4行代码，这则是laravel的优雅之处。我们开始深层次解刨。
# bootstrap\\app.php
这个启动文件也可以看作是一个服务提供者，不过他并没有boot，register方法。因为入口文件直接加载他，所有这些没必要的方法就不存在了。

作为启动文件，首页则是加载框架所有必须的要要件，例如 
- registerBaseBindings
- registerBaseServiceProviders
- registerCoreContainerAliases，
这其中包括了很多基础性的方法和类，例如
- db     ```[\Illuminate\Database\DatabaseManager::class]```
- auth ``` [\Illuminate\Auth\AuthManager::class, \Illuminate\Contracts\Auth\Factory::class] ```
- log ``` [\Illuminate\Log\LogManager::class, \Psr\Log\LoggerInterface::class] ```
- queue ``` [\Illuminate\Queue\QueueManager::class, \Illuminate\Contracts\Queue\Factory::class, \Illuminate\Contracts\Queue\Monitor::class] ```
- redis ``` [\Illuminate\Redis\RedisManager::class, \Illuminate\Contracts\Redis\Factory::class] ```
- 等等 ``` ... ```
而$app这个在服务提供者的核心变量则就是Application实例化所得，而你在服务提供者内使用的make，bind，singleton来自他的父类Container，都说容器是laravel的核心概念。这块的概念后续我们会详细的讲解。
```
$app = new Illuminate\Foundation\Application(
    realpath(__DIR__.'/../')
);
```
上面我们已经获得$app的实例化了，现在通过$app来注册核心类、异常类,并将$app返回给```index.php```
```
$app->singleton(
    Illuminate\Contracts\Http\Kernel::class,
    App\Http\Kernel::class
);

$app->singleton(
    Illuminate\Contracts\Console\Kernel::class,
    App\Console\Kernel::class
);

$app->singleton(
    Illuminate\Contracts\Debug\ExceptionHandler::class,
    App\Exceptions\Handler::class
);
```
# App\\Http\\Kernel
核心类了所有的
- 系统中间件
- 群组中间件
- 路由中间件
当然你需要使用中间件也是在这个类中加载，是经常被使用的一个文件。
```
protected $middleware = [
			\Illuminate\Foundation\Http\Middleware\CheckForMaintenanceMode::class,
			\Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
			\App\Http\Middleware\TrimStrings::class,
			\Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
			\App\Http\Middleware\TrustProxies::class,
		];
		
		/**
		 * The application's route middleware groups.
		 *
		 * @var array
		 */
		protected $middlewareGroups = [
			'web' => [
				\App\Http\Middleware\EncryptCookies::class,
				\Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
				\Illuminate\Session\Middleware\StartSession::class,
				// \Illuminate\Session\Middleware\AuthenticateSession::class,
				\Illuminate\View\Middleware\ShareErrorsFromSession::class,
				\App\Http\Middleware\VerifyCsrfToken::class,
				\Illuminate\Routing\Middleware\SubstituteBindings::class,
			],
			
			'api' => [
				'throttle:60,1',
				'bindings',
			],
		];
```
这个核心类继承自他的父类```Illuminate\Foundation\Http\Kernel::class```,核心类做了很多事情，它会将所有的中间件全部存储到一个指定的数组，方便内核调用及其他类调用。
```
namespace App\Http;
	
use App\Api\Middleware\VerifyApiToken;
use Illuminate\Foundation\Http\Kernel as HttpKernel;
	
class Kernel extends HttpKernel
```
# 回到起点
Laravel的启动经历了很繁琐的一个过程。这也是Laravel优雅的关键点。
```
$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
);

$response->send();

$kernel->terminate($request, $response);
```
将请求传入则完成了整个laravel的启动，至于结果的返回则有开发者自行通过控制器或其他可访问类返回。

# 致谢
感谢你看到这里，本篇文章源码解析靠个人理解。如有出入请拍砖。

希望本篇文章可以帮到你。谢谢