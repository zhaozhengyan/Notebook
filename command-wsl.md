  > ubuntu常用命令

指令 | 注释说明 
---|---
`pip3 install opencv-python -i https://pypi.douban.com/simple/` | python install 依赖
`pip3 install -r requirements.txt -i https://pypi.douban.com/simple/`| 一键安装所有依赖
`killall python3`| kill python 进程
`export FLASK_APP=app`|  load flask
`python3 -m flask run` | run flask
`hash -d pip`| fix pip install error
`pip3 install flask uwsgi -i https://pypi.douban.com/simple/` | install uwsgi
`uwsgi --socket 0.0.0.0:5000 --protocol=http -p 3 -w app:app` | lanuch flask in uwsgi

 
 > wsl常用命令
 
 指令 | 注释说明 
---|---
`su root` | root 账户登录
`netstat -aon findstr "80" ` | linux查找使用指定端口


> windows 常用命令

 指令 | 注释说明 
---|---
`wsl --shutdown` | 关闭WSL  
`netsh http show servicestate `| 查看Http服务端口占用
`netstat -ano|findstr "80" ` | cmd 查看置顶端口
`netsh http show servicestate` | 查看http端口使用状态
> nginx 常用命令

指令 | 注释说明 
---|---
`nginx -s start ` | 启动
`nginx -t ` | 测试配置文件是否有语法错误
`nginx -s reopen ` | 重启Nginx
`nginx -s reload ` | 重新加载配置
`nginx -s stop `   |强制停止Nginx服务
`nginx -s quit ` |处理完所有请求后再停止服务
`service nginx start` | 启用nginx
`service nginx stop` | 停止
`service nginx restart` | 重新启动
`service nginx reload` | 重新加载
