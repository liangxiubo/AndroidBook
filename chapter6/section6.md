# 6.6菜单资源
---
前面已经介绍过Android的菜单支持，前面分别介绍了如何使用Java代码来实现菜单和使用XML资源文件夹来定义菜单。  
实际上，Android推荐使用XML资源文件夹来定义菜单，这样将会提供更好的解耦。由于前面介绍过如何使用XML资源文件定义菜单，因此此处不再详细介绍菜单资源文件的内容，只是对其进行简单的归纳。  
Android菜单资源文件放在/res/menu目录下，菜单资源的根元素通常是<menu.../>元素，<menu.../>元素无须知道任何属性。  
一旦在Android项目中定义了菜单资源，接下来在XML文件中就可通过如下语法格式来访问它：
```
@[<package_name>:]menu/file_name
```
  在Java代码中则按如下语法格式来访问：
```
@[<package_name>:]R.menu.<file_name>
```

###总结：
* 资源位置：res\menu\my_menu.xml;
* 菜单资源文件结构：&lt;memu&gt;根元素，在&lt;memu&gt;根元素里嵌套&lt;item&gt;和&lt;group&gt;子元素，&lt;item&gt;元素中也可以嵌套&lt;memu&gt;形成子菜单；
* &lt;memu&gt;根元素没有属性，它包含&lt;item&gt;和&lt;group&gt;子元素；
* &lt;group&gt;表示菜单组，属性说明：  
id:唯一标示该菜单组的引用id;  
menuCategory:一个分类排序整数；  
checkableBehavior:选择行为，单选、多选或其他，有效值为none、all、single；  
visible:是否可见，true或false；  
enabled:是否可用, true或false；
* &lt;item&gt;表示菜单项，包含在&lt;memu&gt;或&lt;group&gt;中，属性如下：  
id:唯一标示该菜单组的引用id;  
memuCategory:菜单分类；  
title：菜单标题字符串；  
titleCondensed:浓缩标题，适合标题太长的时候使用；  
icon:菜单的图标；
* alphabeticShortcut:字符快捷键；  
numericShortcut:数字快捷键；  
checked:是否已经被选；  
visible:是否可见；  
enabled:是否可用；
