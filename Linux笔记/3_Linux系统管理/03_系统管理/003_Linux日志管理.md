# 日志管理

需要使用日志管理，首先得确保你服务器的日志服务是开启的。CentOS7的日志管理服务是：`rsyslogd`.可以使用ps命令查看：

![image-20211221161922871](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221161922871.png)

## 系统中常见的日志文件

绝大部分的日志文件都保存在`/var/log`目录下：

![image-20211221162126297](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221162126297.png)

![image-20211221162204036](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221162204036.png)

![image-20211221162223816](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221162223816.png)

![image-20211221162240465](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221162240465.png)

除了系统默认的日志意外，rpm包安装的系统服务也会将日志文件写入在`/var/log`下

![image-20211221162353652](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221162353652.png)

## 日志服务rsyslogd

### 日志文件格式

日志文件服务rsyslogd记录的日志文件，他们的格式是一样的。

- 事件产生的时间
- 发生时间的服务器的主机名
- 产生时间的服务名或程序名
- 时间产生的具体信息

### rsyslogd服务的配置文件

```shell
vim /etc/rsyslog.conf
----------------------------
authpriv.*					/var/log/secure
#服务名称【连接符号】日志等级			日志记录位置
#认证相关服务.所有日志等级          记录在/var/log/secure中
```

![image-20211221162955654](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221162955654.png)

#### rsyslogd可以识别的服务

![image-20211221163302975](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221163302975.png)

![image-20211221163317572](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221163317572.png)

#### 连接符号

- `.` ： 代表只要比后面等级高的（包含该等级）日志都会记录下来
- `.=` ： 代表只记录所需等级的日志
- `.!` ： 代表不等于，也就是除了该等级的日志外，其他等级日志都记录。

#### 日志等级

- debug（LOG_DEBUG） ： 一般的调试信息说明
- info（LOG_INFO） ： 基本的通知信息
- notice（LOG_NOTICE） ： 普通信息，但是有一定的重要性
- warning（LOG_WARNING） ： 警告信息，但是还不会影响到服务或系统的运行
- err（LOG_ERR） ： 错误信息，一般达到ERR等级的信息就可以影响到服务或系统运行了
- crit（LOG_CRIT） ： 临界状况信息，比err等级还要严重
- alert（LOG_ALERT） ： 警告状态信息，比crit还要严重，必须采取行动
- emerg（LOG_EMERG） ： 疼痛等级信息，系统已经无法使用了。
- *： 代表所有日志等级。

最后日志等级还可以识别“none”，如果日志等级是none，就说明忽略这个日志服务，该服务的所有日志都不再记录。

#### 日志记录位置

就是日志将被存在什么地方。

- 最常见的就是日志文件的绝对路径。
- 系统设备文件，比如打印机（/dev/lp0）
- 转发给远程主机。
  - UDP：@192.168.0.210：514
  - TCP：@@192.168.0.210：514
- 用户名：这要求，接收日志的用户在线。发送日志时，可以使用*，代表发给所有在线用户。如果需要指定多个用户，使用都好分隔。

#### 定义自己的日志

```shell
vim /etc/rsyslog.conf
-----------------------------------
# 把所有服务的临界点以上的错误都保存在/var/log/alert.log文件中
*。crit 						/var/log/alert.log
```

重启日志服务

```shell
systemctl restart rsyslogd
```

## 日志轮替

#### 日志文件的命名规则

日志轮替就是把旧的日志文件移动并改名，同时建立新的空白日志文件，当旧的日志文件超出保存的范围之后，就会进行删除。旧日志文件的改名规则按照`/etc/logrotate.conf`配置文件中的deteext参数进行更改。

![image-20211221170730491](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211221170730491.png)

- 如果配置中有dateext参数，系统就会按照时间进行更改，如上图。
- 如果没有dateext参数，系统会将就文件改名为filename.1 filename.2 ...以此类推

#### logrotate配置文件

```shell
# see "man logrotate" for details
# rotate log files weekly
weekly
# 每周进行一次轮替
# keep 4 weeks worth of backlogs
rotate 4
# 保存4个日志文件，第五次轮替会删除最老的一个
# create new (empty) log files after rotating old ones
create
# 自动创建新的日志文件
# use date as a suffix of the rotated file
dateext
# 使用日期作为旧文件的后缀
# uncomment this if you want your log files compressed
#compress
# s
# RPM packages drop log rotation information into this directory
include /etc/logrotate.d

# no packages own wtmp and btmp -- we'll rotate them here
/var/log/wtmp {
    monthly
    create 0664 root utmp
        minsize 1M
    rotate 1
}

/var/log/btmp {
    missingok
    monthly
    create 0600 root utmp
    rotate 1
}

# system-specific logs may be also be configured here.

```

