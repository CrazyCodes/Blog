![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/07/2055627634-5b335ff4100b6_articlex.png)

# 前言

抛开Docker那些强大的功能，今天我们来部署下本地的开发环境。并写上几个脚本来提高开发效率。

本章以MacOs系统的Docker演示，其他系统作者为接触过。不知是否有差别。

# 安装
> 傻瓜式安装，这里就不再阐述了。下载地址如下

https://www.docker.com/products/docker-desktop

# 目录
创建一些目录，就如在项目开发中创建Controller,Model,Service一样。我们将本地的Docker开发环境先从目录开始整理以下。
| 目录名 | 用途 |
| -- | -- |
| app | 项目目录,源程序存放的地方 |
| services | 服务目录,例如mysql,php等|
| ssh | 远程服务器目录,用于链接服务器 |
| web | 前端目录，正常node会指向它|

部分文件列表
| 文件名 | 用途 |
| -- | -- |
| .env | 配置公共文件 |
| docker-compose.yml | docker-composer 配置文件 |
| hosts | 系统 hosts 文件 | 
| php.sh | php 相关操作文件 |
| start.sh | 本地环境操作脚本 |

因是个人使用，所以对命名和规范稍有出入。请谅解

# 配置服务
配置你所需要的服务到Docker容器内
## MySQL
docker-composer.yml
```
db:
    container_name: 'dev_db'
    build: ./services/mysql // 指向dockerfile文件
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD} // .env文件的配置项
    ports:
      - "3306:3306"
    volumes:
      - ${MYSQL_DATA_PATH}:/var/lib/mysql
```
创建```mysql-dockerfile```到```services\mysql```
Dockerfile
```
FROM mysql:5.6
MAINTAINER crazycodes <919342864@qq.com>

ADD ./config/default.cnf /etc/mysql/conf.d/default.cnf
```
## Nginx
docker-composer.yml
```
web:
    container_name: 'dev_nginx'
    build: ./services/nginx
    ports:
      - "80:80"
    depends_on:
      - php
    volumes_from:
      - php
    volumes:
      - ${NGINX_VOLUME_CONFIG_PATH}:/etc/nginx/conf.d
    dns: 8.8.8.8
```
Dockerfile
```
FROM nginx:latest
MAINTAINER crazycodes <919342864@qq.com>

RUN apt-get update && apt-get install -y vim sudo gcc make unzip wget mercurial libpcre3-dev zlib1g-dev libssl-dev devscripts debhelper dpkg-dev quilt lsb-release

COPY nginx.conf /etc/nginx/nginx.conf
COPY nginx-rtmp-module /etc/nginx/nginx-rtmp-module
COPY nginx-1.13.9 /etc/nginx/nginx-1.13.9

WORKDIR /etc/nginx/nginx-1.13.9
RUN ./configure --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt='-g -O2 -fdebug-prefix-map=/data/builder/debuild/nginx-1.13.7/debian/debuild-base/nginx-1.13.7=. -specs=/usr/share/dpkg/no-pie-compile.specs -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' --with-ld-opt='-specs=/usr/share/dpkg/no-pie-link.specs -Wl,-z,relro -Wl,-z,now -Wl,--as-needed -pie' --add-module=../nginx-rtmp-module
RUN make && make install
```
站点配置在```services\nginx\config```内，你可以正常配置
```
server {
  listen       80;
  server_name  localhost;
  root          /mnt/app/flarum;
  index         index.php index.html index.htm;

  location / { try_files $uri $uri/ /index.php?$query_string; }
  location /api { try_files $uri $uri/ /api.php?$query_string; }
  location /admin { try_files $uri $uri/ /admin.php?$query_string; }

  location /flarum {
    deny all;
    return 404;
  }
  location ~ .php$  {
     fastcgi_split_path_info ^(.+.php)(/.+)$;
     fastcgi_pass   php:9000; // 容器内，此处要填 容器名称:容器映射端口
     fastcgi_index  index.php;
     include        fastcgi_params;
  }
}
```
## PHP
dockerfile文件内容过多。这里就不贴代码了。文章最后会贴出源码地址。
# 其他
活学活用，代码不仅仅是帮助你开发，也可以帮你提示开发效率。将所有工作变得自动化，更能体现你的本领。
## ssh
ssh中放着所有服务器链接文件
```
set -x
ssh root@xxx.xxx.xxx.xxx
```
每次当你需要链接服务器时，直接使用
```
sh dev.sh
```
即可
## php.sh
如果想要操作容器内php的命令。命令过长，不方便。我们可以将代码放入sh文件中
```
set -x

docker exec -it dev_php /bin/sh -c "cd /mnt/app/${1} && ${2}"
```
这时你如果需要操作容器内的PHP，就可以这样写
```
sh php.sh My_Blog artisan migrate
```
## start.sh
start.sh文件中存放着一些基本命令，当然你也可以继续扩展你想要的。例如对composer的操作，php的操作，mysql的操作等等。具体你可以去点击下方链接查看。
# 致谢
习惯将许多命令封装到执行文件内。方便自己使用。提升开发效率和质量是每位程序员必备的技能。

https://github.com/CrazyCodes/Http-Developers
这并不是一个非常认真的docker“操作”，请勿使用到生产环境。
很高兴你看到这里，希望本篇文章可以帮到你。谢谢。