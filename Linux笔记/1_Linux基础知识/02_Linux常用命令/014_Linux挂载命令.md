# Linux 挂载命令

## mount命令

Linux所有存储设备都必须挂载之后才能使用，包括硬盘

`mount [-l]` 查询系统中已经挂挂载的设备，-l会显示卷标名称

![image-20211215165203181](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215165203181.png)

### 挂载光盘

CentOS中光盘的设备文件名是、dev/sr0

```bash
mount -t iso9660 /dev/cdrom /dev/sr0
```

不管是光盘还是后面说到的U盘，用完之后需要卸载

```bash
umount /dev/sr0
```

### 挂载U盘

一般U盘被识别为/dev/sd

```bash
# 挂载
mount /dev/sdb1 /mnt/usb
# 卸载
umount /dev/sdb1
```

### 挂载NTFS分区

Linux中默认是无法使用NTFS格式文件系统的，因此需要使用第三方模块NTFS-3g

下载：http://www.tuxera.com/community/ntfs-3g-download/

安装：

1. 解压
2. 进入解压的目录
3. 编译安装
4. `./configure`
5. `make`
6. `make install`

挂载：

```bash
# mount -t ntfs-3g 分区设备文件名 挂载点
mount -t ntfs-3g /
```

