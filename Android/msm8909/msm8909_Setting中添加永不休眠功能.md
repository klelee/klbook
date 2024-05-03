项目中需要让Android板开机就进入桌面并且永不休眠。项目使用的是广和通的SC806Android开发板，msm8909平台。

## 配置永不休眠

### diff

diff文件放前面，可以直接apply进去！

```diff
diff --git a/frameworks/base/packages/SettingsProvider/res/values/defaults.xml b/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
index 64378d7ba7..3ee1079fb5 100644
--- a/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
+++ b/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
@@ -18,7 +18,8 @@
 -->
 <resources>
     <bool name="def_dim_screen">true</bool>
-    <integer name="def_screen_off_timeout">60000</integer>
+    <!-- <integer name="def_screen_off_timeout">60000</integer> -->
+    <integer name="def_screen_off_timeout">-1</integer>
     <integer name="def_sleep_timeout">-1</integer>
     <bool name="def_airplane_mode_on">false</bool>
     <bool name="def_theater_mode_on">false</bool>
diff --git a/frameworks/base/services/core/java/com/android/server/power/PowerManagerService.java b/frameworks/base/services/core/java/com/android/server/power/PowerManagerService.java
index 7528a749b6..ff6ca47b87 100644
--- a/frameworks/base/services/core/java/com/android/server/power/PowerManagerService.java
+++ b/frameworks/base/services/core/java/com/android/server/power/PowerManagerService.java
@@ -1830,6 +1830,7 @@ public final class PowerManagerService extends SystemService
     }
 
     private int getScreenOffTimeoutLocked(int sleepTimeout) {
+        int nosleep;
         int timeout = mScreenOffTimeoutSetting;
         if (isMaximumScreenOffTimeoutFromDeviceAdminEnforcedLocked()) {
             timeout = Math.min(timeout, mMaximumScreenOffTimeoutFromDeviceAdmin);
@@ -1840,6 +1841,11 @@ public final class PowerManagerService extends SystemService
         if (sleepTimeout >= 0) {
             timeout = Math.min(timeout, sleepTimeout);
         }
+        nosleep = mScreenOffTimeoutSetting;
+        if(nosleep == 0) {
+            nosleep = mMaximumScreenOffTimeoutFromDeviceAdmin ;
+            return Math.max(nosleep, mMaximumScreenOffTimeoutFromDeviceAdmin);
+        }
         return Math.max(timeout, mMinimumScreenOffTimeoutConfig);
     }
 
diff --git a/packages/apps/Settings/res/values/arrays.xml b/packages/apps/Settings/res/values/arrays.xml
index c10057b423..f50595f501 100755
--- a/packages/apps/Settings/res/values/arrays.xml
+++ b/packages/apps/Settings/res/values/arrays.xml
@@ -46,6 +46,7 @@
 
     <!-- Display settings.  The delay in inactivity before the screen is turned off. These are shown in a list dialog. -->
     <string-array name="screen_timeout_entries">
+        <item>Never</item>
         <item>15 seconds</item>
         <item>30 seconds</item>
         <item>1 minute</item>
@@ -57,6 +58,8 @@
 
     <!-- Do not translate. -->
     <string-array name="screen_timeout_values" translatable="false">
+        <!-- Do not translate. -->
+        <item>0</item>
         <!-- Do not translate. -->
         <item>15000</item>
         <!-- Do not translate. -->

```

### 详细说明

#### Setting UI

首先修改setting -> display -> sleep 菜单数组，增加Never选项

文件路径：`packages/apps/Settings/res/values/arrays.xml`

新增如下两行：

```
        <item>Never</item>
-----------------------------------------------
		<!-- Do not translate. -->
        <item>0</item>
```

![image-20230804110656578](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230804110656578.png)

#### 关闭息屏功能

文件路径：`frameworks/base/packages/SettingsProvider/res/values/defaults.xml`

修改`def_screen_off_timeout` 的值为 -1 

```xml
    <!-- <integer name="def_screen_off_timeout">60000</integer> -->
    <integer name="def_screen_off_timeout">-1</integer>
```

![image-20230804111137664](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230804111137664.png)

#### 更改电源管理方案

文件路径：`frameworks/base/services/core/java/com/android/server/power/PowerManagerService.java`

在`getScreenOffTimeoutLocked` 方法中添加如下语句：

