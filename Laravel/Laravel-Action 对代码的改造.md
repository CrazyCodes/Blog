![](https://blog.fastrun.cn/wp-content/uploads/2018/08/3565416334-5b7686efb4abc_articlex.png)

# 前言
以往写过俩篇文章
[积德篇] 如何少写PHP "烂"代码 
[https://blog.fastrun.cn/2018/08/15/1-51/](https://blog.fastrun.cn/2018/08/15/1-51/)
举枪消灭"烂代码"的实战案例   
[https://blog.fastrun.cn/2018/08/15/1-51/](https://blog.fastrun.cn/2018/08/15/1-51/)

感觉文章中对Action的操作没有一个规范性的调用及编写。特此写了一个laravel-action包
[https://github.com/CrazyCodes/Laravel-Action](https://github.com/CrazyCodes/Laravel-Action)
希望可以帮到你。

# 安装
下载composer包: ```composer require crazycodes/laravel-action```

注入提供者到 ```config/app.php```

```
'providers' => [
    // [...]
   CrazyCodes\ActionServiceProvider::class,
],
```
注册 ```Action``` facade:
```
'aliases' => [
    // [...]
    'Action' => CrazyCodes\Facades\Action::class,
],
```
发布配置文件
```
php artisan vendor:publish --provider=CrazyCodes\ActionServiceProvider
```
配置项就一个
```
actionNamespace //设置你的action所在的命名空间
```

# 使用
继承Action方法获取规范的命名
```
namespace CrazyCodes\Action;

class CreateUser extends Action
{

}
```

继承的Action准备了两个方法

## before
```
public function before($request)
{
    return $request;
}
```
Action被调用的同时会直接调用before方法执行。

## after
```
public function after($request)
{
    return [];
}

```
可以选择不声明after方法。after主要用于调用其他Action

## 成员变量
```
public $beforeResultName = 'beforeResult';
public $afterResultName = 'afterResult';
```
用于获取返回的结果

# 调用
可以通过Facade调用
```
Action::use('YourAction',发送的参数);
```
或者使用全局函数
```
laravel_action('YourAction',发送的参数);
```

# 获取结果
得到的结果默认是对象。可以转换格式
```
function toJson();
function toArray();
```
结果展示

Array
```
array:2 [
  "beforeResult" => array:1 [
    0 => "aaa"
  ]
  "afterResult" => []
]
```
JSON
```
{"beforeResult":["aaa"],"afterResult":[]}
```
# Demo
依旧以创建用户为例
## UserController
```
<?php
	
	namespace Examples;
	
	use CrazyCodes\Facades\Action;
	
	/**
	 * Class UserController
	 * @package Examples
	 */
	class UserController
	{
		public function create()
		{
			$request = [
				'username' => 'test',
				'password' => 'test',
			];
			
			$result = Action::use ('CreateUser', $request);
			
			//全局方法
			//laravel_action ('CreateUser', $request);


//			return $result->toArray();
			return $result->toJson ();
		}
	}
```
## CreateUser
```
<?php
	
	namespace Examples;
	
	use CrazyCodes\Action;
	
	/**
	 * Class CreateUser
	 * 创建用户
	 * @package Examples
	 */
	class CreateUser extends Action
	{
		public function before($request)
		{
			var_dump ($request);
			
			return ['status' => 'success'];
		}
		
		public function after($request)
		{
			var_dump ($request);
			
			return Action::use ('CreateWallet', $request);
		}
	}
```
## CreateWallet
```
<?php
	
	namespace Examples;
	
	use CrazyCodes\Action;
	
	/**
	 * 创建用户钱包
	 * Class CreateWallet
	 * @package Examples
	 */
	class CreateWallet extends Action
	{
		public function before($request)
		{
			var_dump ($request);
			
			return ['status' => 'success'];
		}
		
	}
```

# 致谢
感谢你看到这里,希望这篇文章让你的代码更优雅。谢谢