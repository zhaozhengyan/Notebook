# 首先判断系统是 Debian 还是 Alpine 

`cat /etc/os-releas`

## Debian
> 添加源
``` shell
tee /etc/apt/sources.list << EOF
deb http://mirrors.163.com/debian/ jessie main non-ffree contrib
deb http://mirrirs.163.com/debian/ jessie-updates main non-free contrib
EOF
```

> 安装 curl telnet
```shell
apt-get update && apt-get install -y curl telnet vim
```

## Alpine
> 添加源
```shell
cat > /etc/apk/repositories << EOF
http://mirrors.aliyun.com/alpine/v3.12/main/
http://mirrors.aliyun.com/alpine/v3.12/community
EOF
```
> 安装 curl scp telnet vim
```shell
apk update && apk add curl openssh-client busybox-extras vim
```
