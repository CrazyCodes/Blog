![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/10/2978289583-5bd423e85207c_articlex.png)

# 前言
作为一名PHP程序员,我感到荣幸。但在时代不断的变迁中，要具备足够的知识才可生存。

那就从Go语言学起把。

希望看到本篇文章的你可以对Go有一个基本的认识。本系列文章与我自己学习Go语言的方式去描述。以PHP代码与Go代码的对比加以区分理解。
# 加载
PHP
```
namespace Action
use Action
```
Go
```
package Action
import "action"
```
# 数组
PHP
```
// 初始化
$arr = []
$arr = array()
// 初始化赋值
$arr = [1,2,3]
// 多维数组
$arr = [][]
// 获取值
echo $arr[1]
// 获取数组总数
echo length($arr)
// 获取数组区间
$a=array("red","green","blue","yellow","brown");
print_r(array_slice($a,1,2));
// 设置key=>value
$arr = ["username"=>"zhangsan","age"=>13]
// 删除指定下标
unset($arr[0])
```
Go 数组 & 切片 (切片是数组的一个View,就例如MySQL的视图一样)
```
// 初始化
var arr [5]int
// 初始化赋值
arr := [5]int{1, 2, 3, 4, 5}
// 无需声明数组个数
arr := [...]int{1, 2, 3, 4, 5, 6, 7}
// 多维数组
var arr [4][5]bool
// 获取值
fmt.Println(arr[1])
// 获取数组总数
fmt.Println(len(arr))
// 获取数组区间 显而易见,Go对数组的操作更便利直观
a := [...]string{"red","green","blue","yellow","brown"}
fmt.Println(a[1:2])
// 设置key=>value 这里需要使用Map
m := map[string]string{
	"username": "zhangsan",
    "age" : "13"
}
// 删除指定下标 Go没有删除数组下标的系统方法
arr := arr[1:]
// 删除中间位置的下标 可通过合并的方式去除指定下标
arr := append(arr[:3],arr[4:])
```
# 循环结构
PHP
```
// 基本结构
for($i=0;$i<10;$i++){
    echo $i;
}
// 死循环
for($i=0;$i<10;$i++){
    echo $i;
    $i--
}
// 获取key,value
foreach($arr as $key=>$value){
    echo $key,$value
}
```
Go
```
// 基本结构
for i := 0; i < 10; i++ {
	fmt.Println(i)
}
// 死循环 可见Go写死循环非常方便
for {
	fmt.Println("")
}
// 获取key,value
for k, v := range arr {
	fmt.Println(k, v)
}
```

# 控制结构
PHP
```
// if
if(true){

}
// switch
switch(true){
    case true:
        echo true;
        break;
}
```
Go
```
// if
if true {
		
}
// switch Go语言的Switch的Case不需要break
switch true {
	case true:
		fmt.Println(true)
}
```

# 类
PHP
```
// 声明一个类
class City{}
```
Go
```
// 声明一个结构体 这里并非混淆公众，是因为Go本身没有类的概念，只是其声明及操作方法与类概念相似
type City struct{}
```
Go语言的结构体会在下一个章节来做对比
# 致谢
感谢你看到这里，希望本篇文章可以帮到你。谢谢