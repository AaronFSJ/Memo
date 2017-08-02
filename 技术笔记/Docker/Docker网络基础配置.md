# 网络基础配置 #
>`-P` 参数表示随机映射一个宿主`49000~49900`的端口至容器内部开放的网络端口。

>`-p ip:hostPort:containerPort` 可以指定需要映射的端口

>`--link name:alias` 容器之间通信
```Shell
$docker run -d --name db training/postgres
$docker run -d -P --name web --link db:db training/webapp python app.py
```
