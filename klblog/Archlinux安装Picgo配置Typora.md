Typora堪称为markdown界的老大哥，其大名我们多有耳闻，所见即所的就是他的特点。但是在日常使用中，也经常会碰到一些特别的需求，比如：希望图片能够上传到云端。

怎么将markdown即时粘贴的文件上传到云端？可以采用`Typora + picgo-core + oss`对象存储。

# Archlinux 安装Typora和PicGo-Core并配置使用

## 安装Typora

在archlinux中安装Typora需要配置archlinux CN源。

### 添加中科大的archlinux源

```shell
vim /etc/pacman.conf
# 不会用vim的同学应该不会看这篇文章的吧
---------------------------------
# 在最后一行接着输入
[archlinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```

### 更新源文件、安装gpg密钥

```shell
sudo pacman -Syy
sudo pacman -S archlinuxcn-keyring
```

### 安装Typora

```bash
sudo pacman -S typora
```

## 安装PicGo-Core

PicGo-Core需要使用node去安装，所以这里首先需要安装node.js和npm

### 安装node.js和npm

没错，这俩需要分开安装

```bash
sudo pacman -S nodejs
sudo pacman -S npm
```

### 安装PicGo-Core

这里使用npm安装，但是还是需要管理员权限，所以记得加`sudo`

```bash
sudo npm install picgo -g
# -g 参数表示全局安装
```

到这里整个的安装接结束了。接下来去购买对象存储oss

## 购买对象存储oss

首先打开[阿里云](https://www.aliyun.com) ，直接搜索对象存储OSS

![image-20220101074436883](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220101074436883.png)

这里选择立即购买

![image-20220101074900538](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220101074900538.png)

这里资源包类型选择“标准存储包”，地域选择距离你最近的、存储包规格40G足够了、最后根据自己需要选择购买时常。

![image-20220101075156467](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220101075156467.png)

到这里购买完成。

## 整体配置

### 配置对象存储oss

进入[阿里云控制台](https://homenew.console.aliyun.com/)，点击左上角黄色的菜单按钮，选择对象存储oss

![image-20220101075706732](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220101075706732.png)

到这里选择bucket列表，然后点击新建bucket

![image-20220101075902125](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220101075902125.png)

根据自己的情况填写一下信息，oss版本控制按需开启，个人觉得用不着，但是如果你的资料比较重要，推荐开启。

![image-20220101080045647](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220101080045647.png)

接下来，读写权限推荐公共读。然后确认即可。

![image-20220101080209936](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220101080209936.png)

### 配置Access Key

同样实在阿里云控制台，鼠标移动到右上角头像处，不用点击，会自动下拉菜单，选择AccessKey管理

![image-20220101080510405](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220101080510405.png)

这里选择使用子用户，然后根据自己信息创建一个子用户

![image-20220101080539589](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220101080539589.png)

创建好之后点击你的用户登录名

![image-20220101080707686](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220101080707686.png)

拉到下面，创建AccessKey，创建好之间可以暂时下载一下CVS文件，这个页面管理，密钥就看不到了，所以需要下载文件。

![image-20220101080801224](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220101080801224.png)

然后往上拉，看到权限管理，点击

![image-20220101081035841](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220101081035841.png)

选择添加权限，将管理对象存储服务权限添加给子用户。到这里就ok了。

完成这些之后，你需要有以下四个数据：

1. bucket名字
2. AccessKey ID
3. AccessKeySecret
4. 你的存储区域（例：oss-cn-beijing）

### 自动配置PicGo-Core

原理上这个是可以直接修改配置文件的，但是不知道为什么我直接修改配置文件后，Typora报错，所以还是自动配置吧，使用`picgo set uploader`

```bash
picgo set uploader
```

这里选择aliyun

![image-20220101081708558](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220101081708558.png)

之后需要填写的分别是，上面说到的四个数据，我就不截图了。其中path建议填写：`image/`注意后面的斜杠不可少，名字可以自己写。options和customurl可以不填写，直接回车即可。

### 配置Typora

文件-偏好设置-图像，依次选择图中的选项，就ok了，然后验证图片上传选项，试一下是否可以上传成功。

![image-20220101082512542](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20220101082512542.png)

到这里好像就结束了，有问题评论区见。