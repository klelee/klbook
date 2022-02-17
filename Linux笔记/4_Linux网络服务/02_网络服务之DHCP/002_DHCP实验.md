# 实验

## 安装DHCP

`yum install -y dhcp`

## DHCP基本功能实验

### 生成配置文件

`vim /etc/dhcp/dhcpd.conf`这是DHCP默认的配置文件，但其是空的，需要根据模板配置内容。使用`/usr/share/doc/dhcp-4.*.*/dhcpd.conf.sample`.

![image-20211222162700681](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211222162700681.png)

`cp -a /usr/share/doc/dhcp-4.*.*/dhcpd.conf.sample /etc/dchp/dhcpd.conf ` 生成配置文件。 

### 修改配置文件

修改重要配置文件前要养成备份的好习惯！

```shell
cp -a /etc/dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf.bak
```



将配置文件的前几个subnet声明注释掉。修改最后一个subnet声明：

![image-20211222163756507](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211222163756507.png)

最后一个subnet修改为：

![image-20211222165913981](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211222165913981.png)

完成后重启dhcpd服务，就可以看到67端口已经开放了：

![image-20211222165858122](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211222165858122.png)

### 测试DHCP配置是否成功

在另外一台客户机上，开启dhcp自动获取IP地址

- 开启dhcp，编辑`/etc/sysconfig/network-scripts/ifcfg.ens33`

- ![image-20211222171954951](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211222171954951.png)

- 重启网卡：`ifdown ens33;ifup ens33`

- 查看IP，刚好是，刚刚配置的DHCPIP池的第一个IP

  ![image-20211222171931027](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211222171931027.png)

### 了解租约四部曲

![image-20211222172354197](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211222172354197.png)



## 保留地址（固定地址分配）

DHCP服务器端可以为某个客户机保留固定的IP地址。

### 获取客户端的MAC地址

可以获取到MAC地址的方式很多：

1. ifconfig

   ![image-20211222184843472](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211222184843472.png)

2. `arp -a`

   ![image-20211222185812876](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211222185812876.png)

### 将客户端的mac地址写入服务器端的配置文件

添加客户端的mac地址，将一个ip分配给客户端（最好填IP池之外的IP地址）

![image-20211222190436518](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211222190436518.png)

然后重启服务器端的网络服务，之后在重启客户端的网卡

![image-20211222190840944](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211222190840944.png)

重启客户端的网卡过后显示，新分配的ip地址已经生效了。

### 分析日志文件

`tail -20 /var/log/messages`

![image-20211222191024653](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211222191024653.png)

## 超级作用域

DHCP服务器可为单个物理网络上的客户端提供多个作用域租约地址 。在同一个路由器上，设置两个IP地址。

### 实验环境

三台虚拟机同一网络模式，一个DHCP服务器，两个客户机

### 实验步骤

1. 设置DHCP服务器的单臂路由所需子网卡：

   ```shell
   cd /etc/sysconfig/network-scripts/
   cp ifcfg-ens33 ifcfg-ens33:0
   ```

   然后编辑新的网卡文件

   ```shell
   TYPE="Ethernet"
   PROXY_METHOD="none"
   BROWSER_ONLY="no"
   BOOTPROTO="static"
   DEFROUTE="yes"
   IPV4_FAILURE_FATAL="no"
   IPV6INIT="yes"
   IPV6_AUTOCONF="yes"
   IPV6_DEFROUTE="yes"
   IPV6_FAILURE_FATAL="no"
   IPV6_ADDR_GEN_MODE="stable-privacy"
   NAME="ens33:0" -------------------------------> 需要改
   UUID="9cfab5d6-e509-41c9-a858-e47e21f1bbbc"
   DEVICE="ens33:0" -------------------------------> 需要改
   ONBOOT="yes"
   IPADDR="192.168.201.201" -------------------------------> 需要改
   PREFIX="24"
   GATEWAY="192.168.200.2"
   DNS1="8.8.8.8"
   IPV6_PRIVACY="no"
   ```

2. 开启路由转发

   ```shell
   vim /etc/sysctl.conf
   
   ```

   