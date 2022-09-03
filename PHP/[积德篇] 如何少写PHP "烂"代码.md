![](https://blog.fastrun.cn/wp-content/uploads/2018/07/4140084333-5b20f4171b25c_articlex.png)


> 写给初生牛犊不怕虎的童鞋们,大佬可随意摘看
> 本章基于PHP Laravel
# 前言
经常会有人问
- 目录如何设计比较好？
- 代码如何分布好？
- 怎么写一个可维护的项目?

“烂”项目我也没少写，以下是参考互联网各大佬的文章总结及个人开发经验而来.

# Controller

![](https://blog.fastrun.cn/wp-content/uploads/2018/07/104474369-5b209191cb1e6_articlex.png)

Controller顾名思义是控制器，在入门PHP的时候，就知道Controller代表MVC中的C层，MVC本身的概念就代码分离，教你如何如何将业务分开，但面临着业务的不断发展，代码的复杂度也随之提高，功能与功能之间的链接错综复杂，最后你的MVC就变成了下图,所以仅仅依托MVC的设计思想已经无法支撑不断发展的业务。

现在我们将Controller的任务和能力重新定义，控制器仅仅控制Http Reqeust的请求,这样就符合了SOLID 单一功能原则.

![](https://blog.fastrun.cn/wp-content/uploads/2018/07/3241625265-5b2091ca21f35_articlex.png)

直接将业务代码写在Controller中，会使得代码及其臃肿，不易于维护和扩展
```
<?php
	namespace App\Http\Controller;

	class UserController extends Controller{

		public function register(Request $request){
			$user = new User();
			$user->username = $request->input('username');
			$user->password = $request->input('password');
			$result = $user->save();

			return $result;
		}

	}

```
这时就应该思考如何分离业务代码，我们引入Service的概念
# Service
Service本身译为服务
- 将外部方法，公共方法注入到Service
- 将Service注入到控制器

![](https://blog.fastrun.cn/wp-content/uploads/2018/07/2765013060-5b20941641380_articlex.png)

像上图这样

### UserController
```
<?php
	namespace App\Http\Controller;

	class UserController extends Controller{

		public $request;
		
		protected $userService;
		
		public function __construct(Request $request, UserService $userService)
		{
			$this->request = $request;
			
			$this->userService = $userService;
		}
		
		public function register()
		{
			//... validation
			return $this->userService->register ($this->request->all());
		}

	}

```
### UserService
```
<?php
	namespace App\Service;

    class UserService{
    
        public function register($data)
		{
            $username = $data['username'];
            $password = $data['password'];
         
			$password = encrypt ($password);
			
			$user = new User();
			$user->username = $username;
			$user->password = $password;
			$result = $user->save();

			return $result;
		}

    }

```
到现在为止，我们至少将业务与请求彻底分开了。但还是不如人意，如果把所有的业务及CURD全部写在Service中，那只不过是将Controller的臃肿转移到了Service,那Service就没有什么存在意义了。
所以我们需要继续分割Service，将对数据库的R操作独立出来，因为CUD的操作基本是一贯不变的，而R操作根据业务的复杂度则变的多姿多彩。所以独立R操作。这个时候我们引用Repository的概念。
# Repository
我们使用Repository辅助Model,将相关的查询逻辑封装到不同的repository中，方便逻辑代码的维护
- 符合SOLID的单一原则
- 符合SOLID的依赖反转

![](https://blog.fastrun.cn/wp-content/uploads/2018/07/4012281949-5b20975b2f411_articlex.png)

### UserController
```
<?php
	namespace App\Http\Controller;

	class UserController extends Controller{

		public $request;
		
		protected $userService;
		
		public function __construct(Request $request, UserService $userService)
		{
			$this->request = $request;
			
			$this->userService = $userService;
		}
		
		public function getUserInfo()
		{
			//... validation
			return $this->userService->getUserInfo ($this->request->all());
		}

	}

```
### UserService
```
<?php
	namespace App\Service;

    class UserService{
        public $userRepository;
        
        public function __construct(UserRepository $userRepository){
            $this->userRepository = $userRepository;
        }
        public function getUserInfo()
		{
            return $this->userRepository->getUserInfo($data);
		}

    }

```
### UserRepository
```
<?php
	namespace App\Repository;

    class UserRepository{
    
        public function getUserInfo($data)
		{
            $userId = $data['user_id'];
            $result = User::where('id',$userId)->first();
			
			return $result;
		}

    }

```
解决了R的问题，有人就问了，难道因为CUD比较统一简单就可以放在一起了吗？答案是NO，我们引用一个新的名词Action。
# Action
> 这是看了@Charlie_Jade的文章才学到的

独立每个操作文件，例如CreateUser,DeleteUser,UpdateUser
- 符合SOLID的单一原则

![](https://blog.fastrun.cn/wp-content/uploads/2018/07/1466549309-5b20997c1d4cc_articlex.png)


### UserController
```
<?php
	namespace App\Http\Controller;

	class UserController extends Controller{

		public $request;
		
		protected $userService;
		
		public function __construct(Request $request, UserService $userService)
		{
			$this->request = $request;
			
			$this->userService = $userService;
		}
		
        public function register(){
            //... validation
            return $this->userService->register($this->request->all());
        }

		public function getUserInfo()
		{
			return $this->userService->getUserInfo ($this->request->all());
		}

	}

```
### UserService
```
<?php
	namespace App\Service;

    class UserService{
        
        public function getUserInfo(UserRepository $userRepository)
		{
            return $this->userRepository->getUserInfo($data);
		}

        public function register(){
            $result = (new CreateUser())->execute($this->request->all());
            
            return $result;
        }

    }

```
### UserRepository
```
<?php
	namespace App\Repository;

    class UserRepository{
    
        public function getUserInfo($data)
		{
            $userId = $data['user_id'];
            $result = User::where('id',$userId)->first();
			
			return $result;
		}

    }

```
### CreateUser
```
<?php

	namespace App\Action;
	
	use App\Model\Member;
	
	class CreateUser extends CreateUserWallet
	{
		public function execute(array $data)
		{
			$models           = new Member();
			$models->tel      = $data['tel'];
			$models->password = $data['password'];
			$result           = $models->save ();
				
			return $result;
		}
	}
```
以上代码逻辑见下图

![](https://blog.fastrun.cn/wp-content/uploads/2018/07/2376773134-5b20be2921b9d_articlex.png)

除模版（V）等HTML,JS等，还需要一些其他的规则，或者说是方式去实现一些代码的解耦合，以下不再提供代码案例。

# Common
译为公共的，常用的，再部分开发中，你可能需要一些公共的方法（并非公共的类，例如邮件发送等，用他并不合适），比如查询用户余额，查询用户是否注册或者是否在线，生成订单号等。使用Common更要简单。他更像一个公共函数库的样子

![](https://blog.fastrun.cn/wp-content/uploads/2018/07/1914661340-5b20bfd455c5d_articlex.png)

# Event
不关心执行结果时可以选使用，不过Event的Listen也是提供了队列。

# Exception
不要将你的所有错误提示都使用Return返回，很多时候你的返回未必是你的返回

# 致谢
感谢各位同学看完这篇文章，如果你有新的想法欢迎在评论区讨论.

# 参考文章
Laravel 的中大型專案架構:https://old-oomusou.goodjack.tw/laravel/architecture/#Service
Laravel 程序架构设计思路使用动作类 : https://segmentfault.com/a/1190000015208089
如何使用 Service 模式? : https://old-oomusou.goodjack.tw/laravel/service/
面向对象设计的SOLID原则 : https://www.cnblogs.com/shanyou/archive/2009/09/21/1570716.html
