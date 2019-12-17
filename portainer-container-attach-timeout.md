# Portainer Exec Container 失败解决方案

> 近日，将portainer服务挂了个域名，然后用Nginx代理的时候发现不能attach容器了，经过搜索在issue
找到解决方案

1.修改Nginx config

```shell
server {
    listen 80;
    server_name portainer.xxx.com;
    location / {
        proxy_pass http://47.xxx.xxx.96:9000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
    }
    access_log logs/portainer.xxx.com_access.log;
}
```

2.重启Nginx

```shell
nginx -s reload
```

3.参考

[https://github.com/portainer/portainer/issues/1887#issuecomment-460056691](https://github.com/portainer/portainer/issues/1887#issuecomment-460056691)
