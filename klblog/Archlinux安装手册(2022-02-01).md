## 准备工作

1. 镜像下载：[北京外国语大学镜像](https://mirrors.bfsu.edu.cn/archlinux/iso/2022.02.01/archlinux-2022.02.01-x86_64.iso)

2. 使用ventoy做启动盘：

   (1) ventoy下载：[github下载地址](https://github.com/ventoy/Ventoy/releases/download/v1.0.65/ventoy-1.0.65-windows.zip)

   (2) 解压运行下载好的ventoy，设备选择准备好的U盘（会清空），然后选择安装即可。

   ![image-20220213064903535](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213064903535.png)

   (3) 将下载好的镜像复制到制作好的启动盘

## 开始安装

### 启动到启动项选择菜单

按下电源键开机，屏幕点亮瞬间连续按`F12`进入启动项选择菜单。选择你的U盘作为启动项。回车继续

![](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/40ee661853cc30f02776ae97e290d34.jpg)

进入启动盘之后，选择刚刚复制进来的archlinux的安装镜像（我的启动盘里镜像比较多，忽略）。

![](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/589a4fdc26cf5e5284b9cd522c6157c.jpg)

这里选择安装archlinux 安装

![image-20220213072135295](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213072135295.png)

### 联网

进入到安装界面之后，输入iwctl(回车)进入联网环境

![](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213072420324.png)

```
$ iwctl   //会进入联网模式
[iwd]# help    //可以查看帮助
[iwd]# device list    //列出你的无线设备名称，一般以wlan0命名
[iwd]# station <device> scan    //扫描当前环境下的网络
[iwd]# station <device> get-networks    //会显示你扫描到的所有网络
[iwd]# station <device> connect <network name>
password:输入密码
[iwd]# exit    //退出当前模式，回到安装模式
```

网络测试

```
ping baidu.com
```

![image-20220213073229271](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213073229271.png)

出现下列输出就是连接上网络了。

### 更新软件安装源

#### 禁用reflector

```
systemctl stop reflector
systemctl disable reflector
```

#### 修改安装源

```
vim /etc/pacman.d/mirrorlist
```

建议只保留国内的北外（bfsu）、上海交大（sjtug）、清华（tuna）、中科大（ustc）这几个源，删除其他的

![image-20220213073909877](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213073909877.png)

##### vim使用

http://klelee.com/index.php/archives/5/

### 更新时间

```
timedatectl set-ntp true
```

### 通过ssh连接（可选）

#### 为当前环境设置密码

```
passwd
```

输入密码的时候不会显示

#### 查看ip地址

```
 ip a
```

一般在这个位置

![image-20220213075056194](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213075056194.png)

#### 远程登陆当前安装环境

```
ssh root@<ip address>
```

![image-20220213075337443](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213075337443.png)

然后就可以爽快的粘贴命令了

### 磁盘分区

推荐使用cfdisk，有简单的ui界面。需要分出来三个区，分别是efi分区，swap分区和根分区

| EFI分区  | 300M     |
| -------- | -------- |
| swap分区 | 4GB      |
| 根分区   | 剩余空间 |

```
cfdisk /dev/sda
```

分区表类型选择gpt

![image-20220213075824970](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213075824970.png)

这就是cfdisk的分区界面，new就是新建分区

![image-20220213075842493](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213075842493.png)

选择new之后会提示输入新建分区大小，首先建立efi分区300M

![image-20220213080017196](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213080017196.png)

选择分区类型

![image-20220213080042158](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213080042158.png)

EFI分区当然选择，EFI System

![image-20220213080113786](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213080113786.png)

然后依次新建swap分区和根分区。

![image-20220213080213271](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213080213271.png)

新建好之后，选择Write写入，输入yes确认写入

![image-20220213080240703](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213080240703.png)

然后回来之后选择Quit退出分区工具。

查看刚才的分区是否有效

```
lsblk
```

![image-20220213080418890](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213080418890.png)

### 格式化分区

分别格式化EFI、根、swap

```
mkfs.vfat /dev/sda1
mkfs.xfs -f /dev/sda3
mkswap /dev/sda2
```

![image-20220213080649213](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213080649213.png)

### 挂载分区

```
mount /dev/sda3 /mnt
mkdir -p /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi
swapon /dev/sda2
lsblk -f    ## 查看分区挂载情况
```

![image-20220213080840745](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220213080840745.png)

### 安装系统的基本包

```
pacstrap /mnt linux linux-firmware linux-headers base base-devel vim git bash-completion
```

### 生成文件系统表

```
genfstab -U /mnt >> /mnt/etc/fstab
```

### 进入新系统

```
arch-chroot /mnt
```

### 设置时区

```
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
```

### 本地化配置

#### 设置系统语言

```
vim /etc/locale.gen
---------------------------------
# 取消注释以下两行
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
```

生成本地语言信息

```
locale-gen
```

设置本地语言环境变量

```
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

### 网络配置

#### 主机设置

```
echo arch > /etc/hostname
```

#### 生成hosts文件

```
vim /etc/hosts
----------------------------------------
# 在文件末尾添加
127.0.0.1   localhost
::1         localhost
127.0.1.1   arch.localdomain arch   # 这里的archlinux是主机名
```

### 配置grub

#### 安装相关软件包

```
pacman -S grub efibootmgr efivar networkmanager intel-ucode
```

#### 生成grub配置文件

```
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

### 配置network

```
systemctl enable NetworkManager
```

### 配置root密码

```
passwd
```

### 重启到新系统

```
exit
umount /mnt/boot/efi
umount /mnt
reboot
```

## 基本配置

### 再次联网

输入`nmtui` 选择 “Activate a connection” 回车进入，选择你需要的网络，连接后back返回即可

### 配置ssh

#### 安装openssh

```
pacman -S openssh
```

#### 启动sshd服务

```
systemctl enable sshd
systemctl start sshd
```

#### 修改sshd配置文件

```
vim /etc/ssh/sshd_config
--------------------------
# 将下列的语句值改为yes
PermitRootLogin yes
```

![img](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/v2-ef475a0ef5d4f500947cb126784b65da_720w.jpg)

#### 连接ssh

```
ssh root@<ip address>
```

### 配置bash shell环境变量

```
cd /etc/skel
--------------------------
vim /etc/skel/.bashrc
-------------------------------
# 添加
export EDITOR=vim
alias grep='grep --color=auto'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'

[ ! -e ~/.dircolors ] && eval $(dircolors -p > ~/.dircolors)
[ -e /bin/dircolors ] && eval $(dircolors -b ~/.dircolors)
# 保存退出
-------------------------------
cp -a . ~
```

### 添加标准用户(以下klelee是我的用户名)

```text
# 添加用户
useradd --create-home klelee
# 设置密码
passwd klelee
```

### 设置用户组

```text
usermod -aG wheel,users,storage,power,lp,adm,optical klelee
```

### 修改当前用户权限

```text
visudo
---------------------------------
# 取消注释以下行
%wheel ALL=(ALL) ALL
```

### 添加ArchLinuxCN 存储库

该仓库是由archlinux中文社区驱动的一个非官方的软件仓库。我们使用的很多软件都需要使用这个库去下载，比如typora。

```text
# 编辑/etc/pacman.conf
vim /etc/pacman.conf
--------------------------------------
# 在最后添加
[archlinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch   
# 这是中科大的源，你也可以选择清华、阿里等，当我推荐中科大，因为我喜欢
```

然后更新GPG密钥

```text
pacman -S archlinuxcn-keyring
```

**注** ： 如果以上更新密钥步骤出现错误，就是那种连着一串ERROR的情况，请执行以下步骤

```text
# rm -rf /etc/pacman.c/gnupg
# pacman-key --init
# pacman-key --populate archlinux archlinuxcn
# pacman -Syy
```

### 显卡驱动

```text
pacman -S xf86-video-intel vulkan-intel mesa
```

### 声卡配置

```text
# pacman -S alsa-utils pulseaudio pulseaudio-bluetooth cups
```

## 图形界面

### 显示服务

```text
pacman -S xorg
```

### 安装字体

### 英文字体

```text
pacman -S ttf-dejavu ttf-droid ttf-hack ttf-font-awesome otf-font-awesome ttf-lato ttf-liberation ttf-linux-libertine ttf-opensans ttf-roboto ttf-ubuntu-font-family
```

### 中文字体

```text
pacman -S ttf-hannom noto-fonts noto-fonts-extra noto-fonts-emoji noto-fonts-cjk adobe-source-code-pro-fonts adobe-source-sans-fonts adobe-source-serif-fonts adobe-source-han-sans-cn-fonts adobe-source-han-sans-hk-fonts adobe-source-han-sans-tw-fonts adobe-source-han-serif-cn-fonts wqy-zenhei wqy-microhei
```

### 打开字体引擎

```text
vim /etc/profile.d/freetype2.sh
--------------------------------------------
# 取消注释最后一句
export FREETYPE_PROPERTIES="truetype:interpreter-version=40"
```

### 安装桌面环境（KDE）

### KDE

```text
pacman -S plasma sddm konsole dolphin kate ark okular spectacle yay
```

plasma：就是桌面环境

sddm：登录管理器

konsole：kde下的终端

kate：文本编辑器

ark：解压与压缩

okular：PDF查看器

spectacle：截图工具

AUR：管理工具

### 设置sddm登录

```text
systemctl enable sddm
```

## 常用软件

### 中文输入法

```text
# sudo pacman -S fcitx fcitx-im fcitx-configtool
yay -S fcitx-sogoupinyin
vim ~/.xprofile
-------------------------------
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

重启，这时候会看到系统托盘会有一个键盘的图标，我已经配置过了，这里显示的是sogou的图标



![img](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/v2-c85b46f14936591e6d2e70991a213a29_720w.jpg)



右击那个图标，点击configure,在配置界面点加号



![img](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/v2-32619d63123652eeb4cb4014941d4d03_720w.jpg)



去掉“只显示当前语言”的选项，拉倒最下面选择sogoupinyin，之后回到上面的页面，选择美式键盘，删掉即可



![img](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/v2-241025588fa6b035fbc428c49500b9dc_720w.jpg)



### 其他软件

```text
sudo pacman -S typora visual-studio-code-bin netease-cloud-music
yay -S baidunetdisk-electron google-chrome qv2ray
```

更多软件可以去wiki寻找。

[archlinux wiki](https://link.zhihu.com/?target=https%3A//wiki.archlinux.org/title/Main_page_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

[List of applications](https://link.zhihu.com/?target=https%3A//wiki.archlinux.org/title/List_of_applications_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

### 清理缓存

```text
pacman -Scc
```
