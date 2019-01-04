服务治理 治理的绝笔是服务，在一家公司有玩各种语言的程序员，如何去统一管理他们开发的服务，这是一个问题。

上一章主要讲了下服务治理需要什么，如何实现，这章我们详细的“肢解”一下服务治理的一个非常重要的组员 Thrift

上一章说明他的时候是这样写的

> 暂时大可理解为可以通过它去调用其他开发语言的方法


> 本猿人已经写好的服务治理 https://github.com/CrazyCodes/Service-Govern


# 名词解释
thrift其实是一个软件框架，用来进行可扩展且跨语言的服务的开发。它结合了功能强大的软件堆栈和代码生成引擎，以构建在 C++, Java, Go,Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk, and OCaml 这些编程语言间无缝结合的、高效的服务。

这个时候你就疑惑了?，如何跨语言调用

# 基本概念
如何调用这就需要讲一下我们强大的通信协议了。

## http (tcp)
超文本传输协议，正常访问浏览器啥看新闻、购物的时候必定使用，需要客户端和服务端握手?成功才可以正常显示，这中间握手的流出很复杂，执行各种各样的解码编码(为了方便理解，暂时这么想吧)

## rpc
> 远程过程调用协议,RPC采用客户机/服务器模式。请求程序就是一个客户机，而服务提供者就是一个服务器。首先，客户机调用进程发送一个有进程参数的调用信息到服务进程，然后等待应答信息。在服务器端，进程保持睡眠状态直到调用信息到达为止。当一个调用信息到达，服务器获得进程参数，计算结果，发送答复信息，然后等待下一个调用信息，最后，客户端调用进程接收答复信息，获得进程结果，然后调用执行继续进行。

rpc的优势很多，现在你大可理解为rpc通信要比http通信快很多就是了。

这个时候facebook和apache就厉害了，它们基于rpc通信协议开发出了一套[thrift](http://thrift.apache.org/)

# 实现方法
* 上面假设你都没看懂，这里我们实战下。
* 首先rpc我们通过使用swoole来实现，其他的手码。
* 分为客户端和服务端做下演示

## 客户端
首先我们new一个client类，去调用服务端的UserSerivce这个类,并且调用UserService类中的getUserInfo方法。
```
$client = new Client('UserSerivce');
$userInfo = $client->getUserInfo(1);

var_dump($userInfo);
```
Client中我们只需要干这样的一件事,使用php魔术方法__call去调用一个不存在的方法
```
class Client{
    protected $serviceName;
    		
    public function __construct($serviceName){
    	$this->serviceName = $serviceName;
    }
    public function __call($name, $arguments){
    	$rpcClient = new \swoole_client(SWOOLE_SOCK_TCP);
    	$rpcClient->connect('127.0.0.1',9503,0.5);
    	// 我们将要发送的数据是事先约定好的，跟写对外开放的Api一样
    	$rpcClient->send(json_encode([
    	    'service'=>$this->serviceName,
    	    'action'=>$name,
    	    'params'=>$arguments[0]
    	]));
    	$rpcClient->close();
    }
}
```
这个时候数据就通过rpc协议以json格式发送到了服务端

## 服务端
```
$server = new swoole_server("127.0.0.1", 9503);
$server->on('connect', function ($server, $fd){
    echo "connection open: {$fd}\n";
});
$server->on('receive', function ($server, $fd, $reactor_id, $data) {
    // $data 则就是客户端发送过来的数据，我们可以这样做来做到去调用类,当然你必须遵守PSR-4 Autoloader
    $request   = json_decode ($data, true);
    $className = $request['service'];
    $app       = new $className;
	$response  = $app->{$request['action']}($request['params']);
    
    $server->send($fd, "Swoole: {$data}");
    $server->close($fd);
});
$server->on('close', function ($server, $fd) {
    echo "connection close: {$fd}\n";
});
$server->start();
```

# 往期文章
* [PHP程序员如何简单的开展服务治理架构（一)](http://blog.fastrun.cn/2018/06/26/1/)

# 鸣谢
周梦康 https://mengkang.net/