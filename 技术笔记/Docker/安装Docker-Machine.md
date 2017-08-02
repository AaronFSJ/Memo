# Docker-Machine工具安装 #

```Shell
$curl -L https://github.com/docker/machine/releases/download/v0.8.2/docker-machine-`uname -s`-`uname -m` > /usr/local/bin/docker-machine
$chmod +x /usr/loacal/bin/docker-machine
$docker-machine version
```
安装一些使用的脚本，这些脚本包含以下功能:
>1. A function that displays the active machine in your shell prompt
>2. A function wrapper that adds a docker-machine use subcommand to switch the active machine.

To install [the scripts](https://github.com/docker/machine/tree/master/contrib/completion/bash), copy or link them into your /etc/bash_completion.d or /usr/local/etc/bash_completion.d directory. To enable the docker-machine shell prompt, add $(__docker_machine_ps1) to your PS1 setting in ~/.bashrc.

```Shell
PS1='[\u@\h \W$(__docker_machine_ps1)]\$ '
```
