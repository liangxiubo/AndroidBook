# 7.4逐帧动画
---
逐帧动画按照时间的先后顺序，一次播放排列好的图片。利用人眼“视觉暂留”的原理，给用户造成“动画”的错觉。逐帧动画的原理与放电影的原理完全一样。

### AnimationDrawable与逐帧动画
* 动画资源文件介绍
 * /res/anim/****.xml:资源文件中使用<animation-list…/>元素中使用<item…/>子元素定义动画的全部帧，并指定各帧的持续时间。
* 逐帧动画的简单示例
```<?xml version="1.0" encoding="utf-8"?>
<!-- 指定动画循环播放 -->
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
android:oneshot="false">
<!-- 添加多个帧 -->
  <item android:drawable="@drawable/fat_po_f01" android:duration="60" />
  <item android:drawable="@drawable/fat_po_f02" android:duration="60" />
  <item android:drawable="@drawable/fat_po_f03" android:duration="60" />
  ...
</animation-list>```









