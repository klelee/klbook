**线程**和**进程**，大多数人说到这两个词的时候都在考虑其区别，但是我们不妨先想想其相同点——并行，然后从“并行”的角度去看待两者的不同。

- 线程： 是一个程序中不同功能的并行。
- 进程： 是操作系统中不同程序的并行。

本篇文章主要来聊一聊C语言的多线程编程，也就是怎么让一个程序，在同一时间运行多个功能。

## 创建线程

创建线程函数`pthread_create()`，其原型为：

```c
/* Create a new thread, starting with execution of START-ROUTINE
   getting passed ARG.  Creation attributed come from ATTR.  The new
   handle is stored in *NEWTHREAD.  */
extern int pthread_create (pthread_t *__restrict __newthread,
			   const pthread_attr_t *__restrict __attr,
			   void *(*__start_routine) (void *),
			   void *__restrict __arg) __THROWNL __nonnull ((1, 3));
```

### 参数

- `pthread_t *__restrict __newthread` ： 该**线程的名字**，类型为`pthread_t *`，此处需要注意是名字的指针变量，因此传递的时候需要对名字做取地址操作。名字可以根据实际需求命名，遵守变量的命名规则即可。
- `const pthread_attr_t *__restrict __attr` ： 该**线程的属性**，没有特殊情况时使用`NULL`即可。线程的属性可以定义线程的栈大小，调度优先级等，此部分在后面详细讲解。
- `void *(*__start_routine) (void *)` ： 该**线程的函数**，类型为`void *` ，因为函数名本身就是该函数的地址，所以可以直接传入一个具体的函数名，供该线程执行。需要注意的是，该函数必须为`void *`类型返回值的函数。
- `void *__restrict __arg` ： 要传入该线程的参数，必须为`void *`类型，如果没有需要传递的参数，则传入`NULL`.

### 返回值

- 0 ： 表示线程创建成功。
- !0： 表示线程创建失败，返回的非0值都对应不同的宏，表明失败的原因：
  - EAGAIN：系统资源不足，无法提供创建线程所需的资源。
  - EINVAL：传递给 pthread_create() 函数的 attr 参数无效。
  - EPERM：传递给 pthread_create() 函数的 attr 参数中，某些属性的设置为非法操作，程序没有相关的设置权限。

### 创建线程实例

我在开发过程中就遇到这样一个问题：

程序执行过程中，某一时刻需要播放指定的语音提示，但是不能阻塞程序运行。那么这个功能就得借助新建一个线程来实现。

根据上面创建线程部分的说明，创建一个线程，我们事先准备其需要的参数。我们暂时不需要改变线程的默认属性，但是上面的需求中明显需要指定要播放语音的名称或者路径，所以参数我们是需要的。因此，要创建这个线程我们需要准备三个参数，用来传递：

- 线程名： `pthread_t audio_play_thread`
- 线程函数： `void * audio_play(void * arg) {...}`
- 音频文件：`char * audio = "/video/audio_test.wav"`

到此，我们要创建线程的先决条件准备完毕，接下来我们，完成这段程序：

> c_thread_audio_play.c

```c
#include <stdio.h>
#include <unistd.h>
#include <pthread.h>

// 设置线程名
pthread_t audio_play_thread;  

// 线程函数
void * audio_play(void * arg)
{
    char * audio = (char *)arg;
    sleep(3);
    printf("播放音频 %s \n", audio);
}

int main(int argc, char ** argv)
{
    // 音频文件，实际程序中会根据场景不同做不同的选择
    char * audio = "/video/audio_test.wav";
    // 创建线程
    int ret = pthread_create(&audio_play_thread, NULL, audio_play, (void *)audio);
    if (ret) {
        printf("创建 SCHED_RR（循环）audio_play_thread失败，返回值=%d\n", ret);
    } else {
        printf("创建audio_play_thread成功！\n");
    }

    printf("程序继续执行中......\n");
    // 此处为了能够看到创建线程的效果，调用pthread_join函数等待线程返回。
    pthread_join(audio_play_thread, NULL);
    return 0;
}
```

然后编译运行该程序：

```
klelee@ubuntu18:~/code/demo$ gcc c_thread_audio.c -lpthread
klelee@ubuntu18:~/code/demo$ ./a.out 
创建audio_play_thread成功！
程序继续执行中......
播放音频 /video/audio_test.wav 
```

从运行结果来看，“程序继续执行中......”的打印要早于“播放音频 /video/audio_test.wav”。这说明我们的需求：播放音频不影响程序正常进行，实现了。

## 获取线程的返回值

上面的程序及运行过程中出现了，创建线程部分没有说到的内容：`pthread_join()`和`gcc c_thread_audio.c -lpthread`。其中`pthread_join()`函数用于等待一个线程的返回，并获取其返回值（void * 不代表没有返回值）。接下来我们详细了解一下这个函数，它的原型是：

```c
/* Make calling thread wait for termination of the thread TH.  The
   exit status of the thread is stored in *THREAD_RETURN, if THREAD_RETURN
   is not NULL.

   This function is a cancellation point and therefore not marked with
   __THROW.  */
extern int pthread_join (pthread_t __th, void **__thread_return);
```

### 参数

该函数接收两个参数，分别是 线程名 和 用于接收返回值的二级指针 。

- `pthread_t __th` ： 需要注意的是这里传入的直接是线程名，不是其指针变量。
- `void **__thread_return` ： 用于存储线程返回值指针的二级指针，在创建线程部分说明了，线程函数的返回值必须是`void *`类型。和 `void`类型不同，`void *`的本身是一个指针，实实在在存在的一个地址。

### 返回值

该函数的返回值和`pthread_create()`函数类似，等待线程返回成功返回0，失败返回非0值。

### 获取线程返回值实例

我们可以对上面的创建线程实例做一些小小的改动，假设我们需要知道语音播报是否成功。我们来实现一下：

