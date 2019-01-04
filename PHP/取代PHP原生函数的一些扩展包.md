![](https://blog.fastrun.cn/wp-content/uploads/2018/08/67807839-5b727ad687555_articlex.png)


# 前言
虽然程序员无时无刻都在造轮子，但造轮子也有效率之分，用好轮子才能造出好“🚗”

# guzzlehttp/guzzle
> composer require guzzlehttp/guzzle

你可以用guzzlehttp完全取代curl,file_get_content,fopen等函数。这个扩展包使用起来极为顺手。我们在代码量上看下对比。

## php_curl
```
<?php
    //初始化
    $curl = curl_init();
    //设置抓取的url
    curl_setopt($curl, CURLOPT_URL, 'http://www.baidu.com');
    //设置头文件的信息作为数据流输出
    curl_setopt($curl, CURLOPT_HEADER, 1);
    //设置获取的信息以文件流的形式返回，而不是直接输出。
    curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
    //设置post方式提交
    curl_setopt($curl, CURLOPT_POST, 1);
    //设置post数据
    $post_data = array(
        "username" => "coder",
        "password" => "12345"
    );
    curl_setopt($curl, CURLOPT_POSTFIELDS, $post_data);
    //执行命令
    $data = curl_exec($curl);
    //关闭URL请求
    curl_close($curl);
    //显示获得的数据
    print_r($data);
```
## guzzlehttp
```
use GuzzleHttp\Client;

$client = new GuzzleHttp\Client();

$response = $client->request('POST', 'http://www.baidu.com', [
    'form_params' => [
        'username' => 'coder',
        'password' => '12345'
    ]
]);

print_r($response);
```
# jenssegers/date
> composer require jenssegers/date

使用这个扩展包,让php程序员对date相关的需求实现更简洁、简单了。请看下方对比

## php_date
```
date("Ym", strtotime("-1 day"));  //获取前一天的日期 

date("Ym", strtotime("+1 day"));  //获取后一天的日期
```
## jenssegers_date
```
(new Date('-1 day'))->format ('Ym');  // 获取前一天的日期 

(new Date('+1 day'))->format ('Ym');  //获取后一天的日期
```
显而易见,新的方法对date的处理呈现更加直观。当然这是简单的应用，复杂的日期计算中，它会更显优势。

# chumper/zipper
> composer require chumper/zipper
使用这个包可以简化php本身zip函数使用的复杂度

## php_zip
```
<?php
    $resource = zip_open($filename);
    while($zip = zip_read($resource)) {
        if(zip_entry_open($resource, $zip)) {
    $file_content = zip_entry_name($zip);
            $file_name = substr($file_content, strrpos($file_content, '/') +1);
            if(!is_dir($file_name) && $file_name) {
                $save_path = $dir .'/'. $file_name;
                if(file_exists($save_path)) {
                echo '文件夹内已存在文件 "' . $file_name . '" <pre />';
                }else {
                    echo $file_name . '<pre />';  
                    $file_size = zip_entry_filesize($zip);
                    $file = zip_entry_read($zip, $file_size);
                    file_put_contents($save_path, $file);
                    zip_entry_close($zip);
                }
                 
            }
        }
    }
    zip_close($resource);
```
## chumper/zipper
```
Zipper::make('test.zip')->folder('test')->extractTo('foo');
```
显而易见,我想我不用去解释什么了。

# anchu/ftp
> composer require anchu/ftp
本包可以简化php自身ftp上传代码的流程

## php_ftp
```
<?php
$host = '10.0.0.42';
$user = 'uftp';
$pwd = 'uftp';
 
// 进行ftp连接，根据port是否设置，传递的参数会不同
if(empty($port)){
    $f_conn = ftp_connect($host);
}else{
    $f_conn = ftp_connect($host, $port);
}
if(!$f_conn){
    echo "connect fail\n";
    exit(1);
}
echo "connect success\n";
 
// 进行ftp登录，使用给定的ftp登录用户名和密码进行login
$f_login = ftp_login($f_conn,$user,$pwd);
if(!$f_login){
    echo "login fail\n";
    exit(1);
}
echo "login success\n";
 
// 获取当前所在的ftp目录
$in_dir = ftp_pwd($f_conn);
if(!$in_dir){
    echo "get dir info fail\n";
    exit(1);
}
echo "$in_dir\n";
 
// 获取当前所在ftp目录下包含的目录与文件
$exist_dir = ftp_nlist($f_conn, ftp_pwd($f_conn));
print_r($exist_dir);
 
// 要求是按照日期在ftp目录下创建文件夹作为文件上传存放目录
echo date("Ymd")."\n";
$dir_name = date("Ymd");
// 检查ftp目录下是否已存在当前日期的文件夹，如不存在则进行创建
if(!in_array("$in_dir/$dir_name", $exist_dir)){
    if(!ftp_mkdir($f_conn, $dir_name)){
        echo "mkdir fail\n";
        exit(1);
    }else{
        echo "mkdir $dir_name success\n";
    }
}
// 切换目录
if(!ftp_chdir($f_conn, $dir_name)){
    echo "chdir fail\n";
    exit(1);
}else{
    echo "chdir $dir_name success\n";
}
// 进行文件上传
$result = ftp_put($f_conn, 'bbb.mp3', '/root/liang/ftp/bbb.mp3', FTP_BINARY);
if(!$result){
    echo "upload file fail\n";
    exit(1);
}else{
    echo "upload file success\n";
    exit(0);
}
```
## anchu/ftp
```
Config::set('ftp.connections.key', array(
   'host'   => '',
   'username' => '',
   'password'   => '',
   'passive'   => false,
   'secure'   => false,
));
FTP::uploadFile($fileFrom，$fileTo，$mode)
```


# 致谢

感谢你看到这里，希望本篇文章可以帮助到你。

**向这些具有开源精神的工程师致敬**