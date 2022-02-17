# 启动引导程序

## grub

grub的保存位置：`/boot/efi/EFI/centos`

![image-20211216092611121](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211216092611121.png)

centos7版本的配置文件还是不好找的。但是在很多地方都有配置文件的软链接：

![image-20211216093133623](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211216093133623.png)

上面第二个哦，第一个是坏的，不能用。

但是呢？这个文件里有这么一句话：

![image-20211216095705578](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211216095705578.png)

这句话说：“你不要过来呀~，这个文件是通过grub2-mkconfig自动生成的”。哦，原来所有的设置都来自`/etc/default/grub`和`/etc/grub.d`呀，我们去看看：

首先来看`/etc/dufault/grub`

![image-20211216100753890](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211216100753890.png)

这个文件里写可能最有用的就是第一个了把，开机等待延时，一般不建议修改。5秒挺合适的。但是你要真的想要修改，就直接修改`/etc/default/grub`文件，然后通过grub2-mkconfig命令重新生成grub.cfg文件。

```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
```

再去看看他所说的`/etc/grub.d`文件夹下有什么：

![image-20211216101044530](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211216101044530.png)

这个文件夹下放置的是，grub界面的配置文件，生成grub.cfg文件时，grub.cfg会依次读取这个文件夹中的内容。

- 00_header

  这个脚本主要是一些基础设置和环境的配置，包括启动等待延时，默认启动项等。但是这里可不是设置呀，这个脚本会调用`/etc/dufault/grub`文件中的常量，然后写入到grub.cfg文件的哦，所以要修改还是要去`/etc/dufault/grub`。

- 00_tuned

  调谐文件，调谐是干嘛？官方说是。动态调正系统的性能。

- 01_users

  在这里可以加载一些用户的配置，也是这个问价加载grub2 的密码的。但是和上面同样，脚本只负责加载，要设置密码，需要编辑`/etc/dufault/grub`文件，加入需要的常量。

- 10_Linux

  分析/boot下面的文件，找到正确的Linux内核以及加载内核需要的模块和文件。

- 30_0s-prober

  这个文件回去寻找整个硬盘中存在的其他系统然后加载到启动项

- 41_custom

  自定义模块，自己还有什么相加的可以加在这里



## grub加密

正常情况下，在启动的时候，会提示输入e编辑此选项，如下图。这样可以直接编辑，但是为了安全起见可以给其设置一个密码

![image-20211214164718545](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214164718545.png)

为grub加密就需要重新配置grub.cfg，上面有提到，可以通过修改`/etc/default/grub`和`/etc/grub.d`重新配置：

### 首先，设置密码：

编辑`/etc/default/grub`在最后添加`GRUB2_PASSWORD=想要设置的密码`

```bash
vim /etc/default/grub
----------------------------
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="rhgb quiet"
GRUB_DISABLE_RECOVERY="true"
## 下面设置密码
GRUB2_PASSWORD="root"
```

### 重新生成grub.cfg

```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
```

![image-20211216111536517](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211216111536517.png)

最后一行输出Done表示生成成功

