# DHCP

## 什么是DHCP

DHCP（动态主机配置协议），是一个工作在应用层的局域网协议。数据传输时使用的UDP不可靠传输协议工作，通常被应用在大型的局域网环境中，主要作用是集中的管理、分配网络资源，使网络环境中的主机能动态的获得IP地址、Gateway地址、DNS服务器地址等信息。生产环境中无人值守安装就依靠DHCP

## DHCP的工作原理

![image-20211222131951227](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211222131951227.png)

### DHCP客户端进行IP请求

使用0.0.0.0的地址作为源地址，使用255.255.255.255作为目标地址，使用UDP67端口作为目的端口，来广播请求，IP地址信息。广播信息DHCP Discover中包含了DHCP客户机的MAC地址和计算机名，以便于DHCP服务器能确定是哪个客户机发送的请求。

### DHCP服务器的响应请求

当DHCP服务器接收到客户机请求IP地址的信息，他就在自己的IP地址池查找是否还有合法的IP地址，如果有，DHCP服务器就将此IP地址做上标记，加入到DHCP OFFER的消息中，然后DHCP就广播一则消息，包括：

DHCP客户机的MAC地址；DHCP服务器提供的合法IP；子网掩码，默认网关；租约期限；DHCP服务器的MAC地址。因为DHCP客户机目前还没有IP地址。所以DHCP服务才会选择全局广播的方式发送OFFER包

### DHCP客户机选择IP

DHCP客户机虽然接收到了IP地址，但是暂时还没有配置IP地址，因此DHCP客户机就会再次使用0.0.0.0向255.255.255.255广播DHCP REQUEST包，REQUEST包包含：为该客户机提供IP配置的服务器的服务标识符（IP地址）。

### DHCP服务器确认租约

当DHCP服务器收到客户机广播的REQUEST包之后，确定对方要与自己建立租约。就再次广播一个DHCP ACK包，与DHCP客户端完成租约。当DHCP客户机接收到DHCP ACK包之后，他就配置了IP地址，完成了TCP/IP的初始化。

### DHCP客户机续租

![image-20211222134140432](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211222134140432.png)

- 默认租约：客户机和服务器之间在确认租约关系之后，确认的祖约时间。

DHCP客户机会在租期过去一半的时候，直接向其提供IP地址的DHCP服务器发送DHCP REQUEST消息包。如果收到服务器的DHCP ACK包，那么久完成续租。如果没有收到，则客户机会继续使用当前IP地址，直到租期时间达到87.5%，再次向其服务器发送续租请求，如果还是没有收到恢复，则使用到时间到期，会自动释放IP。然后DHCP客户机会使用169.254.0.0/16中随意一个地址，每隔五分钟重新发送一次DHCP DISCOVER包。

## DHCP服务搭建

### 环境准备

防护的关闭：

- 关闭防火墙
- 禁用selinux
- 关闭VMware虚拟网络编辑器的DHCP功能

### DHCP相关信息

- 软件名：
  - dhcp    # DHCP服务软件包
  - dhcp-common    # DHCP命令软件包

- 服务名：
  - dhcpd    # DHCP服务名
  - dhcrelay   # DHCP中继服务名

- 端口：
  - udp 67     # 作为客户机的目标端口，接收客户端的请求
  - udp 68    # 作为服务器的源端口

- 配置文件
  - dhcpd    /etc/dhcp/dhcpd.conf    #dchp生效的配置文件，但其默认是空的，需要根据模板文件配置
  - dhcpd.conf.sample    /usr/share/doc/dhcp-4.*.*/dhcp.conf.sample    # dhcp模板配置文件
  - dhcrelay    /etc/sysconfig/dhcrelay    # 该文件是中继配置文件，中继实验中用到

### DHCP配置文件详解

dhcp配置文件中，至少有一个subnet的网段是当前主机的网段

```shell
subnet 192.168.88.0 netmask 255.255.255.0(                # 声明要分配的网段和子网掩码
	range 192.168.88.3 192.168.88.254;                     # 声明可用的IP地址池
	option domain-name "klelee.com";                       # 设置DHS域
	option domain-name-serbers 8.8.8.8;                    # 设置DNS服务器地址
	option routers 192.168.88.2;                           # 默认网关地址
	option broadcast-address 192.168.88.255;               # 广播地址
	default-lease-time 600;                                # 默认租约（s）
	max-lease-time 7200;                                   # z
)
```

