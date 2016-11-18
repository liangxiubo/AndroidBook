# 10.6振动器

在某些时候，程序需要启动系统振动器，比如手机静音时使用振动提示用户；再比如玩游戏时，当系统碰撞、爆炸时使用振动带给用户更逼真的体验等。总之，振动是除视频、声音之外的另一种“多媒体”，充分利用系统的振动器会带给用户更好的体验。

系统获取`Vibrator`也是调用`Context`的`getSystemService()`方法即可，接下来就可调用`Vibrator`的方法来控制手机振动了。

## 10.6.1 Vibrator简介

`Vibrator`的使用比较简单，它只有三个简单的方法来控制手机振动。

- `vibrate(long milliseconds)`：控制手机振动millseconds毫秒。
- `vibrate(long[] pattern, int repeat)`：指定手机以`pattern`指定的模式振动。例如指定`pattern`为`new int[100,800,1200,1600]`，就是指定在400ms、800ms、1200ms、1600ms这些时间点交替启动、关闭手机振动器；其中`repeat`指定`pattern`数组的索引，指定对`pattern`数组中从`repeat`索引开始的振动进行循环。
- `cancel()`：关闭手机振动。

掌握这些方法之后，接下来就可在程序中通过`Vibrator`来控制手机振动了。

## 10.6.2 使用Vibrator控制手机振动

下面这个程序十分简单，程序几乎不需要界面，重写了`Activity`的`onTouchEvent(MotionEvent event)`方法，这样使得用户触碰手机触摸屏时将会启动手机振动。程序代码如下。

```````````````` java
public class MainActivity extends Activity
{
	Vibrator vibrator;
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 获取系统的Vibrator服务
		vibrator = (Vibrator) getSystemService(
				Service.VIBRATOR_SERVICE);
	}
	// 重写onTouchEvent方法，当用户触碰触摸屏时触发该方法
	@Override
	public boolean onTouchEvent(MotionEvent event)
	{
		Toast.makeText(this, "手机振动"
				, Toast.LENGTH_SHORT).show();
		// 控制手机振动2秒
		vibrator.vibrate(2000);
		return super.onTouchEvent(event);
	}
}
`````````````````

上面的程序第一行粗体字代码用于获取系统的`Vibrator`对象，第二行粗体字代码用于开始手机振动2秒。

需要指出的是，程序控制手机振动需要得到相应的权限，因此不要忘了在`AndroidManifest.xml`文件中增加如下授权配置代码：

```````````xml
<!-- 授予程序访问振动器的权限 -->
<uses-permission android:name="android.permission.VIBRATE"
```````````
在模拟器中运行该程序看不出自动效果，建议读者将该程序部署到真机上运行改程序。