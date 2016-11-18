# 13.1 基于TCP协议的网络通信
TCP/IP通信协议是一种可靠的端对端网络协议，它在通信的两端各建立一个Socket，从而在通信的两端之间形成网络虚拟链路。一旦建立了网络虚拟链路，两端就可以通信了。Java封装了基于TCP通信，使用Socket对象代表两端的通信接口，并通过它产生IO流进行网络通信。  

## 13.1.1 TCP协议基础
IP协议(Internet Protocol)，可以使Internet成为一个允许连接不同计算机和操作系统的网络。IP协议负责将消息从一个主机传送到另一个主机，消息在传送到过程中被分割成一个个小包。尽管计算机通过安装IP软件，保证了计算机之间可以发送和接收数据，但IP协议还不能解决数据分组在传输过程中可能出现的问题。需要通过安装TCP协议来提供可靠并且无差错的通信服务。

TCP协议负责收集信息包，并将其按适当的次序放好传送，在接收端收到后再将其正确的还原。TCP协议使用重发机制：当一个通信实体发送一个消息给另一个通信实体后，需要收到另一个通信实体的确认消息，如果没有收到，则会重发刚才发送的消息。从而向应用程序提供了可靠的通信连接，并自动适应网上各种诸如网络堵塞的变化。

图13.1显示了TCP协议控制两个通信实体相互通信的示意图

综上所述，虽然IP和TCP两个协议功能不尽相同，也可以分开单独使用，但是她们功能互补，只有结合使用才能保证在复杂的Internet环境下正常运行。故常把两个协议统称为TCP／IP协议。

##13.1.2 使用ServerSocket创建 TCP 服务器端

TCP通信的两个通信实体之间并没有服务器端、客户端之分，但那是两个通信实体已经建立虚拟链路之后的示意图。在两个通信实体没有建立虚拟链路之前， 必须有一个通信实体先做出“主动姿态”，主动接收来自其他通信实体的连接请求。

Java中能接收其他通信实体连接请求的类时ServerSocket，ServerSocket对象用于监听来自客户端的Socket连接，如果没有连接，如果没有连接，它将一直处于等待状态。ServerSocket包含一个监听来自客户端连接请求的方法。

* **Socket accept():**如果接收到一个客户端Socket的连接请求，该方法将返回一个 与连接客户端Socket对应的Socket, 否则该方法将一直处于等待状态，线程也被阻塞。

	为了创建ServerSocket对象，ServerSocket类提供了如下几个构造器．
* **ServerSocket(int port):**用指定的端口port来创建一个ServerSocket。该端口应该是有一个有效的端口整数位：0～65535. 
* **ServerSocket(int port, int backlog):**增加一个用来改变连接队列长度的参数 backlog。 
* **ServerSocket(int port, int backlog, InetAddress localAddr):**
在机器存在多个IP地址的情况下，允许通过localAddr这个参数来指定将ServerSocket　绑定到指定的IP地址。

当ServerSocket使用完毕后，应使用ServerSocket的close切方法来关闭该ServerSocket。 通常情况下，服务器不应该只接收一个客户端请求，而应该不断地接收来自客户端的所有请求。

所以Java程序通常会通过循环不断地调用ServerSocket的accept()方法:

	//创建一个ServerSocket，用于监听客户端Socket的连接请求
	ServerSocket ss = new ServerSocket(30000);
	//采用循环不断接收来自客户端的请求
	while(true)
	{
		//每当接收客户端Socket请求，服务器端也对应产生一个Socket
		Socket s = ss.accept();
		//之后就可以通过Socket通信了
		...
	}

由于手机无线上网的IP地址通常都是由移动运营公司动态分配的，-般不会有自己固定的IP地址，因此很少在手机上运行服务器端，服务器端通常运行在有固定IP的服务器上。上面介绍的应用程序的服务器端也是运行在PC上的。 

##13.1.3 使用 Socket 进行通信 

客户端通常使用Socket的Socket的构造器来连接到指定服务器，Socket通常可提供如下两个构造器。

* **Socket(InetAddress/String remoteAddress, int port):**创建连接到指定远程主机、远程端口的Socket，该构造器没有指定本地地址、本地端口，默认使用本地主机的默认IP地址，默认使用系统动态分配的端口。
* **Socket(InetAddress/String remoteAddress, int port, InetAddress localAddr, int localPort):**创建连接到指定远程主机、远程端口的Socket，并指定本地IP地址和本地端口，适用于本地主机有多个IP地址的情形。

