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
