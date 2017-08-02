# Ubuntu Git 服务器搭建
---
tags: git linux ubutnu

[TOC]
## 1.安装指定软件包
```Shell
root@moonright:~# apt-get -y install openssh-server openssh-client
root@moonright:~# apt-get -y install git
```
## 2.添加用户
```Shell
root@moonright:~# adduser --system --shell /bin/bash --gecos 'Git SCM User' --group --disabled-password --home /home/git git
```
