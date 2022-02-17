# echo命令

用于在shell中打印shell变量的值，或者直接输出指定的字符串。

## 基本语法

```shell
echo [选项] [输出内容]
```

## 常用选项

- `-e` ： 支持反斜杠控制字符转换
- `-n` ：取消输出后行末的换行符号

- 在echo中使用-e参数后，支持控制的字符：

  ![image-20211218085147965](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218085147965.png)

## 应用实例

实例一：输出字符串

![image-20211218085256056](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218085256056.png)

**注意：echo在输出带有空格的字符串的时候，需要给字符串加双引号**

实例二：使用`-n`参数输出字符串

![image-20211218090129855](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218090129855.png)

实例三：使用`-e`参数输出内容

![image-20211218090352270](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218090352270.png)

**注意：echo输出的字符串最后一位如果为”!“，需要在其后面添加一个空格！**

实例四：使用echo输出颜色

```shell
echo -e "\e[1;31m hello world \e[0m"
```

![image-20211218091501544](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218091501544.png)

**注意：echo能识别的颜色不多，有：30m=黑色，31m=红色，32m=绿色，33m=黄色，34m=蓝色，35m=洋红，36m=青色，37m=白色

![image-20211218091828128](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218091828128.png)