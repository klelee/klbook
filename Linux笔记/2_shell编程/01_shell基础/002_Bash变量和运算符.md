# Bash和运算符变量

## 变量概述

### shell变量的命名规则

- 变量名称可以由字母、数字和下划线组成，但是不能以数字开头。

- 在bash中，变量的默认类型都是字符串型，如果要进行数值运算，则必须指定变量类型为数值型。

- 变量用用等号连接值，等号左右不能有空格。（格外注意）

- 变量的值如果有空格，则必须使用双引号包裹变量值

- 在变量的值中可以使用转义字符

- 如果需要增加变量的值，那么可以进行变量值的叠加。不过变量需要用双引号包含”$变量名“或者${变量名}

  ![image-20211218103139381](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218103139381.png)

- 环境变量名建议大写，便于区分。

- 如果把命令的结果作为变量值赋予变量，则需要使用反引号或$()包裹命令

  ![image-20211218103416117](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218103416117.png)

### 变量的分类

- 用户自定义的变量：最常见的变量，由用户自由定义变量名和变量的值
- 环境变量：这种变量主要保存的是和系统操作环境相关的数据。
- 位置参数变量：这种变量主要用来向脚本中传递参数或数据的，变量名不能自定义，变量作用是固定的。
- 预定义变量：bash中已定义号的变量，变量名不能自定义，变量的作用是固定的

## 用户自定义变量

### 变量定义

按照变量的命名规则取名，然后通过等号赋值，等号两边不能有空格。

```shell
name=klelee
```

### 变量的调用

```shell
echo $name
> klelee
```

### 变量查看

变量查看直接使用set命令会输出很多内容，所以可以采用：

`set | more`    分屏查看

`set | grep "xxx"`    搜索想要看到的信息

```shell
set [option]
options:
    -u:如果设定此选项，调用未声明变量时会报错
    -x：如果设定此选项，在命令执行之前，会把命令先输出一次
    
```

### 删除变量

```shell
unset <变量名>
```

![image-20211218104248077](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218104248077.png)

## 环境变量

### 用户自定义环境变量的设置

```bash
export age="10"
```

### 环境变量查询和删除

#### 查询环境变量

`set` 很 `env` 都可以看到环境变量

#### 删除环境变量

`unset`

### 系统默认环境变量

```bash
[root@klelee shell]# env
XDG_SESSION_ID=130                 
HOSTNAME=klelee.com ----------------------------> 主机名
TERM=xterm ----------------------------> 终端
SHELL=/bin/bash ----------------------------> shell环境
HISTSIZE=1000 ----------------------------> 历史命令条数
SSH_CLIENT=192.168.200.1 57448 22 ----------------------------> 客户端IP
SSH_TTY=/dev/pts/0 ----------------------------> ssh链接终端
USER=root ----------------------------> 当前用户
LS_COLORS=rs=…… ----------------------------> 定义颜色显示
MAIL=/var/spool/mail/root ----------------------------> 邮箱设置
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin -----------> PATH环境变量
PWD=/root/shell ----------------------------> 当前目录
LANG=en_US.UTF-8 ----------------------------> 语系
HISTCONTROL=ignoredups 
SHLVL=1 ----------------------------> shell层数
HOME=/root ----------------------------> 家目录
LOGNAME=root ----------------------------> 登录用户
SSH_CONNECTION=192.168.200.1 57448 192.168.200.199 22 --------------------->ssh登录信息 
LESSOPEN=||/usr/bin/lesspipe.sh %s
XDG_RUNTIME_DIR=/run/user/0
OLDPWD=/root
_=/usr/bin/env
[root@klelee shell]# 

```

不属于环境变量，但是比较重要的变量内容，只能用set查看

```bash
BASH=/bin/bash  -----------> bash所在目录
BASH_VERSINFO=([0]="4" [1]="2" [2]="46" [3]="2" [4]="release" [5]="x86_64-redhat-linux-gnu") -----------> bash版本信息
BASH_VERSION='4.2.46(2)-release' -----------> bash版本
MACHTYPE=x86_64-redhat-linux-gnu -----------> 软件兼容类型，x86_64
MAILCHECK=60 -----------> 每60秒扫描邮件
MAIL=/var/spool/mail/root -----------> 邮件保存位置
PS1='[\u@\h \W]\$ ' ----------->  命令提示符
PS2='> '
UID=0 ----------->  当前用户ID

```

#### PATH变量

系统命令查找的路径

![image-20211217090053937](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211217090053937.png)

当在bash中输入命令时，bash会带着输入的命令，去PATH环境变量指定的所有路径下去找相应的命令文件，然后执行。

#### PS1变量

可以进行命令提示符设置。

![image-20211217090537577](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211217090537577.png)

##### 永久设置PS1

PS1 的配置文件在`/etc/bashrc`文件中

![image-20211217091129390](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211217091129390.png)

#### LANG语系变量

- 查看当前bash的语系

  ![image-20211217091235816](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211217091235816.png)

- 查看支持的语系

  ![image-20211217091350203](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211217091350203.png)

![image-20211217091425612](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211217091425612.png)

支持791种语言，包括中文。
