# Arm交叉编译paho.mqtt.c



## 编译openssl

### 下载openssl源码

 https://www.openssl.org/source/old/1.1.1

解压后得到：

![image-20240414180039678](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240414180039678.png)

### 配置编译选项

```bash
./config no-asm shared no-async --prefix=/home/klelee/code/openssl --cross-compile-prefix=arm-linux-gnueabihf-
```



编辑Makefile文件删除， 删除`-m64`

![image-20240414180217064](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240414180217064.png)

### 编译安装

```bash
make && make install
```



### 编译安装结果

![image-20240414180320837](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240414180320837.png)





## 编译paho.mqtt.c

### 下载paho.mqtt.c

https://github.com/eclipse/paho.mqtt.c

可以clone也可以直接下载zip



### 编译

cmake生成makefile，需要替换为自己使用的交叉编译工具

```
$ cd paho.mqtt.c
$ mkdir build_arm
$ cmake .. \
-DPAHO_BUILD_STATIC=TRUE \
-DPAHO_WITH_SSL=TRUE \
-DOPENSSL_ROOT_DIR=/home/klelee/code/openssl/ \
-DCMAKE_C_COMPILER=/opt/arm-linux-uclibcgnueabihf/bin/arm-linux-uclibcgnueabihf-gcc
```



编译

```
make
```



### 编译结果

![image-20240414181028390](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240414181028390.png)