```java
    private int getScreenOffTimeoutLocked(int sleepTimeout) {
        int nosleep;    // 新增
        int timeout = mScreenOffTimeoutSetting;
        if (isMaximumScreenOffTimeoutFromDeviceAdminEnforcedLocked()) {
            timeout = Math.min(timeout, mMaximumScreenOffTimeoutFromDeviceAdmin);
        }
        if (mUserActivityTimeoutOverrideFromWindowManager >= 0) {
            timeout = (int)Math.min(timeout, mUserActivityTimeoutOverrideFromWindowManager);
        }
        if (sleepTimeout >= 0) {
            timeout = Math.min(timeout, sleepTimeout);
        }
        // 新增判断开始
        nosleep = mScreenOffTimeoutSetting;
        if(nosleep == 0) {
            nosleep = mMaximumScreenOffTimeoutFromDeviceAdmin ;
            return Math.max(nosleep, mMaximumScreenOffTimeoutFromDeviceAdmin);
        }
        // 新增判断结束
        return Math.max(timeout, mMinimumScreenOffTimeoutConfig);
    }

    private int getScreenDimDurationLocked(int screenOffTimeout) {
        return Math.min(mMaximumScreenDimDurationConfig,
                (int)(screenOffTimeout * mMaximumScreenDimRatioConfig));
    }
```

![image-20230804111333989](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230804111333989.png)

### 中文环境追加修改

#### diff

老规矩，先上diff，可以直接apply

```diff
diff --git a/vendor/qcom/proprietary/qrdplus/globalization/multi-language/res-overlay/packages/apps/Settings/res/values-zh-rCN/arrays.xml b/vendor/qcom/proprietary/qrdplus/globalization/multi-language/res-overlay/packages/apps/Settings/res/values-zh-rCN/arrays.xml
index 093ff5a..f3457f7 100755
--- a/vendor/qcom/proprietary/qrdplus/globalization/multi-language/res-overlay/packages/apps/Settings/res/values-zh-rCN/arrays.xml
+++ b/vendor/qcom/proprietary/qrdplus/globalization/multi-language/res-overlay/packages/apps/Settings/res/values-zh-rCN/arrays.xml
@@ -60,6 +60,7 @@
         <item>"5分钟"</item>
         <item>"10分钟"</item>
         <item>"30分钟"</item>
+        <item>"永不"</item>
     </string-array>

diff --git a/packages/apps/Settings/res/values-zh-rCN/arrays.xml b/packages/apps/Settings/res/values-zh-rCN/arrays.xml
index 5cfbaa2..04b68e3 100755
--- a/packages/apps/Settings/res/values-zh-rCN/arrays.xml
+++ b/packages/apps/Settings/res/values-zh-rCN/arrays.xml
@@ -37,6 +37,7 @@
     <item msgid="7489864775127957179">"5分钟"</item>
     <item msgid="2314124409517439288">"10分钟"</item>
     <item msgid="6864027152847611413">"30分钟"</item>
+    <item >"永不"</item>
   </string-array>
   <string-array name="dream_timeout_entries">
     <item msgid="3149294732238283185">"永不"</item>
```

#### 详细说明

##### 对默认环境修改方案的小修改

修改路径：`frameworks/base/packages/SettingsProvider/res/values/defaults.xml`

将`def_screen_off_timeout`的值改为0.

说明：发现在-1 的时候，中文环境下永不休眠，及时在sleep中选择15s，也不会休眠。

```diff
diff --git a/frameworks/base/packages/SettingsProvider/res/values/defaults.xml b/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
old mode 100644
new mode 100755
index 61e38a3..0e497ab
--- a/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
+++ b/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
@@ -18,7 +18,7 @@
 -->
 <resources>
     <bool name="def_dim_screen">true</bool>
-    <integer name="def_screen_off_timeout">-1</integer>
+    <integer name="def_screen_off_timeout">0</integer>
     <integer name="def_sleep_timeout">-1</integer>
     <bool name="def_airplane_mode_on">false</bool>
     <bool name="def_theater_mode_on">false</bool>
```

##### 设置中添加“永不”

修改路径：

`vendor/qcom/proprietary/qrdplus/globalization/multi-language/res-overlay/packages/apps/Settings/res/values-zh-rCN/arrays.xml`

添加“永不”选项

```xml
    <!-- Display settings.  The delay in inactivity before the screen is turned off. These are shown in a list dialog. -->
    <add-resource type="array" name="screen_timeout_entries"/>
    <string-array name="screen_timeout_entries">
        <item>"永不"</item>  // 新增一行
        <item>"15秒"</item>
        <item>"30秒"</item>
        <item>"1分钟"</item>
        <item>"2分钟"</item>
        <item>"5分钟"</item>
        <item>"10分钟"</item>
        <item>"30分钟"</item>
    </string-array>
```

![image-20230805062311684](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230805062311684.png)

修改路径：`packages/apps/Settings/res/values-zh-rCN/arrays.xml`

添加“永不”选项

```xml
  <string-array name="screen_timeout_entries">
    <item >"永不"</item>
    <item msgid="3342301044271143016">"15秒"</item>
    <item msgid="8881760709354815449">"30秒"</item>
    <item msgid="7589406073232279088">"1分钟"</item>
    <item msgid="7001195990902244174">"2分钟"</item>
    <item msgid="7489864775127957179">"5分钟"</item>
    <item msgid="2314124409517439288">"10分钟"</item>
    <item msgid="6864027152847611413">"30分钟"</item>
  </string-array>
```

![image-20230805062341991](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20230805062341991.png)

msm8909 android7 验证通过！