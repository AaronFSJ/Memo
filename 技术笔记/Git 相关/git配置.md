# Git配置 #

设置用户名和邮箱
```shell
git config --global user.name = xuemin.zhan
git config --global user.email = zxm0813@gmail.com
```

查看git配置

```
#git config --list

core.symlinks=false
core.autocrlf=true
core.fscache=true
color.diff=auto
color.status=auto
color.branch=auto
color.interactive=true
help.format=html
rebase.autosquash=true
http.sslcainfo=C:/Program Files/Git/mingw64/ssl/certs/ca-bundle.crt
diff.astextplain.textconv=astextplain
filter.lfs.clean=git-lfs clean -- %f
filter.lfs.smudge=git-lfs smudge -- %f
filter.lfs.required=true
filter.lfs.process=git-lfs filter-process
credential.helper=manager
user.name=xuemin.zhang
user.email=zxm0813@gmail.com

```
