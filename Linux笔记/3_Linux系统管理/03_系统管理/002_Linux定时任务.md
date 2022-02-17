# 定时任务

## at——一次性定时任务

at命令的执行需要后台的守护进程`atd`在执行，这个进程默认时开机执行的

![image-20211221131456017](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221131456017.png)

如果没有启动，可以使用systemctl命令启动

### at 服务管理与访问控制

系统中存在at使用的黑白名单，分别为`/etc/at.deny`和`/etc/at.allow`。但是正常情况下，白名单是不存在的，也就是说，所有用户都可以执行at命令。所以如果不想让某个用户执行at，将其加入deny文件就可以了。

### at命令的使用

#### 基本语法

```shell
at [options] 时间
```

#### 常用选项

- `-m` ： 当at执行完毕后，无论命令是否有输出，都用邮件通知命令的执行者
- `-c 工作号` ： 显示该at工作的实际内容

#### at命令支持的时间格式

`HH:MM`

`HH:MM YYY-MM-DD`

`HH:MM[am]/[pm] [month] [date] `

`now + 时间` ：单位可以有：minutes，hours

### at 命令的查询与管理

#### 当前系统存在的at任务查询

`atq`

![image-20211221140937332](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221140937332.png)

#### 删除一个at任务

`atrm 工作号`

![image-20211221141424525](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221141424525.png)

## crontab——循环定时任务

crontab命令也有类似于at的黑白名单机制，其执行方法也和at一样，这里就不重复了。

### 用户的crontab设置

#### 基本语法

```shell
crontab [options]
```

#### 常用选项

- `-e` ： 编辑crontab定时任务
- `-l` ： 查询crontab任务
- `-r` ： 删除当前用户所有的crontab任务。如果有多个任务，只想删除一个，可以使用e选项
- `-u username` ： 修改或删除其他用户的crontab任务，只有root可用。

### crontab的时间规定

crontab的任务格式为：`* * * * * 【执行的任务】`

![image-20211221143041503](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221143041503.png)

如果不规定某个时间，只要填星号即可。星号表示任意时间。

另外：

- `,` ： 逗号，当表示不连续的时间的时候，使用星号分隔

  `* 6,12,18,0 * * *` 表示每天的6点12点18点0点执行一次命令

- `-` ： 用来表示连续的时间

  `* 3-22 * * * *` 表示每天的3点22点每个整点执行命令

- `*/n` ： 代表每隔多久执行命令

  `*/10 * * * *` ： 表示每隔10分钟执行一次命令

​	

### crontab配置文件

```shell
vim /etc/crontab
---------------------------------
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed

# 每隔20分钟备份一次数据库
*/20 * * * * klelee /root/shell/bdbak.sh
```

与命令行直接写任务不同的是，在配置文件中写任务可以指定执行用户，当然，root用户更加方便。