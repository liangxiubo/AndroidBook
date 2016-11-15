# 13.2使用URL访问网络资源

￼￼URL（Uniform Resource Locator）对象代表统一资源定位器，它是指互联网“资源”的指针。资源可以是简单的文件或目录，也可以是对更复杂的对象的引用，例如对数据库或搜索引擎的查询。通常情况而言，URL可以由协议名、主机、端口和资源组成。
> 即满足如下格式：protocol://host:port/resourceName
> 
> 例如如下的URL地址：http://www.crazyit.org/index.php

URL提供了多个构造器用于创建URL对象，一旦获得了URL对象之后，可以调用如下常用方法来访问URL对应的资源。
> String getFile（）：获取此URL的资源名。
> 
> String getHost（）：获取此URL的主机名。
> 
> String getPath（）：获取此URL的路径地址。
> 
> int getPort（）：获取此URL的端口号。
> 
> String getProtocol（）：获取此URL的协议名称。
> 
> String getQuery（）：获取此URL的查询字符串部分。
> 
> URLConnection openConnection（）：返回一个URLConnection对象，它表示到URL所引用的远程对象的连接。

## 13.2.1使用URL读取网络资源
URL对象中前面几个方法都非常容易理解，而该对象提供的openStream（）可以读取该URL资源的InputStream，通过该方法可以非常方便地读取远程资源。

下面的程序示范如何通过URL类读取远程资源。
`public class MainActivity extends Activity
{
	ImageView show;
	// 代表从网络下载得到的图片
	Bitmap bitmap;
	Handler handler = new Handler()
	{
		@Override
		public void handleMessage(Message msg)
		{
			if(msg.what == 0x123)
			{
				// 使用ImageView显示该图片
				show.setImageBitmap(bitmap);
			}
		}
	};
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		show = (ImageView) findViewById(R.id.show);
		new Thread()
		{
			public void run()
			{
				try
				{
					// 定义一个URL对象
					URL url = new URL("http://www.crazyit.org/"
							+ "attachments/month_1008/20100812_7763e970f"
							+ "822325bfb019ELQVym8tW3A.png");
					// 打开该URL对应的资源的输入流
					InputStream is = url.openStream();
					// 从InputStream中解析出图片
					bitmap = BitmapFactory.decodeStream(is);
					// 发送消息、通知UI组件显示该图片
					handler.sendEmptyMessage(0x123);
					is.close();
					// 再次打开URL对应的资源的输入流
					is = url.openStream();
					// 打开手机文件对应的输出流
					OutputStream os = openFileOutput("crazyit.png"
						, MODE_PRIVATE);
					byte[] buff = new byte[1024];
					int hasRead = 0;
					// 将URL对应的资源下载到本地
					while((hasRead = is.read(buff)) > 0)
					{
						os.write(buff, 0 , hasRead);
					}
					is.close();
					os.close();
				}
				catch (Exception e)
				{
					e.printStackTrace();
				}
			}
		}.start();
	}
}`