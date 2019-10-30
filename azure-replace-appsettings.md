## Azure DevOps 替换 appsettings 解决方案

> 之前发布了 [《.Net Core DevOps -免费用Azure四步实现自动化发布（CI/CD）》](https://www.cnblogs.com/zhaozhengyan/p/azure-devops-aliyun.html/)之后，有很多朋友私信我说如何替换 appsettings 里面的 ```ConnectionStrings```的问题。我的解决方案是通过Shell在编译前替换需要替换的字符串。以下是具体步骤：

1. 示例项目配置 ```appsettings.Production.json```

> 并且添加占位符例如```{Writable}```,用于Shell脚本替换用

![](https://github.com/zhaozhengyan/Notebook/raw/master/img/20191030150630.png)

2. 打开解决方案的 ```azure-pipelines.yml``` 添加一个新的Task

![](https://github.com/zhaozhengyan/Notebook/raw/master/img/20191030151305.png)


> ```ls```  列出目录（主要是看azure 的 devops 的目录结构，可以不写）

```
sed -i 's/{Writable}/$(Writable)/g' WebNotebook/appsettings.Production.json 
```
> 上面的脚本的大致意思是：把文件内所有的 ```{Writable}``` 替换成Azure的 ```$(Writable)```

3. 将yml的```$(Writable)``` 配置到Azure的 ```Variables``` 里面

![](https://github.com/zhaozhengyan/Notebook/raw/master/img/webnotebook.gif)

4. 提交代码，查看是否替换成功

- 检查Job执行情

![](https://github.com/zhaozhengyan/Notebook/raw/master/img/20191030154117.png)

- 进入docker查看appsettings,替换成功

```
root@iZs9kgd0x5xmhaZ:~# docker exec -it 715afabf4ac7  bash
root@715afabf4ac7:/app# ls
WebNotebook.Views.dll  WebNotebook.dll                     WebNotebook.runtimeconfig.json  appsettings.json
WebNotebook.Views.pdb  WebNotebook.pdb                     appsettings.Development.json    web.config
WebNotebook.deps.json  WebNotebook.runtimeconfig.dev.json  appsettings.Production.json     wwwroot
root@715afabf4ac7:/app# cat appsettings.Production.json
{
  "ConnectionStrings": {
    "Writable": "Server=127.0.0.1;Database=Demo;Integrated Security=true;",
    "ReadOnly": "Server=127.0.0.1;Database=Demo;Integrated Security=true;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  }
}
root@715afabf4ac7:/app#
```

