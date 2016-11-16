# Intent属性及Intent-filter配置
---
* Intent代表了Android应用的启动“意图”，Android应用将会根据Intent来启动指定组件，至于到底启动哪个组件，则取决于Intent的各属性。下面将详细介绍Intent的各属性值，以及Android如何根据不同属性值来启动相应的组件。

##Component属性
* Intent的Component属性需要接受一个ComponentName对象，ComponentName对象包含如下几个构造器。
* ComponentName（String pkg，String cls）：创建pkg所在包下的cls类所对应的组件
* ComponentName（Context pkg，String cls）：创建pkg所对应的包下的cls类所对应的组件。
* ComponentName（Context pkg，Class<?>cls）：创建pkg所对应的包下的cls类所对应的组件。

* 上面几个构造器的本质是相同的，这说明创建一个ComponentName需要指定包名和类名——这就可以唯一地确定一个组件类，这样应用程序即可根据给的的组件类去启动特定的组件。
* 除此之外，Intent还包含了如下三个方法。
* setClass（Context packageContext，String className）：设置该Intent将要启动的组件对应的类名。
* setClassName（Content packageContext，String className）：设置该Intent将要启动的组件对应的类名。
* setClassName（String packageName，String className）：设置该Intent将要启动的组件对应的类名。

* 指定Component属性的Intent已经明确了它将要启动哪个组件，因此这种Intent也被称为显示Intent，没有指定Component属性的Intent被称为隐式Intent——隐式Intent没有明确指定要启动哪个组件，应用将会根据Intent指定的规则去启动符合条件的组件，但具体是哪个组件则不确定。
* 下面的示例程序示范了如何通过显示Intent（指定了Component属性）来启动另外一个Activity。该程序的界面布局很简单，界面中只有一个按钮，用户单机该按钮将会启动第二个Activity。此处不再给出该程序的界面布局文件。该程序的Java代码如下。

```
public class MainActivity extends Activity
{
@Override
public void onCreate(Bundle savedInstanceState)
{
super.onCreate(savedInstanceState);
setContentView(R.layout.main);
Button bn = (Button) findViewById(R.id.bn);
// 为bn按钮绑定事件监听器
bn.setOnClickListener(new OnClickListener()
{
@Override
public void onClick(View arg0)
{
// 创建一个ComponentName对象
ComponentName comp = new ComponentName(MainActivity.this,
SecondActivity.class);
Intent intent = new Intent();
// 为Intent设置Component属性
intent.setComponent(comp);
startActivity(intent);
}
});
}
}
```
* 上面程序中的三行粗体字代码用于创建ComponentName对象，并将该对象设置成Intent对象的Component属性，这样应用程序即可根据该Intent的“意图”去启动指定组件。

##Action、Category属性与intent-filter配置
##action
* 是用户定义的字符串
* 用于描述一个 Android 应用程序组件
* 一个 Intent Filter 可以包含多个 Action。在  AndroidManifest.xml 的 Activity 定义时可以在其 <intent-filter >节点指定一个 Action 列表用于标示 Activity 所能接受的“动作”，例如：

```
<intent-filter > 
<action android:name="android.intent.action.MAIN" /> 
<action android:name="com.zy.myaction" /> 
……
</intent-filter>
```
* action使用android:name属性指定要为之服务的action的名称
* 如果启动Activity采用如下方法使用Intent 对象
* Intent intent =new Intent(); 
intent.setAction("com.wust.myaction"); 
* 则所有的Action列表中包含了“com.wust.myaction”的 Activity 都将会匹配成功

* 下面通过一个简单的示例来示范Action属性（就是普通字符串）的作用。下面程序的第一个Activity非常简单，它只包括一个普通按钮，当用户单机该按钮时，程序会“跳转”到第二个Activity——但第一个Activity指定跳转的Intent时，并不以“硬编码”的方式指定要跳转的目标Activity，而是为Intent指定Action属性。此处不给出界面布局的代码，第一个Activity的Java代码如下：

```
public class MainActivity extends Activity
{
public final static String CRAZYIT_ACTION =
"org.crazyit.intent.action.CRAZYIT_ACTION";
@Override
public void onCreate(Bundle savedInstanceState)
{
super.onCreate(savedInstanceState);
setContentView(R.layout.main);
Button bn = (Button) findViewById(R.id.bn);
// 为bn按钮绑定事件监听器
bn.setOnClickListener(new OnClickListener()
{
@Override
public void onClick(View arg0)
{
// 创建Intent对象
Intent intent = new Intent();
// 为Intent设置Action属性（属性值就是一个普通字符串）
intent.setAction(MainActivity.CRAZYIT_ACTION);
startActivity(intent);
}
});
}
}
```

