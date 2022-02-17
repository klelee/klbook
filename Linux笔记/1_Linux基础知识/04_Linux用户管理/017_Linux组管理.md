---
title: Linux组管理和权限管理
date: 2021-12-01 07:41:00
categories: Linux学习笔记
tags: Linux
---

在Linux中，每个用户必须属于一个组，不能独立于组外，在Linux中，每个文件有所有者，所在组，其他组的概念。

# 组

## 文件/目录 的所有者

一般为文件的创建者，谁创建了文件，就自然的成为了该文件的所有者。

### 查看文件的所有者

- ls -al 

  ![image-20211215150221950](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215150221950.png)

### 修改文件所有者

- `chown <username> <filename>`

  

  可见修改所有者之后其所在组不会发生变化



## 组的创建

### 基本指令

- `groupadd <group name>`

### 应用实例

1. 创建一个组，monster

   `groupadd monster`

2. 创建一个用户fox，并放入到monster中

   `useradd -g monster fox`

## 文件/目录所在组

### 文件/目录默认所在组

当某个用户创建一个文件后，这个文件的所在组就是用户所在的组

### 修改文件/目录所在的组

- `chgrp <group> <filename>`

### 应用实例

使用root用户创建文件orange.txt，看看当前文件属于哪个组，然后将这个文件所在组，修改到fruit组。

```shell
groupadd fruit
touch orange.txt
stat orange.txt
```

![image-20211215150320320](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215150320320.png)

当前orange.txt还属于root用户，root组，下面修改所在组为fruit

```shell
chgrp fruit orange.txt
```

![image-20211215150429784](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215150429784.png)

所在组修改成功。

#### 补充

- `stat <filename>` 可以查看文件当前的状态

## 其他组

除文件的所有者和所有组的用户外，系统的其他用户组都是文件的其他组

### 改变用户所在组(不推荐之间改变默认组，可以使用gpasswd加入附加组)

在添加用户时，可以指定将该用户添加到指定组，同样的用root的管理权限可以改变某个用户所在的组。

- 改变用户所在组
  1. `usermod -g <groupname> <username>`
  2. `usermod -d <目录> <username>` 改变该用户登录的初始目录

### 补充（该命令更常用）

`gpasswd` 把用户加入组或将用户从组中删除

```
gpasswd -a [user] [group]    # 将用户添加到组
gpasswd -d [user] [group]    # 将用户从组中删除
```