```c
#include <stdio.h>
#include <unistd.h>
#include <malloc.h>
#include <pthread.h>

pthread_t audio_play_thread;

void * audio_play(void * arg)
{
    char * audio = (char *)arg;
    sleep(3);
    int ret = printf("播放音频 %s \n", audio);
    if (ret > 0) {
        printf("语音播报成功！\n");
        return (void *)"OK";
    } else {
        printf("语音播报失败！\n");
        return (void *)"ERROR";
    }
}

int main(int argc, char ** argv)
{
    char * audio = "/video/audio_test.wav";
    int ret = pthread_create(&audio_play_thread, NULL, audio_play, (void *)audio);
    if (ret) {
        printf("创建audio_play_thread失败，返回值=%d\n", ret);
    } else {
        printf("创建audio_play_thread成功！\n");
    }

    printf("程序继续执行中......\n");

    char * result = (char *)malloc(20);
    if (result == NULL) {
        printf("内存不足！\n");
        return -1;
    }
    pthread_join(audio_play_thread, (void *)(&result));
    printf("语音播报结果 %s \n", result);

    return 0;
}
```

编译运行

```
klelee@ubuntu18:~/code/demo$ gcc c_thread_audio.c -lpthread
klelee@ubuntu18:~/code/demo$ ./a.out 
创建audio_play_thread成功！
程序继续执行中......
播放音频 /video/audio_test.wav 
语音播报成功！
语音播报结果 OK 
```

上面的程序在“创建线程实例”的基础上做了一些改动，对语音播报函数增加了返回值，在主函数中等待其返回。最终，如果printf打印的字符数大于0，就表示语音播报成功，该函数返回OK。

## 多线程函数编译

在上面的两个实例的编译过程中，始终增加了一个`-lpthread`的参数，在Linux系统中，多线程程序需要链接pthread库是因为pthread库不是Linux系统默认的库。pthread是Linux下的线程库，当使用多线程时，必须手动链接这个库。



## 线程属性

前面说过，创建线程的时候可以为当前线程指定一些特殊的属性，比如栈大小，CPU调度优先级等等。本节就来说一说线程可以设置的一些属性，但是需要提前声明的是，一般情况下，创建线程使用默认属性完全足够。

### 属性对象

在C语言多线程编程中使用属性对象`pthread_attr_t`来设置线程的属性，线程的属性对象不能显示的被修改，比如显示的设置`stacksize = 12M`。线程的属性需要特定的函数去修改。

### 初始化属性

初始化属性函数`pthread_attr_init()` ，其函数原型为：

```c
/* Initialize thread attribute *ATTR with default attributes
   (detachstate is PTHREAD_JOINABLE, scheduling policy is SCHED_OTHER,
    no user-provided stack).  */
extern int pthread_attr_init (pthread_attr_t *__attr) __THROW __nonnull ((1));
```

#### 参数

- `pthread_attr_t *__attr` 属性对象，注意这里使用的是属性对象的指针变量。

#### 返回值

- 0 ： 初始化成功
- !0 ： 初始化失败

#### 描述

该函数将传入的属性对象初始化为线程属性的默认值，线程属性的默认值如下：

- scope ： 默认值 `PTHREAD_SCOPE_PROCESS`, 新线程与进程中的其他线程发生竞争。 
- detachstate ： 默认值 `PTHREAD_CREATE_JOINABLE` , 线程退出后，保留完成状态和线程 ID。
- stackaddr ： 默认值 `NULL`， 新线程具有系统分配的栈地址
- stacksize : 默认值 `0` ， 新线程具有系统定义的栈大小，一般为8M
- priority ： 默认值 `0` ， 新线程的优先级为0
- inheritsched ：默认值 `PTHREAD_EXPLICIT_SCHED`， 新线程不继承父线程调度优先级。
- schedpolicy ： 默认值 `SCHED_OTHER` ， 新线程对同步对象争用使用 Solaris 定义的固定优先级。线程将一直运行，直到被抢占或者直到线程阻塞或停止为止。

后面会对上面的属性做一一解释和使用。

### 销毁属性

调用`pthread_attr_init()`函数初始化属性的时候会消耗掉一部分的内存和系统资源。使用完毕之后需要调用`pthread_attr_destroy()`函数来销毁这部分资源。该函数的原型为：

```c
/* Destroy thread attribute *ATTR.  */
extern int pthread_attr_destroy (pthread_attr_t *__attr)
     __THROW __nonnull ((1));
```

#### 参数

- `pthread_attr_t *__attr` ： 指定线程属性对象，注意是指针变量

#### 返回值

- 0 ： 表示销毁成功
- !0 ： 表示销毁失败

#### 描述

没啥好描述的，调用了就删除对应属性的资源呗。所以什么时候调用该函数？

调用销毁属性函数之前应该考虑考虑：我是否还需要修改、查看改线程的属性值？如果不需要，请立即调用该函数。如果需要，请等到不需要的时候再调用。

你的顾虑：创建线程成功之后可不可以马上调用该函数销毁属性？

GPT的解答：在线程创建成功后，可以立即调用`pthread_attr_destroy`函数来销毁线程属性。这一操作不会影响已成功创建的线程的运行。实际上，`pthread_attr_destroy`函数主要用于清理和释放在创建线程时复制的线程属性所占用的系统资源。因此，即使在线程运行过程中，也可以安全地调用`pthread_attr_destroy`函数。但需要注意的是，如果在调用`pthread_attr_destroy`后，又有需要修改线程属性的操作，则必须重新设置线程属性并再次调用`pthread_create`函数来创建新的线程。这是因为`pthread_attr_destroy`函数会将线程属性对象恢复为默认状态，任何在此之前对线程属性做出的修改都会丢失。

### 线程分离状态

线程的分离状态就是该线程创建了之后主程序还管不管？不管了？那就是分离状态；管？那就是非分离。二者的区别就在于，非分离状态的线程，在执行结束之后线程所占用的资源不会被立即释放，要么执行`pthread_join()`之后释放，要么整个程序结束后释放；对于分离状态的线程，它执行完之后就会立即释放其占有的资源。设置分离状态函数`pthread_attr_setdetachstate()`其原型为：

```c
/* Set detach state attribute.  */
extern int pthread_attr_setdetachstate (pthread_attr_t *__attr,
					int __detachstate)
     __THROW __nonnull ((1));
```

#### 参数

