# 交叉编译libcurl



## 编译openssl

下载： https://www.openssl.org/source/old/1.1.1/index.html

解压： `tar -zxvf openssl-1.1.1w.tar.gz`

进入openssl源码目录：`cd openssl-1.1.1w`

### 配置编译选项

```
./config --prefix=/home/like/CrossCompiling/output/openssl no-shared no-zlib no-asm no-async --cross-compile-prefix=/opt/arm-rockchip830-linux-uclibcgnueabihf/bin/arm-rockchip830-linux-uclibcgnueabihf-
```

#### 配置选项说明

1. **`./config`**: 这是OpenSSL配置脚本的启动命令，用于设置编译选项。
2. **`--prefix=/home/like/CrossCompiling/output/openssl`**: 这个选项指定了OpenSSL安装的前缀路径。编译后的OpenSSL库和可执行文件将被安装到这个路径下。这里指定的是`/home/like/CrossCompiling/output/openssl`。
3. **`no-shared`**: 这个选项禁用了共享库（动态库）的构建，意味着只构建静态库。这对于某些嵌入式系统或特定部署场景可能是必需的。
4. **`no-zlib`**: 这个选项禁用了对Zlib压缩库的支持。如果目标系统不需要压缩功能或Zlib不可用，可以禁用它。
5. **`no-asm`**: 这个选项禁用了汇编优化。汇编优化通常用于提高性能，但在某些情况下（例如，当交叉编译时没有目标平台的汇编器，或者目标平台不支持特定的汇编指令集时），可能需要禁用它。
6. **`no-async`**: 这个选项禁用了异步I/O支持。异步I/O是一种允许应用程序在等待I/O操作完成时继续执行其他任务的技术。如果目标系统或应用程序不需要这种支持，可以禁用它。
7. **`--cross-compile-prefix=/opt/arm-rockchip830-linux-uclibcgnueabihf/bin/arm-rockchip830-linux-uclibcgnueabihf-`**: 这个选项指定了交叉编译时使用的编译器前缀。这里指定的路径`/opt/arm-rockchip830-linux-uclibcgnueabihf/bin/arm-rockchip830-linux-uclibcgnueabihf-`是存放交叉编译工具链的位置，包括编译器、链接器等



### 编译安装

```
make && make install
```





## 编译libcurl

下载： https://curl.se/download.html ， 可以选择最新的版本进行下载

![image-20240605110643802](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240605110643802.png)

解压： `tar -zxvf curl-8.7.1.tar.gz`

进入libcurl源码目录：`cd curl-8.7.1.tar.gz`

### 配置编译选项

```
./configure --prefix=/home/like/CrossCompiling/output/curl --host=arm-rockchip830-linux-uclibcgnueabihf --with-openssl=/home/like/CrossCompiling/output/openssl --enable-static CC=/opt/arm-rockchip830-linux-uclibcgnueabihf/bin/arm-rockchip830-linux-uclibcgnueabihf-gcc CXX=/opt/arm-rockchip830-linux-uclibcgnueabihf/bin/arm-rockchip830-linux-uclibcgnueabihf-g++
```

#### 编译选项说明

`--prefix` 编译输出目录

`--host` 编译目标平台

`--with-openssl=` 添加ssl依赖，可以访问https

`--enable-static` 编译静态链接库

`CC` gcc交叉编译器路径

`CXX` g++交叉编译器路径



### 编译安装

```
make -j8 && make install
```

