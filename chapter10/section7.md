# 10.7手机闹铃服务

`AlarmManager`通常的用途就是用来开发手机闹钟，但实际上它的作用不止于此。它的本质是一个全局的定时器，`AlarmManager`可在指定时间或制定周期启动其它组件（包括`Activity`、`Service`、`BroadcastReceiver`）。

## 10.7.1 AlarmManager简介

`AlarmManager`不仅可用于开发闹钟应用，还可作为一个全局定时器使用，Android应用的程序中也是通过`Context`的`getSystemService()`方法来获取`AlarmManager`对象，一旦程序获取了`AlarmManager`对象之后，就可调用它的如下方法来设置定时启动指定组件。
- `set(int type, long triggerAtTime, PendingIntent operation)`：设置在`triggerAtTime`时间启动由`operation`参数指定的组件。其中第一个参数指定定时服务的类型，该参数可接受如下值。
 1. `ELAPSED_REALTIME`：指定从现在开始时间过了一定时间后启动`operation`所对应的组件。
 - `ELAPSED_REALTIME_WAKEUP`：指定从现在开始时间过了一定时间后启动`operation`所对应的组件。即使系统关机也会执行`operation`所对应的组件。
 - `RTC`：指定当系统调用`System.currentTimeMillis()`方法返回值与`triggerAtTime`相等时启动`operation`所对应的组件。
 - `RTC_WAKEUP`：指定当系统调用`System.currentTimeMillis()`方法返回值与`triggerAtTime`相等时启动`operation`所对应的组件。即使系统关机也会执行`operation`所对应的组件。
- `setInexactRepeating(int type, long triggerAtTime, long interval, PendingIntent operation)`：设置一个非精确的周期任务。例如我们设置`Alarm`每个小时启动一次，但系统并不一定总在每个小时的开始启动`Alarm`服务。
- `setRepeating(int type, long triggerAtTime, long interval, PendingIntent operation)`：设置一个周期性执行的定时服务。
- `cancel(PendingIntent operation)`：取消`AlarManager`的定时任务。

掌握了`AlarmManager`的如上功能之后，接下来我们通过两个示例来示范`AlarmManager`在实际开发中的用途。

## 设置闹钟

这个程序比较简单，程序提供一个按钮让用户来设置闹铃时间（单击该按钮将会打开一个时间设置对话框），当用户设置好闹铃时间之后，即使退出该程序，到了预设时间`ManagerAlarm`一样会启动指定组件——这是因为`AlarmManager`是一个全局定时器的缘故。

该程序的界面布局很简单，程序界面上只有一个简单的按钮，程序代码如下。

``` java
public class MainActivity extends Activity
{
	Button setTime;
	Calendar currentTime = Calendar.getInstance();
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 获取程序界面的按钮
		setTime = (Button) findViewById(R.id.setTime);
		// 为“设置闹铃”按钮绑定监听器。
		setTime.setOnClickListener(new OnClickListener()
		{
			@Override
			public void onClick(View source)
			{
				Calendar currentTime = Calendar.getInstance();
				// 创建一个TimePickerDialog实例，并把它显示出来。
				new TimePickerDialog(MainActivity.this, 0, // 绑定监听器
					new TimePickerDialog.OnTimeSetListener()
					{
						@Override
						public void onTimeSet(TimePicker tp,
							int hourOfDay, int minute)
						{
							// 指定启动AlarmActivity组件
							Intent intent = new Intent(MainActivity.this,
								AlarmActivity.class);
							// 创建PendingIntent对象
							PendingIntent pi = PendingIntent.getActivity(
									MainActivity.this, 0, intent, 0);
							Calendar c = Calendar.getInstance();
							c.setTimeInMillis(System.currentTimeMillis());
							// 根据用户选择时间来设置Calendar对象
							c.set(Calendar.HOUR, hourOfDay);
							c.set(Calendar.MINUTE, minute);
							// 获取AlarmManager对象
							AlarmManager aManager = (AlarmManager)
								getSystemService(ALARM_SERVICE);
							// 设置AlarmManager将在Calendar对应的时间启动指定组件
							aManager.set(AlarmManager.RTC_WAKEUP,
									c.getTimeInMillis(), pi);
							// 显示闹铃设置成功的提示信息
							Toast.makeText(MainActivity.this, "闹铃设置成功啦"
								, Toast.LENGTH_SHORT).show();
						}
					}, currentTime.get(Calendar.HOUR_OF_DAY), currentTime
					.get(Calendar.MINUTE), false).show();
			}
		});
	}
}
```

上面的程序中粗体字代码控制`AlarmManager`将会在`Calendar`对应的时间启动pi对应的`Activity`组件，而且程序设置了`AlarmManager.RTC_WAKEUP`选项，这意味着即使系统处于关机状态，到了系统预设时间，`AlarmManager`也会控制系统去执行pi对应的`Activity`组件。

上面的程序中`AlarmManager`需要启动的`Activity`为`AlarmActivity`，他是一个非常简单的`Activity`，甚至不需要程序界面，当该`Activity`加载时打开一个对话框提示闹钟时间到，并播放一段“激昂”的音乐提醒用户。`AlarmActivity`程序代码如下。

