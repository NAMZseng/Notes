# Ubuntu 18.04 安装wine及windows软件

## 安装wine

```shell
# 开启32位架构支持
sudo dpkg --add-architecture i386
# 添加wine软件源
# 注：最好使用ubuntu默认镜像源，使用阿里云的源会出现依赖问题
wget -nc https://dl.winehq.org/wine-builds/Release.key
sudo apt-key add Release.key
sudo apt-add-repository https://dl.winehq.org/wine-builds/ubuntu/
sudo add-apt-repository ppa:cybermax-dexter/sdl2-backport
sudo apt update
# 安装wine，默认安装路径： /opt/winehq-stable
sudo apt install --install-recommends winehq-stable
# 安装winetricks
sudo apt install --install-recommends winetricks

# 安装wine依赖，初始执行winecfg或wine或winetricks，软件会要求下载安装 wine-mono 和 wine-gecko
```

## Wine管理Windows软件

- 安装
  - 官网下载对应软件安装包，exe格式
  - 右键exe安装包，选择用wine打开

- 卸载

  终端执行 **wine uninstaller**，再弹出的界面中选择相应软件卸载即可
  
  或者到软件安装目录下找到uninstall.exe，右键用wine运行

## Windows软件使用问题

### 中文显示异常

可将windows相关目录下的中文字体文件，如微软雅黑，仿宋，楷体等文件拷贝到**/opt/wine-stable/share/wine/fonts**路径下 。

### 输⼊框不能输⼊

输入框的实现基本离不开riched20这个模块，目前wine上游的riched20模块还存在很多问题，基本上遇到此类问题直接替换riched20都能解决。

可在终端执行 **winetricks riched20** ，该命令会下载W2KSP4_EN和InstMsiW到目录**~/.cache/winetricks/**下，若执行时遇到网络链接异常，可到对应链接手动下载exe安装包（链接1: [W2KSP4_EN.EXE](https://saimei.ftp.acc.umu.se/mirror/archive/ftp.sunet.se/pub/security/vendor/microsoft/win2000/Service_Packs/usa/W2KSP4_EN.EXE) 链接2: [InstMsiW.EXE](https://whp-aus2.cold.extweb.hp.com/pub/softlib/software/msi/InstMsiW.exe)），放到~/.cache/winetricks/目录下的对应文件夹中，再执行 winetricks riched20 即可。

![image-20210526155658166](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/image-20210526155658166.png)

### 显示应用程序界面出现重复图标

wine安装的windows应用程序图标文件在路径 **.local/share/applications/wine/Programs/**下，删除程序重复的一个.desktop文件，重启电脑即可。

### 腾讯软件相关问题

- 微信更新版本后，有时会弹出程序运行异常，可能是wine暂时还不支持微信的相关新功能，但不影响正常的聊天打字，直接关闭异常弹窗即可。
- 企业微信传输文件时，10M以上的文件容易上传或下载失败，暂时没有找到解决办法，一般大文件移动到share目录下与同事共享。

## 参考文章

- https://wiki.winehq.org/Download
- https://ubuntuhandbook.org/index.php/2021/02/how-to-install-wine-6-3-in-ubuntu-18-04-20-04-20-10/
- https://docs.qq.com/mind/DWFBpbmpjd0RtV2Z0
- https://bbs.deepin.org/zh/post/174950  （注：不要安装该文章中的windows相关依赖，否则可能会导致ubuntu图形化界面无法正常启动）
- https://zhuanlan.zhihu.com/p/333826060

