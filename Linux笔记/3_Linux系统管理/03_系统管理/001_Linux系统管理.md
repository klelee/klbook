# 系统管理

## 查看系统进程

### ps命令

`ps aux` 查看系统中所有进程，使用BSD系统格式输出

![image-20211221103328666](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221103328666.png)

`ps -le`查看系统所有进程，使用Linux标准格式输出

![image-20211221103411032](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221103411032.png)

常用aux格式输出查看

#### aux格式输出

![image-20211221103515730](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221103515730.png)

- ![image-20211221103626947](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221103626947.png)
- 要及时清理僵死进程

![](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221103657902.png)

### top命令

`top [option]`

![image-20211221104136181](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221104136181.png)

#### 常用选项

- `-d 秒数` ： 指定刷新间隔，默认为3秒

- `-b` ： 使用批处理模式输出，一般he`-n`选项配个使用，重定向到另外的文件

- `-n 次数` ： 指定top命令执行的次数，一般和-b选项何用

  ![image-20211221104236736](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221104236736.png)

- 其他选项

  ![image-20211221104338507](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221104338507.png)

#### top顶部内容

![image-20211221104555086](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221104555086.png)

- 第一行是任务队列信息

  ![image-20211221104640697](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221104640697.png)

- 第二行是进程信息

  ![image-20211221104743392](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221104743392.png)

- 第三行是CPU信息

  ![image-20211221104822931](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221104822931.png)

- 第四行是物理内存信息

  ![image-20211221104853259](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221104853259.png)

- 第五行是交换分区信息

  ![image-20211221104922993](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221104922993.png)

### pstree命令

树状查看当前系统进程

![image-20211221105132486](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221105132486.png)

### 系统进程的管理

#### 常用系统信号

![image-20211221105318944](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221105318944.png)

#### kill命令、killall命令和pkill命令

- kill命令

  通过传入进程的PID号杀死进程

- killall命令

  通过传入进程名就可以杀死进程。

- pkill命令

  可以使用终端号将某用户踢出去

  ![image-20211221105748433](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221105748433.png)

  使用终端号踢出需要使用选项-t

### 工作管理

工作管理是指可以用job命令查看的进程的管理，一般是放入后台的工作。

#### 怎么将任务放到后台？

1. 使用&符号将任务放到后台

   比如将/etc目录打包到etc.tar.gz文件，同时将命令放到后台执行

   ![image-20211221110548890](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221110548890.png)

2. 使用ctrl + z将一个任务暂停并放入后台

   ![image-20211221110720183](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221110720183.png)

   显示后台有一项工作处于停止状态

#### 后台命令管理

1. 查看后台的工作

   `jobs` 可以查看后台所有的工作，加上-l，可以显示工作的PID

   ![image-20211221110953550](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221110953550.png)

    

2. 将后台暂停的工作恢复到前台执行

   例如top和vim这样的工作在后台是无效执行，所以会默认暂停，我们试着将他们恢复到前台执行

   ![image-20211221111207872](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221111207872.png)

   恢复到前台：`fg 工作号`

   ![image-20211221111341025](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221111341025.png)

然后就可以在前台正常退出le

3. 将后台暂停的命令在后台恢复执行

   ![image-20211221111542143](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221111542143.png)

   将后台暂停的压缩任务恢复

以上的工作都只是在当前终端执行，一旦关掉这个终端，工作就结束了

#### 后台命令脱离当前终端执行

1. 将需要执行的任务写入`/etc/rc.local`文件中，这样服务器重启之后会自动执行这条命令。但是缺点也很明显，就是无法临时执行。（pass）
2. 使用系统定时任务：at    # 个人觉得at还是很方便的
3. 使用nohup命令
   - 基本命令格式：`nohup [命令] &`

## 系统资源查看

### vmstat命令

- 基本用法：`vmstat [刷新间隔] [刷新次数]`

  ![image-20211221113155476](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221113155476.png)

  procs：进行信息字段

  ​	r：等待运行的进程数

  ​	b：不可被唤醒的进程数量，

  memory：内存信息字段

  ​	swpd：虚拟内存

  ​	free：空闲内存

  ​	buff：缓冲

  ​	cache：缓存

  ![image-20211221124158555](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221124158555.png)

  ### dmesg命令

  显示开机时内核检测信息

  - 查看CPU信息

    ![image-20211221125004462](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221125004462.png)

### free命令

- 基本语法`free [option]`
- 常用选项：
  - `b` ：以字节为单位显示
  - `k`
  - `m`
  - `g`

![image-20211221125524819](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221125524819.png)

#### 查看CPU信息

CPU的主要信息保存在，`/proc/cpuinfo`文件中，直接查看就可以知道CPU的信息

![image-20211221125924587](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221125924587.png)

同样在`/proc/`目录下还有一些其他的信息文件，比如内存的

![image-20211221130301956](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221130301956.png)

## 其他指令

- `uname` 查看系统内核信息
- `lsb_release -a` 显示当前系统的版本