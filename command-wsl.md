 > wsl常用命令
 
 指令 | 注释说明 
---|---
`su root` | root 账户登录
`netstat -aon findstr "80"` | linux查找使用指定端口


> windows 常用命令

 指令 | 注释说明 
---|---
`wsl --shutdown` | 关闭WSL  
`netsh http show servicestate`| 查看Http服务端口占用
`netstat -ano|findstr "80"` | cmd 查看置顶端口
`netsh http show servicestate` | 查看http端口使用状态
> nginx 常用命令

指令 | 注释说明 
---|---
`nginx -s start ` | 启动
`nginx -s reload ` | 停止
`nginx -s restart ` | 重新启动
`nginx -s reload ` | 重新加载配置
`service nginx start` | 启用nginx
`service nginx stop` | 停止
`service nginx restart` | 重新启动
`service nginx reload` | 重新加载