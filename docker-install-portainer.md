# Docker安装portainer

- 拉取docker 镜像

```shell
 docker pull docker.io/portainer/portainer
```

- 创建docker volume

```shell
docker volume create portainer_data
```

- 运行docker

```shell
 docker run -d -p 9000:9000 --name portainer --restart always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```

- docker服务

说明|指令
---|---
设置服务自启动 |systemctl enable docker.service
启动     |   systemctl start docker
守护进程重启 |  sudo systemctl daemon-reload
重启docker |  systemctl restart  docker
开启docker服务 | sudo service docker restart
重启docker服务 | sudo service docker restart
关闭docker |  service docker stop
关闭docker | systemctl stop docker
---

- docker 容器

说明|指令
---|---
查看当前运行的容器 |docker ps
查看所有容器，包括停止的    |   docker ps -a
启动容器|docker start 43e3fef2266c
---

- 安装MSSQL

```shell
docker pull microsoft/mssql-server-linux
```

```shell
docker run --name MSSQL_1433 -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=password' -p 60005:1433 -d microsoft/mssql-server-linux
```

```shell
sudo apt-get install nginx
```

- 安装htop

```shell
sudo apt-get update
sudo apt-get install htop
```