- `pthread_attr_t *__attr` ： 线程属性对象的指针
- `int __detachstate` ： 分离状态`PTHREAD_CREATE_DETACHED = 1` ; 非分离状态`PTHREAD_CREATE_JOINABLE = 0`，这也是创建线程时的默认值

#### 返回值

- 0 ： 设置分离状态成功
- !0 ： 设置分离状态失败

#### 描述

这个真的没啥好描述的，需要返回值就不要分离，不需要返回值就不分离。至于那种一进去就死循环的线程，那你开心就好！

#### 获取线程分离状态

函数`pthread_attr_getdetachstate()`，其原型为：

```c
/* Get detach state attribute.  */
extern int pthread_attr_getdetachstate (const pthread_attr_t *__attr,
					int *__detachstate)
     __THROW __nonnull ((1, 2));
```

##### 参数

- `const pthread_attr_t *__attr`
- `int *__detachstate` 用于接收分离状态

##### 返回值

- 0 ： 获取成功
- !0 ： 获取失败

#### 说在后面

有趣的是在<pthread.h>头文件中，提供了一个函数，专门用来将线程分离的：`pthread_detach()`，其原型为：

```c
/* Indicate that the thread TH is never to be joined with PTHREAD_JOIN.
   The resources of TH will therefore be freed immediately when it
   terminates, instead of waiting for another thread to perform PTHREAD_JOIN
   on it.  */
extern int pthread_detach (pthread_t __th) __THROW;
```

也就是说，如果你需要将一个函数从非分离状态变为分离状态，最方便的方式，可能是这个函数！

### 栈溢出保护区

所谓栈溢出保护区，说的简单一点就是每一个线程的栈内存后面都会紧跟着一段空闲的内存，防止该线程栈空间溢出。栈溢出保护区一般因操作系统、编译器等环境原因大小不定，但一般情况下设为4KB到16KB，最重要的还是要根据环境决定。

#### 设置栈溢出保护区

`pthread_attr_setguardsize()` 函数的原型是：

```c
/* Set the size of the guard area created for stack overflow protection.  */
extern int pthread_attr_setguardsize (pthread_attr_t *__attr,
				      size_t __guardsize)
     __THROW __nonnull ((1));
```

##### 参数

- `pthread_attr_t *__attr`
- `size_t __guardsize` ： 栈溢出保护区大小，设置为0时关闭栈溢出保护区大小。

##### 返回值

- 0 ： 设置栈溢出保护区成功
- !0 ： 设置栈溢出保护区失败

#### 获取栈溢出保护区大小

`pthread_attr_getguardsize()` 函数原型是：

```c
/* Get the size of the guard area created for stack overflow protection.  */
extern int pthread_attr_getguardsize (const pthread_attr_t *__attr,
				      size_t *__guardsize)
     __THROW __nonnull ((1, 2));
```

##### 参数

- `const pthread_attr_t *__attr`
- `size_t *__guardsize` ： 用于接收栈溢出保护区大小

##### 返回值

- 0 ： 获取栈溢出保护区成功
- !0 ： 获取栈溢出保护区失败

### 线程的CPU争用范围

线程时CPU调度的最小单位，每一个线程被CPU调度过程中，可以只和当前进程（也就是当前程序）的其他线程争夺CPU时间，也可以和系统内的所有线程争夺CPU时间。

- `PTHREAD_SCOPE_PROCESS` ： 同一进程的争用
- `PTHREAD_SCOPE_SYSTEM` ： 系统内所有线程争用

#### 设置线程的CPU时间争用范围

`pthread_attr_setscope()` 函数原型：

``` c
/* Set scheduling contention scope in *ATTR according to SCOPE.  */
extern int pthread_attr_setscope (pthread_attr_t *__attr, int __scope)
     __THROW __nonnull ((1));
```

##### 参数

- `pthread_attr_t *__attr`
- `int __scope` 争用范围：
  - `PTHREAD_SCOPE_PROCESS` ： 同一进程的争用，其值为1
  - `PTHREAD_SCOPE_SYSTEM` ： 系统内所有线程争用，其值为0

##### 返回值

- 0 ： 设置争用范围成功
- !0 ： 设置争用范围失败

#### 获取线程的CPU时间争用范围

`pthread_attr_getscope()` 函数原型：

```c
/* Return in *SCOPE the scheduling contention scope of *ATTR.  */
extern int pthread_attr_getscope (const pthread_attr_t *__restrict __attr,
				  int *__restrict __scope)
     __THROW __nonnull ((1, 2));
```

##### 参数

- `const pthread_attr_t *__restrict __attr` 属性对象
- `int *__restrict __scope` 用于接收线程的CPU时间争用范围的变量

##### 返回值

- 0 ： 获取争用范围成功
- !0 ： 获取争用范围失败

### 调度策略

线程的调度策略在POSIX 标准中指定 `SCHED_FIFO`（先入先出）、`SCHED_RR`（循环）或 `SCHED_OTHER`（实现定义的方法）的调度策略属性。

#### 设置调度策略

`pthread_attr_setschedpolicy()` 函数的原型是：

```c
/* Set scheduling policy in *ATTR according to POLICY.  */
extern int pthread_attr_setschedpolicy (pthread_attr_t *__attr, int __policy)
     __THROW __nonnull ((1));
```

##### 参数

- `pthread_attr_t *__attr`

- `int __policy` 策略：

  - `SCHED_FIFO`

    如果调用进程具有有效的用户 ID `0`，则争用范围为系统 (`PTHREAD_SCOPE_SYSTEM`) 的先入先出线程属于实时 (`RT`) 调度类。如果这些线程未被优先级更高的线程抢占，则会继续处理该线程，直到该线程放弃或阻塞为止。对于具有进程争用范围 (`PTHREAD_SCOPE_PROCESS)`) 的线程或其调用进程没有有效用户 ID 0 的线程，请使用 `SCHED_FIFO`。`SCHED_FIFO` 基于 `TS` 调度类。

  - `SCHED_RR`

    如果调用进程具有有效的用户 ID `0`，则争用范围为系统 (`PTHREAD_SCOPE_SYSTEM)`) 的循环线程属于实时 (`RT`) 调度类。如果这些线程未被优先级更高的线程抢占，并且这些线程没有放弃或阻塞，则在系统确定的时间段内将一直执行这些线程。对于具有进程争用范围 (`PTHREAD_SCOPE_PROCESS`) 的线程，请使用 `SCHED_RR`（基于 `TS` 调度类）。此外，这些线程的调用进程没有有效的用户 ID `0`。

