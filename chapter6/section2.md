# 6.2使用字符串、颜色、尺寸资源
* 字符串资源、颜色资源、尺寸资源，它们对应的XML文件都将位于/res/values/目录下，它们默认的文件名以及在R类中对应的内部类如表所示。
![](6_2_1.jpg)
##颜色值的定义
* android 中的颜色值是通过红(Red)、绿(Green)、蓝(Blue)三原色以及一个透明度(Alpha)值来表示的，颜色值总是以(#)开头，接下来就是 Alpha-Red-Green-Blue 的形式。其中 Alpha 值可以省略，如果省略了 Alpha 值，那么该颜色默认是完全不透明的。
* android 颜色值支持常见的 4 种形式。  
1.#RGB：分别指定红、绿、蓝三原色的值（只支持0~f这 16 级颜色）来代表颜色。  
2.#ARGB：分别指定红、绿、蓝三原色的值（只支持0~f这 16 级颜色）及透明度（只支持0~f这 16 级透明度）来代表颜色。  
3.#RRGGBB：分别指定红、绿、蓝三原色的值（只支持00~ff这 256 级颜色）来代表颜色。  
4.#AARRGGBB：分别指定红、绿、蓝三原色的值（只支持00~ff这 256 级颜色）以及透明度（只支持00~ff这 256 级颜色）来代表颜色。
##定义字符串、颜色、尺寸资源文件
* **字符串资源**文件位于```/res/values/```目录下，字符串资源文件的根元素是```<resources…>```，该元素里每个```<string…/>```子元素定义一个字符串常量，其中```<string…/>```元素的 name 属性指定该常量的名称，```<string…/>```元素开始标签和结束标签之间的内容代表字符串值，如下代码所示：
<pre>
&lt;!--定义一个字符串,名称为hello_world,字符串值为Hello world!-->
&lt;string name="hello_world">Hello world!&lt;/string>
</pre>
<pre>
&lt;?xml version="1.0" encoding="utf-8"?>
&lt;resources>
    &lt;string name="app_name">字符串、颜色、尺寸资源&lt;/string>
    &lt;string name="c1">F00&lt;/string>
    &lt;string name="c2">0F0&lt;/string>
    &lt;string name="c3">00F&lt;/string>
    &lt;string name="c4">0FF&lt;/string>
    &lt;string name="c5">F0F&lt;/string>
    &lt;string name="c6">FF0&lt;/string>
    &lt;string name="c7">07F&lt;/string>
    &lt;string name="c8">70F&lt;/string>
    &lt;string name="c9">F70&lt;/string>
&lt;/resources>
</pre>
* **颜色资源文件**位于```/res/values/```目录下，颜色资源文件的根元素是```<resource…>```，该元素里每个```<color…/ >```子元素定义一个字符串常量，其中```<color…/>```元素的 name 属性指定该颜色的名称，```<color…/>```元素开始标签和结束标签之间的内容代表颜色值，如以下代码所示：
<pre>
&lt;!--定义一个颜色,名称为c1,颜色为红色-->
&lt;color name="c1">#F00&lt;/color>
</pre>
<pre>
&lt;?xml version="1.0" encoding="utf-8"?>
&lt;resources>
    &lt;color name="c1">#F00&lt;/color>
    &lt;color name="c2">#0F0&lt;/color>
    &lt;color name="c3">#00F&lt;/color>
    &lt;color name="c4">#0FF&lt;/color>
    &lt;color name="c5">#F0F&lt;/color>
    &lt;color name="c6">#FF0&lt;/color>
    &lt;color name="c7">#07F&lt;/color>
    &lt;color name="c8">#70F&lt;/color>
    &lt;color name="c9">#F70&lt;/color>
&lt;/resources>
</pre>
* **尺寸资源文件**位于```/res/values/```目录下，尺寸资源文件的根元素是```<resource…>```，该元素里每个```<dimen…/>```子元素定义一个尺寸常量，其中```<dimen…/>```元素的 name 属性指定该尺寸的名称，```<dimen…/>```元素开始标签和结束标签之间的内容代表颜色值，如以下代码所示：
<pre>
&lt;?xml version="1.0" encoding="utf-8"?>
&lt;resources>
    &lt;dimen name="spacing">8dp&lt;/dimen>
    &lt;!-- 定义GridView组件中每个单元格的宽度、高度 -->
    &lt;dimen name="cell_width">60dp&lt;/dimen>
    &lt;dimen name="cell_height">66dp&lt;/dimen>
    &lt;!-- 定义主程序的标题的字体大小 -->
    &lt;dimen name="title_font_size">18sp&lt;/dimen>
&lt;/resources>
</pre>
##使用字符串、颜色、尺寸资源
* 正如前面介绍的，在 XML 文件中使用资源按如下语法格式：  
```
@[< package_name >:]< resource_type >/< resource_name >
```
* 下面程序的界面布局中使用了大量前面定义的资源。
<pre>
&lt;?xml version="1.0" encoding="utf-8"?>
&lt;LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center_horizontal">
    &lt;!-- 使用字符串资源，尺寸资源 -->
    &lt;TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/app_name"
        android:gravity="center"
        android:textSize="@dimen/title_font_size"/>
    &lt;!-- 定义一个GridView组件，使用尺寸资源中定义的长度来指定水平间距、垂直间距 -->
    &lt;GridView
        android:id="@+id/grid01"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:horizontalSpacing="@dimen/spacing"
        android:verticalSpacing="@dimen/spacing"
        android:numColumns="3"
        android:gravity="center">
    &lt;/GridView>
&lt;/LinearLayout>
</pre>
<pre>
public class MainActivity extends Activity {
    // 使用字符串资源
    int[] textIds = new int[]
            {
                    R.string.c1, R.string.c2, R.string.c3,
                    R.string.c4, R.string.c5, R.string.c6,
                    R.string.c7, R.string.c8, R.string.c9
            };
    // 使用颜色资源
    int[] colorIds = new int[]
            {
                    R.color.c1, R.color.c2, R.color.c3,
                    R.color.c4, R.color.c5, R.color.c6,
                    R.color.c7, R.color.c8, R.color.c9
            };

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        // 创建一个BaseAdapter对象
        BaseAdapter ba = new BaseAdapter() {
            @Override
            public int getCount() {
                // 指定一共包含9个选项
                return textIds.length;
            }

            @Override
            public Object getItem(int position) {
                // 返回指定位置的文本
                return getResources().getText(textIds[position]);
            }

            @Override
            public long getItemId(int position) {
                return position;
            }

            // 重写该方法，该方法返回的View将作为GridView的每个格子
            @Override
            public View getView(int position
                    , View convertView, ViewGroup parent) {
                TextView text = new TextView(MainActivity.this);
                Resources res = MainActivity.this.getResources();
                // 使用尺度资源来设置文本框的高度、宽度
                text.setWidth((int) res.getDimension(R.dimen.cell_width));
                text.setHeight((int) res.getDimension(R.dimen.cell_height));
                // 使用字符串资源设置文本框的内容
                text.setText(textIds[position]);
                // 使用颜色资源来设置文本框的背景色
                text.setBackgroundResource(colorIds[position]);
                text.setTextSize(20);
                text.setTextSize(getResources()
                        .getInteger(R.integer.font_size));
                return text;
            }
        };
        GridView grid = (GridView) findViewById(R.id.grid01);
        // 为GridView设置Adapter
        grid.setAdapter(ba);
    }
}
</pre>
* 与定义字符串资源类似的是，android也允许使用资源文件来定义boolean常量。例如，在```/res/values/```目录下增加一个bools.xml 文件，该文件的根元素也是```<resources…/>```，根元素内通过```<bool…/>```子元素来定义boolean常量。
<pre>
&lt;?xml version="1.0" encoding="utf-8"?>
&lt;resources>
    &lt;bool name="is_male">true&lt;/bool>
    &lt;bool name="is_big">false&lt;/bool>
