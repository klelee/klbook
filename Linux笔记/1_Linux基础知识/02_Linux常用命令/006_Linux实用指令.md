# Linux实用指令

## 指定运行级别

### 基本介绍

- 0：关机
- 1：单用户（找回丢失密码）
- 2：多用户状态没有网络服务
- 3：多用户状态有网络服务
- 4：系统未使用保留给用户
- 5：图形界面
- 6：系统重启

常用的运行级别是3和5，也可以指定默认运行级别

### 应用

可以使用`init [1,2,3,4,5,6]`来切换不同的运行级别

### 修改默认启动后的运行级别

在centos7以前，在/etc/inittab文件中。可以直接指定。

在centos7当中，进行了简化，

![image-20211214164531892](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214164531892.png)

可以通过systemctl设置默认启动级别

## Linux下如何找回root密码？

1. 首先，启动系统，进入开机界面，在界面按“e”进入编辑界面：

   ![image-20211214164718545](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214164718545.png)

2. 进入编辑界面，使用键盘上的上下键把光标往下移动，找到以““Linux16”开头内容所在的行数”，在行的最后面输入：init=/bin/sh。如图：

   ![image-20211214164849052](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214164849052.png)

3. 接着，输入完成后，直接按快捷键：Ctrl+x 进入***\*单用户模式\****。

4. 接着，在光标闪烁的位置中输入：mount -o remount,rw /（注意：各个单词间有空格），完成后按键盘的回车键（Enter）。如图：

   ![init=/bin/sh](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214165030808.png)

5. 在新的一行最后面输入：passwd， 完成后按键盘的回车键（Enter）。输入密码，***\*然后再次确认密码即\****可(***\*韩顺平提示: 密码长度最好8位以上,但不是必须的\****), 密码修改成功后，会显示passwd.....的样式，说明密码修改成功：

   ![image-20211214165221015](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214165221015.png)

6. 接着，在鼠标闪烁的位置中（最后一行中）输入：touch /.autorelabel（注意：touch与 /后面有一个空格），完成后按键盘的回车键（Enter）

7. 继续在光标闪烁的位置中，输入：exec /sbin/init（注意：exec与 /后面有一个空格），完成后按键盘的回车键（Enter）,等待系统自动修改密码(***\*韩顺平提示：这个过程时间可能有点长，耐心等待\****)，完成后，系统会自动重启, ***\*新的密码生效\****了

   这里会闪一会儿，不要着急

   ![image-20211214165516069](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214165516069.png)

   重启之后，新密码登录

   ![image-20211214165638943](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214165638943.png)

## Linux下帮助指令

- man指令

  基本语法：`man [命令或配置文件]` 获取帮助信息

- help指令

  基本语法：`help 命令` 获得shell内置命令的帮助信息

## 时间日期类指令

- date
  - date：显示当前时间
  - date +%Y
  - date +%m
  - date +%d 
- date指令设置日期
  - `date -s <字符串时间>`

- cal：显示当前日历
