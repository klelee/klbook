# 梁山派GD32F470移植FreeRTOS

## 准备工作

1. 梁山派：梁山派有两个版本1.0.1(GD32F450)和1.0.2(GD32F470)版本
2. GD32F470项目： https://gitea.klelee.org/klelee/GD32F470ZGT6
3. FreeRTOS源码：
   - 官方下载链接 https://www.freertos.org/zh-cn-cmn-s/a00104.html；
   - 本人的OSS下载 https://klelee-files.oss-cn-qingdao.aliyuncs.com/FreeRTOS/FreeRTOSv202212.01.zip



## 移植过程



### FreeRTOS内核精简

我们下载到的FreeRTOS解压后得到如下目录，我们只需要其中的FreeRTOS目录，及其两个子目录Demo和Source下的部分文件，其他的文件和目录均删除。

![image-20240406093920792](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406093920792.png)

精简后的FreeRTOS源码如下：

![image-20240406120300423](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406120300423.png)

### Keil项目导入FreeRTOS

首先将上面裁剪完成的FreeRTOS内核放到工程下面

![image-20240406130224771](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406130224771.png)

双击打开GD32F470.uvprojx

![image-20240406130033784](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406130033784.png)

在工程中创建FreeRTOS目录

![image-20240406130423271](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406130423271.png)

将本地FreeRTOS相关源码导入工程

![image-20240406130627493](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406130627493.png)

在项目下的FreeRTOS目录下找到source目录，选中该目录下的所有c文件，并点击Add

![image-20240406130755014](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406130755014.png)

![image-20240406130916576](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406130916576.png)

继续添加其他c文件，按照同样的步骤找到heap_4.c并添加：

![image-20240406131112552](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406131112552.png)

继续找到port.c并添加

![image-20240406131229067](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406131229067.png)

导入FreeRTOSconfig.h

将`GD32F470ZGT6\FreeRTOS\Demo\CORTEX_M4F_STM32F407ZG-SK` 目录下的`FreeRTOSConfig.h`文件复制到`GD32F470ZGT6\FreeRTOS\Source\include` 下。



将FreeRTOS的头文件路径配置到项目中

![image-20240406131710017](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406131710017.png)

![image-20240406131914629](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406131914629.png)

继续导入port.h 的头文件路径

![image-20240406132102427](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406132102427.png)

至此，FreeRTOS的导入工作就完成了。



## 工程配置

不使用微库

![image-20240406143656245](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406143656245.png)

关闭微库后printf重定向的问题就会报错，可以进行如下调整：

```c
#if !defined(__MICROLIB)
//不使用微库的话就需要添加下面的函数
#if (__ARMCLIB_VERSION <= 6000000)
//如果编译器是AC5  就定义下面这个结构体
struct __FILE
{
	int handle;
};
#endif

FILE __stdout;

//定义_sys_exit()以避免使用半主机模式
void _sys_exit(int x)
{
	x = x;
}
#endif

/* retarget the C library printf function to the USART */
int fputc(int ch, FILE *f)
{
    usart_data_transmit(LOG_USART, (uint8_t)ch);
    while(RESET == usart_flag_get(LOG_USART, USART_FLAG_TBE));
    return ch;
}

void usart_send_data(uint32_t usart_periph, uint8_t ucch)
{
    usart_data_transmit(usart_periph, (uint8_t)ucch);  
    while(RESET == usart_flag_get(usart_periph, USART_FLAG_TBE)); // 等待发送数据缓冲区标志置位
}

void usart_send_buffer(uint32_t usart_periph, uint8_t *ucstr)
{   
    while(ucstr && *ucstr) {     
        usart_send_data(usart_periph, *ucstr++);    
    }
}
```



使用C99标准编译，编译时不优化

![image-20240406143856879](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406143856879.png)



## 编译并修改报错

### 编译报错1

![image-20240406143945787](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406143945787.png)

SVC_Handler等函数重复定义，是因为在gd32f4xx_it.c文件中定义了这几个函数，RTOS又重新定义了。所以注释掉gd32f4xx_it.c文件中相关函数即可。

编辑`GD32F470ZGT6\Firmware\GD32F4xx_standard_peripheral\Source\gd32f4xx_it.c`

```diff
diff --git a/Firmware/GD32F4xx_standard_peripheral/Source/gd32f4xx_it.c b/Firmware/GD32F4xx_standard_peripheral/Source/gd32f4xx_it.c
index c20835c..02ddebf 100644
--- a/Firmware/GD32F4xx_standard_peripheral/Source/gd32f4xx_it.c
+++ b/Firmware/GD32F4xx_standard_peripheral/Source/gd32f4xx_it.c
@@ -107,9 +107,9 @@ void UsageFault_Handler(void)
     \param[out] none
     \retval     none
 */
-void SVC_Handler(void)
-{
-}
+// void SVC_Handler(void)
+// {
+// }

 /*!
     \brief    this function handles DebugMon exception
@@ -127,9 +127,9 @@ void DebugMon_Handler(void)
     \param[out] none
     \retval     none
 */
-void PendSV_Handler(void)
-{
-}
+// void PendSV_Handler(void)
+// {
+// }
 
 /*!
     \brief    this function handles SysTick exception
@@ -137,7 +137,7 @@ void PendSV_Handler(void)
     \param[out] none
     \retval     none
 */
-void SysTick_Handler(void)
-{
-    delay_decrement();
-}
+// void SysTick_Handler(void)
+// {
+//     delay_decrement();
+// }
```



### 编译报错2

![image-20240406150825434](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406150825434.png)

相关宏未打开

编辑：`GD32F470ZGT6\FreeRTOS\Source\include\FreeRTOSConfig.h`

