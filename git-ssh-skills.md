# 五分钟 配置 Git 使用 SSH 访问

>  随便找个文件夹 右键打开Bash
>
> ```shell
>  ssh-keygen -t ed25519 -C "zhaozhengyan@live.com"
> ```
>
> 提示输入文件名, 输入 `id`
>
> ```shel
> Enter file in which to save the key (/c/Users/zhaozhengyan/.ssh/id_ed25519): id
> ```
>
> 提示输入密码：
>
> ```shell
> Enter passphrase (empty for no passphrase):
> ```
>
> 再次输入密码：
>
> ```shell
> Enter same passphrase again:
> ```
>
> 提示创建成功
>
> ```shell
> Your identification has been saved in id
> Your public key has been saved in id.pub
> ```
>
> 输入 clip 命令复制 密钥的key, 或者直接打开pub文件复制里面的内容
>
> ```shell
> clip < id.pub
> ```
>
> 将key复制到Github SSH keys里面，key的格式大致如下
>
> ```shell
> ssh-ed25519 AAAAC3NzaC1lZDI1NTE5adeDbs/VOcg41U2ySIH3DODrU5llBcLlfnFqb0ZrKasvzr7y zhaozhengyan@live.com
> ```
>
> 添加成功后，打开刚刚的 bash 命令窗口
>
> ```shell
> eval "$(ssh-agent -s)"
> ```
>
> 测试是否能连接到Github
>
> ```shell
>  ssh -T git@github.com
> ```
>
> 提示 hi, 大功告成！
>
> ```shell
> Hi zhaozhengyan! You've successfully authenticated, but GitHub does not provide shell access.
> ```

