搜索在我们日常使用电脑是很常用的功能，当日常使用中，我们更注重的是网络资源的搜索。今天的小知识点是，Linux本地文件的搜索。

# locate命令

停停停~，不是说介绍find吗？怎么突然开始locate命令啦？不要着急嘛，locate在有些时候也是很好用的哦！

有人说，locate是比find更好用的搜索工具呢！嘿嘿，我们慢慢介绍，其实在我看来两者各有千秋嘛！

## locate的基本语法

```bash
locate [选择参数] [样式]
```

样式？就是你想搜索的文件大致长什么样子啦！

## 应用实例

比如：你想搜索在/etc目录下的profile文件：

![image-20211214185217322](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214185217322.png)

很快，啊。嗖的一下子就出来这么多。

再比如，查找和pwd相关的所有文件：

![image-20211214185236319](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214185236319.png)

看图中圈出来的，所以你明白“样式”的含义了吗？就是你输入的locate后面的“pwd”以及“/etc/profile”对于这条命令来说，都是一个字符串，而locate就是在检索这个字符串而已。

# find命令

相对locate命令来说，find命令使用的情况就比较多了。搜索方式也多种多样，比如按照文件名搜索、按照文件大小搜索、按照修改时间搜索等等等等。下面我们一一来介绍。

## find命令-基本语法

```bash
find [路径][选项][内容]
```



## find命令-按照文件名搜索

这里的文件名可不是传统意义上的名，这个名可以包括，文件名、inode号等。

### 基本语法

```bash
find [路径] -name [内容]    # 按照文件名搜索
find [路径] -iname [内容]    # 按照文件名搜索，不区分文件名大小写
find [路径] -inum [内容]    # 按照文件的inode号查找
```

### 应用实例

在`findtest`文件夹内使用三种方式搜索`klfind`文件

![image-20211214192500406](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214192500406.png)

什么？什么是inode号？怎么查inode号，inode号，就理解为这个文件的身份证号吧！怎么查嘛！`stat <filename>`

![image-20211214192619590](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214192619590.png)

焯，那我都知道这个了，我能不知道文件在哪儿？别着急嘛，兴许以后就真的遇到这种情况了呢！

## find命令-按文件大小搜索

啊？我还得知道文件多大才能查？当然不是，这个选项可以使用大于和小于哦！

### 基本语法

```bash
find [路径] -size [filesize]    # 查找文件大小为filesize的文件
find [路径] -size [+filesize]    # 查找文件大小大于filesize的文件
find [路径] -size [-filesize]    # 查找文件大小小于filesize的文件
```

**注意**：这里的filesize可不是能乱写的，人家可以有单位的哦：

- 字节：c（小写）
- 双字节：w（小写）
- 千字节：k（小写）
- 兆字节：M（大写哦）
- 京字节（或者叫吉字节）：G（大写）

### 应用实例

在`root`目录下，搜索`/root/findtest`目录下，大于20字节的文件、等于567字节的文件和小于1k的文件。

![image-20211214193302991](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214193302991.png)

## find命令-按照权限搜索

你知道吗？权限也有具体的表示方法哦！可不光是大小。

### 基本语法

```bash
find [路径] -perm [权限值]    # 权限刚好为【权限值】的文件
find [路径] -perm [-权限值]    # 权限囊括【权限值】中所有权限的文件
find [路径] -perm [/权限值]    # 权限包含【权限值】中任一权限的文件
```

**注意**：关于权限，我后面会详细说明。

这里比较抽象，我们一一来介绍。

### 应用实例

- 实例一：查找`findtest`目录下，权限值为644的文件

  ![image-20211214193700734](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214193700734.png)

- 实例二：查找`findtest`目录下，权限值囊括655的文件

  - 分析：何谓囊括？644，三个数分别代表“用户权限”、“用户所在组权限”和“其他组权限”。囊括的意思就是，搜索到的文件的权限的三位数，要分别大于或等于644这三个数。加入文件中有一个文件的权限为755。观察这三个数，是不是每个都大于644呀？`7>6 5>4 5>4`.

    ![image-20211214194355532](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211214194355532.png)

- 实例三：查找`findtest`目录下，权限值包含644任一权限的文件

  - 有了上面囊括的解释，这里的包含任一就不难理解了，只要被检索文件的权限有644其中之一，或者大于其中之一就会被搜索出来。

    ![image-20211215142255562](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215142255562.png)

## find命令-按照时间搜索

这里就要多说几句，说明Linux中，几种常用的时间了。

