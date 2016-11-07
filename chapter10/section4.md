# 10.4短信管理器(SmsManager)
 　　SmsManager是Android提供的另一个非常常见的服务，SmsManager提供了一系列sendXxxMessage()方法用于发送短信 ，不过就现在实际应用来看，短信通常都是普通的文本内容，也就是调用sendTextMessage()方法进行发送即可。

###实例：发送短信
　　下面的程序十分简单，程序提供一个文本框让用户输入收件人号码，一个文本框让用户输入短信内容，接下来单击“发送”按钮即可将短信发送出去。
　　程序代码如下。
　　　　**程序清单：codes\10\10.4\SendSms\src\org\crazyit\manager\SenSms.java**

```java
public class SendSms extends Activity
{
	EditText number, content;
	Button send;
	SmsManager sManager;
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 获取SmsManager
		sManager = SmsManager.getDefault();
		// 获取程序界面上的两个文本框和按钮
		number = (EditText) findViewById(R.id.number);
		content = (EditText) findViewById(R.id.content);
		send = (Button) findViewById(R.id.send);
		// 为send按钮的单击事件绑定监听器
		send.setOnClickListener(new OnClickListener()
		{
			@Override
			public void onClick(View arg0)
			{
				// 创建一个PendingIntent对象
				PendingIntent pi = PendingIntent.getActivity(
						MainActivity.this, 0, new Intent(), 0);
				// 发送短信
				sManager.sendTextMessage(number.getText().toString(),
						null, content.getText().toString(), pi, null);
				// 提示短信发送完成
				Toast.makeText(MainActivity.this, "短信发送完成", 8000).show();
			}
		});
	}
}
```
　　从上面程序代码可以看出，使用SmsManager发送短信十分简单，简单地调用sendTextMessage()方法即可发送。

　　上面的程序中用到了一个PendingIntent对象，PendingIntent是对Intent的包装，一般通过调用PendingIntent的getActivity()、getService()、getBroadcastReceiver()静态方法来获取PendingIntent对象。与Intent对象不同的是：PendingIntent通常会传给其他应用组件，从而由其他应用程序来执行PendingIntent所包装的“Intent”。

　　该程序需要调用SmsManager来发送短信，因此还需要属于该程序发送短信的权限，也就是在AndroidManifest.xml文件中增加如下代码：
```xml
<!--属于发送短信的权限-->
<uses-permission android:name="android.permission.SEND_SMS"/>
```

### 实例：短信群发

　　短信群发也是一个十分使用的功能，逢年过节，很多人都喜欢通过短信。。。。。（你懂得 都是废话 不打了），只要让程序遍历每个收件人号码并依次想每个收件人发送短信即可。

　　该程序也提供了一个带列表框的对话框工用户选择收件人号码，代码如下。

　　　　　程序清单：codes\10\10.4\GroupSend\src\org\crazyit\manager\GroupSend.java

```java
public class GroupSend extends Activity
{
	EditText numbers, content;
	Button select, send;
	SmsManager sManager;
	// 记录需要群发的号码列表
	ArrayList<String> sendList = new ArrayList<String>();
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		sManager = SmsManager.getDefault();
		// 获取界面上的文本框、按钮组件
		numbers = (EditText) findViewById(R.id.numbers);
		content = (EditText) findViewById(R.id.content);
		select = (Button) findViewById(R.id.select);
		send = (Button) findViewById(R.id.send);
		// 为send按钮的单击事件绑定监听器
		send.setOnClickListener(new OnClickListener()
		{
			@Override
			public void onClick(View v)
			{
				for (String number : sendList)
				{
					// 创建一个PendingIntent对象
					PendingIntent pi = PendingIntent.getActivity(
							MainActivity.this, 0, new Intent(), 0);
					// 发送短信
					sManager.sendTextMessage(number, null, content
							.getText().toString(), pi, null);
				}
				// 提示短信群发完成
				Toast.makeText(MainActivity.this, "短信群发完成"
						, Toast.LENGTH_SHORT).show();
			}
		});
		// 为select按钮的单击事件绑定监听器
		select.setOnClickListener(new OnClickListener()
		{
			@Override
			public void onClick(View v)
			{
				// 查询联系人的电话号码
				final Cursor cursor = getContentResolver().query(
						ContactsContract.CommonDataKinds.Phone.CONTENT_URI,
						null, null, null, null);
				BaseAdapter adapter = new BaseAdapter()
				{
					@Override
					public int getCount()
					{
						return cursor.getCount();
					}
					@Override
					public Object getItem(int position)
					{
						return position;
					}
					@Override
					public long getItemId(int position)
					{
						return position;
					}
					@Override
					public View getView(int position, View convertView,
										ViewGroup parent)
					{
						cursor.moveToPosition(position);
						CheckBox rb = new CheckBox(MainActivity.this);
						// 获取联系人的电话号码，并去掉中间的中画线、空格
						String number = cursor
								.getString(cursor.getColumnIndex(ContactsContract
										.CommonDataKinds.Phone.NUMBER))
								.replace("-", "")
								.replace(" " , "");
						rb.setText(number);
						// 如果该号码已经被加入发送人名单，默认勾选该号码
						if (isChecked(number))
						{
							rb.setChecked(true);
						}
						return rb;
					}
				};
				// 加载list.xml布局文件对应的View
				View selectView = getLayoutInflater().inflate(
						R.layout.list, null);
				// 获取selectView中的名为list的ListView组件
				final ListView listView = (ListView) selectView
						.findViewById(R.id.list);
				listView.setAdapter(adapter);
				new AlertDialog.Builder(MainActivity.this)
						.setView(selectView)
						.setPositiveButton("确定",
								new DialogInterface.OnClickListener()
								{
									@Override
									public void onClick(DialogInterface dialog,
														int which)
									{
										// 清空sendList集合
										sendList.clear();
										// 遍历listView组件的每个列表项
										for (int i = 0; i < listView.getCount(); i++)
										{
											CheckBox checkBox = (CheckBox) listView
													.getChildAt(i);
											// 如果该列表项被勾选
											if (checkBox.isChecked())
											{
												// 添加该列表项的电话号码
												sendList.add(checkBox.getText()
														.toString());
											}
										}
										numbers.setText(sendList.toString());
									}
								}).show();
			}
		});
	}
	// 判断某个电话号码是否已在群发范围内
	public boolean isChecked(String phone)
	{
		for (String s1 : sendList)
		{
			if (s1.equals(phone))
			{
				return true;
			}
		}
		return false;
	}
}
```
　　该程序的实现也很简单。程序提供了一个带列表的对话框供用户选择群发短信的收件人号码，程序则使用了一个`ArrayList<String>`集合来保存所有的收件人号码。为了实现群发功能，程序使用循环遍历`ArrayList<String>`中的每个号码，并使用SmsManager一次想每个号码发送短信即可。

> 注意： 上面的程序不进需要调用SmsManager发送短信，也需要访问系统的联系人信息，因此不要忘记了在AndroidManifest.xml文件中给该程序授予相应的权限。

　　还有一点需要指出，该程序有一个潜在的风险：该程序直接在主线程中采用循环想多个人发送短信，如果需要发送短信的人太多且网络延迟严重，群发短信就会变成一个耗时任务，此时可以考虑使用IntentService来群发短信，群发完成后通过广播同志前台Activity。