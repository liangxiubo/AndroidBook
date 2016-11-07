# 10.9接收系统广播信息

除了接受用户发送的广播之外，`BroadcastReceiver`还有一个重要的用途：接收系统广播。如果应用需要在系统特定时刻执行某些操作，就可以通过监听系统广播来实现。Android的大量系统事件都会对外发送标准广播。下面是Android常见的广播Action常亮（具体请参考Android API文档中关于Intent的说明）。
- `ACTION_TIME_CHANGED`：系统时间被改变。
- `ACTION_DATE_CHANGED`：系统日期被改变。
- `ACTION_TIMEZONE_CHANGED`：系统时区被改变
- `ACTION_BOOT_COMPLETED`：系统启动完成
- `ACTION_PACKAGE_ADDED`：系统添加包
- `ACTION_PACKAGE_CHANGED`：系统的包改变
- `ACTION_PACKAGE_REMOVED`：系统的包被删除
- `ACTION_PACKAGE_RESTARTED`：系统的包被重启
- `ACTON_PACKAGE_DATA_CHEARED`：系统的包数据被清空
- `ACTION_BATTERY_CHANGED`：电池电量改变
- `ACTION_BATTERY_LOW`：电池电量低
- `ACTION_POWER_CONNECTED`：系统连接电源
- `ACTION_POWER_DISCONNECTED`：系统与电源断开
- `ACTION_SHUTDOWN`：系统被关闭

通过使用`BroadcastReceiver`来监听特殊的广播，即可让应用随系统执行特定的操作。
## 实例：开机自动运行的Service
前面已经介绍了多个程序，需要使用开机自动运行的Service，例如监听用户来电、监听用户短信、拦截黑名单电话······为了让Service随系统启动自动运行，可以让`BroadcastReceiver`监听Action为`ACTION_BOOT_COMPLETED`常量的Intent，然后在`BroadcastReceiver`中启动特定Service即可。

该程序所用的`BroadcastReceiver`代码如下。
``` java
public class LaunchReceiver extends BroadcastReceiver
{
	@Override
	public void onReceive(Context context, Intent intent)
	{
		Intent tIntent = new Intent(context
				, LaunchService.class);
		// 启动指定Service
		context.startService(tIntent);
	}
}
```
该LaunchReceiver的代码十分简单，只要在`onReceiver()`方法中启动指定Service即可，关键是要让LaunchReceiver监听系统开机发出的广播，因此需要在`AndroidManifest.xml`文件中采用如下代码配置该`BroadcastReceiver`:
``` xml
<!-- 定义一个BroadcastReceiver,监听系统开机广播  -->
<receiver android:name=".LaunchReceiver">
	<intent-filter>
		<action android:name="android.intent.action.BOOT_COMPLETED" />
	</intent-filter>
</receiver>
```
除此之外，为了让程序能访问系统开机事件，还需要为应用程序增加如下权限：
``` xml
<!-- 授予应用程序访问系统开机事件的权限 -->
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
```
至于程序中用到的LaunchService，则可以是用户开发的任意Service，既可以是监听用户来电的Service，也可以是监听用户短信、拦截黑名单电话等的Service，此处不再给出该Service的代码。
## 实例：短信提醒
前面已经提到，当系统收到短信时，系统会对外发送一个有序广播，该广播的Intent的Action为。因此只要在程序中开发一个对应的`BroadcastReceiver`即可监听到系统收到的短信。