##### 返回值

- 0 ： 设置调度策略成功
- !0 ： 设置调度策略失败

#### 获取调度策略

`pthread_attr_getschedpolicy()` 函数的原型是：

```c
/* Return in *POLICY the scheduling policy of *ATTR.  */
extern int pthread_attr_getschedpolicy (const pthread_attr_t *__restrict
					__attr, int *__restrict __policy)
     __THROW __nonnull ((1, 2));
```

##### 参数

- `const pthread_attr_t *__restrict __attr`
- `int *__restrict __policy` 用于接收策略属性值的变量：

##### 返回值

- 0 ： 获取调度策略成功
- !0 ： 获取调度策略失败

### 继承的调度策略

新创建的线程默认继承创建者（父线程）的调度策略，这省去了为子线程单独设置调度策略的麻烦。但是如果你需要子线程使用和父线程不同的调度策略，那就需要调用下面的函数去单独设置它。

#### 设置继承的调度策略

`pthread_attr_setinheritsched` 函数的原型为：

```c
/* Set scheduling inheritance mode in *ATTR according to INHERIT.  */
extern int pthread_attr_setinheritsched (pthread_attr_t *__attr,
					 int __inherit)
     __THROW __nonnull ((1));
```

##### 参数

- `pthread_attr_t *__attr`
- `int __inherit` 是否继承父线程的调度策略
  - PTHREAD_INHERIT_SCHED（默认值）：新线程的调度属性继承自父线程，这个宏的值为 0
  - PTHREAD_EXPLICIT_SCHED：新线程的调度属性继承自 myAttr 规定的值， 这个宏的值为 1

##### 返回值

- 0 ： 设置策略继承成功
- !0 ： 设置策略继承失败

#### 获取继承的调度策略

`pthread_attr_getinheritsched` 函数的原型为：

```c
/* Return in *INHERIT the scheduling inheritance mode of *ATTR.  */
extern int pthread_attr_getinheritsched (const pthread_attr_t *__restrict __attr, int *__restrict __inherit)
     __THROW __nonnull ((1, 2));
```

##### 参数

- `const pthread_attr_t *__restrict __attr`
- `int *__restrict __inherit` 用于获取是否继承的变量

##### 返回值

- 0 ： 获取策略继承成功
- !0 ： 获取策略继承失败

### 调度优先级

线程的默认优先级为0，当线程的调度策略是 `SCHED_FIFO`（先入先出）、`SCHED_RR`（循环）时，可以设置该线程的调度优先级。在不同的操作系统中，线程的优先级是不同的，在设置优先级之前可以使用下面的两个函数来获取当前系统支持的最大优先级和最小优先级。

```c
int sched_get_priority_max(int policy);   //获得最大优先级的值，传入调度策略，返回优先级大小
int sched_get_priority_min(int policy);   //获得最小优先级的值，传入调度策略，返回优先级大小
```

#### 设置调度优先级

`pthread_attr_setschedparam` 函数原型为：

```c
/* Set scheduling parameters (priority, etc) in *ATTR according to PARAM.  */
extern int pthread_attr_setschedparam (pthread_attr_t *__restrict __attr, const struct sched_param *__restrict __param) __THROW __nonnull ((1, 2));
```

##### 参数

- `pthread_attr_t *__restrict __attr`

- `const struct sched_param *__restrict __param` 调度参数结构体

  设置线程的优先级只需要设置`sched_param::param.sched_priority` 的值为你期望的线程优先级即可。

##### 返回值

- 0 ： 设置策略参数成功
- !0 ： 设置策略参数失败

#### 获取调度优先级

`pthread_attr_getschedparam` 函数原型为：

```c
/* Return in *PARAM the scheduling parameters of *ATTR.  */
extern int pthread_attr_getschedparam (const pthread_attr_t *__restrict __attr, struct sched_param *__restrict __param)
     __THROW __nonnull ((1, 2));
```

##### 参数

- `const pthread_attr_t *__restrict __attr`
- `struct sched_param *__restrict __param` 用于存储获取到的优先级值的变量

##### 返回值

- 0 ： 获取策略参数成功
- !0 ： 获取策略参数失败

### 栈大小

一个线程内部的资源分配都将在栈内存上进行，很多时候线程默认栈大小就是足够的，默认大小根据操作系统的不同而不同，比如在我们常用的64为的操作系统中这个值通常是8M。这对于一个线程而言，往往是足够的。

#### 设置栈大小

`pthread_attr_setstacksize()`  函数的原型是：

```c
/* Add information about the minimum stack size needed for the thread
   to be started.  This size must never be less than PTHREAD_STACK_MIN
   and must also not exceed the system limits.  */
extern int pthread_attr_setstacksize (pthread_attr_t *__attr, size_t __stacksize) __THROW __nonnull ((1));
```

##### 参数

- `pthread_attr_t *__attr`
- `size_t __stacksize` 你希望设置的栈大小，再次说一遍，一般情况下不设置栈大小，因为你也不知道要设置多大

##### 返回值

- 0 ： 设置成功
- !0 ： 设置失败

#### 获取栈大小

`pthread_attr_getstacksize()`  函数的原型是：

```c
/* Return the currently used minimal stack size.  */
extern int pthread_attr_getstacksize (const pthread_attr_t *__restrict __attr, size_t *__restrict __stacksize)
     __THROW __nonnull ((1, 2));
```

##### 参数

- `const pthread_attr_t *__restrict __attr`
- `size_t *__restrict __stacksize` 用于存放获取到的栈大小的变量

##### 返回值

- 0 ： 获取成功
- !0 ： 获取失败

### 线程属性设置于获取的实例

#### 实例一 线程的分离状态——pthread_attr_setdetachstate

