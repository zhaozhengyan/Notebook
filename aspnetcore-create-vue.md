> 众所周知，现在国内最火的前端框架非Vue莫属了，作为一个.net程序员，早就想体验一下了，但是无奈微软的项目模板不提供Vue的Spa模板，但是我们还是想用怎么办呢？下面来看下我的解决方案

## 目录
1. 用vue-cli 创建一个项目
2. 用VS创建一个React的应用程序
3. 改造React项目适配Vue
4. 配置Dockerfile

### 一.用vue-cli 创建一个项目

- 我这里贴的是直接创建好的项目，至于怎么创建，点击=》[传送门](https://cli.vuejs.org/zh/guide/creating-a-project.html)

<img src="https://r.zhaoblogs.com/20191106160212.png"/>

### 二.用VS创建一个React的应用程序

<img src="https://r.zhaoblogs.com/20191106142016.png"/>

### 三.改造React项目适配Vue

- 修改 `Startup.cs`,添加`VueCliMiddleware`NuGet包

<img src="https://r.zhaoblogs.com/20191106161939.png"/>

<img src="https://r.zhaoblogs.com/20191106163156.png"/>

- 编辑 项目文件 `DistFiles`
<img src="https://r.zhaoblogs.com/20191106163626.png"/>

- 到这里本地环境就配置完了可以，大家可以直接按`F5`查看下效果（ps:第一次运行可能比较慢哦）

### 四.配置 `Dockerfile` 支持nodejs

```
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0-buster-slim AS base
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
COPY ./ /src
RUN ls -la 
WORKDIR "/src/WebNotebook.Vue"
RUN apt-get update -yq && apt-get upgrade -yq && apt-get install -yq curl git nano
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash - && apt-get install -yq nodejs build-essential
RUN ls -la 

FROM build AS publish
RUN dotnet publish "WebNotebook.Vue.csproj" -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "WebNotebook.Vue.dll"]

```

配置完dockerfile，就可以自动化发布了，这里推荐用Azure做DevOps，至于为什么你懂得

至于Azure的DevOps 怎么配置，大家可以看我的另一篇文章[《.Net Core DevOps -免费用Azure四步实现自动化发布（CI/CD）》](https://www.cnblogs.com/zhaozhengyan/p/azure-devops-aliyun.html/)
