# DJGM自动化构建与部署 #
通过命令`docker pull <image>`下载以下Docker镜像：
> jenkins:latest
> java:8
> maven:3.3.9-jdk-8
> tomcat:8.0

启动`java`镜像，将本地目录映射到`/usr/local/bin/docker-java-home`目录中。



启动镜像
```Shell
$docker run -di --name gitlab-postgresql --env 'DB_NAME=my_gitlab_db' --env 'DB_USER=xuemzhan' --env 'DB_PASS=XSW21qaz' --env 'DB_EXTENSION=pg_trgm' -v /data/srv/postgresql/gitlab:/var/lib/postgresql gitlab-postgresql

$docker run -di --name gitlab-redis -v /data/srv/redis/gitlab:/var/lib/redis gitlab-redis

$docker run -di --name gitlab --link gitlab-postgresql:postgresql --link gitlab-redis:redisio --env 'GITLAB_PORT=10080' --env 'GITLAB_SSH_PORT=10022' --env 'GITLAB_SECRETS_DB_KEY_BASE=long-and-random-alpha-numeric-string' --env 'GITLAB_SECRETS_SECRET_KEY_BASE=long-and-random-alpha-numeric-string' --env 'GITLAB_SECRETS_OTP_KEY_BASE=long-and-random-alpha-numeric-string' --env 'DB_NAME=my_gitlab_db' --env 'DB_USER=xuemzhan' --env 'DB_PASS=XSW21qaz' -p 10022:22 -p 10080:80 -p 443:443 -v /data/srv/gitlab:/home/git/data gitlab
```

安装Jenkins镜像
```Shell
#修改挂载目录权限
$chown 1000 /data/srv/jenkins
$docker run -di -p 50000:50000 -p 58080:8080 -v /data/srv/jenkins:/var/jenkins_home -v /data/srv/maven/apache-maven-3.3.9 -v /data/srv/jdk8/jdk1.8.0_112 -v /var/run/docker.sock:/var/run/docker.sock --name jenkins jenkins
```
