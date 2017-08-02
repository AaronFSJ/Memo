# 多版本Java环境配置 #
在设置Java开发环境时，有时需要用到不同的`JDK`环境，因此需要在系统安装不同版本的Java版本并进行配置。
## JDK版本的安装 ##
下载所需不同的`JDK`版本，本文主要包括`Oracle`的`JDK1.7`、`JDK1.8`和`OpenJDK8`三本版本。下载完成后，放入置顶的路径中。本人根据习惯在根目录`opt`文件夹下创建一个`runtime`文件加用于存放各种开发运行是所需的工具。
```Shell
/opt/runtime/jdk1.7.0_80
/opt/runtime/jdk1.8.0_141
/opt/runtime/java-1.8.0-openjdk-amd64
```
## JDK配置 ##
使用`update-alternatives`工具进行配置
1. 查看系统已有的`java`、`javac`、`jar`的配置信息
```Shell
xuemzhan@iceroad:~$ update-alternatives --list java[javac|jar]
```
其结果为如下，具体详情见`man update-alternatives`
```Shell
There are 1 choices for the alternative java (providing /usr/bin/java).
  Selection    Path    Priority   Status
------------------------------------------------------------------
0  /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java 1081 auto mode
```
2. 分别安装需要配置的`JDK1.7`和`JDK1.8`的`java`,`javac`和`jar`
```Shell
xuemzhan@iceroad:~$ update-alternatives --install /usr/bin/java java /opt/runtime/jdk1.7.0_80/bin/java 700
```
3. 这样就可以使用`update-alternatives`工具更改不同版本的JDK。
```Shell
xuemzhan@iceroad:~$ update-alternatives --config java[javac|jar]
```
为了方便应用，有时我们需要在`.profile`或者`.bashrc`文件中对`JAVA_HOME`进行设置，但不同版的JDK进行切换后，需要更新`JAVA_HOME`，为了在切换JDK版本是同事切换`JAVA_HOME`，我们需要先：
```Shell
xuemzhan@iceroad:~$ touch /etc/alternative/jhome
xuemzhan@iceroad:~$ ln -s /etc/alternative/jhome /usr/share/jhome
```
然后安装不同版本JDK路径
```Shell
xuemzhan@iceroad:~$ update-alternatives --install /usr/share/jhome jhome /opt/runtime/jdk1.7.0_80 700
```
最后，在`.profile`文件中设置`export JAVA_HOME=/usr/shre/jhome`.
## 使用Shell脚本统一切换 ##
使用`Shell`脚本配置`expect`工具对`java`、`javac`、`jar`和`jhome`进行统一切换。
```Shell
xuemzhan@iceroad:~$ mkdir /opt/jsel
xuemzhan@iceroad:~$ touch /opt/jsel/jsel.sh
xuemzhan@iceroad:~$ touch /opt/jsel/jsel.exp
xuemzhan@iceroad:~$ chmod a+x /opt/jsel/*
xuemzhan@iceroad:~$ ln -s /opt/jsel/jsel.sh /usr/share/jsel
xuemzhan@iceroad:~$ chmod a+x /usr/share/jsel
```
文件`jsel.sh`和`jsel.exp`的内容日下：
```expect
#!/usr/bin/expect

set passwd "123456"
set command [lindex $argv 0]
set version [lindex $argv 1]

spawn sudo update-alternatives --config $command

expect {
	"*password" {
		send "$passwd\r"
		exp_continue
	}

	"*selection number*" {
		if { $version == "java7" } {
			send "1\r"
		}
		if { $version == "java8" } {
			send "2\r"
		}
		if { $version == "openjdk" } {
			send "3\r"
		}
		send "1\r"
		exp_continue
	}
}

```
```Shell
#!/bin/bash
ver=$1

for cmd in java javac jar jhome
do
	expect /home/xuemzhan/Scripts/jsel/jsel.exp $cmd $ver
done
```
