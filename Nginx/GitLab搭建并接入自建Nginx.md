<img src="https://resources.blog.fastrun.cn/wp-content/uploads/2019/10/30adcbef76094b3666d98d51abcc7cd98c109dc7.jpg" alt="" width="512" height="337" class="aligncenter size-full wp-image-1526" />

# 前言 
> 我一生的文章都会放在这里，我的博客，我希望每一行代码，每一段文字都能帮助你。https://github.com/CrazyCodes/Blog  

大家好，我是CrazyCodes，这篇文章的标题有些严肃了，算是非常单纯的教学文，愿可以帮助到有需要的人。

# GitLab
GitLab与GitHub和码云一样，都是以web形式存在的在线管理Git仓库并且都拥有优雅的可视化操作页面。当然这篇文章咱们不介绍这没用的。

因为GitLab/Hub或者码云对免费用户不是够友好，无论是在库的容量或者协作人员都有些许限制，GitHub的提交速度对中国程序员更是慢如蜗牛。

网络上关于GitLab安装的文章并不少，因为GitLab在安装过程中会默认再安装一个Nginx，这必然会与已有Nginx冲突，写这篇文章主要的目的还是对已存在Nginx服务的服务器如何配置GitLab做一个指导。

# 安装
服务器版本:(阿里云) CentOS Linux release 7.6.1810
GitLab版本：12.4.0-ee
Nginx版本：nginx/1.17.0

基本都算是比较新的版本了,废话不多说，我们开始吧！

## 第一步：进入GitLab官网 
[https://about.gitlab.com/install/](https://about.gitlab.com/install/)

## 第二步: 执行一大堆牛逼的命令
```
sudo yum install -y curl policycoreutils-python openssh-server
sudo systemctl enable sshd
sudo systemctl start sshd
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo systemctl reload firewalld
```
## 第三步：下载GitLab安装文件
```
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash
```

## 第四步：安装
```
sudo EXTERNAL_URL="https://gitlab.example.com" yum install -y gitlab-ee
```
EXTERNAL\_URL 这个配置就默认好了，毕竟咱们也不用GitLab自带的Nginx

## 第五步：完成
就是这么简单，安装完成了。第一次访问的时候会要求重新设置密码。用户名默认为 root

# 配置
安装完成后，需要对现有的GitLab做一些配置更改。

官方文档：[https://docs.gitlab.com/12.4/omnibus/settings/nginx.html#using-a-non-bundled-web-server](https://docs.gitlab.com/12.4/omnibus/settings/nginx.html#using-a-non-bundled-web-server)

## 禁用NGINX
在文件
```
/etc/gitlab/gitlab.rb
```
下，将配置设置为false
```
nginx['enable'] = false
```

## 设置所属用户
需要将自建Nginx的所属用户加入到配置内
在文件
```
/etc/gitlab/gitlab.rb
```
下，将配置设置为 www-data
> 对于Debian/Ubuntu，默认用户是`www-data`
> 对于RHEL/CentOS，NGINX用户是`nginx`。

当然如果安装nginx未指定所属用户，也有可能会是root。当然可以通过命令
```
cat /usr/local/nginx/conf/nginx.conf | grep user
```
查看所属用户，实际就在配置文件里写着
```
user  root;
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
```
回到正题，修改配置为root（刚刚讲过了,为啥是root）
```
web_server['external_users'] = ['root']
```
这个设置是一个数组，可以添加多个用户。设置好后运行命令
```
sudo gitlab-ctl reconfigure
```
配置已生效

## 添加虚拟主机
GitLab已经为开发者准备好相应的vhost文件。可以通过访问下方链接获取
[https://gitlab.com/gitlab-org/gitlab-recipes/tree/master/web-server](https://gitlab.com/gitlab-org/gitlab-recipes/tree/master/web-server)
配置完成后执行
```
nginx -s reload
```
既完成，原理很简单，其他配置我也不是太明白，大概则是使用反向代理转发了一下请求这样子。

# 问题
在最新版本中，如果按照上述方式配置，可能会在提交过程中出现422错误，具体原因不清楚，我遇到这个问题后配置了ssl，解决了这个问题。

# 致谢
大多都是官方的文字，可能只是不太好找，所以网络上经常出现这个问题，但都无解，所以写这么个水文，帮助需要的人，当然，提倡还是去看官网的文档。

感谢你看到这里，希望本篇文章可以帮到你。