```c
#include <stdio.h>
#include <unistd.h>
#include <malloc.h>
#include <pthread.h>

pthread_t audio_play_thread;
pthread_attr_t audio_play_thread_attr;

void * audio_play(void * arg)
{
    char * audio = (char *)arg;
    sleep(3);
    int ret = printf("播放音频 %s \n", audio);
    if (ret > 0) {
        printf("语音播报成功！\n");
        return (void *)"OK";
    } else {
        printf("语音播报失败！\n");
        return (void *)"ERROR";
    }
}

int main(int argc, char ** argv)
{
    int ret;
    int detachstate;
    char * audio = "/video/audio_test.wav";

    pthread_attr_init(&audio_play_thread_attr);
    pthread_attr_setdetachstate(&audio_play_thread_attr, PTHREAD_CREATE_DETACHED);

    ret = pthread_create(&audio_play_thread, &audio_play_thread_attr, audio_play, (void *)audio);
    if (ret) {
        printf("创建audio_play_thread失败，返回值=%d\n", ret);
    } else {
        printf("创建audio_play_thread成功！\n");
        pthread_attr_getdetachstate(&audio_play_thread_attr, &detachstate);
        printf("线程的分离状态是： %d\n", detachstate);
    }

    printf("程序继续执行中......\n");

    char * result = (char *)malloc(20);
    if (result == NULL) {
        printf("内存不足！\n");
        return -1;
    }
	// 分离状态的线程使用 pthread_join()函数等待返回值是无效的
    pthread_join(audio_play_thread, (void *)(&result));    
    pthread_attr_destroy(&audio_play_thread_attr);
    printf("语音播报结果 %s \n", result);

    return 0;
}
```

编译运行结果

```
klelee@ubuntu18:~/code/demo$ gcc c_thread_audio.c -lpthread
klelee@ubuntu18:~/code/demo$ ./a.out 
创建audio_play_thread成功！
线程的分离状态是： 1
程序继续执行中......
语音播报结果  
klelee@ubuntu18:~/code/demo$ 
```

在上面的demo中，我们调用`pthread_attr_setdetachstate()`函数将该线程改为分离状态。在运行结果中可以看到，即使我们显式的调用了`pthread_join()`函数，也没能等待线程函数的返回结果。

#### 实例二 线程的分离状态——pthread_detach

```c
#include <stdio.h>
#include <unistd.h>
#include <malloc.h>
#include <pthread.h>

pthread_t audio_play_thread;
pthread_attr_t audio_play_thread_attr;

void * audio_play(void * arg)
{
    char * audio = (char *)arg;
    sleep(3);
    int ret = printf("播放音频 %s \n", audio);
    if (ret > 0) {
        printf("语音播报成功！\n");
        return (void *)"OK";
    } else {
        printf("语音播报失败！\n");
        return (void *)"ERROR";
    }
}

int main(int argc, char ** argv)
{
    int ret;
    int detachstate;
    char * audio = "/video/audio_test.wav";

    pthread_attr_init(&audio_play_thread_attr);
    ret = pthread_create(&audio_play_thread, &audio_play_thread_attr, audio_play, (void *)audio);
    if (ret) {
        printf("创建audio_play_thread失败，返回值=%d\n", ret);
    } else {
        printf("创建audio_play_thread成功！\n");
        
        pthread_attr_getdetachstate(&audio_play_thread_attr, &detachstate);
        printf("线程的分离状态是： %d\n", detachstate);
        if (detachstate == PTHREAD_CREATE_JOINABLE) {
            printf("即将分离该线程！\n");
            pthread_detach(audio_play_thread);
        }
    }

    printf("程序继续执行中......\n");

    char * result = (char *)malloc(20);
    if (result == NULL) {
        printf("内存不足！\n");
        return -1;
    }
    pthread_attr_destroy(&audio_play_thread_attr);
    printf("语音播报结果 %s \n", result);
    pthread_join(audio_play_thread, (void *)(&result)); 

    return 0;
}
```

编译运行

```
klelee@ubuntu18:~/code/demo$ ./a.out 
创建audio_play_thread成功！
线程的分离状态是： 0
即将分离该线程！
程序继续执行中......
语音播报结果  
klelee@ubuntu18:~/code/demo$
```

在上面的demo中，我们获取到该线程的分离状态是非分离，我们调用`pthread_detach`函数对线程进行了分离，从而导致`pthread_join()`函数未等待到线程的返回。

#### 实例三 其它线程属性的使用

```c
#include <stdio.h>
#include <unistd.h>
#include <malloc.h>
#include <pthread.h>

pthread_t audio_play_thread;
pthread_attr_t audio_play_thread_attr;

void * audio_play(void * arg)
{
    char * audio = (char *)arg;
    sleep(3);
    int ret = printf("播放音频 %s \n", audio);
    if (ret > 0) {
        printf("语音播报成功！\n");
        return (void *)"OK";
    } else {
        printf("语音播报失败！\n");
        return (void *)"ERROR";
    }
}

int main(int argc, char ** argv)
{
    int ret;
    int detachstate;
    int scope;
    size_t guardsize;
    size_t stacksize;
    int priority_max;
    int priority_min;
    int schedpolicy;
    int inheritsched;
    struct sched_param get_param;
    struct sched_param param;
    char * audio = "/video/audio_test.wav";

    pthread_attr_init(&audio_play_thread_attr);

    pthread_attr_setguardsize(&audio_play_thread_attr, 0);
    pthread_attr_setscope(&audio_play_thread_attr, PTHREAD_SCOPE_PROCESS);
    pthread_attr_setschedpolicy(&audio_play_thread_attr,SCHED_FIFO);
    pthread_attr_setinheritsched(&audio_play_thread_attr,0);
    param.sched_priority = 99;  // ubuntu1804 64为 gcc 7.5.0 线程最高优先级 99
    pthread_attr_setschedparam(&audio_play_thread_attr, &param);

    ret = pthread_create(&audio_play_thread, &audio_play_thread_attr, audio_play, (void *)audio);
    if (ret) {
        printf("创建audio_play_thread失败，返回值=%d\n", ret);
    } else {
        printf("创建audio_play_thread成功！\n");
        pthread_attr_getdetachstate(&audio_play_thread_attr, &detachstate);
        pthread_attr_getguardsize(&audio_play_thread_attr, &guardsize);
        pthread_attr_getscope(&audio_play_thread_attr, &scope);
        pthread_attr_getstacksize(&audio_play_thread_attr, &stacksize);
        pthread_attr_getschedpolicy(&audio_play_thread_attr, &schedpolicy);
        pthread_attr_getinheritsched(&audio_play_thread_attr, &inheritsched);
        priority_max = sched_get_priority_max(SCHED_FIFO);
        priority_min = sched_get_priority_min(SCHED_FIFO);
        pthread_attr_getschedparam(&audio_play_thread_attr, &get_param);

        printf("audio_play线程分离状态是：%d\n", detachstate);
        printf("audio_play线程栈溢出保护区大小是：%ld\n", guardsize);
        printf("audio_play线程CPU时间争用范围是：%d\n", scope);
        printf("audio_play线程栈大小：%ld\n", stacksize);
        printf("audio_play线程调度策略是： %d\n", schedpolicy);
        printf("audio_play线程策略继承机制是：%d\n", inheritsched);
        printf("priority_max=%d\n", priority_max);
        printf("priority_min=%d\n", priority_min);
        printf("audio_play线程调度优先级是：%d\n", get_param.sched_priority);

    }

    printf("程序继续执行中......\n");

    char * result = (char *)malloc(20);
    if (result == NULL) {
        printf("内存不足！\n");
        return -1;
    }
    pthread_attr_destroy(&audio_play_thread_attr);
    printf("语音播报结果 %s \n", result);
    pthread_join(audio_play_thread, (void *)(&result)); 

    return 0;
}
```

