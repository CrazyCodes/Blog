不涉及其他的语言及工具，我们从PHP本身来谈如何实现服务治理
> 本猿人已经写好的服务治理 https://github.com/CrazyCodes/Service-Govern.git
# 治理什么？
这个专业名词很容易发现治理的是服务，而服务则是我们的项目。管理这些服务方案则叫服务治理。

现在在Server上有四项服务，分别为 
* UserService
* ShopService
* GoodsService
* LiveService
这些服务我们叫它服务提供者(既提供对内服务的应用)

调用服务的应用我们称它为服务消费者,例如
* User-Api
* Shop-Api
* Goods-Api
* Live-Api

Service 是对内服务的而Api是对外服务的

![](https://blog.fastrun.cn/wp-content/uploads/2018/07/3373537274-5a98b8a1ccbb7_articlex.png)

图片来源:https://blog.csdn.net/suifeng3051/article/details/53992560

服务治理考虑的问题就是如何管理这四项服务，让它们如何对外服务，如何监控服务进程

# 依托实现
在实现服务治理之前，需要了解以下几块知识点
* thrift
* rpc
* swoole

### thrift 
暂时大可理解为可以通过它去调用其他开发语言的方法
### rpc
> RPC（Remote Procedure Call）—远程过程调用，它是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。RPC协议假定某些传输协议的存在，如TCP或UDP，为通信程序之间携带信息数据。在OSI网络通信模型中，RPC跨越了传输层和应用层。RPC使得开发包括网络分布式多程序在内的应用程序更加容易。
RPC采用客户机/服务器模式。请求程序就是一个客户机，而服务提供程序就是一个服务器。首先，客户机调用进程发送一个有进程参数的调用信息到服务进程，然后等待应答信息。在服务器端，进程保持睡眠状态直到调用信息到达为止。当一个调用信息到达，服务器获得进程参数，计算结果，发送答复信息，然后等待下一个调用信息，最后，客户端调用进程接收答复信息，获得进程结果，然后调用执行继续进行。

服务与服务之间通信可以通过RPC通信，当然也可以选择UDP等

### swoole
PHP圈内跨世纪的产物，使用他的原因是因为Swoole本身支持RPC通信，所以我们本章通过Swoole Rpc的方法去实现消费者与服务提供者之间的通信

# 实现RPC通信
服务治理非常重要的一个环节，要在无感知的情况让消费者A调用服务提供者A,B,C，当然实际情况下，这是永远不可能的，根本不在一个内存空间中，我们需要自己模拟出来这种使用方式
```
$userSerivce = $client->client ('UserService');
$result = $userSerivce->getUserInfo (['user_id' => 100]);
var_dump($result);
```
在消费者内没有UserService，更没有getUserInfo 方法，这些都在服务提供者的应用中，如何去调用它们？

首先通过php的__call 方法去截取一个不存在的方法
```
public function __call($name, $arguments)
{
	$client = new ClientRpc($this->serviceName);
			
	$response = $client->send ($this->serviceName, $name, $arguments);
			
	return (json_decode ($response, true));
			
}
```
获取后调用自己写的send 方法,swoole出场
```
class ClientRpc
{
	protected $client;
		
	public function __construct($service_name, $centerConfig)
	{
		$this->client = new \swoole_client(SWOOLE_SOCK_TCP);
			
		$center = Dispatcher::loadBalance ($service_name, $centerConfig);
		$this->client->connect ($center['ip'], $center['port'], 0.5);
	}
		
	public function send($service, $action, $arguments)
	{
		$request = new Request();
		
		$request->setService ($service);
		$request->setAction ($action);
		$request->setParameters ($arguments[0]);
        // 重组参数，组合成你希望的格式，最后转成json发送到服务提供者
		$this->client->send (json_encode ((array)$request));
			
		return $this->client->recv ();
	}
		
	public function __destruct()
	{
		$this->client->close ();
		unset($this->client);
	}
}
```

# 鸣谢
周梦康  [https://mengkang.net/]
