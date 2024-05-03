项目使用的是广和通的SC806，平台是高通的msm8909。本文基于该项目，简单记录修改开机logo和开机动画的过程。

## 开机logo修改

开机logo就是从bootloader开始到Android起来这段时间的一个屏幕展示效果，一般都是一张静态的图片。要修改此logo最好选用一张和屏幕分辨率一致的图片进行替换。   
比如我的项目使用的屏幕分辨率是1280x720的，那就准备一张大小为1280x720的图片作为更换的logo。

### 环境准备
生成splash.img镜像是需要使用到python的PIL第三方库，所以需要来安装它。   
``` bash
sudo apt install python-pil
```

### 生成splash.img
源码中splash.img镜像存储的位置是：`Android/device/qcom/common/display/logo/` 
![logo_dir](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image.png)

生成splash.img镜像的命令格式是：

```bash
python logo_gen.py <logo_filename>
```

例如：

```bash
python logo_gen.py aucma_logo.png
```

执行完上面的命令之后就会生成一个splash.img镜像。然后重新刷入该进行就完成了开机logo的替换。

### 刷入splash.img

```bash
adb reboot bootloader
fastboot flash splash splash.img
```

或者也可以使用QFIL进行刷机。



## 开机动画

开机动画的配置相对来说复杂了一些，需要将视频拆帧，需要配置帧率配置循环等等。

### 视频拆帧

视频拆帧可以借助Potplayer工具进行，[potplayer官网](https://potplayer.daum.net/?lang=zh_CN)  。

点击Potplayer左上角的名称可以打开菜单：

![image-20231205145436641](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20231205145436641.png)

首先打开需要拆帧的视频文件：

打开视频文件之后视频会自动播放一遍，无所谓的，继续下一步即可。

![image-20231205150351827](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20231205150351827.png)

依次选择： 视频 -> 图像截取 -> 连续截图

![image-20231205145616347](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20231205145616347.png)

根据实际情况进行帧图片保存位置的配置、文件前缀名和命名方式，最重要的是截取的图片的大小。尺寸设置出，选择截取为自定义尺寸大小，然后改成自己屏幕对应大小。格式最好设置为JPEG和Android默认的开机动画格式一致。

![image-20231205145858205](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20231205145858205.png)

上面的选项配置完毕之后点击开始，然后重新开始播放视频即可。

待视频播放完成之后，在上面设置的目录中就出现了刚刚截取到的图片：

![image-20231205150702963](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20231205150702963.png)



### 配置bootanimation

建议将上面截图的图片打包传到Linux环境下进行之后的操作。

Android 的 bootanimation.zip在 `Android/vendor/qcom/proprietary/qrdplus/Extension/apps/BootAnimation/`目录下。

![image-20231205151311542](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20231205151311542.png)

直接在此目录下操作就可以。首先解压该文件到当前目录 `unzip bootanimation.zip bootanimation`   解压后得到bootanimation目录：

![image-20231205152719702](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20231205152719702.png)

开机动画本身也分为两个部分，一般我们将动态的放在第一部分也就是part0，将静态部分放在第二部分也就是part1. 如我的第一部分就是动画的过程：

![image-20231205152949562](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20231205152949562.png)

第二部分展示动画结束之后的静态部分：

![image-20231205153039702](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20231205153039702.png)

最后 desc.txt文件是动画指令，指示动画该如何播放。

![image-20231205153159174](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20231205153159174.png)

- `1280 720` 表示的是图片的大小
- `24` 表示帧率，每秒播放24张图片
- `p 1 0 part0` p表示标志符， 1表示循环次数为1次， 0 表示播放后延迟时间为 0
- `p 0 0 part1` p表示标志符， 0表示无线循环， 0 表示播放后延迟时间为 0

根据上面的结束，自己配置相关的part和desc.txt文件之后，打包这些文件。

这里有一个巨坑：

1. 不要使用系统的压缩工具；
2. 不要直接在`Android/vendor/qcom/proprietary/qrdplus/Extension/apps/BootAnimation/` 目录下打包

就在part0 、 part1 和 desc.txt文件所在的目录下压缩：

```bash
zip -r -0 bootanimation.zip part0 part1 desc.txt
```

然后用得到的bootanimation.zip，替换掉上一级目录的bootanimation.zip。

### 开机动画测试

测试的过程中可以直接将得到的bootanimation.zip文件push到`/system/media/`目录下：

```bash
adb push bootanimation.zip /system/media/
```

然后直接重启机器。等待过了开机logo就可以看到替换了的开机动画了。



等待确认没有问题的话，可以直接编译system镜像进行烧录。
