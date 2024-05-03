之前写过一篇针对archlinux配置typora+picgo的文章。做完自己按照文章顺序走了一遍发现居然走不通，绝绝子！所以重新配置了一番，现在分享一下！

## 安装

archlinux的安装就不说了，相对来说网上资源丰富。安装后配置archlinuxcn源也不是什么难事，所以直接从安装picgo开始！

### 安装node.js 和 npm

```bash
sudo pacman -S nodejs
sudo pacman -S npm
```

### 安装picgo

这里容易失败，失败了就重新运行一下下面的命令即可！

```
sudo npm install picgo -g
```

### 安装typora

```
sudo pacman -S typora
```

到这里，所有需要安装的软件就安装完成了，接下来进行配置！

## 配置

声明：对于图床的选择，我个人不做任何推荐，我只是以阿里云作为演示！

### 购买阿里云OSS

打开[阿里云官网](https://www.aliyun.com/)直接搜素对象存储OSS进行购买。

![image-20220101075156467](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/790ca677199f19172600e9f074af5cc5.png)

购买完成后打开控制台，搜索或者直接找到对象存储OSS的控制台并进入！点击左侧的bucket列表

![image-20220101075902125](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/e0c7843f7e086951efe01e5ca2cb2677.png)

然后选择创建bucket，存储类型选择标准存储，地域根据需求选择！收费的东西不推荐开！

![image-20220101080045647](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/c31659a04044407b5545ff6c8dcc5784.png)

接下来，读写权限推荐**公共读**。然后确认即可。

![image-20220101080209936](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/ae3355013f5f5b39490e81ac494568f8.png)

然后确定创建就行！

最后建议对于bucket下的文件列表进行分类管理！

![image-20230402064417511](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230402064417511.png)

到这里购买就结束了，为了能够在picgo中使用阿里云的图床还需要获取一下accesskey！

同样在阿里云控制台，鼠标移动到右上角头像处，不用点击，会自动下拉菜单，选择AccessKey管理。

![image-20230402064640155](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230402064640155.png)

![image-20230402064710338](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230402064710338.png)

怎么说呢，除非你比较大牛，知名度比较高，更加注重安全性，这样的话建议使用子用户的Accesskey。个人日常使用的话，其实无所谓的。

![image-20230402064858672](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230402064858672.png)

点击创建，身份验证之后会自动创建AccessKey, 然后显示出来：

![image-20230402065031043](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230402065031043.png)

到这里相关信息准备完成。配置中你需要的信息有：

1. bucket名字
2. AccessKey ID
3. AccessKeySecret
4. 你的存储区域（例：oss-cn-beijing）
5. 存储路径，比如我的是：typora/    # 一定要带这个斜杠

### 配置picgo-core

```
picgo set uploader
```

这个过程需要手动去填相关的信息。

1. 首先是选择默认的图床名称，亲测在这里修改无效。随缘选，完了之后需要在config文件中修改！
2. AccessKey ID
3. AccessKeySecret
4. 存储区域
5. 存储路径

这样，一套配置模板就生成了。

然后编辑配置文件：

```
vim .picgo/config.json
```

![image-20230402070229054](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230402070229054.png)

主要任务就是确认这里是你需要的厂商的名字！



然后打开typora. 依次进入 File -> Preferences -> Image

![image-20230402065802055](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230402065802055.png)

根据上面图片该选的选，该改的改。最后的Command是自己安装picgo的位置，可以使用whereis命令查看，不过在archlinux安装我的方式来的话，一般都是这个。

