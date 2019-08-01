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

- 设置docker service 开机自启动

```shell
systemctl enable docker.service
```

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
