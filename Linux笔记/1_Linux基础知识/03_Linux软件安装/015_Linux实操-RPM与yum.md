# Linux实操-RPM与YUM

## rpm包管理

### rpm包的简单查询指令

- 查询已安装的rpm列表 `rpm -qa | grep xx`

  ![image-20211215145837042](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215145837042.png)

  

- rpm包名基本格式

  `firefox-60.2.2-1.el7.centos.x86-64.rpm`

### rpm包的其他查询指令

```
rpm -qa    # 查询所有安装的rpm软件包
rpm -qa | more
rpm =qa | grep X
```

```
rpm -q [软件包名]    # 查询软件包是否安装
```

![image-20211215145904996](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215145904996.png)

```
rpm -qi [软件包名]    # 查询软件包信息
rpm -ql [软件包名]    # 查询与软件包相关文件
```

![image-20211215145945983](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215145945983.png)

![image-20211215150004671](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215150004671.png)

```
rpm -qf [文件全路径名]    # 查询文件所属的软件包
```

![image-20211215150054077](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215150054077.png)

### rpm包的卸载

`rpm -e [rpm包的名称]`

### rpm包的安装

`rpm -ivh [rpm包全路径名称]`

#### 参数说明

| 参数 | 说明        |
| ---- | ----------- |
| i    | install     |
| v    | verbose提示 |
| h    | hash 进度条 |

### rem包的升级

`rpm -Uvh [包全名]`

### 验证包管理

`rpm -Va`

`rpm -V [已安装的包名]`检测软件包是否被修改

#### 数字证书

数字证书导入：`rpm --import RPM-GPG-KEY-CentOS-7`

检测安装是否成功：`rpm -qa | grep gpg`

### rpm包中文件提取

`cpio`命令，原来是备份还原命令，但现在多用于提取文件

`rpm2cpio 包全名 | cpio -idv . 文件绝对路径`

## yum - 包管理工具

### 介绍

yum是一个shell前端软件包管理器，基于rpm包管理，能够从指定的服务器自动下载rpm包并且进行安装，可以自动处理依赖关系，并且一次安装所有依赖的软件包。

### 基本指令

`yum list | grep xx软件列表`    # 查询yum服务器是否有需要安装的软件

### 安装指定的yum包

`yum install xxx`    # 下载安装

### 搜索包

`yum search [包关键字]`

### 升级包

`yum update [包名]`

### 卸载

`yum remove 包名` 

yum的卸载命令会破坏依赖性

### yum组管理命令

- 查询可以安装的软件包组

  yum grouplist

- 查看软件包组具体信息

  yum groupinfo [包组名]



## 源码包的安装

注意事项：

1. 源码包安装一般需要指定安装位置：推荐`usr/local`
2.  

### 安装过程

1. 下载源码包

2. 解压

3. 进入解压目录

4. `./configure`（执行配置命令）

   `./configure --prefix=/usr/local/...`

   1. 安装前检测安装依赖
   2. 定义需要的功能选项（安装位置等）
   3. 把系统环境的检测结果和定义好的功能选项写入makefile文件

5. `make`编译

6. `make clean` 如果安装过程报错，需要执行这个命令

7. `make install`安装



### 源码包报错

1. 安装停止
2. 是否出现no。warning。error等



### 源码包打入补丁

#### 补丁的生成

`diff -Naur old new` 对比新文件和旧文件的不同，不同的部分就是补丁了，两个文件要写绝对路径。`-Naur`会自动生成补丁文件

#### 打入补丁

`patch -pn < 补丁文件` 按照补丁文件进行更新

选项n为数字，代表按照补丁文件中的，指定更新文件的位置。

## 脚本安装程序

类似于Windows端安装程序的方式，可以通过执行shell脚本来安装程序。
