# 分区

## lsblk

查看当前系统分区情况

```
lsblk -f    # 显示详细的分区信息
```

![image-20211215200614488](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215200614488.png)

## cfdisk分区工具

简单易用的分区工具，什么fdisk遭淘汰了

```
cfdisk /dev/sda    # 对sda磁盘进行分区
```

![image-20211215200714779](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215200714779.png)

## 格式化

格式化不是为了清除数据！格式化不是为了清除数据！格式化不是为了清除数据！

格式化是为了建立合适的文件系统

比如EFI需要的就是Vfat文件系统，swap分区需要的就是swap文件系统，根分区需要的是xfs文件系统。格式化就是为不同的分区建立不同的文件系统的。

```
mkfs.vfat /dev/sda1    # 将/dev/sda1格式化为vfat文件系统
mkfs.xfs -f /dev/sda3    # 强制将/dev/sda3 格式化为xfs文件系统
## swap就不一样啦
mkswap /dev/sda2
```

## 建立合适的挂载点

建立空目录嘛 `mkdir`

## mount 挂载

## 查看挂载情况

`lsblk -f`

## 自动挂载

若需要磁盘自动挂载，需要修改fstab文件：`/etc/fstab`

![image-20211215201512410](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215201512410.png)

### 文件中每一列的含义

1. 设备文件名，可以使用UUID表示

2. 挂载点

3. 文件系统类型

4. 挂载选项

5. 是否可以被备份

   0 表示不备份    1表示每天备份    2表示不定期备份

6. 是否检测磁盘fsck

   0表示不检测    1表示启动时检测    2表示启动后检测

7. 
