仅个人感觉，在平时敲代码过程中涉及到的文件操作是比较少的，这就导致C语言的文件和目录操作等技术无法得到足够的练习，所以本文章就慢慢的积累偶尔会用到的文件和目录操作。本文多会以我的实际项目遇见的需求为例子展开记录文件和目录操作的一些方法。

## 文件操作

最基本的文件操作，比如打开关闭文件、读取写入文件、新建删除文件等等。

### 打开关闭文件

```c
#include <stdio.h>

int main() {
    int ret = 0;
    FILE * fp = NULL;

    fp = fopen("TestFile.txt", "w");
    if (fp == NULL) {
        printf("ERROR: 打开文件失败！\n");
    }

    fclose(fp);
}
```



相关函数原型：

> fopen()函数

```c
/* Open a file and create a new stream for it.

   This function is a possible cancellation point and therefore not
   marked with __THROW.  */
extern FILE *fopen (const char *__restrict __filename,
		    const char *__restrict __modes) __wur;
```

- 参数： 文件名和打开模式
- 返回值：文件指针

打开模式，有如下一些类型，根据实际需求选择不同的读写类型。

| 模式 | 描述                                                         |
| :--- | :----------------------------------------------------------- |
| r    | 打开一个已有的文本文件，允许读取文件。                       |
| w    | 打开一个文本文件，允许写入文件。如果文件不存在，则会创建一个新文件。在这里，您的程序会从文件的开头写入内容。如果文件存在，则该会被截断为零长度，重新写入。 |
| a    | 打开一个文本文件，以追加模式写入文件。如果文件不存在，则会创建一个新文件。在这里，您的程序会在已有的文件内容中追加内容。 |
| r+   | 打开一个文本文件，允许读写文件。                             |
| w+   | 打开一个文本文件，允许读写文件。如果文件已存在，则文件会被截断为零长度，如果文件不存在，则会创建一个新文件。 |
| a+   | 打开一个文本文件，允许读写文件。如果文件不存在，则会创建一个新文件。读取会从文件的开头开始，写入则只能是追加模式。 |

> fclose()函数

```c
/* Close STREAM.

   This function is a possible cancellation point and therefore not
   marked with __THROW.  */
extern int fclose (FILE *__stream);
```

传入要关闭的文件即可。



### 读取写入文件

读取和写入文件的方式有很多，在不同的场景下可以选择不同的函数进行读取和写入文件。

#### 以字符的形式读写文件

##### fgetc()

以字符的形式读取文件内容，和while循环搭配使用。也就是每次读取一个字符，直到遇到EOF。

```c
#include <stdio.h>

int main() {
    int ret = 0;
    FILE * fp = NULL;
    char ch;

    fp = fopen("TestFile.txt", "r");
    if (fp == NULL) {
        printf("ERROR: 打开文件失败！\n");
    }

    while( (ch=fgetc(fp)) != EOF ){
        printf("%c", ch);
        // putchar(ch);
    }
    putchar('\n');

    fclose(fp);
}
```

##### fputc()

下面demo中，不断从键盘获取输入，转而输入到该文件。

```c
#include <stdio.h>

int main() {
    int ret = 0;
    FILE * fp = NULL;
    char buffer[1025] = {0};
    char ch;

    fp = fopen("TestFile.txt", "w");
    if (fp == NULL) {
        printf("ERROR: 打开文件失败！\n");
    }

    while((ch = getchar()) != '\n') {
        fputc(ch, fp);
    }

    fclose(fp);
}
```

#### 以字符串的形式读写文件

##### fgets()

一次读取多个字符，可以制定一次读取多少个字符。需要注意的是fgets的停止条件是：遇到了换行或者到了文件结尾。也就是说该函数适合用来做行读取。只要吧要读取的字符数设置的足够大即可。要读取整个文件的内容，使用while循环即可。

```c
#include <stdio.h>

int main() {
    int ret = 0;
    FILE * fp = NULL;
    char buffer[1025] = {0};
    char ch;

    fp = fopen("TestFile.txt", "r");
    if (fp == NULL) {
        printf("ERROR: 打开文件失败！\n");
    }

    while(fgets(buffer, 1024, fp) != NULL) {
        printf("%s", buffer);
    }

    fclose(fp);
}
```

##### fputs()

与fgets函数相反，fputs就是以字符串的形式往文件里面写内容。

```c
#include <stdio.h>

int main() {
    int ret = 0;
    FILE * fp = NULL;
    char str[] = "人非生而知之者，孰能无惑？"; 
    char buffer[1025] = {0};


    fp = fopen("TestFile.txt", "w");
    if (fp == NULL) {
        printf("ERROR: 打开文件失败！\n");
    }

    fputs(str, fp);

    fclose(fp);
}
```

#### 以数据块的形式读写文件

