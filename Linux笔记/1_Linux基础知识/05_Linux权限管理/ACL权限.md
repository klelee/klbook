# ACL权限

ACL（Access Control List），中文译为访问控制列表。主要目的是为单一用户，文件或目录赋予特定的权限。

## 开启ACL权限

较新的linux版本中，默认是开启了acl权限的，可以看看内核挂载的时候的信息确认：`dmesg | grep -i acl`

![image-20211215091420649](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215091420649.png)

可以看到至少在xfs中ACL权限是开启的。

## ACL基本命令

- 查询文件的ACL权限

  `getfacl <filename>`

- 设定ACL权限

  `setfacl {option} <filename>`

  options:

  ​	-m : 设定ACL权限

  ​	-吧：删除ACL权限

### ACL基础命令的基本使用

`setfacl -m u:username:权限 文件名` ===》 `set -m u:st:5 www` 给test目录赋予aa是读写执行的ACL权限

![image-20211215092016304](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215092016304.png)

### 递归赋权

`setfacl -m u:st:5 -R www/`

![image-20211215092516337](https://klelee-image.oss-cn-qingdao.aliyuncs.com/image/image-20211215092516337.png)

但是Linux中递归赋ACL权限一定会造成权限溢出。

`setfacl -m g:组名:权限 文件名` 也可以将一个文件对一个组进行权限设置



