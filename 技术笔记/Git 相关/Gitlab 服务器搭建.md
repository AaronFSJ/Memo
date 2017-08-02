# Ubuntu 16.04 Gitlab服务器搭建 #
tags: gitlab ssl
## 安装依赖包##
> root@moonright:~# apt-get install curl openssh-server openssh-client ca-certifiates postfix

邮件配置时，选择`Internet`

## 添加Gitlab镜像 ##
打开源列表
>root@moonright:~# vim /etc/apt/sources.list.d/gitlab-ce.list

添加国内镜像（清华大学）

>deb https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/ubuntu xenial main

添加`Gitlab`的公钥

>root@moonright:~# curl https://packages.gitlab.com/gpg.key 2> /dev/null | sudo apt-key add - &>/dev/null

## 安装Gitlab ##
>root@moonright:~# apt-get update

>root@moonright:~# apt-get install gitlab-ce

## 配置Gitlab ##
新建 `ssl`文件夹，并将`[domain_name].crt`和`[domain_name].key.unsecure`拷贝到文件夹中。openssl的key和密钥申请如[《StartSSL申请》](local://StartSSL申请)。
>root@moonright:~# mkdir -p /etc/gitlab/ssl

>root@moonright:~# cp domain_name.* /etc/gitlab/ssl

打开`gitlab.rb`文件
>root@moonright:~# vim /etc/gitlab/gitlab.rb

修改并添加如下内容：
> external_url 'https://[hostname]:[port]' </br> nginx['redirect_http_to_https'] = true </br>
nginx['ssl_certificate'] = "/etc/gitlab/ssl/tonytoy_cn.crt" </br>
nginx['ssl_certificate_key'] = "/etc/gitlab/ssl/tonytoy_cn.key.unsecure"

其中`hostname`和`port`分别指服务器名称和端口号，端口号可缺损，默认为80。 保存并重新配置：

>root@moonright: gitlab-ctl reconfigure

## 设置防火墙 ##
> root@moonright: iptables -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