&lt;/resources>
</pre>
* Java代码中按如下语法格式访问即可：  
```[&lt; package_name >.]R.bool.bool_name```
* 在 XML 文件按如下格式即可访问资源：  
```@[&lt; package_name >:]bool/bool_name```
* 例如，为了在Java代码中获取指定boolean变量的值，可通过如下代码来实现：
<pre>
Resources res = getResources();
boolean is_male = res.getBoolean(R.bool.is_male);
</pre>
* 与定义字符串资源类似的是，android 也允许使用资源文件来定义整型常量。例如，在```/res/values/```目录下增加一个integer.xml文件(文件名可以自由选择)，该文件的根元素也是```<resource…/>```，根元素内通过```<integer…/>```子元素来定义整型常量。
<pre>
&lt;resources>
    &lt;integer name="my_size">32&lt;/integer>
    &lt;integer name="book_numbers">12&lt;/integer>
&lt;resources/>
</pre>
* Java 代码中按如下语法格式访问即可：  
```[&lt; package_name >.]R.integer.integer_name```
* 在 XML 文件中按如下格式即可访问资源：  
```@[&lt; package_name >:]integer/integer_name```
* 例如，为了在 Java 代码中获取指定 整型变量的值，可通过如下代码来实现：
<pre>
Resources res = getResources();
int my_size = res.getInteger(R.integer.my_size);
</pre>