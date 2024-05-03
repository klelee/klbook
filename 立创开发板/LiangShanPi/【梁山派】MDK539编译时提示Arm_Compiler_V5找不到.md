## 问题现象
使用Keil MDK539 编译连山派demo时出现如下报错：
```
Rebuild started: Project: GD32F450 *** Target 'GD32F450' uses ARM-Compiler 'Default Compiler Version 5' which is not available. *** Please review the installed ARM Compiler Versions: 'Manage Project Items - Folders/Extensions' to manage ARM Compiler Versions. 'Options for Target - Target' to select an ARM Compiler Version for the target. *** Rebuild aborted. Build Time Elapsed: 00:00:01
```
提示默认使用的Compiler Version 5不存在。
## 解决方案
手动安装Compiler Version 5。首先在Arm官网下载安装包：https://developer.arm.com/downloads/view/ACOMP5
然后进行安装，首先解压该文件，解压后双击ARMCompiler_506_Windows_x86_b960\Installer\setup.exe 文件进行安装。

1. 点击next

   ![1280X1280](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/1280X1280.PNG)

2. 勾选同意，然后next

   ![1280X1280](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/1280X1280.PNG)

3. 选择安装位置

   ![1280X1280](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/1280X1280.PNG)

4. 新建C:\Keil_v5\ARM\ARMCC\ 文件夹，并选中其作为安装目录，然后点OK进行安装。

   ![cdc04bfc-9e84-4a92-a811-d44405080e50](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/cdc04bfc-9e84-4a92-a811-d44405080e50.png)


5. Next -> install 进行安装即可

   ![4b65bb79-f260-4a0f-9d22-3cb6dca24d89](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/4b65bb79-f260-4a0f-9d22-3cb6dca24d89.png)

   ![e5141350-14ad-43fd-b8ef-2bfd1821edf7](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/e5141350-14ad-43fd-b8ef-2bfd1821edf7.png)


6. 完成之后取消勾选launch release note，然后点击Finish完成安装。

   ![b4a809fc-3456-4e1e-9e41-8e2a8bce98fa](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/b4a809fc-3456-4e1e-9e41-8e2a8bce98fa.png)

7. 进行Keil中编辑器的配置，点击环境配置按钮

   ![be549843-2b7d-4980-b619-1464c92c1fb9](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/be549843-2b7d-4980-b619-1464c92c1fb9.png)

8. 打开Folders/Extensions -> Setup Defaule ARM Compiler Version 后面的选择点

   ![c2368d5a-daee-411c-861c-336679a66975](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/c2368d5a-daee-411c-861c-336679a66975.png)

9. 选择之前设置的安装位置，并确认

   ![afce6332-3d1b-4177-9114-c6591eb43641](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/afce6332-3d1b-4177-9114-c6591eb43641.png)

10. 点close退出

    ![114b647e-5efe-4dbb-b40d-a377c1176162](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/114b647e-5efe-4dbb-b40d-a377c1176162.png)

11. 点击魔术手开始配置编译器

    ![f514538b-875a-4d15-9910-cdd18fd1a1e3](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/f514538b-875a-4d15-9910-cdd18fd1a1e3.png)

12. 确认编译器版本是v5

    ![8770d3b9-ec43-4ba4-ab07-56b9b31391f5](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/8770d3b9-ec43-4ba4-ab07-56b9b31391f5.png)

13. 重新编译通过。

  