```diff
diff --git a/FreeRTOS/Source/include/FreeRTOSConfig.h b/FreeRTOS/Source/include/FreeRTOSConfig.h
index a2eca83..34c0bdc 100644
--- a/FreeRTOS/Source/include/FreeRTOSConfig.h
+++ b/FreeRTOS/Source/include/FreeRTOSConfig.h
@@ -41,7 +41,7 @@
  *----------------------------------------------------------*/

 /* Ensure stdint is only used by the compiler, and not the assembler. */
-#ifdef __ICCARM__
+#if defined(__ICCARM__) || defined(__CC_ARM) || defined(__GUNC__)
        #include <stdint.h>
        extern uint32_t SystemCoreClock;
 #endif
```



### 编译报错3

![image-20240406144507954](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406144507954.png)

FreeRTOS中相关的宏值不对。

编辑：`GD32F470ZGT6\FreeRTOS\Source\include\FreeRTOSConfig.h`

修改一下几个宏的值

![image-20240406144757592](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240406144757592.png)

修改之后编译通过。



## 测试验证



### 测试demo

这是一个点灯的程序：

```c
#include "gd32f4xx.h"
#include "systick.h"
#include <stdio.h>
#include "main.h"

#include "FreeRTOS.h"
#include "task.h"


TaskHandle_t StartTask_Handler;

void LED_Init(void);
void CrateTask(void *pvParameters);
void task_led2(void *pvParameters);
void task_led4(void *pvParameters);


int main(void) {	
	systick_config();
	LED_Init();
	usart_init();
	
	xTaskCreate(CrateTask ,"CrateTask" , 128, NULL, 1, &StartTask_Handler);
	vTaskStartScheduler();
	
	while(1) {
		;
	}
}

void LED_Init(void)
{
	//LED2
	rcu_periph_clock_enable(RCU_GPIOD);
	gpio_mode_set(GPIOD,GPIO_MODE_OUTPUT,GPIO_PUPD_NONE,GPIO_PIN_7);
	gpio_output_options_set(GPIOD,GPIO_OTYPE_PP,GPIO_OSPEED_50MHZ,GPIO_PIN_7);
	
	//LED4
	rcu_periph_clock_enable(RCU_GPIOA);
	gpio_mode_set(GPIOA,GPIO_MODE_OUTPUT,GPIO_PUPD_NONE,GPIO_PIN_5);
	gpio_output_options_set(GPIOA,GPIO_OTYPE_PP,GPIO_OSPEED_50MHZ,GPIO_PIN_5);
}

void CrateTask(void *pvParameters){
	
	taskENTER_CRITICAL();
	
	xTaskCreate(task_led2 , "task_led2" ,50 ,NULL , 2 ,0);
	
	xTaskCreate(task_led4 , "task_led4" ,50 ,NULL , 2 ,0);
	
	vTaskDelete(StartTask_Handler);
	
	taskEXIT_CRITICAL(); 
	
}

void task_led2(void *pvParameters)
{  
	while(1)
	{	
		printf("task_led2\n");
		gpio_bit_write(GPIOD, GPIO_PIN_7, SET);
		vTaskDelay(500);
		gpio_bit_write(GPIOD, GPIO_PIN_7, RESET);
		vTaskDelay(500);
	}
}   

void task_led4(void *pvParameters)
{
	while(1)
	{
		printf("task_led4\n");
		gpio_bit_write(GPIOA, GPIO_PIN_5, SET);
		vTaskDelay(1000);
		gpio_bit_write(GPIOA, GPIO_PIN_5, RESET);
		vTaskDelay(1000);
		
	}
}

```

注意：如果根据我开始提供的GD32F470项目模板走的话，上面的程序需要安装关闭微库的那个位置修改printf重定向部分代码，否则烧录后会卡在systemInit阶段：

编辑：`GD32F470ZGT6\Hardware\usart\bsp_usart.c`

```
diff --git a/Hardware/usart/bsp_usart.c b/Hardware/usart/bsp_usart.c
index 975ed45..882c89f 100644
--- a/Hardware/usart/bsp_usart.c
+++ b/Hardware/usart/bsp_usart.c
@@ -25,23 +25,41 @@ void usart0_init(uint32_t baudrate)
     usart_enable(LOG_USART);
 }

-void usart_send_data(uint32_t usart_periph, uint8_t ucch)
+#if !defined(__MICROLIB)
+#if (__ARMCLIB_VERSION <= 6000000)
+struct __FILE
 {
-       usart_data_transmit(usart_periph, (uint8_t)ucch);
-       while(RESET == usart_flag_get(usart_periph, USART_FLAG_TBE));
-}
+       int handle;
+};
+#endif
+
+FILE __stdout;

-void usart_send_string(uint32_t usart_periph, uint8_t *ucstr)
+void _sys_exit(int x)
 {
-       while(ucstr && *ucstr) {
-         usart_send_data(usart_periph, *ucstr++);
-       }
+       x = x;
 }
+#endif

+/* retarget the C library printf function to the USART */
 int fputc(int ch, FILE *f)
 {
-     usart_send_data(LOG_USART, ch);
-     return ch;
+    usart_data_transmit(LOG_USART, (uint8_t)ch);
+    while(RESET == usart_flag_get(LOG_USART, USART_FLAG_TBE));
+    return ch;
+}
+
+void usart_send_data(uint32_t usart_periph, uint8_t ucch)
+{
+    usart_data_transmit(usart_periph, (uint8_t)ucch);  
+    while(RESET == usart_flag_get(usart_periph, USART_FLAG_TBE));
+}
+
+void usart_send_buffer(uint32_t usart_periph, uint8_t *ucstr)
+{   
+    while(ucstr && *ucstr) {     
+        usart_send_data(usart_periph, *ucstr++);    
+    }
 }
 
 void usart_init()
```





测试验证通过。