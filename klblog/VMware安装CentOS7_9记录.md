# Linux学习环境搭建

## Vmware安装

1. VMware下载：https://www.vmware.com/go/getworkstation-win

2. 运行安装程序，该重启安装驱动就重启，不需要就下一步，傻瓜式安装。

3. 勾选项：增强型键盘……

4. 取消勾选项：

   1. 自动获取更新
   2. 分享数据

5. 其他默认

6. 安装结束之后，不要着急点击完成，先输入许可证：

   ```
   ZF3R0-FHED2-M80TY-8QYGC-NPKYF
   ```

7. 然后完成安装，重启设备。

## 虚拟网络配置

从虚拟机或者直接在电脑开始菜单找到“虚拟网络编辑器”

![image-20211214111400711](http://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214111400711.png)

在虚拟网络编辑器的右下角

![image-20211214111714468](http://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214111714468.png)

根据以下图示配置NAT网络

![image-20211214112437582](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214112437582.png)

记住这三组数值

![image-20211214112533857](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214112533857.png)

然后确认退出！

## 安装CentOS前准备

### 配置虚拟磁盘

打开VMware，创建新的虚拟机

![image-20211214112806055](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214112806055.png)

选择自定义

![image-20211214112833448](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214112833448.png)

这里默认

![image-20211214112851495](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214112851495.png)

稍后安装操作系统

![image-20211214112927327](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214112927327.png)

客户机操作系统选择Linux，版本选择CentOS

![image-20211214113020888](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214113020888.png)

取一个响当当的名字

![image-20211214113114374](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214113114374.png)

根据个人电脑配置选择内核数量，我比较小气，就给少点

![image-20211214113203352](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214113203352.png)

根据个人电脑配置选择内存大小，推荐2g以上，不能小于512M，小于512会被默认最小安装。

![image-20211214113343818](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214113343818.png)

网络类型选择NAT

![image-20211214113421680](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214113421680.png)

I/O控制器默认

![image-20211214113449694](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214113449694.png)

磁盘类型默认

![image-20211214113510158](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214113510158.png)

创建新的虚拟磁盘

![image-20211214113533447](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214113533447.png)

磁盘默认20就够了，不够的话后面可以再加。

![image-20211214113654710](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214113654710.png)

这里默认就可以

![image-20211214113722966](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214113722966.png)

这里先不要退出，选择自定义硬件

![image-20211214113805549](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214113805549.png)

打印机和声卡不需要，可以移除掉。点击选中，然后下面有移除。

![image-20211214113935860](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214113935860.png)

选择安装镜像，在新CD/DVD下，连接中选择“使用ISO映像文件”，浏览找到下载的文件。（浏览器下载文件一般在用户文件下的下载目录里`C:\user\<username>\Download`）

![image-20211214114050333](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214114050333.png)

选好之后点关闭 - 然后完成

### 开启安全启动UEFI

编辑虚拟机设置

![image-20211214120535283](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214120535283.png)

选项 - 高级 - UEFI

![image-20211214120722284](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214120722284.png)

确定退出

磁盘配置好了。下面开始安装。



### 安装CentOS7

点击“开启此虚拟机”开机，通过键盘的方向键，移动到“install CentOS 7”，回车确认

![image-20211214120920766](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214120920766.png)

然后就进入到安装界面了，推荐使用英文，所以默认就可以了！

![image-20211214114844330](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214114844330.png)

接下来设置时区和要安装的软件

![image-20211214115104946](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214115104946.png)

首先点击 DATE & TIME 进入时区设置界面，然后找到祖国的位置，点一下。观察左上角是Asia和Shanghai就好了。最后点击`Done`.

![image-20211214115241932](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214115241932.png)

然后点击 SOFTWARE SELECTION 选择要安装的软件。这里我选择基本的Web服务，安装一下基本的工具。然后`Done`

![image-20211214115606062](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214115606062.png)

接下来时最重要的两个步骤，系统分区和网络配置

![image-20211214115956954](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214115956954.png)

首先系统分区，点击 INSTALLATION DESTINATION 进入选择磁盘，这里选择我要手动分区，然后`Done`

![image-20211214120108223](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214120108223.png)

添加新分区

![image-20211214120319386](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214120319386.png)

EFI分区（启动引导分区）：mount point选择`boot/efi`大小300M

![image-20211214121223460](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214121223460.png)

Swap分区（交换空间）：mount point 选择`swap`大小选择4G（你选择的内存大小的两倍，最大不超过8G）

![image-20211214121458810](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214121458810.png)

/分区（根分区）：mount point 选择`/`大小不用填，直接添加

![image-20211214121643197](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214121643197.png)

然后点击`Done`完成，下面选择接受

![image-20211214121750319](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214121750319.png)

接下来进入Kdump，取消勾选后Done退出

![image-20211214121856029](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214121856029.png)

最后进行网络配置，点击进入 NETWORK & HOST NAME

先修改主机名

![image-20211214122122044](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214122122044.png)

然后点击右侧的Configure进入配置界面。步骤1-6都要完成

![image-20211214123006443](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214123006443.png)

这样所有配置就完成了，开始安装

![image-20211214123246345](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214123246345.png)

这里需要设置root密码，点击进入

![image-20211214123329661](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214123329661.png)

输入root密码，如果你输入的密码过短，就点击两次Done保存退出。

![image-20211214123431296](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214123431296.png)

静静等待安装完成。等进度条走完之后点击reboot重启。

![image-20211214123516599](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214123516599.png)

重启之后进入这样的黑框框，就表示安装好了。

![image-20211214123702792](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214123702792.png)

## 安装CentOS之后的配置

先用ssh链接到虚拟机。Windows可以使用xshell

### 更换国内镜像源

推荐使用中科大的镜像源：https://mirrors.ustc.edu.cn/help/centos.html

#### 本分原文件

```bash
cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
```

#### 编辑镜像文件

```bash
vi /etc/yum.repos.d/CentOS-Base.repo
-------------------------------------
# 删除原来内容
dG
# 粘贴中科大源地址
-------------------------------------
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the
# remarked out baseurl= line instead.
#
#

[base]
name=CentOS-$releasever - Base
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
baseurl=https://mirrors.ustc.edu.cn/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#released updates
[updates]
name=CentOS-$releasever - Updates
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
baseurl=https://mirrors.ustc.edu.cn/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
baseurl=https://mirrors.ustc.edu.cn/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
baseurl=https://mirrors.ustc.edu.cn/centos/$releasever/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

```

#### 更新本地缓存

```bash
yum makecache
```

### 配置静态IP

编辑网络配置文件`/etc/sysconfig/network-scripts/ifcfg-ens33`修改BOOTPROTO的值为static

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

