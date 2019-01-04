![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/10/2978289583-5bd423e85207c_articlex.png)

# 前言
作为一名PHP程序员,我感到荣幸。但在时代不断的变迁中，要具备足够的知识才可生存。

那就从Go语言学起把。

希望看到本篇文章的你可以对Go有一个基本的认识。本系列文章与我自己学习Go语言的方式去描述。以PHP代码与Go代码的对比加以区分理解。
# 变量
PHP
```
// 初始化变量
$domain = "blog.fastrun.cn";
// 批量赋值
$domain = $domain1 = $domain2 = "blog.fastrun.cn";

```
Go
```
// 初始化变量
var domain string = "blog.fastrun.cn"
// 批量赋值
var domain,domain1,domain2 string = "blog.fastrun.cn"
// 批量声明赋值
var username,age,local = "zhangsan",13,"BeiJing"
var(
    username="zhangsan"
    age = 13
    local = "BeiJing"
)
```
# 常量
PHP
```
define("FOO","something");
```
Go
```
// 单独声明
const FOO [string]= something
// 批量声明
const (
	USERNAME = "zhangsan"
	AGE      = 30
)
```
# 打印
PHP
```
// 基本输出
echo "blog.fastrun.cn";
// 格式化输出
printf("my blog %s","blog.fastrun.cn");
```
Go
```
// 基本输出
fmt.Println("blog.fastrun.cn")
// 格式化输出
fmt.Printf("my blog %s","blog.fastrun.cn")
```
# 函数
PHP
```
// 基本声明
function printString(string $string){
    echo $string;
}
// 带返回值
function printString(string $string) : string{
    return $string;
}
```
Go
```
// 基本声明
func printString(s string){
    fmt.Println(s)
}
// 带返回值
func printString(s string) string{
    return s
}
```
# 致谢
感谢你看到这里，希望本篇文章可以帮到你。谢谢