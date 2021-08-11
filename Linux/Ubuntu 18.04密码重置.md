# Ubuntu 18.04密码重置

## 场景

运行研华的ECU开发虚拟机时，其Advantech文件需要访问权限，而用户手册中没有说明虚拟机的root用户与vmuser用户的密码，所以需要重置root与vmuser用户的密码。

## 密码重置

- 重启时按shift键，进入grub界面

- 选择ubuntu高级选项，回车

- 选择最高的Linux内核版本对应的recovery mode模式，**按e**，进入编辑（注：不要按回车）

- 在倒数第五行找到【recovery nomodeset】并将之删除替换成【quiet splash rw init=/bin/bash】

- 按F10进入终端，更新密码

  ```shell
  # 更新root密码，设为root
  passwd root
  # 更新vmuser密码，设为vmuser
  passwd vmuser
  ```

- 返回password updated successfully 表示修改成功，重启即可

## 参考文章

- https://www.linuxrumen.com/rmxx/889.html