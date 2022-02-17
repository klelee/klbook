# 服务管理

Linux中的服务有哪几种？

## 服务的介绍

### 服务的分类

- RPM包安装的服务
  1. 独立的服务
  2. 基于xinetd服务（越来越少，过时）
- 源码包的安装服务

## RPM包包默认安装的服务管理

### 独立的服务

1. 绝对路径启动服务脚本

   一般用于启动源码包

2. systemctl启动相应的服务

   启动：`systemctl start <servicename>`

   自启动：`systemctl enable <servicename>`

## httpd启动脚本分析

## rpm包安装的服务

### 启动

`/usr/local/apache2/bin/apachectl start|stop`

### 自启动

```shell
vim /etc/rc.local
/usr/local/apache2/bin/apachectl start|sto
```

## 服务管理总结

![image-20211221102256399](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221102256399.png)

## Linux常见服务的作用

![image-20211221102457777](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221102457777.png)

![image-20211221102533579](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221102533579.png)

![image-20211221102556299](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221102556299.png)

![image-20211221102633257](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221102633257.png)

![image-20211221102654228](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221102654228.png)

![image-20211221102714147](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221102714147.png)

![image-20211221102730136](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221102730136.png)

![image-20211221102753823](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221102753823.png)
