# Android的事件处理
---
##Android事件概述
* 不管是桌面应用还是手机应用程序，面对最多的就是用户，经常需要处理的就是用户动作——也就是需要为用户的动作提供响应，这种为用户动作提供响应的机制就是事件处理。
* Android提供了强大的事件处理机制，包括两套事件处理机制：
 * *基于监听的事件处理* 和 *基于回调的事件处理*。
* 对于Android基于回调的事件处理而言，主要做法就是重写Android组件特定的回调方法，或者重写Activity的回调方法。Android为绝大部分界面组件都提供了事件响应的回调方法，开发者只要重写它们即可。
* 一般来说，基于回调的事件处理可用于处理一些具有通用性的事件，基于回调的事件处理代码会显得比较简洁。但对于某些特定的事件，无法使用基于回调的事件处理，只能采用基于监听的事件处理。
**Tips：**
 * *Android还允许在界面布局文件中为UI组件的 android:onClick属性指定事件监听方法，通过这种方式指定事件监听方法时，开发址需要在Activity中定义该事件监听方法（该方法必须有一个View类型的形参，该形参代表被单击的UI组件）。当用户单击该UI组件时，系统将会激发 android:onClick属性所指定的方法。*