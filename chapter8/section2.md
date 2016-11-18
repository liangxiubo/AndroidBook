# 8.2 SQLite数据库

### 8.3.1 SQLite简介

SQLite是一个开源的嵌入式关系数据库，在2000年由D. Richard Hipp发布。

SQLite数据库特点：

- 更加适用于嵌入式系统，嵌入到使用它的应用程序中
- 空间占用非常少，运行高效可靠，可移植性好
- 提供了零配置（zero-configuration）运行模式

SQLite数据库不仅提高了运行效率，而且屏蔽了数据库使用和管理的复杂性，程序仅需要进行最基本的数据操作，其他操作可以交给进程内部的数据库引擎完成。

SQLite数据库具有很强的移植性，可以运行在Windows，Linux，BSD，Mac OS X和一些商用Unix系统，比如Sun的Solaris，IBM的AIX。

SQLite数据库也可以工作在许多嵌入式操作系统下，例如QNX，VxWorks，Palm OS，Symbin和Windows CE。

SQLite的核心大约有3万行标准C代码，模块化的设计使这些代码更加易于理解。

### 8.3.2 手动建库

手动建立数据库指的是使用sqlite3工具，通过手工输入命令行完成数据库的建立过程。

sqlite3是SQLite数据库自带的一个基于命令行的SQL命令执行工具，并可以显示命令执行结果。

sqlite3工具被集成在Android系统中，用户在Linux的命令行界面中输入sqlite3可启动sqlite3工具，并得到工具的版本信息，如下面的代码所示：

~~~ sql lite
# sqlite3
SQLite version 
Enter “.help” for instructions
sqlite>
~~~

启动Linux的命令行界面的方法是在CMD中输入`adb shell`命令。

在启动sqlite3工具后，提示符从“#”变为“sqlite>”，表示命令行界面进入与SQLite数据库的交互模式，此时可以输入命令建立、删除或修改数据库的内容。

正确退出sqlite3工具的方法是使用.exit命令：

~~~ sql lite
sqlite> .exit
#
~~~

原则上，每个应用程序的数据库都保存在各自的`/data/data/<package name>/databases`目录下，但如果使用手工方式建立数据库，则必须手工建立数据库目录，目前版本无须修改数据库目录的权限：

~~~ sql lite
# mkdir databases
# ls –l
drwxrwxrwx root     root              2009-07-18 15:43 databases
drwxr-xr-x system   system           2009-07-18 15:31 lib
#
~~~

在SQLite数据库中，每个数据库保存在一个独立的文件中，使用sqlite3工具后加文件名的方式打开数据库文件，如果指定文件不存在，sqlite3工具则自动创建新文件。

下面的代码将创建名为people的数据库，在文件系统中将产生一个名为people.db的数据库文件：

~~~ sql lite
# sqlite3 people.db
SQLite version 
Enter “.help” for instructions
sqlite>
~~~

下面的代码在新创建的数据库中，构造了一个名为peopleinfo的表，使用create table命令，关系模式为peopleinfo ( _id, name, age, height)：

~~~sql lite
sqlite> create table peopleinfo 
...> (_id integer primary key autoincrement,
...> name text not null,
...> age integer,
...> height float);
sqlite>
~~~

表包含四个属性，_id是整型的主键；name表示姓名，字符型，not null表示这个属性一定要填写，不可以为空值；age表示年龄，整数型；height表示身高，浮点型。

为了确认数据表是否创建成功，可以使用.tables命令，显示当前数据库中的所有表。

从下面的代码中可以观察到，当前数据库仅有一个名为peopleinfo的表：

~~~ sql lite
sqlite> .tables
poepleinfo
sqlite>
~~~

当然，也可以使用.schema命令查看建立表时使用的SQL命令。如果当前数据库中包含多个表，则可以使用[.schema 表名]的形式，显示指定表的建立命令：

~~~ sql lite
sqlite>.schema
CREATE TABLE peopleinfo (_id integer primary key autoincrement,
name text not null, age integer, height float);
sqlite>
~~~

向peopleinfo表中添加数据，使用insert into … values命令：

~~~ sql lite
sqlite> insert into peopleinfo values(null,'Tom',21,1.81);
sqlite> insert into peopleinfo values(null,'Jim',22,1.78);
sqlite> insert into peopleinfo values(null,'Lily',19,1.68);
~~~

