![](https://blog.fastrun.cn/wp-content/uploads/2018/07/798834834-5b3ae8331e687_articlex.png)

> 基于 [https://laravel-china.org/docs/dingo-api/2.0.0](https://laravel-china.org/docs/dingo-api/2.0.0) 文档更简洁的描述Dingo，直戳重点，注重实践

# 概述
Dingo API帮助您轻松快速地构建自己的API。虽然这个方案的目标是尽可能保持灵活性，但它仍然不能涵盖所有情况并解决所有问题。

# 安装
将以下代码加入到composer.json中，并执行composer update 或 composer install
```
"require": {
    "dingo/api": "2.0.0-alpha1"
}
```
## Laravel
发布配置文件，执行此命令后会在config目录下生成api.php配置文件
```
php artisan vendor:publish --provider="Dingo\Api\Provider\LaravelServiceProvider"
Lumen
```
## Lumen
如果是你使用的lumen,因lumen没有vendor命令,请打开 bootstrap/app.php 并注册服务提供者：
```
$app->register(Dingo\Api\Provider\LumenServiceProvider::class);
```

## Facade
API 自带了两个 Facade，你可以酌情使用。
```
Dingo\Api\Facade\API
```
这个是调度器的 Facade ，并提供了一些好用的辅助方法。
```
Dingo\Api\Facade\Route
```
你可以使用这个 Facade 来获取 API 的当前路由、请求、检查当前路由的名称等。

你可以在config/app.php aliases数组内注册Facade
```
'aliases' => [
	...
	'API'          => Dingo\Api\Facade\API::class,
	'ApiRoute'     => Dingo\Api\Facade\Route::class,
],
```
# 配置
在.env文件中配置你的Dingo API

- API_STANDARDS_TREE
- API_SUBTYPE
- API_PREFIX
- API_VERSION
- API_NAME
- API_CONDITIONAL_REQUEST
- API_STRICT
- API_DEBUG
- API_DEFAULT_FORMAT

## API_STANDARDS_TREE
Standards Tree 标准树
- 未注册的树（x）主要表示本地和私有环境
- 私有树（prs）主要表示没有商业发布的项目
- 供应商树（vnd）主要表示公开发布的项目
是一种概念上的东西,类似与git的分支,如果正常开发就按照 x,prs,vnd 的描述来填写即可。
## API_SUBTYPE
在请求header中需要用到他
## API_PREFIX
地址的前缀,如果不需要请填写 '/'
## API_VERSION
接口的版本，填写后是默认访问的版本
## API_NAME
接口的名称，用于生成api文档，其他地方不使用
## API_CONDITIONAL_REQUEST
> 条件请求默认为开启状态，这有利于客户端的缓存机制在可能的情况下缓存 API 请求。
## API_STRICT
强制每次请求必须带版本，既
```
Accept:application/vnd.{API_SUBTYPE}.v2+json
```
## API_DEBUG
是否开启调试，开启后访问api会看到
## API_DEFAULT_FORMAT
返回的类型，一般都是json

## 一个栗子
```
API_STANDARDS_TREE=vnd
API_SUBTYPE=catering
API_PREFIX=/
API_VERSION=v1
API_NAME="My API"
API_CONDITIONAL_REQUEST=false
API_STRICT=false
API_DEBUG=true
API_DEFAULT_FORMAT=json
```

# 使用
> 这里与实际业务结合来讲解
## 表结构
### member
```
CREATE TABLE `member` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `tel` bigint(20) DEFAULT NULL COMMENT '手机号码',
  `password` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT '登录密码',
  `status` tinyint(4) NOT NULL DEFAULT '0' COMMENT '账号状态 0:正常',
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `member_tel_unique` (`tel`),
  KEY `member_tel_status_index` (`tel`,`status`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### member_data
```
CREATE TABLE `member_data` (
  `member_id` bigint(20) NOT NULL COMMENT '用户编码',
  `sex` enum('0','1','2') COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '性别 0=>女生 1=>男生 2=>未知',
  `nick_name` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '姓名/昵称',
  `img` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '用户头像',
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL,
  UNIQUE KEY `member_data_member_id_unique` (`member_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```
## 新建一个路由
```
$api = app ('Dingo\Api\Routing\Router');
$api->post ('user/register', 'App\Api\Controllers\UserController@register');
});
```
## 创建自定义响应
```
<?php

    namespace App\Api;
	
	class Response
	{
		public static function success($data)
		{
			return [
				'status_code' => 200,
				'data'        => $data,
			];
		}
		
		public static function error($message = '')
		{
			return [
				'status_code' => 0,
				'message'     => $message,
			];
		}
		
		public static function return($statusCode, $message, $data = [])
		{
			return [
				'status_code' => $statusCode,
				'message'     => $message,
				'data'        => $data,
			];
		}
	}
```

## 创建控制器
```
<?php
	namespace App\Api\Controllers;
	
	use App\Api\DingoController;
	use App\Api\Response;
	use App\Api\Services\UserService;
	use Illuminate\Http\Request;
	
	class UserController extends DingoController
	{
		public $request;
		
		protected $userService;
		
		public function __construct(Request $request, UserService $userService)
		{
			$this->request = $request;
			
			$this->userService = $userService;
		}
		
		public function register()
		{
			$result = $this->userService->register ($this->request->all ());
			
			if ($result['status_code'] == 200) {
				return $this->response->array (Response::return (200, '注册成功', [
					'user_id' => $result['data'],
				]));
			}
			
			return $this->response->error ($result['message'], 500);
		}
	}
```
## 创建服务
```
<?php
	namespace App\Api\Services;
	
	use App\Api\Actions\CreateUser;
	use App\Api\Response;
	use App\Models\Member;
	
	class UserService
	{
		public $member;
		
		public function __construct(Member $member)
		{
			$this->member = $member;
		}
		
		public function register($data)
		{
			try {
				return Response::success ((new CreateUser())->execute ($data));
			} catch (\Exception $e) {
				return Response::error ($e->getMessage ());
			}
		}
	}
```
## 创建动作
```
<?php
	namespace App\Api\Actions;
	
	use App\Models\Member;
	use App\Models\MemberData;
	
	class CreateUser
	{
		/**
		 * @param array $data
		 *
		 * @return mixed
		 * @throws \Exception
		 */
		public function execute(array $data)
		{
			
			$member           = new Member();
			$member->tel      = $data['tel'];
			$member->password = md5 ($data['password']);
			$result           = $member->save ();
			
			if (!$result) {
				throw new \Exception('注册失败');
			}
			
			$memberData            = new MemberData();
			$memberData->member_id = $member->id;
			$memberData->sex       = "2";
			$memberData->nick_name = "";
			$memberData->img       = "";
			$memberData->save ();
			
			return $member->id;
		}
	}
```
# 请求
```
<?php

$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => "http://api.c.com/user/register",
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => "",
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 30,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => "POST",
  CURLOPT_POSTFIELDS => "-----011000010111000001101001\r\nContent-Disposition: form-data; name=\"tel\"\r\n\r\n18510362698\r\n-----011000010111000001101001\r\nContent-Disposition: form-data; name=\"password\"\r\n\r\nzjk1221\r\n-----011000010111000001101001--",
  CURLOPT_HTTPHEADER => array(
    "accept: application/vnd.catering.v1+json",
    "cache-control: no-cache",
    "content-type: multipart/form-data; boundary=---011000010111000001101001",
    "postman-token: e7cf665f-3698-217a-cd71-35c3a44f42bc"
  ),
));

$response = curl_exec($curl);
$err = curl_error($curl);

curl_close($curl);

if ($err) {
  echo "cURL Error #:" . $err;
} else {
  echo $response;
}
```
# 疑难杂症
列出一些常见问题及解决方案
## 版本区分后如何访问
API_VERSION 设置的版本只是默认访问版本，如果想访问其他版本，需在header内添加 
```
Accept:application/vnd.{API_SUBTYPE}.v2+json
```
## 不想添加前缀怎么办
```
API_PREFIX=/
```
API_PREFIX不能为空，必须填写内容，那理所当然的```/```一定是正确的

# 致谢
感谢你看到这里，以上为个人研究开发的总结以及代码，如果可以帮到你，我很高兴。如果有什么问题或者文章有哪些错误，请在评论区回复，及时阻止我误导他人。谢谢