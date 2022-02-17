有关于CentOS7配置网络，有两种方式：

1. 修改配置文件

   网络配置的绝大对数内容都可以在文件`/etc/sysconfig/network-scripts/ifcfg-ens33`下修改。

   ```bash
   vim /etc/sysconfig/network-scripts/ifcfg-ens33
   ----------------------------------------------
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
   NAME="ens33"
   UUID="1821e847-7c8c-45b8-9875-7c4e2333bd3d"
   DEVICE="ens33"
   ONBOOT="yes"
   IPADDR="192.168.200.100"
   PREFIX="24"
   GATEWAY="192.168.200.2"
   DNS1="8.8.8.8"
   IPV6_PRIVACY="no"
   ```

   我们常常关注的参数有：

   - BOOTPROTO    启动协议，是否随机分配ip地址

   - NAME    网卡名（系统分配）
   - DEVICE    网卡名（硬件网卡，与NAME要保持一致）
   - IPADDR    IP地址
   - GATEWAY    网关
   - ONBOOT    开机加载

2. 同归图形界面配置网络

   `nmtui`

   ![image-20211214164035345](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214164035345.png)

   在编辑模式中就可以对网卡内容进行各种自定义

   ![image-20211214164125199](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214164125199.png)

   ![image-20211214164207489](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214164207489.png)