##Category
* Category
* 字符串，包含了处理该Intent的组件的种类信息
* 为执行动作的附加信息，起着对action的补充说明作用
* 一个Intent对象可以有多个category
* Android系统中定义了category 常量
* 对category的操作
* addCategory() 添加一个category
* removeCategory()删除一个category()
* getCategorys()获取所有的category()
* category类别匹配
* 使用android:category属性指定应该在哪种环境下为动作提供服务
* 每个Intent Filter标签可以包含多个category标签
* 可以自行指定category或者Android系统提供的标准值
* <intent-filter >节点中可以为组件定义一个Category 类别列表
* 当 Intent 中包含这个列表的所有项目时 Category 类别匹配才会成功

* 接下来的示例程序将会示范Category属性的用法。该程序的第一个Activity的代码如下。

```

public class MainActivity extends Activity
{
// 定义一个Action常量
final static String CRAZYIT_ACTION =
"org.crazyit.intent.action.CRAZYIT_ACTION";
// 定义一个Category常量
final static String CRAZYIT_CATEGORY =
"org.crazyit.intent.category.CRAZYIT_CATEGORY";
@Override
public void onCreate(Bundle savedInstanceState)
{
super.onCreate(savedInstanceState);
setContentView(R.layout.main);
Button bn = (Button) findViewById(R.id.bn);
bn.setOnClickListener(new OnClickListener()
{
@Override
public void onClick(View arg0)
{
Intent intent = new Intent();
// 设置Action属性
intent.setAction(MainActivity.CRAZYIT_ACTION);
// 添加Category属性
intent.addCategory(MainActivity.CRAZYIT_CATEGORY);
startActivity(intent);
}
});
}
}

```
*下面是程序要启动的目标Action所对应的配置代码。

```
<activity android:name=".SecondActivity"
android:label="@string/app_name">
<intent-filter>
<!-- 指定该Activity能响应action为指定字符串的Intent -->
<action android:name="org.crazyit.intent.action.CRAZYIT_ACTION" />
<!-- 指定该Activity能响应category为指定字符串的Intent -->
<category android:name="org.crazyit.intent.category.CRAZYIT_CATEGORY" />
<!-- 指定该Activity能响应category为android.intent.category.DEFAULT的Intent -->
<category android:name="android.intent.category.DEFAULT" />
</intent-filter>
</activity>
```
* 上面配置Activity时也指定该Activity的实现类为SecondActivity，该实现类的代码如下：

```
public class SecondActivity extends Activity
{
@Override
public void onCreate(Bundle savedInstanceState)
{
super.onCreate(savedInstanceState);
setContentView(R.layout.second);
EditText show = (EditText) findViewById(R.id.show);
// 获取该Activity对应的Intent的Action属性
String action = getIntent().getAction();
// 显示Action属性
show.setText("Action为：" + action);
EditText cate = (EditText) findViewById(R.id.cate);
// 获取该Activity对应的Intent的Category属性
Set<String> cates = getIntent().getCategories();
// 显示Category属性
cate.setText("Category属性为：" + cates);
}
}
```
* 上面的程序也很简单，它只是在启动时把启动该Activity的Intent的Action、Category属性值分别显示在不同的文本框内。

##指定Action、Category调用系统Activity
* Intent代表了启动某个程序组件的“意图”，实际上Intent对象不仅可以启动本应用内程序组件，也可以启动Android系统的其他应用的程序组件，包括系统自带的程序组件——只要权限允许。


