本文基于archlinux系统，搭配dwm窗口管理器进行配置

## fcitx5安装

一些基础包的安装，关于fcitx5框架我们只需要安装fcitx5-im包就可以，这个包实际上就包含了：

- fcitx5
- fcitx5-configtool
- fcitx5-gtk
- fcitx5-qt

除此之外再安装中文插件和基本的主题

```
sudo pacman -S fcitx5-im fcitx5-chinese-addons fcitx5-material-color
```

## fcitx5配置

### 配置开机自启

使用窗口管理器的话，需要手动配置fcitx5自动重启，dwm使用的是X11，所以可以在xinitrc中配置fcitx5的自动重启！

只需要在`~/.xinitrc`中添加：

```
fcitx5 &
```

![image-20230601220154233](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230601220154233.png)

然后重启dwm！重启之后在系统托盘中就会显示fcitx5的图标：

![image-20230601220346800](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230601220346800.png)

### fcitx5环境变量配置

```
vim ~/.pam_environment
-----------------------
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
```

### 开启中文输入法

我们需要手动运行fcitx5-configtool,如果你安装了rofi，可以直接使用rofi启动：

![image-20230601220549007](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230601220549007.png)

如果没有安装rofi，也可以直接在terminal中输入`fcitx5-configtool`启动。

启动之后，第一步就是取消下面的对勾：

![image-20230601220712717](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230601220712717.png)

然后将右侧拖到最下面，选中“pinyin”，然后点击向左的箭头，表示启动pinyin输入法，然后点击Apply！

![image-20230601220937204](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230601220937204.png)

### 输入法全局配置





### 输入法插件配置

#### 1. 用户界面配置

打开fcitx5-configtool之后，进入Addons，然后点击Classic User Interface菜单的configure按钮！

![image-20230601222000811](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230601222000811.png)

可以在这里配置主题：

![image-20230601222434407](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230601222434407.png)

![image-20230601222503081](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230601222503081.png)

然后点击OK，回去之后Apply！

#### 2.字库配置

首先来配置云字库， 继续在Addons窗口，下拉找到Pinyin菜单，点击configure。

![image-20230601222707404](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230601222707404.png)

启用Cloud Pinyin

![image-20230601222828423](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230601222828423.png)

不要退出，继续下拉找到，Cloud Pinyin配置一下云字库

![image-20230601222932194](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230601222932194.png)

将Backend选项改成baidu

![image-20230601223008564](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230601223008564.png)

配置离线字库

安装相关软件包

```
sudo pacman -S fcitx5-pinyin-zhwiki fcitx5-pinyin-moegirl
```

上面的离线字库，安装即用，不用再进行多余的配置！



