## 前言

【前言都是废话，可以直接看解决方案】事情是这样的，最近在做IMX6ULL的开发，刚开始就遇到了这个拦路虎问题，我使用的闪迪的TF卡32GB的，搭配绿联的读卡器使用。在windows以及物理机装的archlinux都能正常识别并进行挂载，离谱的就是在虚拟机上识别不了。

- 虚拟机版本： VMware Workstation pro 17
- ubuntu版本： ubuntu18.04

## 问题现象

可以看到鼠标所在的这个位置USB连接是灰色的

![image-20230915232817963](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230915232817963.png)

U盘连接的位置为灰色，无法连接！

## 解决方案

编辑虚拟机配置文件！

1. 关闭虚拟机

2. 打开虚拟机配置文件

   虚拟机配置文件的名字`ubuntu1804.vm` 和其他文件名字一样，我们需要通过文件类型辨别，如下，可以使用VScode或者记事本打开该文件进行修改。

   ![image-20230915233247499](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230915233247499.png)

3. 修改配置文件

   第28行，默认值为FALSE，将这里改为TRUE即可

   ![image-20230915233346474](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230915233346474.png)

## 验证修改

再次开启虚拟机ubuntu1804查看USB连接位置：

![image-20230915233630589](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230915233630589.png)

已经可以连接！
