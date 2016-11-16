# 8.3手势#

---
所谓手势，其实是指用户手指或触摸笔在触摸屏上的连续触碰行为，比如在大屏幕上从左至右划出一个动作，再比如在屏幕上画一个圆圈。手势这种连续的触碰会形成某个方向上的移动趋势，也会形成一个不规则的几何图形。Android对两种�手势行为都提供了支持。
> * 对于第一种手势行为，android提供了手势检测，并为手势检测提供了相应的监听器。
> * 对于第二种手势行为，android允许开发者添加手势，并提供了相应的API识别用户手势。

**8.3.1手势检测**
Android为手势检测提供了一个GestureDetector类，GestureDetector实例代表了一个手势检测器，创建GestureDetector时需要传入一个GestureDetector.OnGestureListener实例，GestureDetector.OnGestureListener就是一个监听器，负责对用户的手势行为提供响应。
GestureDetector.OnGestureListener里包含的事件处理方法如下：
> * boolean onDown(MotionEvent e):当触碰事件按下时触发该方法。
> * boolean onFling(MotionEvent e1,MotionEvent e2,float velocityX,float velocityY):当在触摸屏上“拖过”时触发该方法。
> * boolean onLongPress(MotionEvent e):当触碰事件是长按下时触发该方法
> * boolean onScroll(MotionEvent e1,MotionEvent e2,float velocityX,float velocityY):当在触摸屏上“滚动”时触发该方法。
> * boolean onShowPress(MotionEvent e):当手指在触摸屏上按下，而且还未移动和松开时触发该方法。
> * boolean onSingleTapUp(MotionEvent e):当手指在触摸屏上轻击时触发该方法。

使用android的手势检测只需要两个步骤：
1、创建一个GestureDetector对象。创建时需要实现一个GestureDetector.OnGestureListener监听器实例。
2、为应用程序的Activity（偶尔也可以为特定组件）的TouchEvent事件绑定监听器。把TouchEvent事件交给GestureDetector处理。
下面是测试不同手势的代码：
```java
public class MainActivity extends Activity
		implements OnGestureListener
{
	// 定义手势检测器实例
	GestureDetector detector;
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		//创建手势检测器
		detector = new GestureDetector(this, this);
	}
	//将该Activity上的触碰事件交给GestureDetector处理
	@Override
	public boolean onTouchEvent(MotionEvent me)
	{
		return detector.onTouchEvent(me);
	}
	@Override
	public boolean onDown(MotionEvent arg0)
	{
		Toast.makeText(this,"onDown"
				, Toast.LENGTH_SHORT).show();
		return false;
	}
	@Override
	public boolean onFling(MotionEvent e1, MotionEvent e2
			, float velocityX, float velocityY)
	{
		Toast.makeText(this , "onFling"
				, Toast.LENGTH_SHORT).show();
		return false;
	}
	@Override
	public void onLongPress(MotionEvent e)
	{
		Toast.makeText(this ,"onLongPress"
				, Toast.LENGTH_SHORT).show();
	}
	@Override
	public boolean onScroll(MotionEvent e1, MotionEvent e2
			, float distanceX, float distanceY)
	{
		Toast.makeText(this ,"onScroll" ,
				Toast.LENGTH_SHORT).show();
		return false;
	}
	@Override
	public void onShowPress(MotionEvent e)
	{
		Toast.makeText(this ,"onShowPress"
				, Toast.LENGTH_SHORT).show();
	}
	@Override
	public boolean onSingleTapUp(MotionEvent e)
	{
		Toast.makeText(this ,"onSingleTapUp"
				, Toast.LENGTH_SHORT).show();
		return false;
	}
}
```

**8.3.2识别用户手势**

GestureLibrary提供了recognize(Gesture ges)方法来识别手势，该方法会返回该手势库中所有与ges匹配的手势————两个手势的图形越相似，相似度越高。
recognize(Gesture ges)方法的返回值为Array`<Prediction>`，其中，Prediction封装了手势的匹配信息，Prediction对象的name属性代表了匹配的手势名，score属性代表了手势的相似度。
下面的程序会利用自己创建的手势�库来识别手势：
```java
public class MainActivity extends Activity
{
	// 定义手势编辑组件
	GestureOverlayView gestureView;
	// 记录手机上已有的手势库
	GestureLibrary gestureLibrary;
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 读取自己所创建的手势库
		gestureLibrary = GestureLibraries
				.fromFile("/mnt/sdcard/mygestures");
		if (gestureLibrary.load())
		{
			Toast.makeText(MainActivity.this, "手势文件装载成功！",
					Toast.LENGTH_SHORT).show();
		}
		else
		{
			Toast.makeText(MainActivity.this, "手势文件装载失败！",
					Toast.LENGTH_SHORT).show();
		}
		// 获取手势编辑组件
		gestureView = (GestureOverlayView) findViewById(R.id.gesture);
		// 为手势编辑组件绑定事件监听器
		gestureView.addOnGesturePerformedListener(
				new OnGesturePerformedListener()
				{
					@Override
					public void onGesturePerformed(GestureOverlayView
														   overlay, Gesture gesture)
					{
						// 识别用户刚刚所绘制的手势
						ArrayList<Prediction> predictions = gestureLibrary
								.recognize(gesture);
						ArrayList<String> result = new ArrayList<String>();
						// 遍历所有找到的Prediction对象
						for (Prediction pred : predictions)
						{
							// 只有相似度大于2.0的手势才会被输出
							if (pred.score > 2.0)
							{
								result.add("与手势【" + pred.name + "】相似度为"
										+ pred.score);
							}
						}
						if (result.size() > 0)
						{
							ArrayAdapter<Object> adapter = new
									ArrayAdapter<Object>(MainActivity.this,
									android.R.layout.simple_dropdown_item_1line
									, result.toArray());
							// 使用一个带List的对话框来显示所有匹配的手势
							new AlertDialog.Builder(MainActivity.this)
									.setAdapter(adapter, null)
									.setPositiveButton("确定", null).show();
						}
						else
						{
							Toast.makeText(MainActivity.this
									, "无法找到能匹配的手势！",
									Toast.LENGTH_SHORT).show();
						}
					}
				});
	}
}
```

手势库会识别用户输入的手势，判断它的相似度。