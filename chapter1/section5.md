# Android应用结构分析
---
Android项目必需的目录和文件包括：**res目录**、**src目录**、**AndroidManifest.xml文件**：
###res目录

存放Android项目的各种资源：layout（界面布局）、menu（菜单）、values（字符串、颜色、尺寸）、drawable系列目录分别存放不同大小的图片资源。
* res目录说明：将不同的资源放在res目录下的不同文件夹内，方便aapt工具扫描并生成资源清单类R.java
   * 如res/values/strings.xml定义了一条条的字符串常量
   
   ```
   <?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">HelloWorld1</string>
    <string name="action_settings">Settings</string>
    <string name="hello_world">Hello world!</string>
</resources>

   ```
   * 在Java代码中可通过R.string.app_name来引用“HelloWorld”字符串常量
   * 在XML文件中可通过@string/app_name来使用“HelloWorld”字符串，例外情况：XML中使用标识符无需专门的资源定义，如android:id=“@+id/ok”，这里自动为该组件分配了一个标识符

###src目录

普通的、保存Java源文件的目录。

###AndroidManifest.xml文件

Android项目的全局描述文件，说明了应用的名称、图标及所包含的组件，通常包含：
* 应用程序的包名，该包名将作为该应用的唯一标识
* 应用程序所包含的组件，如Activity、Service、BroadcastReceiver和Content Provider等
* 应用程序兼容的最低版本
* 应用程序使用系统所需的权限声明
* 其他程序访问该应用程序所需的权限声明
#####应用程序权限
* 声明自身所拥有权限：在元素<manifest…/>添加 <uses-permission…/> 子元素，如声明应用本身需要打电话的权限
：

```
<uses-permission android:name=“android.permission.CALL_PHONE”>
```
  * 声明调用某组件所需权限：在应用的各组件元素添加permission属性，如:

```
 <activity android:name=".PhoneActivity"   
      android:permission="scott.permission.MY_CALL_PHONE">
```
如要使用该组件的功能，则必须通过<uses-permission…/>声明该权限

###自动生成的R.java
* R.java文件是由aapt工具根据应用中的资源文件自动生成的，写代码时不需要修改该文件，可将R.java理解成Android应用的资源字典。aapt生成R.java文件的规则：
 * 每类资源对应R类的一个内部类。比如所有节目布局资源对应于layout内部类；所有字符串资源对应于string内部类；所有标识符资源对应于id内部类。
 * 每个具体的资源项对应于内部类的一个public static final int类型的Field。比如HelloWorld程序中界面布局中用到了textview1、button1标识符，R.id类里就包含了这两个Field。














