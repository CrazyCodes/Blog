![clipboard.png](https://blog.fastrun.cn/wp-content/uploads/2018/07/1220604718-5b3cd86641ab8_articlex.png)

# 概述
> RabbitMQ是一款消息队列中间件。他提供了几乎覆盖所有语言的SDK与文档，简直强大的不的了。要详细的去了解学习RabbitMQ，我建议还是看官方文档吧。http://www.rabbitmq.com/getstarted.html

消息队列有以下几个基本用途

- 异步处理
- 应用解耦
- 流量削峰
- 系统架构

> 消息队列的这几个用途我会在后续的文章以真实案例去表述

# 生产者
## 创建RabbitMQ链接
```
$connection = new AMQPStreamConnection('localhost', 5672, 'guest', 'guest');
$channel    = $connection->channel ();
```
与链接一个数据库概念差不多
## 创建一个队列声明
```
$channel->queue_declare ('hello');
```
创建多个队列与创建多个数据库一样,hello则是队列名

## 创建一条消息到队列
```
$message = new AMQPMessage('Hello World!');
```

## 发布消息到队列
```
$channel->basic_publish ($message, '', 'hello');
```
hello 是上面创建的队列声明

## 关闭链接
```
$channel->close ();
$connection->close ();
```
释放资源

# 消费者

## 创建RabbitMQ链接
```
$connection = new AMQPStreamConnection('localhost', 5672, 'guest', 'guest');
$channel    = $connection->channel ();
```
与链接一个数据库概念差不多
## 创建一个队列声明
```
$channel->queue_declare ('hello');
```
创建多个队列与创建多个数据库一样,hello则是队列名

## 消费
```
$channel->basic_consume ('hello', '', false, true, false, false, function ($msg) {
	echo ' [x] Received ', $msg->body, "\n";
});
```
通过回调函数处理消息队列

## 等待
```
while (count ($channel->callbacks)) {
	$channel->wait ();
}
```
无消息时，挂起保持等待状态

## 关闭链接
```
$channel->close ();
$connection->close ();
```
释放资源

# 完整的案例
完整的按钮则是上面所有代码的整理
## 生产者
```
$connection = new AMQPStreamConnection('localhost', 5672, 'guest', 'guest');
$channel    = $connection->channel ();
$channel->queue_declare ('hello');

$message = new AMQPMessage('Hello World!');
$channel->basic_publish ($message, '', 'hello');

$channel->close ();
$connection->close ();
```
## 消费者
```
$connection = new AMQPStreamConnection('localhost', 5672, 'guest', 'guest');
$channel    = $connection->channel ();
$channel->queue_declare ('hello');
// basic_consume 方法第7个方法可以直接传入函数
$channel->basic_consume ('hello', '', false, true, false, false, function ($msg) {
	echo ' [x] Received ', $msg->body, "\n";
});
while (count ($channel->callbacks)) {
	$channel->wait ();
}
$channel->close ();
$connection->close ();
```

## 执行
```
php {生产者}.php
php {消费者}.php
```

# 其他
RabbitMQ支持多线程处理消息队列，所有你可以开启多个消费者去执行消息队列内的任务。你可以像我这样

![clipboard.png](https://blog.fastrun.cn/wp-content/uploads/2018/07/3389455799-5b3cd628ce5cc_articlex.png)

> 如果你感觉RabbitMQ这玩意还需要安装啥的感觉特麻烦,那你可以看下我的Laravel队列如何简单的玩起来。
https://segmentfault.com/a/1190000010604659

# 致谢
感谢你看完我这篇文章，纯手记的一篇文章，官方文档对新手的理解造成很多的误解，所以整理此文档，尽量避免新手“进”坑吧。对文章有什么问题或疑问，欢迎在评论区留言。谢谢