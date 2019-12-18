# 在群晖上搭建 Nexus 私有仓库

最近在做的项目不能把包上传到公有仓库，而私有仓库不仅收费，而且类型单一。想到自己的群晖自带了 Docker，自建一个私有仓库似乎是个极好的选择。

1. 公有仓库代理
特别是 NPM 库这种零碎文件特别多的，直接走 npmjs.org 在中国真的特别慢。通过私有仓库作为代理，后续的 `npm install` 就可以享受内网千兆网络的畅快了。
2. 群晖自身和软件仓库本质上都是文件储存系统。群晖的双盘提供了一定的高可用。私有仓库，只要数据不会丢，重新搭一个再恢复数据浪费不了多少时间。
3. 因项目安全问题，包无法上传至公有仓库。而国内好像也没有 Nexus 类似的全功能仓库服务？

而仓库选型，我毫不犹豫地在选择了用得最熟悉的 Nexus( [Nexus Repository OSS](https://www.sonatype.com/nexus-repository-oss) )。 Nexus 支持 npm, Maven, Docker 在内的几乎所有开发者需要用的仓库类型。支持仓库代理和私有仓库。基本上能满足所有需要。

- - - -
1. 下载 Nexus 3 Docker 映像
[Docker Hub](https://hub.docker.com/r/sonatype/nexus3/)
新增 -> 从 URL 添加 -> 输入 `https://hub.docker.com/r/sonatype/nexus3/`  -> 点击新增。
然后静待群晖下载映像直至完毕。
![](/static/4B849AF3-8C77-4B3B-91CD-40EEE98B0AB9.png)

2. 启动 Nexus 3 Docker 容器
下载完成后，选中 Nexus 3 映像 然后点击启用。
![](/static/56D9A7F7-8CB0-48A8-AB83-40DF00F3A559.png)
![](/static/4F134472-67CB-4D89-B9B0-473671066DCE.png)

几乎没有什么需要额外设置，除了端口号。
点击高级设置 -> 切换到端口设置页面 -> 把本地端口从“自动”改为8081
（你可以使用包括8081在内的所有端口，此设置只是为了以后使用方便记忆。）

![](/static/4E87B949-5AEF-4163-BDCF-2953110FB8E3.png)
然后点击应用 -> 下一步 -> 应用。

此时，你的 Nexus 3 容器已经设置完毕并且启动。 在左侧菜单中切换到容器页面，你应该可以看到你的容器状态。

![](/static/D1E52747-BD81-4478-8D10-45FDE34AE9F6.png)

稍微等待一段时间，你就可以通过 `http://<你的群晖内网ip>:8081` 访问 Nexus 主页（Nexus 从启动到可以使用需要一段时间。我…等了15分钟）。

![](/static/896211BC-A551-4D76-B03B-E5FAB6722FE6.png)

点击右上角的登录，你可以用初始账户  `admin` 登录。
如对话框所示，初始密码在 `/nexus-data/admin.password`。

![](/static/545608B5-FF75-4247-9CCD-C8473E54D129.png)


回到群晖的 Docker 页面，右击你的容器，选择详情并且切换到终端机页面。
然后点击新增。你会看到一个新的 bash 终端机。
在小黑框里输入 `echo "Your password is $(cat /nexus-data/admin.password)"` 并回车，你就可以看到你的初始密码了。
![](/static/C5512F5F-100C-46D4-ADB1-5D5A8BD8B173.png)

![](/static/332FEF25-F013-42F6-BB60-5D0D218CEC25.png)

然后， Here you go~

![](/static/CF55FDAB-8079-417D-A945-FC3A82B3B1A1.png)
