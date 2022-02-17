# shell概述

## 什么是shell？

就是一个命令解释器，将Linux命令解释成计算机可以识别的语言。

## shell的分类

shell的两种主要语法类型有：Bourne和C，这两种语法互不兼容。

Bourne：sh、ksh、Bash、Psh、zsh

C：csh、tcsh（bash和zsh不同程度上支持Csh语法）

在Linux系统的`/etc/shells` 文件中可以查看系统支持的shell

![image-20211218084334460](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218084334460.png)

## shell脚本的执行方式

![image-20211218091945365](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218091945365.png)

对于脚本`hello.sh`，主要有两种执行方式：

### 赋予执行权限直接运行

这是shell最常用的脚本执行方式，也是最简单直接的。

1. 先赋予脚本可执行权限

   `chmod 755 hello.sh`

2. 然后通过文件路径执行

   1. 相对路径：`./hello.sh`
   2. 绝对路径：`/root/hello.sh`

   ![image-20211218092302469](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218092302469.png)

### 通过Bash调用脚本执行

这里说一下bash执行命令的流程，首先是在shell接收到命令，然后去环境变量中寻找这个命令，找到之后执行，下面看一下环境变量的目录：

![image-20211218092504845](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218092504845.png)

也就是说这种方法就是将写的脚本放到上面列出的这几个目录中，bash就可以调用执行了。但是，除非是每天都要使用的脚本，其它不建议放在环境变量下。

## bash的基本功能

### 历史命令

#### 基本语法

```shell
history [选项] [历史命令保存文件]
```

#### 常用选项

- `-c` ： 清空历史命令    `history -c`
- `-w` ： 把缓存中的历史命令写入历史保存文件。默认保存路径为`~/.bash_history`

#### 历史命令的调用

1. 上下箭头调用
2. `!<命令号码>` 调用命令
3. `!!`执行上一条命令
4. `!<str>` 重复执行以str开头命令
5. `!$` 重复上一条命令的最后一个参数

#### 应用实例

实例一：查看历史命令

`history` 

![image-20211216143122385](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211216143122385.png)

这个命令是查看的家目录下的`.bash_history`文件

实例二：重复执行历史命令第612条

![image-20211218094108814](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218094108814.png)



### 命令与文件的补全

在bash中，输入命令或文件路径名称时，可以使用`tab`键进行代码补全

### 别名

#### 基本语法

```shell
alias    # 查看当前系统存在的别名
alias 别名='原命令'    # 设置新的别名
```

![image-20211216144040370](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211216144040370.png)

希望永久生效的别名可以写入在用户目录下的`.bashrc`文件下

```bash
vim ~/.bashrc
```

![image-20211216144140722](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211216144140722.png)

#### 别名的优先级

1. 第一顺位执行命令的绝对路径或相对路径
2. 第二顺位执行别名
3. 第三顺位执行bash命令
4. 第四顺位执行按照$PATH环境变量定义的目录查找顺序找到的第一个命令

这里最值得注意的就是2，3顺位，别名的优先级更高

### bash常用的快捷键

![image-20211218094910075](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218094910075.png)

### 输入输出重定向

#### bash的标准输入输出

| 设备   | 设备文件名  | 文件描述符 | 类型         |
| ------ | ----------- | ---------- | ------------ |
| 键盘   | /dev/stdin  | 0          | 标准输入     |
| 显示器 | /dev/stdout | 1          | 标准输出     |
| 显示器 | /dev/stderr | 2          | 标准错误输出 |

#### 输出重定向

将命令的输出不在输出到显示器而是其他地方

- `>` :覆盖重定向 ------------------------------------------------------------------------------- 常用

  ![image-20211219210307095](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211219210307095.png)

- `>>`：追加重定向 ------------------------------------------------------------------------------- 常用

  ![image-20211219210335601](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211219210335601.png)

但是上面的只能表示正确的输出

- `2>文件` 错误输出覆盖重定向

- `2>>文件` 错误信息追加重定向

  ![image-20211219211037748](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211219211037748.png)

但是上面的只能在错误的情况下才重定向

- `&>文件` 正确信息和错误信息都覆盖重定向到文件

- `&>>文件`  正确信息和错误信息都追加重定向到文件 

  ![](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211219211318937.png)

- `command >> 文件1 2>>文件2` 把正确的执行写到文件1，错误的写到文件2 --------------------------------------------- 常用

  ![image-20211219211830362](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211219211830362.png)

#### 输入重定向

- wc命令

  基本选项：

  ​	`-c`     统计字节数

  ​	`-w`    统计单词数

  ​	`-l`    统计行数

### 多命令顺序执行

1. 在多条命令后面加分号就可以一次性执行多条命令

   `ls ; date; cd /etc;`

2. 在两条命令中间加入`&&`符号时，当前面的命令正确执行之后，后面的命令就可以正确执行，否则不能执行

   `ls && date`

3. 在两条命令中间加入`||`符号时，当前面的命令不正确执行之后，后面的命令就可以正确执行；当前面的命令正确执行，则后面的命令不能执行

4. 利用`&&`和`||`可以判断命令有没有正确执行

   `ls && echo yes || echo no` 类似于if的判断

### 管道符

`|` 将前一条命令的输出传递给下条命令作为输入

### 通配符

详见find和grep的前世今生

### bash中的特殊符号

`''` ：单引号。在单引号中所有的特殊符号，都没有其特殊含义

`""`：双引号，在双引号中特殊符号都没有特殊含义，但是`$`和"`"(反引号)是例外，拥有调用变量的值、引用命令和转义符的特殊含义

`&()`：用来调用系统命令。

`()` ：用于一串命令执行时，（）中的命令会在子shell中执行

`{}` ：用于一串命令执行时，{}中的命令会在当前shell中执行。也可以用于变量变形与替换。

`[]` ：用于变量的测试

`#` ：在shell脚本中，#开头的表示注释

`$` ：用于调用变量的值

`\` ：转义字符，跟在\之后的特殊符号将时区特殊含义。

