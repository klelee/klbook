# linux的目录结构

Linux经典名言：

在Linux的世界里，一切皆文件！

![img](https://www.runoob.com/wp-content/uploads/2014/06/d0c50-linux2bfile2bsystem2bhierarchy.jpg)



![image-20211214164246455](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214164246455.png)



## 具体目录结构

- /bin 【常用】 （usr/bin  /usr/local/bin）是binary的缩写，这个目录存放着最经常使用的【命令】
- /sbin (/usr/sbin  /usr/local/sbin) s 就是super user的意思，这里存放的是系统管理员使用的系统管理程序
- /home【常用】  存放普通用户的主目录
- /root 【常用】 该目录为系统管理员的主目录，超级用户
- /lib 系统开机所需要的最基本的动态链接库，起作用类似于Windows的DLL文件
- /lost+found 这个目录一般情况下是空的，当系统非法关机后，这里存放一些文件
- /etc 【常用】 所有的系统管理所需要的配置文件和子目录，比如安装mysql数据库，my.conf
- /usr 【常用】 用户的很多应用程序和文件都放在这个文件夹下
- /boot 【常用】启动引导文件，包括一些连接文件，以及一些镜像文件
- /proc【不能动】这个目录是一个虚拟的目录，他是内存的映射，访问这个目录来获取系统信息
- /srv 【不能动】该目录存放一些服务启动后需要提供的数据
- /sys【不能动】该目录中安装了2.6内核中新出现的一个文件系统
- /tmp 临时文件
- /dev  设备
- /media  自动识别设备
- /mnt  挂载目录
- /opt 给主机额外安装文件所存放的目录
- /usr/share 【常用】另一个给主机额外安装软件所存放的目录
- /var 【常用】这个目录中存放着不断扩充的东西，习惯上将日志文件，各种经常修改的文件放在这个目录
- /selinux 安全子系统

