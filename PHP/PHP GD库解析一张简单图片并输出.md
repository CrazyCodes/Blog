![](https://blog.fastrun.cn/wp-content/uploads/2018/06/73678c8cd3692cf204938a47fc1af0e7.png)

> 这里只演示一下2种颜色值的图片，简单描述下概念。

![](https://blog.fastrun.cn/wp-content/uploads/2018/07/3158259558-5ae2851854b2f_articlex.png)

首先要安装下GD库。否则下面的代码运行不了。

```php
$size = getimagesize('2.png');  // 获取图片大小 
$res = imagecreatefrompng('2.png'); // 获取指定图片的资源对象

for ($i = 0; $i < $size[1]; ++$i) {
    for ($j = 0; $j < $size[0]; ++$j) {
        $rgb = imagecolorat($res, $i, $j); // 获取坐标索引

        $rgbarray = imagecolorsforindex($res, $rgb); // 获取每个坐标的rgb颜色


        $sum = $rgbarray['red'] + $rgbarray['green'] + $rgbarray['blue']; // rgb颜色数值相加，主要为了区分

        /**
         * 演示图片有纯黑色 rgb(0,0,0) 颜色和其他颜色组成
         */
        if ($sum == 0) {
            $data[$i][$j] = 1;
        } else {
            $data[$i][$j] = 2;
        }

}
```
上述代码已经生成了整张图片每个像素的颜色块。

```
echo "<div style='border:1px solid #ccc;width: {$size[1]}px;height: {$size[0]}px;'>";
	
	for ($i = 0; $i < count ($data); $i++) {
		if (array_sum ($data[$i]) != 200) {
			for ($j = 0; $j < count ($data[$i]); $j++) {
				if ($data[$i][$j] == 1) {
					echo '<div style="width:1px;height:1px;background: #cccccc;float:left;"></div>';
				} else {
					echo '<div style="width:1px;height:1px;background: red;float:left;"></div>';
				}
			}
		}
	}
	
	echo "</div>";

```
通过上述代码就可以生成一个与指定图片一样的通过像素块堆积出来的图片。

> similar_text 函数可以判断2个值的相似度。我再考虑是否可以使用a图片的二进制码和b图片的比对。判断相似度呢。
仅仅是个概念，还再继续研究，这样就可以实现文字识别的功能了。