以数据块的形式读写文件可能是日常编程操作文件中最常用的两个方法了。从字符到字符串读取再到块读取，都是读取（写入）内容在增多。块读取和字符串读取最大的不同就在于，字符串读取，每次最多只能读取一行，假设一个文件有多行，但字节数固定，那是不是可以一次性读取完成呢？当然可以。

##### fread()

`fread()`函数就可以完成这个操作。它的原型是：

```c
__fortify_function __wur size_t
fread (void *__restrict __ptr, size_t __size, size_t __n,
       FILE *__restrict __stream)
{
  if (__bos0 (__ptr) != (size_t) -1)
    {
      if (!__builtin_constant_p (__size)
	  || !__builtin_constant_p (__n)
	  || (__size | __n) >= (((size_t) 1) << (8 * sizeof (size_t) / 2)))
	return __fread_chk (__ptr, __bos0 (__ptr), __size, __n, __stream);

      if (__size * __n > __bos0 (__ptr))
	return __fread_chk_warn (__ptr, __bos0 (__ptr), __size, __n, __stream);
    }
  return __fread_alias (__ptr, __size, __n, __stream);
}
```

简单点：

```c
size_t fread (void * ptr, size_t size, size_t n, FILE * stream)
```

这个函数看起来很复杂是不是？因为这个这篇文章中参数最多的一个函数了。来看看他的参数：

- `void * ptr` 读取到的内容存放的地方
- `size_t size` 一次性读取块的大小
- `size_t n` 一次性读取多少块
- `FILE * stream` 要读取的文件的指针

虽然看起来有四个参数，但是实际上我们可以把size和n当作一个参数去看待，怎么说呢？如果我们知道文件包含1024字节的数据，那么我们读取块大小1024, 这个时候n为1就可以了。总而言之，这个函数每次读取的字节数就是 `size × n`

然后是返回值，正常情况下这个函数的返回值是读取的块数，也就是上面参数里的n。但是异常情况下，比如读到的文件末尾也没有读够一块，会返回0. 

来看看具体的实现：

```c
#include <stdio.h>

int main() {
    size_t ret = 0;
    FILE * fp = NULL;
    char buffer[1025] = {0};


    fp = fopen("TestFile.txt", "r");
    if (fp == NULL) {
        printf("ERROR: 打开文件失败！\n");
    }

    ret = fread(buffer, 10, 1, fp);
    printf("%s\n", buffer);
    printf("读取文件返回值%ld\n", ret);

    fclose(fp);
}
```

当`TestFile.txt`文件中的内容是：

```
klelee
```

时，执行结构为：

```bash
klelee@linux-mint:~/code/demo$ gcc open_and_close.c -o open_and_close && ./open_and_close
klelee
读取文件返回值0
```

因为此时文件中的内容小于块大小，读不够一块，因此返回了0.



当`TestFile.txt`文件中的内容是：

```
klelee klelee
```

时，执行结构为：

```bash
klelee@linux-mint:~/code/demo$ gcc open_and_close.c -o open_and_close && ./open_and_close
klelee kle
读取文件返回值1
```

此时文件中的内容足以读够一块，因此正常返回成功读取的块数



##### fwrite()

比较常用的想要一次性写入多行数据到文件的如：json数据。我们使用cJSON构建的JSON字符串，如果使用format格式的话就会出现换行符，此时就需要使用fwrite()函数一次性写入到文件。该函数原型是：

```c
/* Write chunks of generic data to STREAM.

   This function is a possible cancellation point and therefore not
   marked with __THROW.  */
extern size_t fwrite (const void *__restrict __ptr, size_t __size, size_t __n, FILE *__restrict __s);
```

简单点：

```c
size_t fwrite (const void * ptr, size_t size, size_t n, FILE * stream);
```

可以看到在参数上，write和read基本上是一致的。不同的是ptr参数，对于write来说，是要写入的数据的指针。

#### 格式化读写文件

`fprintf()`和`fscanf()`函数，是对格式化输入输出延伸使用。在用法上也只是多了一个FILE类型的指针而已。

先来看看读文件内容。

##### fscanf()

使用fscanf读取文件内容就需要明确知道文件中内容的格式。假设我的文件`TestFile.txt`中的内容是：

```
like klelee
```

