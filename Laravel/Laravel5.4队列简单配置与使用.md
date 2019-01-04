# 概述

## 什么是队列？


百度百科是这样说的

> “队列”是在传输过程中保存数据的容器。

举几个生活中例子：
* iphone手机新款发布，三里屯iphone进的新货。大家要排队买，不能说一大堆人一起冲进去，那么这店就完了。
* 你有一大堆女朋友。你不得一个一个来，一起来你也受不了 ?
* C语言中堆的概念也是这样，先进先出，不能起个大早赶个晚集。

回到正题：

消息队列则是为大批量处理数据而准备的一个概念，他有很多实现方式，并不是单一的代码结构。

这里有比较专业的一篇文章：
http://www.cnblogs.com/xuyatao/p/6864109.html

还有适合新手揣摩的教程：
http://www.imooc.com/learn/852

<hr>

废话说完了，彻底进入正题：

Laravel 为我们提供了一个简单并很容易配置的队列类.

引用一点官方翻译的话：
> Laravel 队列为不同的后台队列服务提供统一的 API ， 例如 Beanstalk，Amazon SQS， Redis，甚至其他基于关系型数据库的队列。 队列的目的是将耗时的任务延时处理，比如发送邮件，从而大幅度缩短Web请求和相应的时间。

> 队列配置文件存放在 config/queue.php。 每一种队列驱动的配置都可以在该文件中找到， 包括数据库， Beanstalkd， Amazon SQS， Redis， 以及同步（本地使用）驱动。 其中还包含了一个null队列驱动用于那些放弃队列的任务。


Laravel 在 5.4版本中直接提供了全局函数 dispatch(),你可以再任意地方调用。并且无需加载任何对象或者实例化类。 这个函数主要的用途就是将你的队列需求加入到指定的容器中（专业点的叫生产者，其实你大可理解为你在商城购物完排队结账的时候）


# 设置驱动
Laravel神奇数据库迁移我就不多说了。我相信你知道。
``` php
php artisan queue:table

php artisan migrate
```
执行完上面两条命令，费力打开 config\queue.php, key=default 的数组中使用env配置文件加载方式，laravel安装后默认为sync（同步），我们需要改为异步（你现在可以暂时认为同步!=队列）,这里我们选择使用关系型数据库来实现队列
```shell
QUEUE_DRIVER=database
```

# 创建任务
创建任务 = 搞一个生产者 = （其实就是写一个在队列中你想执行的业务逻辑）,名字随意取，但最好遵守命名规范
```
php artisan make:job SendReminderEmail
```
这个生成的文件大概分2部分：__construct() 构造方法 , handle 队列执行方法(意思就是在队列执行的时候，就用你这里面写的代码)
```
class SendReminderEmail implements ShouldQueue
{
    // 这块你不用搭理他
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
    
    protected $name;

    /**
     * Create a new job instance.
     *
     * @return void
     */
    public function __construct($name)
    {
        $this->name = $name;
    }

    /**
     * Execute the job.
     *
     * @return void
     */
    public function handle()
    {
        DB::table('email')->insert([
            'name' => $this->name,
            'img'=>1,
            'sort'=>1
        ]);
    }
}
```
# 生产者
随后在控制器内使用dispatch方法调用即可,下面我则for循环创建了100个业务
```
public function index(Request $request)
{
    for ($i = 0; $i <= 100; $i++) {
        dispatch(new SendReminderEmail("email" . $i));
    }
}
```

你通过数据迁移的数据表中就基本成这样了
![](https://blog.fastrun.cn/wp-content/uploads/2018/07/4045092428-598c7ac72a29d_articlex.png)

# 消费者
消费者 = 队列处理 = （你在商城购物已经开始付钱了）,使用下面命令则你开始消费，队列也会按照你上面的业务逻辑开始处理。处理完毕后当前任务会自动删除。
```
php artisan queue:work
```
基本就下面这个样

![](https://blog.fastrun.cn/wp-content/uploads/2018/07/1645244754-598c7b6a96b37_articlex.png)

到此Laravel5.4 队列简单配置与使用就结束了。

更多专业吊炸天的教程请参考China Laravel
http://d.laravel-china.org/docs/5.4/queues