# Python 代码加密打包

## PyArmor原理

### 分片式技术

PyArmor 使用分片式技术来保护 Python 脚本。所谓分片保护，就是单独加密每一个函数， 在运行脚本的时候，只有当前调用的函数被解密，其他函数都没有解密。而一旦函数执行完成，就又会重新加密。

### 交叉保护

Python 代码和 c 代码相互进行校验和保护。
PyArmor 的核心代码使用 c 来编写，所有的加密和解密算法都在动态链接库中实现。 首先 _pytransform 自身会使用 JIT 技术，即动态生成代码的方式来保护自己，加密后的 Python 脚本由动态库 _pytransform 来保护，反过来， 在加密的 Python 的脚本里面，也会来校验动态库，确保其没有进行任何修改。

### 脚本加密过程

- 源代码编译成代码块
- 改装代码块
- 把改装后的代码块转换成为字符串，把字符串进行加密

### 全局密钥箱

全局密钥箱是存放在用户主目录的一个文件 `/home/user/.pyarmor/.pyarmor_capsule.zip` 。当 PyArmor 加密脚本或者生成加密脚本的许可文件的时候，都需要从这个文件中读取数据。

目前我们使用的为试用版本，所有的试用坂本使用的是同一个公共密钥箱 ，这个密钥箱使用 1024 位 RSA 密钥对。

对于正式版本，每一个用户都会收到一个专用的 私有密钥箱 ，这种密钥箱使用 2048 位 RSA 密钥对。

**通过密钥箱是无法恢复加密后的脚本，所以即便都使用公共密钥箱 ，加密后的脚本也是安全的，但是使用相同的密钥箱为加密脚本生成的许可是通用的。使用公共密钥箱是无法真正的限制加密脚本的使用期限或者绑定到某一台指定设备上，因为别人同样可以使用公共密钥箱为你的加密脚本生成合法的许可。**

运行加密脚本并不需要密钥箱，只有在加密脚本和为加密脚本生成许可的时候才会用到。

## 安装与加密

```shell
# 安装
pip install pyarmor
# 设置项目主脚本，这里重点是wsgi.py文件，和manage.py文件
 pyarmor init --entry=django_project_name/__init__.py,django_project_name/wsgi.py,manage.py
 # 加密
 # 加密后的py文件存放在自动生成的dist文件夹中，发布时，用加密的py文件替换源码文件即可
 pyarmor build
```

## 问题

启动报错ModuleNotFoundError: No module named 'project_name.pytransform'

原因是pyarmor创建入口加密入口的__init__.py文件中有生成了import 错误的代码，把django_project_name/__init__.py中的 **.pytransform**改为**pytransform**即可。

```python
# from .pytransform import pyarmor_runtime
#修改为 (去掉相对import）
from pytransform import pyarmor_runtime
```

## 参考文章

- https://pyarmor.readthedocs.io/zh/latest/project.html
- https://www.jianshu.com/p/c1d3d79e3545