该程序对应的`BroadcastReceiver`代码如下。
``` java
public class SmsReceiver extends BroadcastReceiver
{
	// 当接收到短信时被触发
	@Override
	public void onReceive(Context context, Intent intent)
	{
		// 如果是接收到短信
		if (intent.getAction().equals(
				"android.provider.Telephony.SMS_RECEIVED"))
		{
			// 取消广播（这行代码将会让系统收不到短信）
			abortBroadcast();  // ①
			StringBuilder sb = new StringBuilder();
			// 接收由SMS传过来的数据
			Bundle bundle = intent.getExtras();
			// 判断是否有数据
			if (bundle != null)
			{
				// 通过pdus可以获得接收到的所有短信消息
				Object[] pdus = (Object[]) bundle.get("pdus");
				// 构建短信对象array,并依据收到的对象长度来创建array的大小
				SmsMessage[] messages = new SmsMessage[pdus.length];
				for (int i = 0; i < pdus.length; i++)
				{
					messages[i] = SmsMessage
							.createFromPdu((byte[]) pdus[i]);
				}
				// 将发送来的短信合并自定义信息于StringBuilder当中
				for (SmsMessage message : messages)
				{
					sb.append("短信来源:");
					// 获得接收短信的电话号码
					sb.append(message.getDisplayOriginatingAddress());
					sb.append("\n------短信内容------\n");
					// 获得短信的内容
					sb.append(message.getDisplayMessageBody());
				}
			}
			Toast.makeText(context, sb.toString()
					, Toast.LENGTH_LONG).show();
		}
	}

}
```
上面的程序重写了`BroadcastReceiver`的`onReceive()`方法，并在该方法中获取所收到的短信内容，然后使用Toast显示短信内容。

为了让该程序在系统的短信接收程序之前被启动，我们将该`BroadcastReceiver`的优先级设得高一些，在`AndroidManifest.xml`文件中通过如下代码来配置该`BroadcastReceiver`：
``` xml
<receiver android:name="SmsReceiver">
	<intent-filter android:priority="1000">
		<action android:name="android.provider.Telephony.SMS_RECEIVED" />
	</intent-filter>
</receiver>
```
上面的配置片段中粗体字代码指定了该`BroadcastReceiver`的优先级为800，这样它就可以在系统短信接收程序之前被触发。为了让该程序拥有读取短信的权限，还需要在`AndroidManifest.xml`文件中为该程序进行授权，授权的配置代码如下：
``` xml
<!-- 授予程序接收短信的权限 -->
<uses-permission android:name="android.permission.RECEIVE_SMS"/>
```
由于`BroadcastReceiver`将会位于系统短信接收程序之前被启动，如果保持该程序中①号代码生效，那么该程序接收到短信广播之后，广播将不会被传播到系统的短信接收程序——也就是系统本身不会收到短信。

运行该程序，然后再启动另一个模拟器向盖程序所在模拟器发送短信，将可看到图下界面。
![](image/9_1.png)

当然，该程序也可改变城所谓“短信窃听”程序，只要让该程序接收到短信之后并不将该短信显示出来，而是将短信存入系统中，或者再以短信的方式发送到指定手机，这样就实现了“短信窃听”的功能，不过这样做是“不道德”的。

## 实例：手机电量提示
当手机电量发生改变时，系统会对外发送Intent的Action为ACTION_BATTERY_CHANGED常量的广播；当手机电量过低时，系统会对外发送Intent的Action为ACTION_BATTERY_LOW常量的广播，通过开发监听对应Intent的`BroadcastReceiver`，即可让系统对手机电量进行提示。

下面的程序即可对手机电量过低进行提示。

``` java
public class BatteryReceiver extends BroadcastReceiver
{
	@Override
	public void onReceive(Context context, Intent intent)
	{
		System.out.println("+++++++++++++++++++++++");
		Bundle bundle = intent.getExtras();
		// 获取当前电量
		int current = bundle.getInt("level");
		// 获取总电量
		int total = bundle.getInt("scale");
		// 如果当前电量小于总电量的15%
		if (current * 1.0 / total < 0.15)
		{
			Toast.makeText(context, "电量过低，请尽快充电！"
					, Toast.LENGTH_LONG).show();
		}
	}
}
```
上面的程序中粗体字代码即可通过接收到的广播消息获取系统总电量、当前电量，如果当前电量小于总电量的15%，则程序会显示电量过低的提示信息。