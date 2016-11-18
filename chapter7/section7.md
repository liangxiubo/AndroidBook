# 7.7使用SurfaceView实现动画
---
虽然前面有大量的使用view进行绘图，但View的绘图机制存在如下缺陷。

* View缺乏双缓冲机制。
* 当程序需要更新View上的图片时，程序必须重绘View上显示的整张图片。
* 新线程无法直接更新View组件。

因此，Android提供了一个SurfaceView来代替View，在实现游戏绘图方面，SurfaceView比View更加出色。

### SurfaceView的绘图机制

SurfaceView一般会与SurfaceHolder结合使用，SurfaceHolder用于向与之关联的SurfaceView上绘图，调用SurfaceView的getHolder()方法即可获取SurfaceView关联的SurfaceHolder。

SurfaceHolder提供了如下方法来获取Canvas对象。

* Canvas lockCanvas()：锁定SurfaceView对象，获取SurfaceView上的Canvas。
* Canvas lockCanvas(Rect dirty)：锁定SurfaceView上Rect划分的区域，获取该SurfaceView上的Canvas。

释放绘图、提交所绘制的图形：

* unlockCanvasAndPost(canvas)

当调用SurfaceHolder的unlockCanvasAndPost()方法之后，该方法之前所绘制的图形还处于缓冲区中，下一次lockCanvas()方法锁定的区域可能会“遮挡”它。

* SurfaceView绘图机制示例：
```
// 当SurfaceView被创建时回调该方法
	@Override
	public void surfaceCreated(SurfaceHolder holder)
	{
		hasSurface = true;
		resume();
	}

	// 当SurfaceView将要被销毁时回调该方法
	public void surfaceDestroyed(SurfaceHolder holder)
	{
		hasSurface = false;
		pause();
	}

	// 当SurfaceView发生改变时回调该方法
	public void surfaceChanged(SurfaceHolder holder, int format, int w, int h)
	{
		if (updateThread != null)
			updateThread.onWindowResize(w, h);
	}
```

* 该程序使用FishView(该段关键代码的类名)自身作为SurfaceHolder的Callback实例，并实现了Callback中定义的三个方法：
  * void surfaceChanged(SurfaceHolder holder,int format,int width,int height)：当一个SurfaceView的格式或大小发生改变时回调该方法。
  * void surfaceCreated(SurfaceHolder holder)：当SurfaceView被创建时回调该方法；
  * void SurfaceDestroyed(SurfaceHolder holder)：当SurfaceView将要被销毁时回调该方法。
* 该程序中的UpdateViewThread线程:
```
class UpdateViewThread extends Thread
	{
		// 定义一个记录图形是否更新完成的旗标
		private boolean done;
		UpdateViewThread()
		{
			super();
			done = false;
		}

		@Override
		public void run()
		{
			SurfaceHolder surfaceHolder = holder;
			// 重复绘图循环，直到线程停止
			while (!done)
			{
				// 锁定SurfaceView，并返回到要绘图的Canvas
				Canvas canvas = surfaceHolder.lockCanvas();  // ①
				// 绘制背景图片
				canvas.drawBitmap(back, 0, 0, null);
				// 如果鱼“游出”屏幕之外，重新初始鱼的位置
				if(fishX < 0)
				{
					fishX = 778;
					fishY = 500;
					fishAngle = new Random().nextInt(60);
				}
				if(fishY < 0)
				{
					fishX = 778;
					fishY = 500;
					fishAngle = new Random().nextInt(60);

				}
				// 使用Matrix来控制鱼的旋转角度和位置
				matrix.reset();
				matrix.setRotate(fishAngle);
				matrix.postTranslate(fishX -= fishSpeed * Math
					.cos(Math.toRadians(fishAngle))
					, fishY -= fishSpeed * Math.sin(Math.toRadians(fishAngle)));
				canvas.drawBitmap(fishs[fishIndex++ % fishs.length], matrix, null);
				// 解锁Canvas，并渲染当前图像
				surfaceHolder.unlockCanvasAndPost(canvas);  // ②
				try
				{
					Thread.sleep(60);
				}
				catch (InterruptedException e){}
			}
		}

		public void requestExitAndWait()
		{
			// 把这个线程标记为完成，并合并到主程序线程
			done = true;
			try
			{
				join();
			}
			catch (InterruptedException ex){}
		}

		public void onWindowResize(int w, int h){
			// 处理SurfaceView的大小改变事件
		}
	}
```

* resume()启动线程
* pause()停止更新线程
```
public void resume()
	{
		// 创建和启动图像更新线程
		if (updateThread == null)
		{
			updateThread = new UpdateViewThread();
			if (hasSurface == true)
				updateThread.start();
		}
	}

	public void pause()
	{
		// 停止图像更新线程
		if (updateThread != null)
		{
			updateThread.requestExitAndWait();
			updateThread = null;
		}
	}
```





