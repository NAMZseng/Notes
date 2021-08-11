# Ubuntu 18.04搭建SVN服务器

## SVN数据存储

SVN服务器版本库有两种格式，一种为FSFS，一种为Berkeley DB。把文件上传到SVN版本库后,上传的文件不再以文件原来的格式存储,而是被SVN以它自定义的格式压缩成版本库数据,存放在版本库中。SVN服务器默认采用FSFS格式，将每次commit的内容增量方式存放的，每个增量包存成1个文件，这个增量包中包括了这次commit的全部数据。

另：FSFS与Berkeley DB不同的一点是，其与具体的操作系统内核结构无关，可以在不同的系统之前迁移。

<img src="https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/Strategies%20for%20Repository%20Deployment.png"  />

## 安装与配置

```shell
# 安装
sudo apt  install subversion
mkdir /home/user/svn
mkdir /home/user/svn/repository
chmod -R 777 /home/user/svn/repository
# 创建svn版本仓库
svnadmin create /home/user/svn/repository

# 配置svn访问权限
vim /home/user/svn/repository/conf/svnserve.conf
# 取消下面的注释（注：删除注释后还要删除空格，要顶格）
anon-access = none             （设置为 none 才可以用TortoiseSVN看 svn 日志）
auth-access = write            （权限用户可写）
password-db = password         （密码文件为 password）
authz-db = authz               （权限文件为 authz）

# 配置用户权限
vim  /home/user/svn/repository/conf/authz
# 在 [groups]下添加组的成员信息
admin = user
[repository:/]
@admin = rw
* =

# 配置用户密码
vim /home/user/svn/repository/conf/passwd
[users]
user = user


# 启动svn服务
svnserve -d -r /home/user/svn
# 停止svn服务
killall svnserve
# 查看svn服务
ps -aux|grep svnserve
```

## 设置文件删除权限

需求背景：为防止用户不小心或恶意删除服务器文件，需配置仅管理员可以删除服务器文件的权限。

- 在服务器仓库的hooks目录下（/home/user/svn/SVN2021/hooks）

  ```shell
  cp pre-commit.tmpl pre-commit
  ```

- 编辑pre-commit

  ```shell
  # Check that the author of this commit has the rights to perform
  # the commit on the files and directories being modified.
  #"$REPOS"/hooks/commit-access-control.pl "$REPOS" $TXN \
  #  "$REPOS"/hooks/commit-access-control.cfg
  
  USER=`$SVNLOOK author -t $TXN $REPOS`
  ADMINLIST=User  #设置可以删除文件的SVN成员
  if [ "`echo $ADMINLIST|grep -w $USER|wc -l`" -eq 0 ];then
          if [ `$SVNLOOK changed -t $TXN $REPOS |grep "^D "|wc -l` -gt 0 ];then
      # echo "You Don't have the pemmision of delete!Please contact your administrator!" >&2
                  echo "你没有权限删除，请联系管理员删除！" >&2
                  exit 1
          fi
  fi
  
  ```

- 但不同普通用户删除本地仓库文件后提交删除记录时，就会收到如下提示：

![image-20210702163252502](photos/image-20210702163252502.png)

## 设置提交时必须添加注释

- 编辑pre-commit

  ```shell
  LOGMSG=$($SVNLOOK log -t "$TXN" "$REPOS" | grep "[a-zA-Z0-9]" | wc -c)
  if [ "$LOGMSG" -lt 1 ]; then
          echo "提交时必须添加注释!" >&2
          exit 1
  fi
  ```

- 当用户提交本地仓库文件时，若没有添加注释，就会收到如下提示：

  ![image-20210714185651832](photos/image-20210714185651832-1626260213138.png)

## 设置开机自启动

- 在/etc/systemd/system/目录创建单元文件

```shell
sudo touch /etc/systemd/system/svn.service
sudo chmod 664 /etc/systemd/system/svn.service
```

- 打开svn.service文件，添加服务配置：

```shell
[Unit]
Description=Subversion Server
[Service]
Type=forking
ExecStart=/usr/bin/svnserve -d -r /home/user/svn
ExecStop=/usr/bin/killall svnserve
Restart=always
[Install]
WantedBy=default.target
```

- 通知systemd有个新服务添加：

```shell
systemctl daemon-reload
```

- 启动和停止SVN服务

```shell
systemctl start svn.service
systemctl stop svn.service
```

- 配置开机自启动

```shell
systemctl enable svn.service
```

- 其他

```shell
# 列出systemd管理的所有服务状态
systemctl list-units --type service --all

# 检查SVN服务运作状态
systemctl status svn.service
```

## 常用命令

```shell
# 拉取本地服务器repository仓库中的项目到本地
sudo svn co svn://127.0.0.1/repository

# 注：可以将拉取的项目文件夹权限设为777，方便在编程软件中使用SVN插件
sudo chmod -R  777 repository

# 添加文件到仓库副本中
sudo svn add LinkSDK-Test/
sudo svn delete file
#  提交更新到服务器仓库
 sudo svn  ci -m "add LinkSDK project"
 
 # 回滚到某一version
 sudo svn up -r [version id]
# 查看仓库信息
sudo svn info LinkSDK-Test

# 解决版本uuid不一致问题，用local uuid 更新服务器仓库uuid
# svn: E170009: Repository UUID 'c876d49d-15ba-44df-820c-4bc48f4c7ec8' doesn't match expected UUID '3bddc738-b1ab-4fc5-8ba2-e138065071fe'
svnadmin setuuid svn/repository/  3bddc738-b1ab-4fc5-8ba2-e138065071fe
```



## 相关文档

- https://zhuanlan.zhihu.com/p/70721353
- http://subversion.apache.org/docs/
- http://svnbook.red-bean.com/