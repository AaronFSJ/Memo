# SSH容器搭建 #
SSH容器搭建的Dockerfile如下：
```Shell
#!/usr/bin/docker
#
# Creator : Xuemin.Zhang <zxm0813@gmail.com>
# Purpose : For creating ssh server container
# Version : 0.1
# Date    : 2016/12/29
#

#使用的基础镜像
From ubuntu

#作者信息
MAINTAINER Xuemin.Zhang <zxm0813@gmail.com>

#安装SSH
RUN apt-get update
RUN apt-get install -y openssh-server
RUN mkdir -p /var/run/sshd


#配置SSH
RUN sed -ri 's/account    required     pam_nologin.so/#account    required     pam_nologin.so/g' /etc/pam.d/sshd
RUN sed -ri 's/PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
RUN echo root:l1admin | chpasswd

#添加启动文件
RUN echo "#!/bin/bash" > /root/run.sh
RUN echo "/usr/sbin/sshd -D" >> /root/run.sh
RUN chmod u+x /root/run.sh

#导出端口
EXPOSE 22

#设置默认命令
CMD ["/root/run.sh"]

```
编译并运行容器
```Shell
$docker build -t="ssh:v1" .
$docker run -d -p 2022:22 --name sshd ssd:v1
```
测似如下：
```Shell
root@moonright:~# ssh [host ip] -p2022
root@[host ip]'s password:
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-57-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

root@175a63653d55:~#
```
