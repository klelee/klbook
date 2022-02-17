# 小知识

## 键盘接收

`read [option] [变量名]`

### 常用选项

```
-p "提示信息"    在等待read输入时，输出提示信息
-t 秒数         read命令会一直等待用户输入，使用此选项可以指定等待时间
-n 字符数       read命令只接受指定的字符数，就会执行，这个参数默认不换行
-s             隐藏输入的数据，适用于机密信息的
```

![image-20211218113522709](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218113522709.png)

## 数值运算方法

1. declare方法
2. 使用expr和let命令（不常用）
3. $((运算式))

这三种方法中，最常用的依然是第三种方法，前两种做了解就好。

### 使用declare生命变量类型

由于shell所有的变量默认类型都是字符串类型的，因此在数值运算中很不方便，这时候就可以使用declare声明变量为数值类型。

#### 基本语法

```bash
declare [+/-][option] 变量名
```

#### 常用选项

```
-: 给变量设定类型属性
+: 取消变量的类型属性
-a: 将变量声明为数组
-i: 将变量声明为整数型
-r: 将变量指定为只读类型
-x: 声明变量为环境变量
-p: 查询变量属性或取消变量属性
```

- 数值运算

  ![image-20211218122332544](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218122332544.png)

- 数组运算

  数组：相同数据类型的元素按一定顺序排列的集合

  有关数组的声明，以下两种声明方式都是可以的：

  ```shell
  declare -a name[0]="klelee"
  name[1]=wang
  ```

  ![image-20211218122901589](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218122901589.png)

  数组的输出：

  - 输出全部元素`echo ${name[*]}`

    ![image-20211218123016404](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218123016404.png)

  - 输出单个元素`echo ${name[1]}`

    ![image-20211218123119086](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218123119086.png)

  - 输出首元素`echo ${name[0]}`或者`echo ${name}`

    ![image-20211218123238099](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218123238099.png)

- 环境变量

  declare也可以用来声明环境变量，和export的作用时一样的

  ```shell
  declare -x test=111    # 把test声明为环境变量
  ```

  ![image-20211218124103512](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218124103512.png)

- 只读属性

  一旦给变脸设定了只读属性，这个变量既不能修改变量的值，也不能删除变量，甚至不能使用+r选项取消只读属性。但是这个命令时命令行声明的，重新登录之后，变量就消失了。

  ```shell
  declare -r test
  ```

- 查询变量和取消变量属性

  ```bash
  declare -p xx    查询变量
  ```

  ![image-20211218123902874](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218123902874.png)

- 取消变量属性

  ```bash
  declare +x test
  ```

  ![image-20211218124335773](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218124335773.png)

  取消成功

### 使用expr和let数值运算工具

这种数值运算并不常用，了解就行

![image-20211218124707519](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218124707519.png)

**注意：使用expr时，+号两侧必须有空格。**

![image-20211218124846702](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218124846702.png)

### 使用`$((运算式))`的方式

![image-20211218124942238](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211218124942238.png)

### Calculate(version_1)

```shell
#!/bin/bash

read -p "would you like to do(+,-,*,/):" ch
read -p "please input a num for first:" num1
read -p "please input a num for second:" num2

[ "$ch" == "+" ] && echo "$(($num1+$num2))" && exit
[ "$ch" == "-" ] && echo "$(($num1-$num2))" && exit
[ "$ch" == "*" ] && echo "$(($num1*$num2))" && exit
[ "$ch" == "/" ] && echo "$(($num1/$num2))" && exit

echo "please input a funtion"

```

### 变量测试

![image-20211217133014736](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211217133014736.png)

