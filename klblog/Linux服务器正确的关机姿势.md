# Linux正确的关机和重启姿势

## 一 观察系统的使用状态

### 1. 观察当前在线用户

为啥这么做呢？我们都知道Linux是多用户使用的，万一关机的时候，其他用户正在进行非常重要的环境配置，都快要完成了，然后你啪一下，服务器重启了，那另一个用户可能就，40米大刀为你而来。

`who`命令或者`w`命令

![image-20211214165751347](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214165751347.png)

这两个命令都是通过读取` /var/run/utmp`文件来判断当前在线用户的。

### 2. 观察网络链接状态

`netstat -a` 这个命令列出的选项可能比较多，可以使用more分屏查看`netstat -a | more`

![image-20211214165822751](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214165822751.png)

### 3. 观察后台正在执行的程序

`ps -aux`或者`top`命令，后者可能会更让你舒服一些。

![image-20211215151717333](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215151717333.png)

![image-20211215151728969](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215151728969.png)

## 二 通知在线用户关机时刻

可以使用shutdown命令来达到这个效果，`shutdown -k`

![image-20211215152619232](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215152619232.png)

## 三 使用正确的关机命令

一般情况下，我们会先多次执行同步命令`sync` ,然后稍作等待后执行关机或重启命令

前面说过，工作环境中，一般不关机，但不能不说关机命令是不是：`shutdown -h [时间]`

重启`shutdown -r [时间]`