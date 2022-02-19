Linux下解压缩不想Windows下那么方便，但实际工作中，打包与解包确实很常用的技能。所以熟练掌握很重要。

## 压缩与解压缩

### gzip/gunzip指令

gzip命令对文件有60%~70%的压缩率。

- gzip：用于压缩文件，压缩后文件格式为：`xxx.gz`

  - 基本语法

    `gzip [选项] [源文件]`

  - 常用选项

    | 参数               | 描述                   |
    | ------------------ | ---------------------- |
    | -d  ：--decompress | 解开压缩文件           |
    | -l  ：--list       | 列出压缩文件的相关信息 |
    | -L  ： --license   | 显示版本与版权信息     |
    | -r  ： --recursive | 递归压缩               |
    | -v  ： --verbose   | 显示指令执行过程       |

  - 应用实例

    1. 把abcd目录下的每个文件压缩成.gz文件

       在abcd目录下执行： `gzip *` 

       ![image-20211215145148736](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215145148736.png)

    2. 把上例中每个压缩的文件解压，并列出详细信息

       在abcd目录下执行： `gzip -dv *`

       ![image-20211215145219398](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215145219398.png)

    3. 详细显示例1中每个压缩文件的信息，并不解压

       在test6中执行： `gzip -l *`

       ![image-20211215145248166](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215145248166.png)

    4. 递归压缩文件

       `gzip -rv abcd`

       ![image-20211215145432017](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215145432017.png)

       这样的话，abcd下的所有文件都变成了.gz文件，目录依然存在，只是目录里面的文件相应变成了.gz格式文件。这就是压缩和打包的不同。因为是对目录的操作，因此需要加上-r递归压缩。

    5. 递归的解压目录

       `gzip -dr abcd`

       ![image-20211215145526825](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215145526825.png)

       又回到最初的起点！

- gunzip：用于解压缩文件。事实上，gunzip是gzip的硬连接，因此不论是压缩还是解压缩，都可以通过gzip单独完成。换句话说：gunzip和`gzip -d` 是相同的作用


### zip/unzip指令

- zip：用来解压缩文件，或者对文件进行打包操作。zip是个使用广泛的压缩程序，文件经它压缩后会生成具有.zip后缀的压缩文件。

  - 基本语法

    `zip [options] [参数]`

  - options（选项）

    | 选项 | 描述                                           |
    | ---- | ---------------------------------------------- |
    | -q   | 不显示指令执行过程                             |
    | -r   | 递归处理，将指定目录下所有文件和子目录一并处理 |
    | -d   | 从压缩文件内删除指定的文件                     |

  - 参数

    - zip压缩包：指定要创建的zip压缩包
    - 文件列表：指定要压缩的文件列表

  - 应用实例

    1. 将`/home/klelee/html/` 这个目录下所有文件和文件夹打包为当前目录下的html.zip

       `zip -q -r html.zip /home/klelee/html/`

       ![image-20211215145704384](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215145704384.png)

    2. 压缩效率选择

       `1 ~ 9  ===>   faster ~ better`

- unzip：用于解压由zip命令压缩的压缩包（.zip）

  - 基本语法

    `unzip [选项] [参数]`

  - 选项

    | 选项     | 描述                                    |
    | -------- | --------------------------------------- |
    | -n       | 解压缩时不要覆盖原文件                  |
    | -d<目录> | 指定文件解压缩后要存储的目录            |
    | -v       | 执行时显示详细信息                      |
    | -o       | 不必先询问用户，unzip执行后覆盖原有文件 |
    | -r       | 递归压缩                                |

  - 参数

    - 压缩包：指定要解压的压缩包

  - 应用实例

    1. 将压缩文件text.zip在当前目录下解压缩

       `unzip text.zip`

    2. 将压缩文件text.zip在指定目录/tmp下解压缩，如果已有相同的文件存在，要求unzip命令不覆盖原先的文件

       `unzip -n text.zip -d /tmp`

    3. 查看压缩文件目录，但不解压缩

       `unzip -v text.zip`

    4. 将压缩文件text.zip在指定目录/tmp下解压缩，如果已有相同的文件存在，要求unzip命令覆盖原先的文件

       `unzip -o text.zip -d /tmp`

    5. 解压指定文件，*用作通配符

       `unzip text.zip "*.jpg"`

