# shell 执行多ftp命令 #
```Shell
#!/bin/bash
FILE_PATH=$1

ftp -i -v -n <<EOF
open 192.168.65.1
user admin 123456
cd /admin
get $FILE_PATH
bye
EOF
```