上面两个构造器中指定远程主机时既可以使用InetAddress来指定，也可直接使用String对象（如192.168.2.23）指定远程IP地址。当本地主机只有一个IP地址时，使用第一个方法更简单，如下代码所示。

```
//创建连接到192.168.1.88、30000端口的Socket
Socket s = new Socket("192.168.1.88", 30000);
//下面就可以使用Socket进行通信了
```

当程序执行上面的粗体字代码时，该代码将会连接到指定服务器，让服务器端的Server Socket的accept()的方法向下执行，于是服务器端和客户端就产生一对互相连接的Socket。

当客户端、服务器端产生了对应的Socket之后，此时就到了图13.1所示的通信示意图，程序无须再区分服务器端、客户端，而是通过各自的Socket进行通信。Socket提供了如下两个方法来获取输入流和输出流。

* **InputStream getInputStream():**返回该Socket对象对应的输入流，让程序通过该输入流从Socket。
* **Socket(InetAddress/String remoteAddress, int port, InetAddress localAddr, int localPort):**返回该Socket对象对应的输出流，让程序通过该输出流向Socket中输出数据。

看到这两个方法返回的inputStream和OutputStream，读者应该可以明白java在设计I/O 体系的苦心了：不管底层的I/O流是怎样的节点流:文件流也好，网络Socket产生的流也好，程序都可以将其包装成处理流，从而提供更多方便的处理。下面以一个最简单的网络通信程序为例来介绍基于TCP协议的网络通信。

下面的服务器程序需要在PC上运行，该程序非常简单，因此不符要建立Android项目，直接定义一个Java类，并运行该Java类即可。它仅仅建立ServerSocket监听，井使用Socket获取输出流输出。
<p align = "center">程序清单：codes\13\13.1\SimpleServer\SimpleServer.java
</p>

```
public class SimpleServer
{
	public static void main(String[] args)
		throws IOException
	{
		// 创建一个ServerSocket，用于监听客户端Socket的连接请求
		ServerSocket ss = new ServerSocket(30000);  // ①
		// 采用循环不断接受来自客户端的请求
		while (true)
		{
			// 每当接受到客户端Socket的请求，服务器端也对应产生一个Socket
			Socket s = ss.accept();
			OutputStream os = s.getOutputStream();
			os.write(" 您好，您收到了服务器的新年祝福！\n"
				.getBytes("utf-8"));
			// 关闭输出流，关闭Socket
			os.close();
			s.close();
		}
	}
}
```
上面的程序中建立了一个ServerSocket, 该ServerSocket在30000端口监听，该ServerSocket将会一直监听，等待客户端程序的连接。程序接下来的4行代码用于打开Socket对应输出流，并向输出流中写入一段字符串数据。


***注意***

