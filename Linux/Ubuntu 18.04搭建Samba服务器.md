# Ubuntu 18.04搭建Samba服务器

## 创建共享文件夹

```shell
mkdir /home/user/share
chmod 777 /home/user/share # 设置权限rwx
```

## 安装Samba服务器

```shell
sudo apt-get install samba samba-common

# 配置Samba服务
sudo vim /etc/samba/smb.conf
# 文本最后添加一下内容
[share]
   path = /home/user/share
   available = yes
   browseable = yes
   writable = yes
   
 # 创建共享文件的账号密码（使用系统账号）
 sudo smbpasswd -a xxx
   
 #重新启动Samba服务器
 sudo /etc/init.d/smbd restart 
 sudo /etc/init.d/samba-ad-dc restart
 #查看samba是否正在运行
 ps -aux | grep samba
```

## 连接

- 网络->ubuntu-hostname->share

