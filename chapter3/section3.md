# 基于回调的事件处理
---
## 回调机制和监听机制
* Android基于回调的事件处理，主要做法是重写Android组件特定的回调方法，或者重写Activity的方法。
* 对于基于回调的事件处理模型来说，事件源和事件监听器是统一的，或者说是事件监听器完全消失了。当用户在GUI组件上激发某个事件时，组件自己特定的方法将会负责处理该事件。
* 为了实现回调机制的事件处理，Android为所有GUI组件都提供了一些事件处理的回调方法，以View为例，该类包含如下方法：
 * boolean onKeyDown(int keyCode ,KeyEvent event)：当用户在该组件上按下某个按键时触发该方法。
 * boolean onKeyLongPress(int keyCode, KeyEvent event):当用户在该组件上长按某个按键时触发该方法。
 * boolean onKeyShortcut(int keyCode, KeyEvent event):当一个键盘快捷键事件发生时触发该方法。
 * boolean onKeyUp(int keyCode, KeyEvent event):当用户在该组件上松开某个按键时触发该方法。
 * boolean onTouchEvent(MotionEvent event):当用户在该组件上触发触摸屏事件时触发该方法。
 * boolean onTrackballEvent(MotionEvent event):当用户在该组件上触发轨迹球屏事件时触发该方法。
* Android平台中，每个View都有自己的处理事件的回调方法，开发人员可以通过重写View中的这些回调方法来实现需要的响应事件。当某个事件没有被任何一个View处理时，便会调用Activity中相应的回调方法。

## 基于回调的事件传播
* 几乎所有基于回调的事件处理方法都有一个boolean类型的返回值，该返回值用语标识该处理方法是否能够完全处理该事件。对于基于回调的事件传播而言，某组件上所发生的事件不仅会激发该组件上的回调方法，也会出发该组件所在的Activity的回调方法——只要事件能传播到Activity。
* 下面的程序示范了Android系统中的事件传播，该程序重写了Button类的onKeyDown(int keyCode, KeyEvent event)方法，而且重写了该Button所在Activity的onKeyDown(int keyCode, KeyEvent event)方法——程序没有阻止事件传播，因此程序可以看到事件从Button传播到Activity的情形。下面是从Button派生而出的MyButton子类代码。
```
public class MyButton extends Button
{
	public MyButton(Context context , AttributeSet set)
	{
		super(context , set);
	}
	@Override
	public boolean onKeyDown(int keyCode, KeyEvent event)
	{
		super.onKeyDown(keyCode , event);
		Log.v("-MyButton-", "the onKeyDown in MyButton");
		// 返回false，表明并未完全处理该事件，该事件依然向外扩散
		return false;
	}
}
```
* 上面的MyButton子类重写了onKeyDown(int keyCode, KeyEvent event)方法，当用户在该按钮上按下某个键是将会触发该方法。但由于该方法反悔了false，这意味着还会像外传播。
```
public class MainActivity extends Activity
{
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Button bn = (Button) findViewById(R.id.bn);
		// 为bn绑定事件监听器
		bn.setOnKeyListener(new OnKeyListener() {
			@Override
			public boolean onKey(View source
					, int keyCode, KeyEvent event) {
				// 只处理按下键的事件
				if (event.getAction() == KeyEvent.ACTION_DOWN) {
					Log.v("-Listener-", "the onKeyDown in Listener");
				}
				// 返回false，表明该事件会向外传播
				return true; // ①
			}
		});
	}
	// 重写onKeyDown方法，该方法可监听它所包含的所有组件的按键被按下事件
	@Override
	public boolean onKeyDown(int keyCode, KeyEvent event)
	{
		super.onKeyDown(keyCode , event);
		Log.v("-Activity-" , "the onKeyDown in Activity");
		//返回false，表明并未完全处理该事件，该事件依然向外扩散
		return false;
	}
}
```
* 从上面程序可以看出Activity的onKeyDown(int keyCode, KeyEvent event)方法被重写了，当在该Activity包含的所有组件上按下某个键是，该方法都可能被触发。

## 重写onTouchEvent方法响应触摸屏事件
* 对比Android提供的两种事件处理模型，不难发现基于监听的事件处理模型具有更大的优势：
 * 基于监听的事件模型分工更明确，事件源、事件监听器由两个类分开实现，因此具有更好的可维护性。
 * Android的事件处理机制保证基于监听的事件监听器会被优先触发。