上面的程序并未把OutputStream点包装成PrintStream, 然后使用PrintStream直接输出整个字符串，这是因为该服务器瑞程序运行于Windows主机上，当直接使用PrintStream输出宇符串时默认使用系统平台的字符串（即GBK)进行编码，但该程序的客户端是Android应用，运行于Linux平台(Android是Linux内核的），因此当客户端读取网络数据时默认使用UTF-8字符集进行解码，这样势必引起乱码，为了保证客户端能正常解析到数据，此处手动控制字符串的编码，强行指定使用UTF-8字符集进行编码，这样就可以避免乱码问题了。

接下来的客户端程序也非常简单，它仅仅使用Socket建立与指定IP、指定端口的连接，井使用Socket获取输入流读取数据，该客户端程序是一个Android应用，因此还是先建Android项目，该程序的界而中包含一个文本框，用于显示从服务器端读取的字符串数据。

<p align = "center">程序清单：codes\13\13.1\SimpleClient\src\org\crazyit\net\SimpleClient.java
</p>

```
public class MainActivity extends Activity
{
	EditText show;
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		show = (EditText) findViewById(R.id.show);
		new Thread()
		{
			@Override
			public void run()
			{
				try
				{
					// 建立连接到远程服务器的Socket
					Socket socket = new Socket("192.168.1.88" , 30000);  // ①
					// 将Socket对应的输入流包装成BufferedReader
					BufferedReader br = new BufferedReader(
							new InputStreamReader(socket.getInputStream()));
					// 进行普通I/O操作
					String line = br.readLine();
					show.setText("来自服务器的数据：" + line);
					// 关闭输入流、socket
					br.close();
					socket.close();
				}
				catch (IOException e)
				{
					e.printStackTrace();
				}
			}
		}.start();
	}
}
```

上面的程序中①号粗体字代码是使用ServerSocket和Socket建立网络连接的代码，接下的几行粗体字代码是通过ServerSocket获取输入流、输出流进行通信的代码。通过程序不难行出，一旦使用ServerSocket、Socket建立网络连接之后，程序通过网络通信与普通IO并没有太大的区别。 

该Android应用需耍访问互联网，因此还需要为该应用赋予访问互联网的权限，也就是在AodrnidManifest.xml文件中增加如下配置片段:
<!-- 授权访问互联网 --> 
<uses-permission android:name="android.permission.INTERNET"/>

先运行上面程序中的SimpleServer类，服务器一直处于等待状态，因为服务器使用了死循环来接收来自客户端的请，再运行客户端AndrnidClient类，将可行到程序输出: 
”来自服务器的数据：您好，您收到了服务器的新年祝福!"如图 13.2所示，这表明客户端和服务器端通信成功． 

上面的程序为了突出通过ServerSocket和Socket建立连接并通过底层IO流进行通信的主题，程序没有进行异常处理，也没有使用finally块来关闭资源。 

实际应用中，程序可能不想让执行网络迕接、读取服务器数据的进程一直阻塞，而是希望当网络连接、读取操作超过合理时间之后，系统自动认为该操作失败，这个合理时间就是超时长。Socket对象提供了一个setSoTimeout(int timeout)来设置超时时长，如下面的代码段所示。

```
try
{
	//使用Scanner来读取网络输入流中数据
	Scanner scan = new Scanner(s.getInputStream());
	//读取一行字符
	String line = scan.nextLine();
	...
}
//捕捉SocketTimeoutException异常
catch(SocketTimeoutException ex)
{
	//对异常进行处理
	...
}	
```
假设程序需要为Socket连接服务器时指定超时时长：即经过指定时间后，如果该Socket还未连接到远程服务器，则系统认为该Socket连接超时。但Socket的所有构造器里都没有提供指定超时时长的参数，所以程序应该先创建一个无连接的Socket,再调用Socket的connect() 方法来连接远程服务器，而connect()方法就可以接受一个超时时长参数。如下代码所示。

```
//创建一个无连接的Socket
Socket s = new Socket();
//让该Socket连接到远程服务器，如果经过10秒还没有连接到，则认为连接超时
s.connect(new InetAddress(host, port), 10000);
```

##13.1.4 加入多线程
在实际应用中的客户端可能需要和服务器端保持长时间通信，即服务器需要不断地读取客户端数据，并向客户端写入数据；客户端也需要不断地读取服务器数据，并向服务器写入数据。

当使用传统BufferedReader的readLine()方法读取数据时，在该方法成功返回之前，线程被阻塞，程序无法继续执行。考虑到这个原因，服务器应该单独为每个Socket单独启动一条线程，每条线程负责与一个客户端进行通信。

客户端读取服务器数据的线程同样会被阻塞，所以系统应该单独启动一条线程，专门负责读取服务器数据。

下面考虑实现一个简单的C/S聊天室应用。服务器端应该包含多条线程，每个Socket对应一条线程，负责读取Socket对应输入流的数据（从客户端发来的数据），并将读到的数据向每个Socket输出流发送一遍。用List来保存所有的Socket。

下面是服务器端的实现代码。服务器提供了两个类：一个是创建ServerSocket监听的主类；另一个是负责处理每个Socket通信的线程类。

<p align = "center">程序清单：codes\13\13.1\MultiThreadServer\MyServer\MyObject.java</p>
```java
public class MyServer
{
	//定义保存所有的Socket的ArrayList
	public static ArrayList<Socket> socketList = new ArrayList<Socket>();
	public static void main(String[] args) throws IOException
	{
		ServerSocket ss = new ServerSocket(30000);
		while(true)
		{
			//此行代码会阻塞，将一直等待别人的连接
			Socket s = ss.accept();
			socketList.add(s);
			//每当客户端连接后启动一条ServerThread线程为客户端服务
			new Thread(new ServerThread(s)).start();
		}
	}
}
```

上面的程序是服务器端只负责接收客户端Socket的连接请求，每当客户端Socket迕接到该ServerSocket之后，程序将对应Socket加入SocketList中保存，并为该Socket启动一 条线程。该线程负责处理该Socket所有的通信任务，如程序中粗体字代码所示。服务端线程类的代码如下。

<p align = "center">程序清单：codes\13\13.1\MultiThreadServer\ServerThread.java</p>

```java
//负责处理每个线程通信的线程类
public class ServerThread implements Runnable
{
	//定义当前线程所处理的Socket所
	Socket s = null;
	//该线程所处理的Socket对应的输入流
	BufferedReader br = null;
	public ServerThread(Socket s) throws IOException
	{
		this.s = s;
		//初始化该Socket对应的输入流
		br = new BufferedReader(new InputStreamReader(s.getInputStream(), "utf-8"));
	}
	
	public void run()
	{
		try
		{
			String content = null;
			//循环读取客户端数据
			while((content = readFromClient()) != null)
			{
				for(Socket s : MyServer.socketList)
				{
					OutputStream os = s.getOutputStream();
					os.write((content + "\n").getBytes("utf-8"));
				}
			}
		}
		catch(IOException e)
		{
			e.printStackTrace();
		}
	}
	//定义读取客户端数据的方法
	private String readFromClient()
	{
		try
		{
			return br.readLine();
		}
		//如果捕捉到异常，表明该Socket对应的客户端已经关闭
		catch(IOException e)
		{
			MyServer.socketList.remove(s);
		}
		return null;
	}
}
```
上面的服务器端线程类不断读取客户蠕数据，程序使用readFromClient()方法来读取客户 端数据，如果读取数据过程中捕获到IOException异常，则表明该Socket对应的客户端Socket出现了问题，程序就将该Socket从socketList中删除。

当服务器线程读到客户端数据之后，遍历socketList，并将该数据向socketList 
中的每个Socket发送一次，该服务器线程将把从Socket中读到的数据向socketList中 
的每个Socket转发一次。 

每个客户端应该包含两条线程 一条负责生成主界面，并响应用户动作，并将用户输入的数据写入Socket对应的输出流中，另一条负责读取Socket对应输入流中的数据（从服务器发送过来的数据），并负责将这些数据在程序界面上显示出来。 

客户端程序同样是一个Android应用，因此需要创建一个Android项目，这个Android应用的界面中包含两个文本框：一个用于接收用户输入，另一个用于显示聊天信息：界面中还有一个按钮，当用户单击该按钮时，程厅向服务器发送聊天信息。该程序的界面布局代码如下。
<p align = "center">程序清单：codes\13\13.1\MultiThreadClient\res\layout\main.xml</p>

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
			  android:orientation="vertical"
			  android:layout_width="match_parent"
			  android:layout_height="match_parent">
	<LinearLayout
		android:orientation="horizontal"
		android:layout_width="match_parent"
		android:layout_height="wrap_content">
		<!-- 定义一个文本框，它用于接收用户的输入 -->
		<EditText
			android:id="@+id/input"
			android:layout_width="280dp"
			android:layout_height="wrap_content" />
		<Button
			android:id="@+id/send"
			android:layout_width="match_parent"
			android:layout_height="wrap_content"
			android:paddingLeft="8dp"
			android:text="@string/send"/>
	</LinearLayout>
	<!-- 定义一个文本框，它用于显示来自服务器的信息 -->
	<TextView
		android:id="@+id/show"
		android:layout_width="match_parent"
		android:layout_height="match_parent"
		android:gravity="top"
		android:background="#ffff"
		android:textSize="14dp"
		android:textColor="#f000"/>
</LinearLayout>
```

客户端的Activity负责生成程序界面井为程序的按钮单击事件绑定事件监听器，当用户单击按钮时向服务器发这信息。客户端的Activity代码如下。
<p align = "center">程序清单：codes\13\13.1\MultiThreadClient\src\org\crazyit\net\MultiThreadClient.java</p>

```
package org.crazyit.net;

import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;


public class MainActivity extends Activity
{
	// 定义界面上的两个文本框
	EditText input;
	TextView show;
	// 定义界面上的一个按钮
	Button send;
	Handler handler;
	// 定义与服务器通信的子线程
	ClientThread clientThread;
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		input = (EditText) findViewById(R.id.input);
		send = (Button) findViewById(R.id.send);
		show = (TextView) findViewById(R.id.show);
		handler = new Handler() // ②
		{
			@Override
			public void handleMessage(Message msg)
			{
				// 如果消息来自于子线程
				if (msg.what == 0x123)
				{
					// 将读取的内容追加显示在文本框中
					show.append("\n" + msg.obj.toString());
				}
			}
		};
		clientThread = new ClientThread(handler);
		// 客户端启动ClientThread线程创建网络连接、读取来自服务器的数据
		new Thread(clientThread).start(); // ①
		send.setOnClickListener(new OnClickListener()
		{
			@Override
			public void onClick(View v)
			{
				try
				{
					// 当用户按下发送按钮后，将用户输入的数据封装成Message
					// 然后发送给子线程的Handler
					Message msg = new Message();
					msg.what = 0x345;
					msg.obj = input.getText().toString();
					clientThread.revHandler.sendMessage(msg);
					// 清空input文本框
					input.setText("");
				}
				catch (Exception e)
				{
					e.printStackTrace();
				}
			}
		});
	}
}
```
当用户单击该程序界面中的“发送"按钮之后，程序将会把input输入框中的内容发送该clientThread的revHandler对象，clientThread将负货将用户输入的内容发送给服务器。

为了避免UI线程被阻寒，该程序将建立网络连接、与网络服务器通信等工作都交给 ClientThread线程完成。 

由于Android不允许子线程访问界面组件，因此上面的程序定义了一个Handle, 来处理来自子线程的消息。 

ClientThcead子线程负责建立与远程服务器的连接，并负责与远程服务器通信，读到数据之后使通过Handler对象发送一条消息；当ClientThcead子线程收到UI线程发送过来的消息（消息携带了用户输入的内容）之后，还负责将用户输入的内容发送给远程服务器。该子线程代码如下。

<p align = "center">程序清单：codes\13\13.1\MultiThreadClient\src\org\crazyit\net\ClientThread.java</p>

```
package org.crazyit.net;