###实例：查看并获取联系人电话
---
* 这个实例将会在程序中提供一个按钮，用户单机该按钮时会显示系统的联系人列表，当用户单机指定联系人之后，程序将会显示该联系人的名字、电话。
* 这个程序非常有用，比如我们要开发一个发送短信的程序，当用户编写短信完成之后，可能需要浏览联系人列表，并从联系人列表中选出短信接收人，那就可以用到该程序了。
* 该程序的界面布局代码如下

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:gravity="center_horizontal">
<!-- 显示联系人姓名的文本框 -->
<EditText
android:id="@+id/show"
android:layout_width="match_parent"
android:layout_height="wrap_content"
android:editable="false"
android:cursorVisible="false"/>
<!-- 显示联系人的电话的文本框 -->
<EditText
android:id="@+id/phone"
android:layout_width="match_parent"
android:layout_height="wrap_content"
android:editable="false"
android:cursorVisible="false"/>
<Button
android:id="@+id/bn"
android:layout_width="wrap_content"
android:layout_height="wrap_content"
android:text="查看联系人"/>
</LinearLayout>
```
* 上面的界面布局中包含了两个文本框、一个按钮，其中按钮用于浏览系统的联系人列表并选择其中的联系人。两个文本框分别用于显示联系人的名字和电话号码。
* 该程序的Java代码如下：

```
public class MainActivity extends Activity
{
final int PICK_CONTACT = 0;
@Override
public void onCreate(Bundle savedInstanceState)
{
super.onCreate(savedInstanceState);
setContentView(R.layout.main);
Button bn = (Button) findViewById(R.id.bn);
// 为bn按钮绑定事件监听器
bn.setOnClickListener(new OnClickListener()
{
@Override
public void onClick(View arg0)
{
// 创建Intent
Intent intent = new Intent();
// 设置Intent的Action属性
intent.setAction(Intent.ACTION_GET_CONTENT);
// 设置Intent的Type属性
intent.setType("vnd.android.cursor.item/phone");
// 启动Activity，并希望获取该Activity的结果
startActivityForResult(intent, PICK_CONTACT);
}
});
}
@Override
public void onActivityResult(int requestCode
, int resultCode, Intent data)
{
super.onActivityResult(requestCode, resultCode, data);
switch (requestCode)
{
case (PICK_CONTACT):
if (resultCode == Activity.RESULT_OK)
{
// 获取返回的数据
Uri contactData = data.getData();
CursorLoader cursorLoader = new CursorLoader(this
, contactData, null, null, null, null);
// 查询联系人信息
Cursor cursor = cursorLoader.loadInBackground();
// 如果查询到指定的联系人
if (cursor.moveToFirst())
{
String contactId = cursor.getString(cursor
.getColumnIndex(
ContactsContract.Contacts._ID));
// 获取联系人的名字
String name = cursor.getString(cursor
.getColumnIndexOrThrow(
ContactsContract.Contacts.DISPLAY_NAME));
String phoneNumber = "此联系人暂未输入电话号码";
//根据联系人查询该联系人的详细信息
Cursor phones = getContentResolver().query(
ContactsContract.CommonDataKinds.
Phone.CONTENT_URI, null,
ContactsContract.CommonDataKinds.Phone. 												CONTACT_ID
+ " = " + contactId, null, null);
if (phones.moveToFirst())
{
//取出电话号码
phoneNumber = phones
.getString(phones
.getColumnIndex(ContactsContract
.CommonDataKinds.Phone.NUMBER));
}
// 关闭游标
phones.close();
EditText show =(EditText)findViewById(R.id.show);
//显示联系人的名称
show.setText(name);
EditText phone =(EditText)findViewById(R.id.phone);
//显示联系人的电话号码
phone.setText(phoneNumber);
}
// 关闭游标
cursor.close();
}
break;
}
}
}
```
* 上面的Intent对象除了设置Action属性之外，还设置了Type属性，关于Intent的Type属性的作用，等下将会进行更加详细的介绍。

###实例：返回系统Home桌面
---
*本实例将会提供一个按钮，当用户单击该按钮时，系统将会返回Home桌面，就像单击模拟器右边的按钮一样。这也需要通过Intent来实现，程序为Intent设置合适的Action、Category属性，并根据该Intent来启动Activity即可返回Home桌面。该实例程序如下：

```
public class MainActivity extends Activity
{
@Override
public void onCreate(Bundle savedInstanceState)
{
super.onCreate(savedInstanceState);
setContentView(R.layout.main);
Button bn = (Button) findViewById(R.id.bn);
bn.setOnClickListener(new OnClickListener()
{
@Override
public void onClick(View v)
{
// 创建Intent对象
Intent intent = new Intent();
// 为Intent设置Action、Category属性
intent.setAction(Intent.ACTION_MAIN);
intent.addCategory(Intent.CATEGORY_HOME);
startActivity(intent);
}
});
}
}
```
* 上面程序代码设置了Intent的Action属性值为android.intent.action.MAIN字符串、Category属性值为android.intent.category.HOME字符串，满足该Intent的Activity其实就是Android系统的HOME桌面。因此，运行上面的程序时单机“返回桌面”按钮，即可返回Home桌面。






