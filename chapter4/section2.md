# Activity的会调机制

*  Activity被开发出来，开发者只要在AndroidManifest.xml文件配置该Activity即可，至于该Activity何时被实例化，方法何时被调用，开发者不必关心。
* 前面介绍了事件的回调机制， Activity的回调机制也类似，当Activity被部署在Android应用中之后，随着应用的运行， Activity会不断地在不同的状态之间切换，该Activity中特定的方法就会被回调.