> 系统 : Linux Centos 7.0

**前言：想让PHP可以操作Oracle数据库，那绝对是需要安装关于Oracle扩展。php的Oracle扩展叫oci**

**oracle扩展包下载地址：**
http://www.oracle.com/technetwork/database/features/instant-client/index-097480.html

网页上会显示如下的下载列表：

Download Oracle Database 10g Instant Client for Microsoft Windows (32-bit)

Download Oracle Database 10g Instant Client for Microsoft Windows 64-bit Itanium

Download Oracle Database 10g Instant Client for Microsoft Windows (x64)

Download Oracle Database 10g Instant Client for Linux x86

Download Oracle Database 10g Instant Client for Linux x86-64

Download Oracle Database 10g Instant Client for Linux Itanium ...

如系统是32位则选择第4个，64位选择第5个。

选择错误会造成make php oci8的时候报兼容失败。

下面是64位系统的演示。

**第一步：安装oracle**

```
rpm -ivh oracle-instantclient12.1-basic-12.1.0.2.0-1.x86_64.rpm
 
rpm -ivh oracle-instantclient12.1-devel-12.1.0.2.0-1.x86_64.rpm
 
rpm -ivh oracle-instantclient12.1-sqlplus-12.1.0.2.0-1.x86_64.rpm
```

说明：oracle包版本要与oci8包版本兼容，php官网给出的参考文字是

> Use the OCI8 extension to access Oracle Database. The extension can be
> linked with Oracle client libraries from Oracle Database 10.2, 11, or
> 12.1. These libraries are found in the database installation, or in the free Oracle Instant Client available from Oracle. Oracle's
> standard cross-version connectivity applies. For example, PHP OCI8
> linked with Instant Client 11.2 can connect to Oracle Database 9.2
> onward. See Oracle's note "Oracle Client / Server Interoperability
> Support" (ID 207303.1) for details. PHP OCI8 2.0 can be built with PHP
> 5.2 onward. Use the older PHP OCI8 1.4.10 when using PHP 4.3.9 through to PHP 5.1.x, or when only Oracle Database 9.2 client libraries are
> available.

官网的意思是在安装oci8的时候要保证 oracle扩展+oci8+php版本 要达成一致，否则就会出问题。 - - 英文不好大概就这个意思吧。

**第二步：下载php oci扩展**

安装oci8扩展 下载地址：http://pecl.php.net/package/oci8

我的php版本是5.5.4的依照官网的描述我选择的是 oci8-2.0.0.tgz 包.

```
tar zxvf oci8-2.0.0.tgz
 
cd oci8-2.0.0.tgz
 
/usr/bin/phpize
 
./configure --with-php-config=/usr/bin/php-config --with-oci8=shared,instantclient,/usr/lib/oracle/11.2/client/lib
 
make
 
make install
```

说明： 

1.oci8-2.0.0.tgz这个是单独的扩展包，也可以下载完整的php安装包，如php-5.5.28.tar.gz，解压后，cd到ext目录下的oci8目录即可。

2.phpize和php-config都不一定在上面的路径中，因为安装lamp环境的方法每个人不尽相同，可以用which命令查找，如which phpize。

3.关键点是要保证phpize，php-config，以及oracle的安装路径要正确

**第三步：配置 php.ini**

其实大部分时候是不需要第三步的，系统会默认把扩展加上

可以用find命令找到这个文件，找到类似extension = ""的配置项，加一行extension = "oci8.so" 说明：经过第二步的make，makeinstall后会生成一个oci8.so文件，可以用find命令查找一下路径，extentsion="oci8.so"要结合extention_dir="/usr/lib/php/modules" 这个配置项来看，这两句的意思就是在/usr/lib/php/modules下找oci8.so扩展，换句话说就是如果你的oci8扩展不是生成在/usr/lib/php/modules目录下，那么你就要改动extention_dir以确保oci8.so的路径是正确的

上面步骤完成后，重启代理服务。PHPINFO() 查看下。是不是已经成功了？！
