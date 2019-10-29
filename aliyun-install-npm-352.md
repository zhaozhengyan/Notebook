## Aliyun 安装NPM  总是3.5.2 解决方案
> 由于默认的命令 阿里云安装的 Node 是 8.x 版本
导致NPM 一直安装的都是 3.5.2 版本，死活升级不上去

> 最后手动安装指定版本解决
```
wget -qO- https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs
```
> 安装 npm
```
apt-get install nodejs
```
查看 ``` npm -v ```

```
root@iZs9kgd0x5xmhaZ:/# npm -v  
6.9.0             
 ```             
