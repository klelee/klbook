# Linux用户管理

## 用户相关文件

- `/etc/passwd` 用户信息文件

  ```
  root:x:0:0:root:/root:/bin/bash    # 以冒号分隔
  用户名：密码位：用户标识号：组标识号：注释性描述：主目录：登录shell
  ```

  root用户的userID是0。

- `/etc/shadow` 用户密码文件

  ```
  root:$6$kUBSRYzZq.SLrtFo$7KXIYly.APcxGrOhIiDLcLeAAMfw8EkFMo3Q/YQL8I00eRIJlWOwdXLy8W7ZI45./JwRS9XdLG3ptHcOihktL.::0:99999:7:::
  登录名：加密密码：最后一次修改时间：最小时间间隔：最大时间间隔：警告时间：不活动时间：失效时间：标志
  ```

  如何暂时性禁止一个用户登录？

  ​	在其用户密码前添加一个 "!" 号。

- `/etc/group` 组配置文件

  ```
  klelee:x:1000:klelee
  组名：口令：组标识号：组内用户列表
  ```

## 添加用户

### 基本用法

`useradd <username>` 

### 常见选项

| 选项 | 描述                     |
| ---- | ------------------------ |
| -u   | 指定UID                  |
| -g   | 指定初始组               |
| -G   | 指定附加组，把用户加入组 |
| -c   | 添加说明                 |
| -d   | 手动指定家目录           |
| -s   | 指定默认shell            |

### 细节说明

- 新添加的用户的家目录在`/home/<username>`
- 可以使用`useradd -d /home/<yourpath> <username>`自定义新建用户的家目录

### 指定、修改密码

`passwd <username>`

## 修改用户信息

`usermod [option] [参数]`

举例，修改用户所在组

`usermod -g [new group]`

## 删除用户

### 基本语法

`userdel <username>` 保留用户的家目录，仅删除用户

`userdel -r <username>` 删除用户，同时删除家目录

### 细节说明

一般不会删除家目录

## 查询用户信息

`id <username>` 

## 切换用户

`su - <username>`

高权限->低权限    不需要密码

低权限->高权限    需要密码

## 查看当前用户/登录信息

### 基本语法

`who am i` 显示登录者的相关信息

## 用户组

### 新增组

`groupadd <groupname>`

### 删除组

`groupdel <groupname>`

### 增加用户时同时入组

`useradd -g <usergroup> <username>`

### 修改用户组

`usermod -g <usergroup> <username>`

## 用户和组相关文件

- /etc/group 文件

  组（group）的配置文件，记录Linux包含的组的信息

  每行含义：组名：口令：组标识号：组内用户列表