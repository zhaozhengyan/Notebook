# .Net Core DevOps -免费用Azure四步实现自动化发布（CI/CD）

## 前言

>linux 大行其道的今天想必大家都已经拥抱 core 了吧,通常的方案都是 gitlab+jenkins+centos，但是这样的方案不适合我这种懒人，一直在寻求简单的解决方案，在寻求方案的过程中发现目前国内的第三方的对c#不够友好啊，没发现好用的，所以就想到的Azure，不得不说MS还是挺给力的。

## 必备清单

- GitHub ==>[GitHub 入门教程](https://help.github.com/cn/articles/set-up-git)
- [阿里云容器镜像服务](https://cr.console.aliyun.com/cn-hangzhou/instances/repositories) 或 <https://hub.docker.com>
- Azure DevOps 注册=》<https://azure.microsoft.com/zh-cn/services/devops/>
- 一台服务器 ps : 薅的299三年的阿里云ECS

## 一、 搞定GitHub

1. 打开VS，新建 ```ASP.NET Core Web 应用程序``` ，起名为 ```WebNotebook```
2. 点击下一步，记得启用 Docker 支持，点击 创建
3. 如果上一步没有点击启用Docker支持，通过新建添加即可 
4. 把代码Push到GitHub

> 下面是我的Docker文件配置

```docker

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2-stretch-slim AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/core/sdk:2.2-stretch AS build
WORKDIR /src

RUN ls -al

COPY ["WebNotebook/WebNotebook.csproj", "WebNotebook/"]
RUN dotnet restore "WebNotebook/WebNotebook.csproj"
COPY . .
WORKDIR "/src/WebNotebook"
RUN dotnet build "WebNotebook.csproj" -c Release -o /app

FROM build AS publish
RUN dotnet publish "WebNotebook.csproj" -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "WebNotebook.dll"]
```

以下是我的项目的Demo

<https://github.com/zhaozhengyan/WebNotebook>

<!-- <img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190731170310.png" alt="20190731170310.png"> -->

## 二、配置阿里云容器镜像服务

- 登录阿里云,找到容器镜像服务=》<https://cr.console.aliyun.com/>
- 创建命名空间，起个名字，我这里叫  ```zohnz``` (PS:私有公开看个人需要)
- 创建镜像仓库，起个名字，我这里叫  ```webnotebook``` (注意:只能小写)
- 进去查看镜像仓库的基本信息，标红的复制下来，一会配置 Azure Pipeline 要用

<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801141126.png" alt="add container">

<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190731172346.png" alt="20190731172346.png">

## 三、Azure CI/CD 配置

- 首先注册注册Azure 账户，进入 <https://dev.azure.com/>
- 创建一个组织， 我这里叫 ```zohnz```
- 创建一个project,我这里叫 ```Nexter```
- 进入  ```Pipelines```,添加一个新的 Build（PS:这个应该叫持续集成）

<!-- 
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190731165912.png" alt="点击Pipeline">
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190731170123.png" alt="New build Pipeline">
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190731170704.png" alt="选择GitHub">
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190731170832.png" alt="选择WebNotebook">
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190731171114.png" alt="选择Asp.Net Core"> -->

- 编辑  ```azure-pipelines.yml```，复制如下代码，把下面参数替换成自己的

```yaml

# Starter pipeline
# Start with a minimal pipeline that you can customize to build and deploy your code.
# Add steps that build, run tests, deploy, and more:
# https://aka.ms/yaml

trigger:
- master

pool:
  vmImage: 'ubuntu-latest'


variables:
  dockerId: xxx@163.com                  # 阿里云 login username
  namespace: zohnz                              # 阿里云 命名空间
  imageName: webnotebook                        # 阿里云 仓库名称
  registry: registry.cn-hangzhou.aliyuncs.com   # 阿里云 公网地址
  dockerfilepath:  WebNotebook/Dockerfile       # Github Dockerfile相对路径


steps:
- script: |
    docker build -f $(dockerfilepath) -t $(imageName) .
    docker login -u $(dockerId) -p $(pwd) $(registry)
    docker tag $(imageName) $(registry)/$(namespace)/$(imageName)
    docker push $(registry)/$(namespace)/$(imageName)
  displayName: push to aliyun

```

这里简要解释下 yaml 的脚本参数的大致意思

指令 | 注释说明
---|---
$(xxx) | 这个是azure 的占位符，上面我用variables 声明了
-f | 指定要使用的Dockerfile路径；这个很重要，不加会报找不到文件夹的错误
-t | 镜像的名字及标签，通常 name:tag 或者 name 格式；可以在一次构建中为一个镜像设置多个标签。
$(pwd) | 这个pwd 可以直接写，但是出于安全考虑，配置到azure的variables(参数环境变量里面)了

> 读者可以通过如下方式设置

<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190731175832.png" alt="设置pwd参数">

- 接下来添加一个新的 ```Release```,主要功能是Pull镜像和Run镜像
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190731180209.png" alt="new release pipeline">
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190731180604.png" alt="add empty stage">
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801091512.png" alt="click task">
- 配置Agent的名字我这里叫```Push and Run``` ，还有一些其他配置，参考下图
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801091628.png" alt="config agent">
- 点击刚刚的```Push and Run```右边的 ```+``` 号，添加Task
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801091802.png" alt="add Task">
- 搜索```SSH```，点击```ADD``` ，这里的意思是在远程主机上执行自己的脚本
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801092059.png" alt="config task">
- 点击```Manage``` 配置自己的登录账号和密码,添加一个 SSH Service connection
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801092612.png" alt="add service connection">
- 选择刚刚添加的名字为```ssh_aliyun``` 的 SSH service connection
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801092810.png" alt="chose ssh service connection">
- 接下来填写需要配置的脚本，选择```Inline Script```，我这里写了四个task
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801093302.png" alt="preview task list">
- 以下是我的四个task的脚本

1. Remove Container

    ```shell

    #判断是否存在webnotebook容器
    docker ps | grep webnotebook &> /dev/null
    #如果不存在，则Remove
    if [ $? -ne 0 ]
    then
        echo "webnotebook container not exist continue.. "
    else
        echo "remove webnotebook container"
        docker rm webnotebook -f
    fi

    ```

2. Remove old Image

    ```shell

    #判断是否存在registry.cn-hangzhou.aliyuncs.com/zohnz/webnotebook 镜像
    docker images | grep registry.cn-hangzhou.aliyuncs.com/zohnz/webnotebook &> /dev/null
    #如果不存在，不做操作
    if [ $? -ne 0 ]
    then
        echo "image does not exist , continue..."
    else
        echo "image exists !!! remove it"
        docker rmi --force registry.cn-hangzhou.aliyuncs.com/zohnz/webnotebook
    fi

    ```

3. Pull Image

    ```shell
    #从阿里云拉取刚刚push的镜像
    docker pull registry.cn-hangzhou.aliyuncs.com/zohnz/webnotebook
    ```

4. Run Image

    ```shell
    # 运行镜像对宿主及暴露5003端口
    docker run --restart unless-stopped -p 5003:80 --name webnotebook -d registry.cn-hangzhou.aliyuncs.com/zohnz/webnotebook
    ```

- 配置完Task我们需要把刚刚的Build和Release串联起来,选择之前Build pipeline,添加完之后，记得Save
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801094656.png" alt="add artifacts">

- 这里是我配置好的 build pipeline

<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801095115.png" alt="20190801095115.png">

## 四、配置阿里云

- 登录自己的阿里云服务器，用win自带的cmd就可以，我这里是ubuntu

```shell
C:\Users\zhaozy>ssh root@xx.xx.xx.xx
root@47.98.193.96's password:
Welcome to Ubuntu 18.04.2 LTS (GNU/Linux 4.15.0-52-generic x86_64)
```

- 安装Docker

```shell
sudo apt-get update #刷新源
sudo apt-get -y install docker.io #安装docker
```

- 输入```docker -v``` 查看docker是否安装成功

```shell
root@iZs9kgd0x5xmhaZ:~# docker -v
Docker version 18.09.7, build 2d0083d
```

---

> 至此我们已经完成了所有配置阶段，下面我来提交一次代码，测试发布流程

- github 提交代码
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801140442.png" alt="github commit code">
- 进入Azure查看Build构建进度，我这构建很快，我进去的时候已经构建Build完了
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801140651.png" alt="Azure build">
- 点进去查看Job日志，发现都已经执行完了
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801140802.png" alt="build job detail">
- 去阿里云容器镜像服务查看是否Push上来
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801141315.png" alt="aliyun image detail">
- 去Azure 里面查看，Release 是否执行成功
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801141508.png" alt="preview release">
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801141957.png" alt="pipeline">
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801142028.png" alt="release detail">

- 去阿里云安全组添加```5003```端口的外网访问
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801142602.png" alt="add port">
- 打开我们服务器的地址+端口号
<img src="https://github.com/zhaozhengyan/Notebook/raw/master/img/20190801142737.png" alt="preview webnotebook">

## 总结

至此，我们的四部Linux发布之路已全部完成，大家可以根据自己的需要灵活配置