那么使用fscanf读取该文件内容时就可以这样写：

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    size_t ret = 0;
    FILE * fp = NULL;
    char name[10] = {0};
    char nickname[10] = {0};


    fp = fopen("TestFile.txt", "r");
    if (fp == NULL) {
        printf("ERROR: 打开文件失败！\n");
    }

    fscanf(fp, "%s %s", name, nickname);
    printf("%s\'s nickname is %s\n", name, nickname);

    fclose(fp);
    
    return ret;
}
```

此时的输出为：

```bash
klelee@linux-mint:~/code/demo$ cd "/home/klelee/code/demo/" && gcc open_and_close.c -o open_and_close && "/home/klelee/code/demo/"open_and_close
like's nickname is klelee
klelee@linux-mint:~/code/demo$ 
```



##### fprintf()

同样的，对于写入文件，我们可以使用fprintf()函数：

```C
int main() {
    size_t ret = 0;
    FILE * fp = NULL;
    char buffer[1025] = {0};
    char * name = "like";
    char * nickname = "klelee";


    fp = fopen("TestFile.txt", "w");
    if (fp == NULL) {
        printf("ERROR: 打开文件失败！\n");
    }

    fprintf(fp, "%s\'s nickname is %s\n", name, nickname);

    fclose(fp);
}
```

### 新建删除文件

新建文件实际上可以直接调用`fopen()`函数进行，因此不多做介绍。删除文件时我们需要使用`remove()`函数，该函数原型是：

```c
/* Remove file FILENAME.  */
extern int remove (const char *__filename) __THROW;
```

这个函数很简单，就是传入需要删除文件的路径即可，删除成功返回0,删除失败返回-1



### 检查文件是否存在

程序中的很多代码都是为异常情况做的准备，谁都不敢保证异常情况一定不会发生。所以为了程序健壮性，有些时候要使用其他进程或县城创建的文件时，最好检查一下文件是否存在，使用`access()`函数：

```c
/* Test for access to NAME using the real UID and real GID.  */
extern int access (const char *__name, int __type) __THROW __nonnull ((1));
```

#### 参数

- `const char *__name` 要检查的文件的名字

- `int __type` 检查类型，如果要检查文件是否存在，此处传入`F_OK`

  检查类型有下面这些：

  ```c
  #define	R_OK	4		/* Test for read permission.  */
  #define	W_OK	2		/* Test for write permission.  */
  #define	X_OK	1		/* Test for execute permission.  */
  #define	F_OK	0		/* Test for existence.  */
  ```

### 文件的随机读写

文件的随即读写实际上就是对文件内部位置指针位置的调整，主要设计到`rewind()`函数和`fseek()`函数，前者将位置指针移动到开头的位置，而fseek函数将位置指针移动到任意位置，前提是你要清楚你要移动到哪里？

#### rewind()

rewind函数比较简单，原型为：

```c
void rewind ( FILE *fp );
```

只需要传入需要移动位置指针的文件指针即可。

#### fseek()

相对来说fseek函数就更加强悍，支持将指针移动到任意位置，其原型为：

```C
int fseek ( FILE *fp, long offset, int origin );
```

##### 参数

- `FILE × fp` 需要操作的文件的指针

- `long offset` 要移动的字节数

- `int origin` 从哪里开始移动

  | 起始点   | 常量     | 值   |
  | -------- | -------- | ---- |
  | 文件开头 | SEEK_SET | 0    |
  | 当前位置 | SEEK_CUR | 1    |
  | 文件末尾 | SEEK_END | 2    |



## 目录操作

目录的常见操作有：新建删除目录，获取当前目录路径，列出当前目录所有文件和文件夹，切换目录，下面依次说明。

先来看获取当前目录吧

### 获取当前路径

在linux下可以使用pwd直接获取当前路径，那么使用C语言应该如何实现呢？C语言的<unistd.h>头文件中提供了一个函数`getcwd()`函数用于获取当前路径，该函数的原型是：

```c
/* Get the pathname of the current working directory,
   and put it in SIZE bytes of BUF.  Returns NULL if the
   directory couldn't be determined or SIZE was too small.
   If successful, returns BUF.  In GNU, if BUF is NULL,
   an array is allocated with `malloc'; the array is SIZE
   bytes long, unless SIZE == 0, in which case it is as
   big as necessary.  */
extern char *getcwd (char *__buf, size_t __size) __THROW __wur;
```

#### 参数

getcwd函数把当前目录的名字写到给定的缓冲区buff里。如果目录的名字超出了参数size给出的缓冲区长度（一个ERANGE错误），它就返回NULL。**如果成功，它返回指针buff，我们可以访问buff来获取当前的目录。**

- `char * buff` 用于存放当前路径的字符串
- `size_t size` 缓冲区大小

#### 返回值

- 获取成功： 获取成功时会返回我们用来存放路径的buff的指针。由于函数本身就能返回当前目录的地址，所以对于buff参数，我们也可以设置为NULL，当然这是不推荐的。
- 获取失败：  返回NULL

![image-20231129153022721](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20231129153022721.png)

### 新建和删除目录

在C语言中新建和删除目录的函数和linux中的命令一样，都是mkdir和rmdir

### 新建目录

新建目录的函数`mkdir()`函数所在的头文件是`<sys/stat.h>` ，所以要使用这个函数，需要包含该头文件。mkdir函数的原型是：

```c
/* Create a new directory named PATH, with permission bits MODE.  */
extern int mkdir (const char *__path, __mode_t __mode)
     __THROW __nonnull ((1));