在数据添加完毕后，使用select命令，显示指定数据表中的所有数据信息，命令格式为[select 属性 from 表名]。

下面的代码用来显示peopleinfo表的所有数据：

~~~ sql lite
select * from peopleinfo;
1|Tom|21|1.81
2|Jim|22|1.78
3|Lily|19|1.68
sqlite>
~~~

sqlite3工具还支持大量的命令，可以使用.help命令查询sqlite3的命令列表。

| 编号   | 命令                 | 说明                                   |
| ---- | ------------------ | ------------------------------------ |
| 1    | .bail  ON\|OFF     | 遇到错误时停止，缺省为OFF                       |
| 2    | .databases         | 显示数据库名称和文件位置                         |
| 3    | .dump  ?TABLE? ... | 将数据库以SQL文本形式导出                       |
| 4    | .echo  ON\|OFF     | 开启和关闭回显                              |
| 5    | .exit              | 退出                                   |
| 6    | .explain  ON\|OFF  | 开启或关闭适当输出模式，如果开启模式将更改为column，并自动设置宽度 |

| 编号   | 命令                     | 说明                |
| ---- | ---------------------- | ----------------- |
| 7    | .header(s)  ON\|OFF    | 开启或关闭标题显示         |
| 8    | .help                  | 显示帮助信息            |
| 9    | .import  FILE TABLE    | 将数据从文件导入表中        |
| 10   | .indices  TABLE        | 显示表中所的列名          |
| 11   | .load  FILE ?ENTRY?    | 导入扩展库             |
| 12   | .mode  MODE ?TABLE?    | 设置输入格式            |
| 13   | .nullvalue  STRING     | 打印时使用STRING代替NULL |
| 14   | .output  FILENAME      | 将输入保存到文件          |
| 15   | .output  stdout        | 将输入显示在屏幕上         |
| 16   | .prompt  MAIN CONTINUE | 替换标准提示符           |

| 编号   | 命令                  | 说明            |
| ---- | ------------------- | ------------- |
| 17   | .quit               | 退出            |
| 18   | .read  FILENAME     | 在文件中执行SQL语句   |
| 19   | .schema  ?TABLE?    | 显示表的创建语句      |
| 20   | .separator  STRING  | 更改输入和导入的分隔符   |
| 21   | .show               | 显示当前设置变量值     |
| 22   | .tables  ?PATTERN?  | 显示符合匹配模式的表名   |
| 23   | .timeout  MS        | 尝试打开被锁定的表MS毫秒 |
| 24   | .timer  ON\|OFF     | 开启或关闭CPU计时器   |
| 25   | .width  NUM NUM ... | 设置column模式的宽度 |

### 8.3.3 代码建库

在代码中动态建立数据库是比较常用的方法。

在程序运行过程中，当需要进行数据库操作时，应用程序会首先尝试打开数据库，此时如果数据库并不存在，程序则会自动建立数据库，然后再打开数据库。

在编程实现时，一般将所有对数据库的操作都封装在一个类中，因此只要调用这个类，就可以完成对数据库的添加、更新、删除和查询等操作。

下面内容是DBAdapter类的部分代码，封装了数据库的建立、打开和关闭等操作：

~~~ java
public class DBAdapter {
	private static final String DB_NAME = "people.db";
	private static final String DB_TABLE = "peopleinfo";
	private static final int DB_VERSION = 1;
	
	public static final String KEY_ID = "_id";
	public static final String KEY_NAME = "name";
	public static final String KEY_AGE = "age";
	public static final String KEY_HEIGHT = "height";
	
	private SQLiteDatabase db;
	private final Context context;
	private DBOpenHelper dbOpenHelper;
	private static class DBOpenHelper extends SQLiteOpenHelper {}
 
	public DBAdapter(Context _context) {
	        context = _context;
	}
 
	public void open() throws SQLiteException {
      dbOpenHelper = new DBOpenHelper(context, DB_NAME, null, DB_VERSION);
      try {
        db = dbOpenHelper.getWritableDatabase();
      }catch (SQLiteException ex) {
        db = dbOpenHelper.getReadableDatabase();
      }
	}
  public void close() {
      if (db != null){
        db.close();
        db = null;
      }
	}
}
~~~

