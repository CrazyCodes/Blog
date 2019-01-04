# 概述
整理一些日常生活中基本用不到的PHP函数，也可以说在框架内基本都内置了，无需我们去自行使用的函数。量不多。后续在日常开发中如遇到更多的冷门，会更新本文章

# sys_getloadavg

> 获取系统的负载

```
<?php
$load = sys_getloadavg();
if ($load[0] > 80) {
    header('HTTP/1.1 503 Too busy, try again later');
    die('Server too busy. Please try again later.');
}
?>
```
# compact
>  创建一个包含变量名和它们的值的数组
```
<?php
$firstname = "Bill";
$lastname = "Gates";
$age = "60";

$result = compact("firstname", "lastname", "age");

print_r($result);
?>
```
# uniqid
> 基于以微秒计的当前时间，生成一个唯一的 ID。
```
<?php
echo uniqid();
?>
```
# pack
> 把数据装入一个二进制字符串。
```
pack(format,args+)
```
| 参数 | 描述 |
| -- | -- |
 | format | 必需。规定在包装数据时所使用的格式。 | 
| args+ | 可选。规定被包装的一个或多个参数。 | 

```
<?php
echo pack("C3",80,72,80);
?>
```
# exif_imagetype
> 判断一个图像的类型
```
<?php

if (exif_imagetype("image.gif") != IMAGETYPE_GIF) {
    echo "The picture is not a gif";
}

?>
```

# 致谢
这篇文章很短，感谢你看完这篇文章。如果有什么冷门的函数，可在评论区留言。有时冷门函数也会帮上大忙不是嘛？