编译运行

```
klelee@ubuntu18:~/code/demo$ gcc c_thread_audio.c -lpthread
klelee@ubuntu18:~/code/demo$ ./a.out 
创建audio_play_thread成功！
audio_play线程分离状态是：0
audio_play线程栈溢出保护区大小是：0
audio_play线程CPU时间争用范围是：0
audio_play线程栈大小：8388608
audio_play线程调度策略是： 1
audio_play线程策略继承机制是：0
priority_max=99
priority_min=1
audio_play线程调度优先级是：99
程序继续执行中......
语音播报结果  
播放音频 /video/audio_test.wav 
语音播报成功！
klelee@ubuntu18:~/code/demo$ 
```

## 线程同步

线程同步机制也称为线程之间的锁机制，其目的是限制对公共资源的访问。C语言常用的线程间同步机制有四种：

- 互斥锁
- 信号量
- 条件变量
- 读写锁

下面将对上面提到的四种锁机制进行一一介绍：

### 互斥锁

互斥锁是C语言多线程编程中使用最多的一种锁机制。其核心思想也很简单，当A线程要访问公共资源时，就对公共资源加锁，此时其他任何线程要访问此公共资源都要阻塞等待。

从本质上来讲，“互斥锁”本身就是一个全局变量，有`lock`和`unlock`两个值。因此，在使用互斥锁的时候需要首先声明一个互斥锁：

```c
pthread_mutex_t audio_mutex;
```

#### 互斥锁的初始化

互斥锁在使用的时候需要首先进行初始化，初始化有两种方式，分别是静态初始化和动态初始化：

静态初始化，使用特定的宏进行初始化：

```c
pthread_mutex_t audio_mutex = PTHREAD_MUTEX_INITIALIZER;
```

静态初始化的互斥锁在静态存储区，初始化之后可以直接使用，不使用了也不需要进行删除和释放。



动态初始化，使用互斥锁的初始化函数进行初始化：

```c
pthread_mutex_t audio_mutex;
pthread_mutex_init(&audio_mutx, NULL);
```

动态初始化在堆内存区，互斥锁使用完毕之后需要进行销毁释放。`pthread_mutex_init()`函数的原型是：

```c
/* Initialize a mutex.  */
extern int pthread_mutex_init (pthread_mutex_t *__mutex, const pthread_mutexattr_t *__mutexattr)
     __THROW __nonnull ((1));
```

##### 参数

- `pthread_mutex_t *__mutex` 互斥锁对象

- `const pthread_mutexattr_t *__mutexattr` 互斥锁属性，一般情况下初始化使用NULL，即默认属性即可。

  - `PTHREAD_MUTEX_TIMED_NP` 就是普通锁。当一个线程加锁以后，其余请求锁的线程将形成一个等待队列，并在解锁后按优先级获得锁。这种锁策略保证了资源分配的公平性。
  - `PTHREAD_MUTEX_RECURSIVE_NP` 允许同一个线程对同一个锁成功获得多次，并通过多次unlock解锁。如果是不同线程请求，则在加锁线程解锁时重新竞争。
  - `PTHREAD_MUTEX_ERRORCHECK_NP` 如果同一个线程请求同一个锁，则返回EDEADLK，否则与PTHREAD_MUTEX_TIMED_NP类型动作相同。这样就保证当不允许多次加锁时不会出现最简单情况下的死锁。
  - `PTHREAD_MUTEX_ADAPTIVE_NP` 动作最简单的锁类型，仅等待解锁后重新竞争

  与线程的属性类似，初始化互斥锁的属性也需要使用相应的属性修改函数进行操作，相关函数声明在`<pthread.h>`头文件中：

  ![image-20231114173358982](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20231114173358982.png)

##### 返回值

- 初始化成功返回 0
- 初始化失败返回 !0

#### 互斥锁的销毁

使用`pthread_mutex_init()`函数初始化互斥锁之后需要特别的使用`pthread_mutex_destroy();`来销毁互斥锁，销毁互斥锁的动作就是在free这个互斥锁所占用的内存空间。需要特别注意的是，销毁互斥锁之后不可以再对此互斥锁进行加锁操作。

`pthread_mutex_destroy()`函数的原型是：

```c
/* Destroy a mutex.  */
extern int pthread_mutex_destroy (pthread_mutex_t *__mutex)
     __THROW __nonnull ((1));
```

该函数仅需要传入一个参数，即需要销毁的互斥锁，互斥锁销毁成功返回0，失败返回非0值。

#### 互斥锁的加锁

互斥锁加锁提供了两种选择，分别是“阻塞加锁”和“非阻塞加锁”：

- 阻塞加锁： 如果该锁已经被别的线程持有，则阻塞等待锁被释放
- 非阻塞加锁：如果该锁已经被别的线程持有，则返回 `EBUSY`

