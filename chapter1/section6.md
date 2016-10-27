# Android应用的基本组件介绍

---
###Activity
* Activity是Android应用中负责与用户交互的组件，大致可将它想象成Swing编程中的JFrame控件。View组件是所有UI控件、容器控件的基类，View组件需放到容器组件或Activity将它显示出来，调用Activity的setContentView()方法即可。
 * setContentView(layout); setContentView(R.layout.main)
* 一个Android应用一般包含多个Activity，多个Activity组成Activity栈，当前活动的Activity位于栈顶
* Activity包含了一个setTheme(int resid)方法来设置窗口风格，如可设置窗口不显示标题、以对话框的形式显示窗口





###Service
* Service与Activity功能类似，区别在于Service通常位于后台运行，一般不需要与用户交互，因此Service组件没有用户界面
* 与Activity组件需要继承Activity基类相似，自定义Service组件需要继承Service基类。
* Service运行后，拥有自己独立的生命周期。
* Service组件通常用于为其他组件提供后台服务或监控其他组件的运行状态。
###BroadcastReceiver
* BroadcastReceiver，广播接收器。从编程角度来看，BroadcastReceiver非常类似事件编程中的监听器，与普通监听器不同的是：BroadcastReceiver是系统级的监听器，监听的事件源是其他Android应用组件，而普通事件监听器监听的事件源是本程序中的对象。
* BroadcastReveiver是Android应用四大组件中最简单的组件，编码比较简单，只要实现自己的BroadcastReveiver子类，并重写onReceive()方法即可。当其他组件通过sendBroadcast()、 sendStickyBroadcast()、 sendOrderedBroadcast()方法发送广播消息时，如果与该BroadcastReveiver匹配， onReceive()方法将会被调用。
###ContentProvider
* Android系统中，各个应用相互独立运行于各自的Dalvik虚拟机实例中，但有的应用之间需要实时数据交换。
*如开发一个发送短信的应用程序，需要从联系人管理应用中读取指定联系人的信息*
* Android为这种跨应用的数据交换提供了一个标准ContentProvider。*实现自己的ContentProvider时，需要实现insert、delete、update、query方法。*
###Intent
* Intent是Android应用内不同组件之间通信的载体，当Android应用需要连接不同的组件时，通常需要借助Intent来实现。
* Intent可以应用中启动另一个Activity、Service，还可用于发送广播消息来触发BroadcastReceiver。
* Intent分两类：
>*显式Intent：明确指定需要启动或触发的组件名称
>隐式Intent：指定需要启动或触发的组件应满足怎样的条件，这需要通过IntentFilter指定过滤条件*

* 








