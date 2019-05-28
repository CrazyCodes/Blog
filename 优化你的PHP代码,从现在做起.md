![clipboard.png](https://resources.blog.fastrun.cn/wp-content/uploads/2019/05/2588740952-5ce6624e0ca28_articlex.png)

# 前言
> 我一生的文章都会放在这里，我的博客，我希望每一行代码，每一段文字都能帮助你。https://github.com/CrazyCodes/Blog

大家好，我是CrazyCodes ，今天我们不聊工具、规范等等等等的辅助，就聊一下该如何写一段“好”的代码，本文以我的职业生涯碰到的代码为例，如有出入请在评论区提出异议，谢谢。

# 搜索功能
搜索很常见，复杂的搜索大多出行在后台，举个栗子，大概需求是这样的

**这是一个后台用户列表的搜索功能**
| 搜索条件 | 可否并行 | 是否必填 |
| -- | -- | -- |
| 用户名 | 可以 | 否 |
| 手机号码 | 可以 | 否 |
| 是否已认证 | 可以 | 是 |
| 用户性别 | 可以 | 否 |
| 最近登录时间 | 可以 | 否 |
| 账户余额 | 可以 | 否|

# 初学者代码
看到这些例子你是否不由的一颤，又要开始造轮子的是不是？
以原生的例子为例，开始你可能会这样写(以下为伪代码)
```
if (IS_POST) {
    $like = '';
    if (isset($_POST['username'])) {
        $username = $_POST['username'];
        $like .= "username like '%" . $username . "%' and ";
    }

    if (isset($_POST['phone'])) {
        $phone = $_POST['phone'];
        $like .= "phone like '%" . $phone . "%' and";

    }

    if ($_POST['is_auth']) {
        $isAuth = $_POST['is_auth'];
        $like .= "is_auth like '%" . $isAuth . "%' and";

    }

    if ($_POST['sex']) {
        $sex = $_POST['sex'];
        $like .= "sex like '%" . $sex . "%' and";
    }


    if ($_POST['time']) {
        $time = $_POST['time'];
        $like .= "time like '%" . $time . "%' and";
    }


    if ($_POST['wallet']) {
        $wallet = $_POST['wallet'];
        $like .= "wallet like '%" . $wallet . "%' and";

    }

    $like = rtrim($like, 'and');

    $sql = "SELECT * FROM `user` WHERE {$like}";


} else {
    return view('user');
}
```
# 封装
恩...,还不错，结构清晰，传统的初学者条型代码，接下来我们先封装一下几块代码。
```
function post($param)
{
    return isset($_POST[$param]) ? $_POST[$param] : null;
}

if (IS_POST) {
    $like = '';
    if (post('username')) {
        $username = post('username');
        $like .= "username like '%" . $username . "%' and ";
    }

    if (post('phone')) {
        $phone = post('phone');
        $like .= "phone like '%" . $phone . "%' and";

    }

    if (post('is_auth')) {
        $isAuth = post('is_auth');
        $like .= "is_auth like '%" . $isAuth . "%' and";

    }

    if (post('sex')) {
        $sex = post('sex');
        $like .= "sex like '%" . $sex . "%' and";
    }


    if (post('time')) {
        $time = post('time');
        $like .= "time like '%" . $time . "%' and";
    }


    if (post('wallet')) {
        $wallet = post('wallet');
        $like .= "wallet like '%" . $wallet . "%' and";

    }

    $like = rtrim($like, 'and');

    $sql = "SELECT * FROM `user` WHERE {$like}";


} else {
    return view('user');
}
```
# 适当使用迭代
恩，至少我们可以自由控制post方法了，但是这类过程化代码维护性太低，我们再改进下
```
function post($param)
{
    return isset($_POST[$param]) ? $_POST[$param] : false;
}

function postAll()
{
    return $_POST;
}

if (IS_POST) {
    $like = '';


    foreach (postAll() as $key => $value) {
        if (post($key)) {
            $like .= "{$key} like '%{$value}%' and";
        }
    }

    $like = rtrim($like, 'and');

    $sql = "SELECT * FROM `user` WHERE {$like}";


} else {
    return view('user');
}
```
# 面向对象
加个迭代代码看起来还算是整洁了点，作为PHP程序员，写代码不面向对象不靠谱，把class加上
```
function request($param = null)
{
    return new Request($param);
}

class Request
{
    public function __construct(string $param = null)
    {
        return isset($_POST[$param]) ? $_POST[$param] : false;
    }

    public function all()
    {
        return $_POST;
    }
}


class User
{
    public function index()
    {
        if (IS_POST) {
            $like = '';

            foreach (request()->all() as $key => $value) {
                if (request($key)) {
                    $like .= "{$key} like '%{$value}%' and";
                }
            }

            $like = rtrim($like, 'and');

            $sql = "SELECT * FROM `user` WHERE {$like}";

        } else {
            return view('user');
        }
    }
}

```
# 对User的改造
我们在对User的类进行改造,做一些判断及筛选
```
function request($param = null)
{
    return new Request($param);
}

class Request
{
    public function __construct(string $param = null)
    {
        return isset($_POST[$param]) ? $_POST[$param] : false;
    }

    public function all()
    {
        return $_POST;
    }
}


class User
{
    public $request = [
        'username',
        'phone',
        'is_auth',
        'sex',
        'time',
        'wallet'
    ];

    public function index()
    {
        if (IS_POST) {
            $like = '';

            foreach (request()->all() as $key => $value) {
                if (in_array($key, $this->request) && request($key)) {
                    $like .= sprintf("%s like %s and", $key, $value);
                }
            }

            $like = rtrim($like, 'and');

            $sql = "SELECT * FROM `user` WHERE {$like}";

        } else {
            return view('user');
        }
    }
}


```
这就差不多了，对比真是的代码可能还相差甚远，我写这篇文章的目的不是教会你如何写代码，是想说明编码不是一次性的，应经过多次修改，使代码具有可维护性，扩展性等等的，各种“性”
# 致谢
感谢你看到这里，希望本篇文章可以帮到你，谢谢
