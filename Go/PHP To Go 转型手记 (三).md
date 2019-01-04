![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/10/2978289583-5bd423e85207c_articlex.png)
# 前言
作为一名PHP程序员,我感到荣幸。但在时代不断的变迁中，要具备足够的知识才可生存。

那就从Go语言学起把。

希望看到本篇文章的你可以对Go有一个基本的认识。本系列文章与我自己学习Go语言的方式去描述。以PHP代码与Go代码的对比加以区分理解。

Go没有类的概念，本章在语法使用上来对比PHP与Go之间的区别。

# 声明类
PHP
```
class User{}
```
Go
```
type User struct{}
```
# 成员变量
PHP
```
class User{
    public $name;
    public $age;
}
```
Go
```
type User struct {
	username string
	age      int
}
```
# 变量修饰
PHP
```
class User{
    public $name;
    private $age;
}
```
Go
```
// 没有看错，Go中没有保护(protected),变量名首字母大写为public,小写为private
type User struct {
	Username string
	Age      int
}
```
# 成员方法
PHP
```
class User{
    public $name;
    public $age;
    
    function setName(){
        
    }
    
    function getName(){
    
    }
}
```
Go
```
type User struct {
	username string
	age      int
}

func (u User) setName(name string) bool {
	u.username = name

	return true
}

func (u User) getName() string {
	return u.username
}
```
# 初始化
PHP
```
// php 没有构造方法的话，新建对象无需传参数
new User();
```
Go
```
// go 内结构体声明的变量是可选参数，既可传可不传，go既没有类概念，所以也没有构造方法。
User{"zhangsan",15}
```
# 使用
PHP
```
$user = new User();
$user->getName();
```
Go
```
u := User{"zhangsan",15}
fmt.Println(u.getName())
```
# 致谢
感谢你看到这里，希望本篇文章可以帮到你。谢谢