从代码的第2行到第9行可以看出，在DBAdapter类中首先声明了数据库的基本信息，包括数据库文件的名称、数据库表格名称和数据库版本，以及数据库表中的属性名称。

从这些基本信息上不难发现，这个数据库与前一小节手动建立的数据库是完全相同的。

第11行代码声明了SQLiteDatabase对象db。SQLiteDatabase类封装了非常多的方法，用以建立、删除数据库，执行SQL命令，对数据进行管理等工作。

第13行代码声明了一个非常重要的帮助类SQLiteOpenHelper，这个帮助类可以辅助建立、更新和打开数据库。

第21行代码定义了open()函数用来打开数据库，但open()函数中并没有任何对数据库进行实际操作的代码，而是调用了SQLiteOpenHelper类的getWritableDatabase()函数和getReadableDatabase()函数。这个两个函数会根据数据库是否存在、版本号和是否可写等情况，决定在返回数据库对象前，是否需要建立数据库。

在代码第30行的close()函数中，调用了SQLiteDatabase对象的close()方法关闭数据库。

这是上面的代码中，唯一的一个地方直接调用了SQLiteDatabase对象的方法。

SQLiteDatabase中也封装了打开数据库的函数openDatabases()和创建数据库函数openOrCreateDatabases()，因为代码中使用了帮助类SQLiteOpenHelper，从而避免直接调用SQLiteDatabase中的打开和创建数据库的方法，简化了数据库打开过程中繁琐的逻辑判断过程。

代码第15行实现了内部静态类DBOpenHelper，继承了帮助类SQLiteOpenHelper。

如果程序开发人员不希望使用SQLiteOpenHelper类，同样可以直接创建数据库：

- 首先调用openOrCreateDatabases()函数创建数据库对象
- 然后执行SQL命令建立数据库中的表和直接的关系

示例代码如下：

~~~ java
private static final String DB_CREATE = "create table " + 
	DB_TABLE + " (" + KEY_ID + " integer primary key autoincrement, " +
	KEY_NAME+ " text not null, " + KEY_AGE+ " integer," + KEY_HEIGHT + " float);";
public void create() {
	db.openOrCreateDatabases(DB_NAME, context.MODE_PRIVATE, null)
	db.execSQL(DB_CREATE);
}
~~~

### 8.3.4 数据操作

数据操作是指对数据的添加、删除、查找和更新的操作。

通过执行SQL命名完成数据操作，但推荐使用Android提供的专用类和方法，这些类和方法更加简洁、易用。

为了使DBAdapter类支持对数据的添加、删除、更新和查找等功能，在DBAdapter类中增加下面的这些函数

* insert(People people)用来添加一条数据
* queryAllData()用来获取全部数据
* queryOneData(long id)根据id获取一条数据
* deleteAllData()用来删除全部数据
* deleteOneData(long id)根据id删除一条数据
* updateOneData(long id , People people)根据id更新一条数据

SQLiteDatabase类的公共函数insert()、delete()、update()和query()，封装了执行的添加、删除、更新和查询功能的SQL命令。

下面分别介绍如何使用SQLiteDatabase类的公共函数，完成数据的添加、删除、更新和查询等操作。

**添加功能**

- 首先构造一个ContentValues对象，然后调用ContentValues对象的put()方法，将每个属性的值写入到ContentValues对象中，最后使用SQLiteDatabase对象的insert()函数，将ContentValues对象中的数据写入指定的数据库表中
- insert()函数的返回值是新数据插入的位置，即ID值。ContentValues类是一个数据承载容器，主要用来向数据库表中添加一条数据

~~~ java
public long insert(People people) {
	ContentValues newValues = new ContentValues();
	
	newValues.put(KEY_NAME, people.Name);
	newValues.put(KEY_AGE, people.Age);
	newValues.put(KEY_HEIGHT, people.Height);
	
	return db.insert(DB_TABLE, null, newValues);
}
~~~

第4行代码向ContentValues对象newValues中添加一个名称/值对，put()函数的第1个参数是名称，第2个参数是值。

在第8行代码的insert()函数中，第1个参数是数据表的名称，第2个参数是在NULL时的替换数据，第3个参数是需要向数据库表中添加的数。