阻塞加锁`pthread_mutex_lock()` 可能是我们更常用的加锁类型，其函数原型是：

```c
/* Lock a mutex.  */
extern int pthread_mutex_lock (pthread_mutex_t *__mutex)
     __THROWNL __nonnull ((1));
```

调用时只需要传入该公共资源对应的互斥锁即可，加锁成功返回0，加锁失败返回非0值。

非阻塞加锁`pthread_mutex_trylock()` 函数原型是：

```c
/* Try locking a mutex.  */
extern int pthread_mutex_trylock (pthread_mutex_t *__mutex)
     __THROWNL __nonnull ((1));
```

加锁成功返回0，加锁失败返回`EBUSY`

#### 互斥锁的解锁

互斥锁解锁`pthread_mutex_unlock()` 函数原型是：

```c
/* Unlock a mutex.  */
extern int pthread_mutex_unlock (pthread_mutex_t *__mutex)
     __THROWNL __nonnull ((1));
```

传入需要解锁的互斥锁，返回0表示解锁成功，否则解锁失败。

### 信号量

信号量本质上也是一个全局变量，不同与互斥锁的是，信号量的取值可以大于一，并且运行对信号量的值进行加和减。根据信号量初始值的不同，将其分为两类：

- 二进制信号量： 初始值为1 的信号量，因此该类信号量取值就只有1和0.此时信号量在功能上和互斥锁相同。
- 计数信号量： 初始值大于1的信号量，当当前进程中存在多个线程要访问某些公共资源，但资源数量小于线程数量，就可以用计数信号量来限制同时使用公共资源的线程数量。

根据使用场景的不同，信号量还可以分为：

- 无名信号量：只可以在共享内存的情况下，比如实现进程中各个线程之间的互斥和同步，因此无名信号量也被称作基于内存的信号量；

- 有名信号量：通常用于不共享内存的情况下，比如进程间通信。

  由于本文主要写线程间的同步机制，所以进程间通信问题不多赘述，有名信号量相关操作函数简单列出：

  ```c
  /* Open a named semaphore NAME with open flags OFLAG.  */
  extern sem_t *sem_open (const char *__name, int __oflag, ...)
    __THROW __nonnull ((1));
  
  /* Close descriptor for named semaphore SEM.  */
  extern int sem_close (sem_t *__sem) __THROW __nonnull ((1));
  
  /* Remove named semaphore NAME.  */
  extern int sem_unlink (const char *__name) __THROW __nonnull ((1));
  ```

  

信号量使用`sem_t`类型表示，该类型被定义在`<semaphore.h>` 头文件中。

以下是信号量的一些典型应用场景：

1. 系统中只有一台打印机，就可以设置初值为1的信号量，以实现对打印机的并发控制。
2. 环形队列和生产消费问题，这种情况下互斥锁适用于一整块临界资源，而信号量更适用于看似一块临界资源，但实际可以分成多个小部分的资源控制。
3. 线程同步，例如使用信号量控制两个线程间的同步，信号量的值初始化为0，表示没有信号量资源实例；尝试获取该信号量的线程会被阻塞，直到其他线程通过V操作释放信号量。
4. 控制线程开启的数量，如某一个界面需要同时发多个请求，全部请求都成功后再一起刷新界面的场景。

总的来说，信号量提供了一种高效而强大的并发控制手段，能够在多任务环境下保证对共享资源的合理分配和使用。

#### 信号量的初始化

初始化信号量需要使用`sem_init()`函数进行，函数原型是：

```c
/* Initialize semaphore object SEM to VALUE.  If PSHARED then share it
   with other processes.  */
extern int sem_init (sem_t *__sem, int __pshared, unsigned int __value)
  __THROW __nonnull ((1));
```

##### 参数

- `sem_t *__sem` 需要初始化的信号量
- `int __pshared` 是否和其他进程共享该信号量，0 表示不共享， 1 表示共享
- `unsigned int __value` 初始值

##### 返回值

初始化成功返回0，失败返回非0值

#### 信号量的销毁

函数`sem_destroy()`原型：

```c
/* Free resources associated with semaphore object SEM.  */
extern int sem_destroy (sem_t *__sem) __THROW __nonnull ((1));
```

传入需要销毁的信号量， 销毁成功返回0，销毁失败返回非0值

#### 等待信号量

其实这里将等待动作理解为占坑就可以，假设有三个坑，但是5个人。前三个人来之前查看坑的数量都是有坑的，那就自己占了。第四个人调用阻塞等待方式来查看还有没有坑，发现没了，但是他实在着急，于是就一直等着，工作也不干了。但是摸鱼的第五个人调用非阻塞等待方式来查看还有没有坑，发现没了，但是他不着急，他就回去了。

也就是其中一个线程去访问公共资源，由于该线程是被信号量限制的，所以该线程需要先等待信号量值大于1时才可以访问资源。等待信号量有两种方式：

- 阻塞等待 ： 线程查看信号量值是否大于1， 如果是，则将信号量减1并返回，程序继续运行；如果不是，则等待信号量的值变为1，等待过程中阻塞程序运行。

  `sem_wait()` 函数的原型是：

  ```c
  /* This function is a cancellation point and therefore not marked with
     __THROW.  */
  extern int sem_wait (sem_t *__sem) __nonnull ((1));
  ```

  该函数接收要等待的信号量，等待成功返回0，失败返回非0值。

  

- 非阻塞等待 ： 不管信号量的值是否大于1，非阻塞等待模式均直接返回。信号量大于1时，减 1 ，返回0；信号量小于1 时，直接返回非0.

  `sem_trywait()` 函数的原型是：

  ```c
  /* Test whether SEM is posted.  */
  extern int sem_trywait (sem_t *__sem) __THROWNL __nonnull ((1));
  ```

  该函数接收要等待的信号量，等待成功返回0，失败返回非0值。

#### 释放信号量

前三个人占的坑逐渐使用完了，他们离开的动作就是对坑的释放。

`sem_post()` 函数的原型是：

```c
/* Post SEM.  */
extern int sem_post (sem_t *__sem) __THROWNL __nonnull ((1));
```