import android.os.Handler;
import android.os.Looper;
import android.os.Message;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.Socket;
import java.net.SocketTimeoutException;

public class ClientThread implements Runnable
{
	private Socket s;
	// 定义向UI线程发送消息的Handler对象
	private Handler handler;
	// 定义接收UI线程的消息的Handler对象
	public Handler revHandler;
	// 该线程所处理的Socket所对应的输入流
	BufferedReader br = null;
	OutputStream os = null;
	public ClientThread(Handler handler)
	{
		this.handler = handler;
	}
	public void run()
	{
		try
		{
			s = new Socket("192.168.1.88", 30000);
			br = new BufferedReader(new InputStreamReader(
					s.getInputStream()));
			os = s.getOutputStream();
			// 启动一条子线程来读取服务器响应的数据
			new Thread()
			{
				@Override
				public void run()
				{
					String content = null;
					// 不断读取Socket输入流中的内容
					try
					{
						while ((content = br.readLine()) != null)
						{
							// 每当读到来自服务器的数据之后，发送消息通知程序
							// 界面显示该数据
							Message msg = new Message();
							msg.what = 0x123;
							msg.obj = content;
							handler.sendMessage(msg);
						}
					}
					catch (IOException e)
					{
						e.printStackTrace();
					}
				}
			}.start();
			// 为当前线程初始化Looper
			Looper.prepare();
			// 创建revHandler对象
			revHandler = new Handler()
			{
				@Override
				public void handleMessage(Message msg)
				{
					// 接收到UI线程中用户输入的数据
					if (msg.what == 0x345)
					{
						// 将用户在文本框内输入的内容写入网络
						try
						{
							os.write((msg.obj.toString() + "\r\n")
									.getBytes("utf-8"));
						}
						catch (Exception e)
						{
							e.printStackTrace();
						}
					}
				}
			};
			// 启动Looper
			Looper.loop();
		}
		catch (SocketTimeoutException e1)
		{
			System.out.println("网络连接超时！！");
		}
		catch (Exception e)
		{
			e.printStackTrace();
		}
	}
}
```
上面线程的功能也非常简单，它只是不断获取Socket输入流中的内容，当读到Socket输入流中的内容后，便通过Handler对象发送一条消息，消息负责携带读到的数据。除此之外，该子线程还负责读取UI线程发送的消息，接收到消息之后，该子线程负责将消息中携带的数据发送给远程服务器。 

先运行上面程序中的MyServer类，该类运行后只是作为服务器，看不到任何输出，接着可以运行Android客户端一一相当于启动聊天室客户端登录该服务器，接着可以看到在任何一个Android客户端输入一些内容后单击“发送”按钮，将可行到所有客户端（包括自己）都会收到他刚刚输入的内容，如招13.3所示，这就粗略实现了一个C/S结构聊天室的功能。 

借助十此处介绍的网络通信机制，我们可以开发大量功能强大的网络通信程序，如网络五子棋、网络斗地主等， 游戏可能需要在网络上交换不同类型的数据，这可能需要封装自己的网络协议，而通信部分都可按上面介绍的方式来实现。