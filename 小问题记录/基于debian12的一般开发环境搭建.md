本篇文章的背景是配置Android7.1的编译环境。项目采用的是广和通的SC806芯片，基于高通的msm8909平台。相关配置可以作为通用的Linux开发环境。

- 开发环境： Debian12
- Android版本： android7.1.2
- jdk：JDK8
- python： Python2

## 常用软件包

```
sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl zlib1g-dev gcc-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev ccache libgl1-mesa-dev libxml2-utils xsltproc unzip libc6-dev x11proto-core-dev tofrodos lib32ncurses5-dev lib32z1-dev software-properties-common lib32stdc++6 libxml-simple-perl gcc g++ gdb make cmake git vim
```

还需安装：

```
sudo apt-get install libncurses*
```



## Git配置

```
git config --global user.name "username"
git config --global user.email "useremail"
git config --global core.editor "vim"
```

如果有相关工具，可以为git配置代理（亲测，Github速度能跑满）：

```
git config --global http.proxy 'socks5://127.0.0.1:7890' 
git config --global https.proxy 'socks5://127.0.0.1:7890'
```

生成ssh密钥：

```
ssh-keygen -t rsa -C "useremail"
# 弹出的选项全部回车即可
```

## Python 2 安装

debian12中取消了对Python2的支持，所以需要手动编译源码进行安装！

### 下载python 2 源码

https://www.python.org/downloads/source/

![image-20231001161019286](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20231001161019286.png)

也可以直接点击此链接下载：https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz

### 解压源码包

```
tar -xf Python-2.7.18.tgz
```

### 编译并配置

编译

```
sudo mv Python-2.7.18 /opt/    # 建议将Python放在opt目录下
cd /opt/Python-2.7.18    # 进入源码目录
./configure --with-pydebug    # 配置编译python2的环境
make -s -j16    # 编译
```

配置环境变量

```
sudo vim /etc/profile
-----------------------------------
# 在最后一行添加
export PATH=$PATH:/opt/Python-2.7.18/
```

将python可执行文件放到系统命令目录

```
ln -s /opt/Python-2.7.18/python /usr/bin/
```

Python2的环境就OK了！

## JDK8

Debian12的包管理库里已经删除了openjdk-8-jdk的包，所以需要手动下载。

### 下载openjdk-8-jdk

这里是中科大镜像站的地址： http://mirrors.ustc.edu.cn/debian/pool/main/o/openjdk-8/

![image-20231001162253921](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20231001162253921.png)

可以点击下面的链接直接下载：

http://mirrors.ustc.edu.cn/debian/pool/main/o/openjdk-8/openjdk-8-jdk-headless_8u382-ga-2_amd64.deb

http://mirrors.ustc.edu.cn/debian/pool/main/o/openjdk-8/openjdk-8-jdk_8u382-ga-2_amd64.deb

http://mirrors.ustc.edu.cn/debian/pool/main/o/openjdk-8/openjdk-8-jre-headless_8u382-ga-2_amd64.deb

http://mirrors.ustc.edu.cn/debian/pool/main/o/openjdk-8/openjdk-8-jre_8u382-ga-2_amd64.deb

### 安装openjdk-8-jdk

```
sudo dpkg -i openjdk-8-jdk-headless_8u382-ga-2_amd64.deb
sudo dpkg -i openjdk-8-jdk_8u382-ga-2_amd64.deb
sudo dpkg -i openjdk-8-jre-headless_8u382-ga-2_amd64.deb
sudo dpkg -i openjdk-8-jre_8u382-ga-2_amd64.deb
# 在上面的安装中如果出现缺少依赖，可以直接下面的语句解决之后重新安装
sudo apt --fix-broken install
```

