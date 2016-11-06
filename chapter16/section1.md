# 16.1支持GPS的核心API

##GPS介绍
Android为GPS功能支持专门提供了一个**LocationManage**r类，它的作用和TelephonyManager，AudioManager等服务类的作用类似，所有GPS定位相关的服务、对象都由该对象产生。
与程序中获取TelephonyManager、AudioManager的方法类似，程序并不能直接创造
LocationManager的实例，而是通过调用Context的`getSystemService（）`方法来获取，例如如下代码：
```java
LocationManager lm = (LocationManager) getSystemService(Context.LOCATION_SERVICE);
```
一旦在程序中获得了LocationManager对象之后，接下来即可调用LocationManager的方法来获取GPS定位相关服务和对象了。LocationManager提供了如下常用方法。

* **boolean addGpsStatusListener(GpsStatus.Listener listener)**:添加一个监听GPS状态的监听器； 
* **void addProximityAlert(double latitude,double longitude,float radius,long expiration,PendingIntent intent)**:添加一个临近警告； 
* **List getAllProviders()**:获取所有的LocationProvider列表； 
* **String getBestProvider(Criteria criteria,boolean enabledOnly)**:根据制定条件返回最优的LocationProvider对象； 
* **GpsStatus getGpsStatus(GpsStatus status)**:获取GPS状态； 
* **Location getLastKnownLocation(String provider)**:根据LocationProvider获取最近一次已知的Location； 
* **LocationProvider getProvider(String name)**:根据名称来获取LocationProvider； 
* **List getProviders(Criteria criteria,boolean enabledOnly)**:根据制定条件获取满足条件的全部LocationProvier的名称； 
* **List getProviders(boolean enabledOnly)**:获取所有可用的LocationProvider； 
* **boolean isProviderEnabled(String provider):**判断制定名称的LocationProvider是否可用； 
* **void removeGpsStatusListener(GpsStatus.Listener listener)**:删除GPS状态监听器； 
* **void removeProximityAlert(PendingIntent intent)**:删除一个趋近警告； 
* **void requestLocationUpdates(String provider,long minTime,float minDistance,PendingIntent intent)**:通过指定的LocationProvider周期性获取定位信息，并通过Intent启动相应的组件； 
* **void requestLocationUpdates(String provider,long minTime,float minDistance,LcoationListener listener)**:通过指定的LocationProvider周期性的获取定位信息，并触发listener对应的触发器；

---

在上面的方法别表中设计一个**GPS定位**支持的另一个重要的API：
LocationProvider（定位提供者），LocationProvider对象就是定位组建的抽象表示，通过LocationProvider可以获取该定位组建的相关信息。LocationProvider提供了如下常用方法。
* **String getName()**:返回该LocationProvider的名称； 
* **int getAccuracy()**:返回该LocationProvider的精度； 
* **int getPowerRequirement()**:返回该LocationProvider的电源需求； 
* **boolean hasMonetaryCost()**:返回LocationProvider是收费还是免费； 
* **boolean meetsCriteria(Criteria criteria)**:判断该LocationProvider是否满足Criteria条件； 
* **boolean requiresCell()**:判断该LocationProvider是否需要访问网路基站； 
* **boolean requiresNetword()**:判断该LocationProvider是否需要网路数据； 
* **boolean requiresStatellite()**:判断该LocationProvider是否需要访问卫星的定位系统； 
* **boolean supportsAltitude()**:判断该LocationProvider是否支持高度信息; 
* **boolean supportsBearing()**:判断该LocationProvider是否支持方向信息; 
* **boolean supportsSpeed()**:判断该LocationProvider是否支持速度信息;

---
除此之外，GPS支持还有一个支持API：**Location**，它就是一个代表位置信息的抽象类，提供了如下方法来获取定位信息。

* **loat getAccuracy()**:获取定位信息的精度； 
* **double getAltitude()**:获取定位信息的高度； 
* **float getBearing()**:获取定位信息的方向； 
* **double getLatitude()**:获取定位信息的经度； 
* **double getLongitude()**:获取定位信息的纬度； 
* **String getProvider()**:获取提供该定位信息的LocationProvider； 
* **float getSpeed()**:获取定位信息的速度； 
* **boolean hasAccuracy()**:判断该定位信息是否有经度信息； 
* **boolean hasAltitude()**:判断定位信息是否有高度信息; 
* **boolean hasSpeed()**:判断定位信息是否有速度信息;


上面三个API就是Android GPS支持的三个核心API，使用它们来获取GPS信息的通用步骤为：
1. 获取系统的LocationManager对象； 
2. 使用LocationManager，通过制定LocationProvider来获取定位信息，定位信息由Location表示；
3. 从Location对象中获取定位信息；