``` java
public class AlarmActivity extends Activity
{
	MediaPlayer alarmMusic;
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		// 加载指定音乐，并为之创建MediaPlayer对象
		alarmMusic = MediaPlayer.create(this, R.raw.alarm);
		alarmMusic.setLooping(true);
		// 播放音乐
		alarmMusic.start();
		// 创建一个对话框
		new AlertDialog.Builder(AlarmActivity.this).setTitle("闹钟")
				.setMessage("闹钟响了,Go！Go！Go！")
				.setPositiveButton("确定", new OnClickListener()
				{
					@Override
					public void onClick(DialogInterface dialog, int which)
					{
						// 停止音乐
						alarmMusic.stop();
						// 结束该Activity
						AlarmActivity.this.finish();
					}
				}).show();
	}
}
```

![](image/7_1.png)

上面的`Activity`的作用只是通过一个对话框、一点音乐来提醒用户：脑中时间到了。

不要忘记在`AndroidManifest.xml`文件中配置`AlarmActivity`，如果用户忘记配置该`Activity`，系统甚至不会提示用户：该`Activity`不存在！只是到了系统预设时间之后，程序将什么也不干。

运行改程序，简单地把闹钟时间设为下一分钟，即使我们退出该程序，接下来一分钟之后将可以看到上图所示的闹钟提示。

## 实例：定时更换壁纸

本例程序将会通过`AlarmManager`来周期性地调用某个`Service`，从而让系统实现定时更换壁纸的功能。

更换壁纸的API为`WallpaperManager`，它提供了`clear()`方法来清除壁纸，还提供了如下方法来设置系统的壁纸。

- `setBitmap(Bitmap bitmap)`：将壁纸设置`bitmap`所代表的位图。
- `setResource(int resid)`：将壁纸设为`resid`资源所代表的图片。
- `setStream(InputStream data)`：将壁纸设置为`data`数据所代表的图片。

该程序的界面只有两个按钮，一个按钮用于启动定时更换壁纸，另一个按钮用于关闭定时更换壁纸。该程序的代码如下。

``` java
public class MainActivity extends Activity
{
	Button start, stop;
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		start = (Button) findViewById(R.id.start);
		stop = (Button) findViewById(R.id.stop);
		// 指定启动ChangeService组件
		Intent intent = new Intent(MainActivity.this, ChangeService.class);
		// 创建PendingIntent对象
		final PendingIntent pi = PendingIntent.getService(MainActivity.this, 0, intent, 0);
		start.setOnClickListener(new OnClickListener()
		{
			@Override
			public void onClick(View arg0)
			{
				// 获取AlarmManager对象
				AlarmManager aManager = (AlarmManager) getSystemService(
					Service.ALARM_SERVICE);
				// 设置每隔5秒执行pi代表的组件一次
				aManager.setRepeating(AlarmManager.ELAPSED_REALTIME_WAKEUP
					, 0, 5000, pi);
				start.setEnabled(false);
				stop.setEnabled(true);
				Toast.makeText(MainActivity.this
					, "壁纸定时更换启动成功啦",
					Toast.LENGTH_SHORT).show();
			}
		});
		stop.setOnClickListener(new OnClickListener()
		{
			@Override
			public void onClick(View arg0)
			{
			start.setEnabled(true);
			stop.setEnabled(false);
			// 获取AlarmManager对象
			AlarmManager aManager = (AlarmManager) getSystemService(
				Service.ALARM_SERVICE);
			// 取消对pi的调度
			aManager.cancel(pi);
			}
		});
	}
}
```

上面的程序中粗体字代码指定程序每隔5秒执行一次pi所代表组件。程序中pi代表了`ChangeService`组件，因此还需要为该应用程序提供一个`ChangeService`组件，该组件的代码如下。

``` java
public class ChangeService extends Service
{
	// 定义定时更换的壁纸资源
	int[] wallpapers = new int[]{
			R.drawable.shuangta,
			R.drawable.lijiang,
			R.drawable.qiao,
			R.drawable.shui
	};
	// 定义系统的壁纸管理服务
	WallpaperManager wManager;
	// 定义当前所显示的壁纸
	int current = 0;
	@Override
	public int onStartCommand(Intent intent, int flags, int startId)
	{
		// 如果到了最后一张，系统重新开始
		if(current >= 4)
			current = 0;
		try
		{
			// 改变壁纸
			wManager.setResource(wallpapers[current++]);
		}
		catch (Exception e)
		{
			e.printStackTrace();
		}
		return START_STICKY;
	}
	@Override
	public void onCreate()
	{
		super.onCreate();
		// 初始化WallpaperManager
		wManager = WallpaperManager.getInstance(this);
	}
	@Override
	public IBinder onBind(Intent intent)
	{
		return null;
	}
}
```

上面的程序重写了`Service`的`onStartCommand()`方法。这意味着程序每次启动该`Service`都会执行`onStartCommand()`方法中的代码，而`onStartCommand()`方法中的粗体字代码负责更换系统的壁纸。

为了允许该程序改变壁纸，还需要在`AndroidManifest.xml`文件中为该程序授予相应的权限，也就是在`AndroidManifest.xml`文件中增加如下代码：

``` xml
    <!-- 授予用户修改壁纸的权限 -->
	<uses-permission android:name="android.permission.SET_WALLPAPER"/>
```
运行该程序，并单击程序界面上的“启动定时更换”按钮，接着退出该程序，返回到程序桌面，将可以看到系统桌面每5秒更换一次，如下图所示。

![](image/7_2.png)

当然这个程序还有值得改进的地方，这个程序所更新的壁纸只是程序预设的几张图片，如果把这个程序与前面介绍的SD卡文件浏览器结合起来，让用户可以添加SD卡中的图片作为可供更换的壁纸图片，那么这个程序就算比较完善了。