**删除功能**

删除数据比较简单，只需要调用当前数据库对象的delete()函数，并指明表名称和删除条件即可。

```java
public long deleteOneData() {
	return db.delete(DB_TABLE, null, null);
}
public long deleteOneData(long id) {
	return db.delete(DB_TABLE,  KEY_ID + "=" + id, null);
}
```

delete()函数的第1个参数是数据库的表名称，第2个参数是删除条件。

在第2行代码中，删除条件为null，表示删除表中的所有数据。

第6行代码指明了需要删除数据的id值，因此deleteOneData()函数仅删除一条数据，此时delete()函数的返回值表示被删除的数据的数量。

**更新功能**

更新数据同样要使用ContentValues对象，首先构造ContentValues对象，然后调用put()函数将属性的值写入到ContentValues对象中，最后使用SQLiteDatabase对象的update()函数，并指定数据的更新条件。

~~~ java
public long updateOneData(long id , People people){
	ContentValues updateValues = new ContentValues();	  
	updateValues.put(KEY_NAME, people.Name);
	updateValues.put(KEY_AGE, people.Age);
	updateValues.put(KEY_HEIGHT, people.Height);
	
	return db.update(DB_TABLE, updateValues,  KEY_ID + "=" + id, null);
}
~~~

在代码的第7行中，update()函数的第1个参数表示数据表的名称，第2个参数是更新条件。update()函数的返回值表示数据库表中被更新的数据数量。

**查询功能**

- 首先介绍Cursor类。在Android系统中，数据库查询结果的返回值并不是数据集合的完整拷贝，而是返回数据集的指针，这个指针就是Cursor类
- Cursor类支持在查询的数据集合中多种方式移动，并能够获取数据集合的属性名称和序号

**Cursor类的方法和说明**

| 函数                    | 说明                       |
| --------------------- | ------------------------ |
| moveToFirst           | 将指针移动到第一条数据上             |
| moveToNext            | 将指针移动到下一条数据上             |
| moveToPrevious        | 将指针移动到上一条数据上             |
| getCount              | 获取集合的数据数量                |
| getColumnIndexOrThrow | 返回指定属性名称的序号，如果属性不存在则产生异常 |
| getColumnName         | 返回指定序号的属性名称              |
| getColumnNames        | 返回属性名称的字符串数组             |
| getColumnIndex        | 根据属性名称返回序号               |
| moveToPosition        | 将指针移动到指定的数据上             |
| getPosition           | 返回当前指针的位置                |

从Cursor中提取数据可以参考ConvertToPeople()函数的实现方法。

在提取Cursor数据中的数据前，推荐测试Cursor中的数据数量，避免在数据获取中产生异常，例如代码的第3行到第5行。

从Cursor中提取数据使用类型安全的get<Type>()函数，函数的输入值为属性的序号，为了获取属性的序号，可以使用getColumnIndex()函数获取指定属性的序号。

~~~ java
private People[] ConvertToPeople(Cursor cursor){
	int resultCounts = cursor.getCount();
	if (resultCounts == 0 || !cursor.moveToFirst()){
	            return null;
	}
	People[] peoples = new People[resultCounts];
	for (int i = 0 ; i<resultCounts; i++){
	            peoples[i] = new People();
	            peoples[i].ID = cursor.getInt(0);
	            peoples[i].Name = cursor.getString(cursor.getColumnIndex(KEY_NAME));
	            peoples[i].Age = cursor.getInt(cursor.getColumnIndex(KEY_AGE));
	            peoples[i].Height = cursor.getFloat(cursor.getColumnIndex(KEY_HEIGHT));
	            cursor.moveToNext();
	}
	return peoples; 
}
~~~

**query()函数的参数说明**

| 位置   | 类型+名称                   | 说明                          |
| ---- | ----------------------- | --------------------------- |
| 1    | String  table           | 表名称                         |
| 2    | String[]  columns       | 返回的属性列名称                    |
| 3    | String  selection       | 查询条件                        |
| 4    | String[]  selectionArgs | 如果在查询条件中使用的问号，则需要定义替换符的具体内容 |
| 5    | String  groupBy         | 分组方式                        |
| 6    | String  having          | 定义组的过滤器                     |
| 7    | String  orderBy         | 排序方式                        |