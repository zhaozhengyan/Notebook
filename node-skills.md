# Node 配置

## `NVM` 管理nodejs 不同版本

> 下载 NVM

 https://github.com/coreybutler/nvm-windows/releases

> 配置 源
```
root: C:\Node\nvm
path: C:\Node\nodejs

node_mirror: https://npm.taobao.org/mirrors/node/
npm_mirror: https://npm.taobao.org/mirrors/npm/
```

> 常用命令
```
# 下载
$ nvm install ***
# 切换版本
$ nvm use 版本号
# 已经安装的版本
$ nvm ls
# 罗列远程版本
$ nvm ls-remote
# 设置默认的版本
$ nvm alias default 版本
```

## 快速删除 `node_modules` 的方法

> 全局安装rimraf：

```
npm install rimraf -g
```

> 切换到你的项目根目录下（即有node_modules的目录），执行命令：

```
rimraf node_modules
```