![image-20211215142325370](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215142325370.png)

1. atime（Access time：读取时间）：当这个文件被读取时，就会更新这个时间，可以看到，上面的/etc/profile文件上次读取时间，就是今天的晚上七点多。
2. mtime（Modification time：修改时间）：当这个文件【内容】被修改之后，就更新这个时间。
3. ctime（status time：状态时间）：当这个文件的【状态】被修改之后，就会更新这个时间。？？？状态？？？，就比如：文件的权限呀属性呀什么的改变，这个时间就会改变的哦。

### 基本语法

这里就以mtime为例介绍了哦~

```bash
find [PATH] -mtime n    # 查找n天之前的【一天之内】被修改过的文章
find [PATH] -mtime +n    # 查找n天之前被修改过内容的文件（不包括n天这一天）
find [PATH] -mtime -n    # 查找n天内被修改过内容的文件（包括n天）
find [PATH] -newer <file>    # 查找比file文件更新的文件
```

### 应用实例

哎呀，太抽象了，赶紧举例子~

- 实例一：查找昨天修改内容的文件，由于系统是昨天装的，所以比较多

  `find . -mtime 1`

  ![image-20211215142511721](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215142511721.png)

- 实例二：查找10天以前修改过内容的文件（好家伙，，一下子多了起来）使用more查看

  `find . -mtime +10`

  ![image-20211215142610834](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215142610834.png)

- 实例三：查找近5天修改过内容的文件

  `find . -mtime -5`

  ![image-20211215142649837](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215142649837.png)

- 实例四：查找过去24小时内修改过内容的文件

  `find . -mtime -1`

  ![image-20211215142742135](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215142742135.png)

- 实例五：查找比hello.py文件还新的文件

  ![image-20211215143033848](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215143033848.png)

## find命令-按文件归属搜索

其实，文件归属这块可用的搜索项很多，我主要介绍几个我认为重要的，其他的我列出来：

```bash
-uid n    # 查找用户id号为n的文件
-gid n    # 查找组id号为n的文件
```

### 基本语法

下面四个我认为较常用

```bash
find [PATH] -user [username]    # 查找用户名为username的文件
find [PATH] -group [groupname]    # 查找组名为groupname的文件
find [PATH] -nouser     # 查找没有用户归属的文件（常见于Windows端建立的文件，没有归属用户）
find [PATH] -nogroup    # 查找没有组的文件
```

### 应用实例

- 实例一：在根目录查找属于wang的文件

![image-20211215143241930](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215143241930.png)

- 实例二：在root目录查找没有归属用户的文件，一般是没有的

  ![image-20211215143311953](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215143311953.png)

## find命令-按照文件类型搜索

### 基本语法

常见的文件类型有：

- f：普通文件
- b，c：设备文件
- d：目录
- l：链接
- s：socket
- p：FIFO

```
find [PATH] -type <filetype>    # 查找文件类型为filetype的文件
```

### 应用举例

- 实例：查找`/home/klelee`下的普通文件

![image-20211215143346382](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215143346382.png)

## find命令-逻辑运算

什么是逻辑运算？与或非是也！

### 基本语法

```bash
find [PATH] [option1] [p1] -a [option2] [p2]    # 查找选项1与选项二的条件同时符合的文件
find [PATH] [option1] [p1] -0 [option2] [p2]    # 查找符合选项一的条件或符合选项二条件的文件
find [PATH] -not [option] [p]    # 查找不符合选项条件的文件
find [PATH] ! [option] [p]    # 同上（注意感叹号是英文的感叹号，且感叹号两边都有空格）
```

### 应用实例

- 实例：查找根目录下属于wang的普通文件

  ![image-20211215143425045](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215143425045.png)

## find命令-其他选项

### -exec & -ok

它们的功能是，将前一个命令得到的结果传递给下一条命令作为参数，其中`-exec`会直接执行后面的命令，而`-ok`在执行后面的命令时会进行确认。

### 基本语法

```bash
find [PATH] [option] [p] -exec [commend] {} \;
find [PATH] [option] [p] -ok [commend] {} \;
```

上面个语法中，前面命令的查找结果，就将格式输出到`{}`的位置，作为后边命令的参数，而`\;`其实就是转义了一个分号表示语句结束，使用反斜杠是因为分号在bash语句中有特殊的用途 

### 应用实例

- 实例查找根目录下所有属于wang的普通文件并且使用ls按行输出到控制台

  ![image-20211215143555070](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215143555070.png)