该函数接收待释放的信号量作为参数，释放成功返回0，释放失败返回非0值。

### 条件变量

以下是一些典型的应用场景：

1. 生产者消费者问题：条件变量可以用于解决生产者和消费者之间的同步问题。生产者生产数据并将其放入缓冲区中，而消费者从缓冲区中取出数据进行处理。当缓冲区为空时，消费者将等待；当缓冲区满时，生产者将等待。
2. 有限资源池：条件变量可以用于管理有限数量的资源池。当资源池中的资源被占用时，请求资源的线程将被阻塞，直到有可用资源为止。
3. 线程池任务队列：条件变量可以用于实现线程池中的任务队列。当任务队列为空时，工作线程将被阻塞，直到有新任务到达为止。
4. 倒计时器：条件变量可以用于实现倒计时器。当倒计时结束时，等待的线程将被唤醒。
5. 信号量：条件变量可以用作信号量的替代品。与信号量不同，条件变量允许多个线程同时等待某个条件成立。

条件变量的作用主要在阻塞线程的进行，待某条件成立之后再继续执行该线程。常用于消息队列处理函数中。POSIX 标准中使用`pthread_cond_t`类型来表示条件变量。定义一个条件变量如下：

```c
#include <pthread.h>
pthread_conf_t testCond;
```

#### 条件变量的初始化

条件变量的初始化与互斥锁的初始化类似，分为静态初始化和动态初始化：

- 静态初始化： 使用特定的宏对条件变量进行初始化：

  ```c
  pthread_cond_t myCond = PTHREAD_COND_INITIALIZER;
  ```

- 动态初始化： 调用`pthread_cond_init()`函数进行条件变量的初始化。

静态初始化很简单，初始化之后就可以直接使用了，用完也不需要进行销毁。重点说说动态初始化，`pthread_cond_init()`函数的原型是：

```c
/* Initialize condition variable COND using attributes ATTR, or use
   the default values if later is NULL.  */
extern int pthread_cond_init (pthread_cond_t *__restrict __cond,
			      const pthread_condattr_t *__restrict __cond_attr)
     __THROW __nonnull ((1));
```

##### 参数

- `pthread_cond_t *__restrict __cond` 要初始化的条件变量
- `const pthread_condattr_t *__restrict __cond_attr` 条件变量属性，该属性一般不需要进行修改，可以直接传入NULL

##### 返回值

初始化成功返回0, 初始化失败返回非0值



#### 条件变量的销毁

通过`pthread_cond_init()`函数初始化的函数需要手动进行销毁，`pthread_cond_destroy()`函数的原型是：

```c
/* Destroy condition variable COND.  */
extern int pthread_cond_destroy (pthread_cond_t *__cond)
     __THROW __nonnull ((1));
```

使用方法很简单，只需要传入需要销毁的条件变量即可，销毁成功返回0, 销毁失败返回-1 



#### 等待条件变量条件成立

条件变量的作用就是阻塞需要访问公共资源的线程，这个时候就存在一个问题：如果同时阻塞很多线程，这个时候一旦条件成立，就会有很多线程争夺这个公共资源，就出现了线程不安全的问题。因此，在使用条件变量的时候一般都会和一个互斥锁搭配使用。当然这个互斥锁也是需要初始化的，具体的方法参考上面互斥锁部分。

##### 阻塞等待

下面主要看看条件变量的等待函数：`pthread_cond_wait()`函数原型是：

```c
/* Wait for condition variable COND to be signaled or broadcast.
   MUTEX is assumed to be locked before.

   This function is a cancellation point and therefore not marked with
   __THROW.  */
extern int pthread_cond_wait (pthread_cond_t *__restrict __cond,
			      pthread_mutex_t *__restrict __mutex)
     __nonnull ((1, 2));
```

这个函数会将调用该函数的线程持续阻塞，直到条件成立，至于条件什么时候成立，成立之后怎么告诉该线程。下面马上就会说到。

###### 参数

- `pthread_cond_t *__restrict __cond` 条件变量
- `pthread_mutex_t *__restrict __mutex` 互斥锁

###### 返回值

阻塞成功返回0, 阻塞失败返回非0



##### 定时阻塞等待

除了上述的长时间阻塞线程的函数之外，还有一个不长时间阻塞线程的函数：`pthread_cond_timedwait()` ，该函数的原型是：

```c
int pthread_cond_timedwait(pthread_cond_t *restrict cond, pthread_mutex_t *restrict mutex, const struct timespec *restrict abstime);
```

###### 参数

- `cond`：指向条件变量的指针。
- `mutex`：指向互斥锁的指针，用于保护条件变量。
- `abstime`：指向一个`timespec`结构体的指针，表示等待的最长时间。如果设置为NULL，则表示无限等待。

###### 返回值

- 成功时返回0。
- 失败时返回错误码。

#### 解除线程阻塞状态

##### pthread_cond_signal()

上面的例子中已经使用了一个接触线程阻塞状态的函数：`pthread_cond_signal()`函数，该函数用于解除一个线程的阻塞状态，也就是说此时如果有多个线程在等待该条件变量，只解除其中一个的等待状态，具体解除哪一个由操作系统决定。该函数原型是：

```c
/* Wake up one thread waiting for condition variable COND.  */
extern int pthread_cond_signal (pthread_cond_t *__cond)__THROWNL __nonnull ((1));
```

这个函数的使用比较简单，只需要传入需要接触阻塞的条件变量即可，如果解除阻塞成功则，返回0,否则返回非0值。

##### pthread_cond_broadcast()

该函数与上面的signal函数相对应，broadcast函数用于一次性接触所有等待该条件变量的线程。

```c
/* Wake up all threads waiting for condition variables COND.  */
extern int pthread_cond_broadcast (pthread_cond_t *__cond)__THROWNL __nonnull ((1));
```

在实际应用中，选择使用哪种函数取决于具体的情境：

- 当生产者一次产生一个产品，并且最好只有一个消费者时，适合使用`pthread_cond_signal`。
- 若有多个生产者或消费者，或者生产者能一次产生多个产品的情况，如一个生产者多消费者的场景或读写锁实现（写入后通知所有读者），则应使用`pthread_cond_broadcast`。











