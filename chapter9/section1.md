#9.1数据共享标准
ContentProvider是不同应用程序之间进行数据交换的标准API，ContentProvider以某种Uri的形式对外提供数据，允许其他应用访问或修改数据；其他应用程序使用ContentResolver根据Uri去访问操作指定数据。
###9.1.1ContentProvider简介
如果把ContentProvider当成一个“网站”来看，那么如何对外提供数据呢？是否需要像Java Web开发一样编写JSP、Servlet之类呢？不需要，如果那样就太复杂了，毕竟ContentProvider只是提供数据的访问接口，并不是想一个网站一样对外提供完整的页面。

如果把ContentProvider当成一个“网站”来看，那么如何完整地开发一个ContentProvider呢？步骤其实很简单，如下所示。
>定义自己的ContentProvider类，该类需要继承Android提供的基类。向Android系统注册这个“网站”，也就是在AndroidManifest.xml文件中注册这个ContentProvider，就像注册Activity一样。注册ContentProvider时需要为它绑定一个Uri。

向Android系统中注册ContentProvider只要在<application.../>元素下添加如下子元素即可：

	<!--下面配置中name属性制定ContentProvider类。
	   authorities就相当于为该ContentProvider制定域名-->
	<provider android:name=".DictProvider"
	   android:authorities="org.crazyit.providers.dictprovider"
	   android:exported="true"/>

当我们通过上面配置文件注册了DictProvider之后，其他应用程序就可通过该Uri来访问DictProvider所暴露的数据了。

###9.1.2Uri简介
ContentProvider要求的Uri与最常用的互联网类似，例如如下Uri：

	content：//org.crazyit.providers.dictprovider/words
它也可分为如下三部分：

- content：//这个部分是Android的ContentProvider规定的，就像上网的协议默认是http：//一样，暴露ContentProvider、访问ContentProvider的协议默认是content：//。
- org.crazyit.providers.dictprovider:这个部分就是ContentProvider的authorities。系统就是由这个部分来找到操作哪个ContentProvider的。只要访问指定的ContentProvider，这个部分就是固定的。
- words:资源部分（或者说数据部分）。当访问者需要访问不同资源时，这个部分是动态改变的。

###9.1.3使用ContentResolver操作数据
前面已经提到，ContentProvider相当于一个“网站”，它的作用是暴露可供操作的数据；其他应用程序则通过ContentResolver来操作ContentProvider所暴露的数据，ContentResolver相当于HttpClient。
Content提供了如下方法来获取ContentResolver对象。

- getContentResolver()：获取应用默认的ContentResolver。一旦在程序中获得了ContentResolver对象之后，接下来就可调用ContentResolver的如下方法来操作数据了。
- insert(Uri url,ContentValues values):向Uri对应的ContentProvider中插入values对应的数据。
- delete(Uri url,String where,String[] selectionArgs):删除Uri对应的ContentProvider中where提交匹配的数据。
- update(Uri uri,ContentValues values,String where,String[] selectionArgs):更新Uri对应的ContentProvider中where提交匹配的数据。
- query(Uri uri,String[] projection,String selection,String[] selectionArgs,String sortOrder):查询Uri对应的ContentProvider中where提交的数据。

一般来说，ContentProvider是单实例模式的，当多个应用程序通过ContentResolver来操作ContentProvider提供的数据时，ContentResolver调用的数据操作将会委托给同一个ContentProvider处理。