```

#### 参数

- `const char * path` 表示要创建的目录路径
- `__mode_t __mode` 表示要创建的目录的权限

#### 返回值

创建成功返回0, 创建失败返回-1

### 删除目录

C语言删除目录的函数是`rmdir()`,个人认为这是一个很鸡肋的函数，因为他无法删除非空目录，如果要删除一个非空的目录的话就需要递归的调用rmdir去删除其子目录。rmdir的原型是：

```c
/* Remove the directory PATH.  */
extern int rmdir (const char *__path) __THROW __nonnull ((1));
```

函数很简单，传入需要删除的目录路径即可，删除成功返回0,删除失败返回-1;

那么如果要删除一个非空的目录怎么办呢？个人使用:

```c
system("rm -rf <path>");
```



### 切换目录

切换目录相当于linux的cd命令，在C语言中使用`chdir()`函数，该函数的原型是：

```c
/* Change the process's working directory to PATH.  */
extern int chdir (const char *__path) __THROW __nonnull ((1)) __wur;
```

参数是需要去的目录路径，成功进入该目录返回0,失败返回-1



### 列出目录文件

最后就要说说ls命令了，在C语言中没有直接能够ls的函数，因此需要使用opendir和readdir两个函数配合使用。

这也是我要写的第一个需求，因此这里不重复说明了。



## 需求一 遍历目录下文件

我的需求是：开机之后查看/video/datatrade_bak 目录下是否有订单，有订单的话就对订单进行处理。

对于这个需求，我的实现步骤如下：

1. 检查目录是否存在，不存在直接返回；
2. 如果目录存在，则打开该目录；
3. 遍历目录下所有的文件或目录；
4. 如果有则进行操作；

### 实现需求

1. 检查目录是否存在和打开目录放到一起，使用`opendir()`函数实现。该函数的原型是：

   ```c
   /* Open a directory stream on NAME.
      Return a DIR stream on the directory, or NULL if it could not be opened.
   
      This function is a possible cancellation point and therefore not
      marked with __THROW.  */
   extern DIR *opendir (const char *__name) __nonnull ((1));
   ```

   - 参数： `const char *__name`需要打开的目录的绝对路径。
   - 返回值： 返回一个目录对象的指针

2. 遍历该目录，使用while循环和`readdir()`函数搭配。readdir()函数的原型是：

   ```c
   /* Read a directory entry from DIRP.  Return a pointer to a `struct
      dirent' describing the entry, or NULL for EOF or error.  The
      storage returned may be overwritten by a later readdir call on the
      same DIR stream.
   
      If the Large File Support API is selected we have to use the
      appropriate interface.
   
      This function is a possible cancellation point and therefore not
      marked with __THROW.  */
   extern struct dirent *readdir (DIR *__dirp) __nonnull ((1));
   ```

   - 参数： `DIR *__dirp` 就是在 1 中获取到的目录对象指针

   - 返回值： 返回一个dirent类型的指针。该结构如下：

     ```c
     struct dirent
       {
     #ifndef __USE_FILE_OFFSET64
         __ino_t d_ino;
         __off_t d_off;
     #else
         __ino64_t d_ino;
         __off64_t d_off;
     #endif
         unsigned short int d_reclen;
         unsigned char d_type;
         char d_name[256];		/* We must not include limits.h! */
       };
     ```

3. 最后使用`closedir()`函数关闭打开的目录，该函数原型是：

   ```c
   /* Close the directory stream DIRP.
      Return 0 if successful, -1 if not.
   
      This function is a possible cancellation point and therefore not
      marked with __THROW.  */
   extern int closedir (DIR *__dirp) __nonnull ((1));
   ```

   - 参数： 目录对象
   - 关闭成功返回0,关闭失败返回-1

### 具体实现

```c
void * datatrade_restore(void * arg)
{
	DIR * datatrade_bak_dir;
	struct dirent * entry;

	if ((datatrade_bak_dir = opendir("/video/datatrade_bak")) == NULL) {
		LOGE("无法打开订单备份文件夹！\n");
		return "ERROR";
	}

	while ((entry = readdir(datatrade_bak_dir)) != NULL) {
		if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0) {
			continue;
		}
        // 此处会有一些我项目中实际对订单信息的处理逻辑
		LOGI("订单 %s 视频待上传！\n", entry->d_name);  // 可以使用entry->d_name作为获取到的文件文件或目录名，实际上就是一个字符串，怎么用就看你了。
	}
   	closedir(datatrade_bak_dir);
	system("rm -rf /video/datatrade_bak");
	LOGD("订单恢复线程结束！\n");
}
```

