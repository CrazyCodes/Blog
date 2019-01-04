![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/10/2978289583-5bd423e85207c_articlex.png)

# 前言
作为一名PHP程序员,我感到荣幸。但在时代不断的变迁中，要具备足够的知识才可生存。

那就从Go语言学起把。

希望看到本篇文章的你可以对Go有一个基本的认识。本系列文章与我自己学习Go语言的方式去描述。以PHP代码与Go代码的对比加以区分理解。

这是转型手记的最后一章，在往下没办法再去写了，需要详细的看书去了解。本章以实战方式来对比以下PHP与Golang的写法。

这里使用Laravel与Beego(基于Go开发的MVC框架)来演示

# 安装
Laravel
```
// 通过composer直接安装,简单易用
composer global require "laravel/installer"
// 创建一个项目
laravel new blog
```
Beego
```
// go自身就有包管理
go get github.com/astaxie/beego
// 创建项目也非常简单
bee api blog
```
# 目录结构
Laravel
```
// laravel 的结构这里就不再阐述
| - app
| - bootstrap
| - config
| - database
| - public
| - resources
| - routes
| - storage
| - tests
| - vendor
```
Beego
```
// 显而易见，beego并没有laravel那样过度设计(虽然过度设计并非指目录，
// 但以看目录就知道beego真的没有太多东西)
blog
├── conf
│   └── app.conf
├── controllers
│   └── object.go
│   └── user.go
├── docs
│   └── doc.go
├── main.go
├── models
│   └── object.go
│   └── user.go
├── routers
│   └── router.go
└── tests
    └── default_test.go
```
# 路由
Laravel
```
Route::get('/user', 'UserController@index');
```
Beego
```
// 与laravel的使用方式差不多
// 这里为了统一，路由直接绑定控制器方法只有下列这种
// beego 还提供了注解方式 ， 详情见 https://beego.me/docs/mvc/controller/router.md
beego.Router("/user",&UserController{},"get:index")
```
# 模型 (Model)
Laravel
```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * 与模型关联的数据表。
     *
     * @var string
     */
    protected $table = 'user';
}
```
Beego
```
// Beego通过结构体名称作为表名,并且orm操作的所有字段都必须提前声明
package models

import (
	"github.com/astaxie/beego/orm"
)
type User struct {
	Id       int    `json:"id"`
	Tel      string `json:"tel"`
	Password string `json:"password"`
	Status   string `json:"status"`
}

func init() {
	orm.RegisterModel(new(User))
}
```
# 控制器
Laravel
```
<?php

namespace App\Http\Controllers;

use App\User;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
    /**
     * 显示给定用户的概要文件
     *
     * @param  int  $id
     * @return Response
     */
    public function index($id)
    {
        return view('user.profile', ['user' => User::findOrFail($id)]);
    }
}
```
Beego
```
package controllers

import (
	"github.com/astaxie/beego"
	"github.com/astaxie/beego/orm"
)

// 这里相当于继承了父类 beegoController
type MemberController struct {
	beego.Controller
}


func (c *CityController) Index() {
	var results []orm.Params

	orm.NewOrm().QueryTable("member").
		Values(&results)

	c.Data["json"] = results
	c.ServeJSON()
}
```
# 总结
学Go有1个多月的时间了。写这类的文章也遭到了很多质疑，作为一个手记去发布仅仅为了让想去学习其他语言的朋友了对新语言的一个认识，不是去对比其不同。而找其语法相似点。

语言只是工具，希望各位PHP工程师不要仅限于去使用PHP，这一年看到很多写PHP程序员未来之路一类的文章，但从未提出要去学习其他语言。

不要将自己圈在一个领域，不要做井底之蛙。
# 致谢
感谢你看到这里，希望本篇文章可以帮到你。谢谢