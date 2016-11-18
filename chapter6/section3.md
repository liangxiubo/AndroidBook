# 6.3数组资源
* 上面的程序在 Java 代码中定义了两个数组，android 并不推荐在 Java 代码中定义数组，因为 android 允许通过资源文件来定义数组资源。
* android采用位于```/res/values```目录下的arrays.xml文件来定义数组资源，定义数组时XML资源文件的根元素也是```<resources…/>```，该元素内可包含如下三种子元素。  
1.```<arrary…/>```子元素：定义普通类型的数组，例如Drawable数组。  
2.```<string-array…/>```子元素：定义字符串数组。  
3.```<integer-array…/>```子元素：定义整型数组。
* Java 程序中通过如下形式访问资源：  
```[< package_name >.]R.array.array_name```
* 在 XML 文件中则可通过如下形式进行访问：   
```@[< package_name >:]arrary/array_name```  
* 为了能在 Java 程序中访问到实际数组，Resources 提供了如下方法：  
1.```String[] getStringArray(int id)```：根据资源文件中字符串数组资源的名称来获取实际的字符串数组。  
2.```int[] getIntArray(int id)```：根据资源文件中整型数组资源的名称来获取实际的整型数组。  
3.```TypedArray obtainTypedArray(int id)```：根据资源文件中普通数组资源的名称来获取实际的普通数组。  
* TypedArray代表一个通用类型的数组，该类提供了getXxx(int index)方法来获取指定索引处的数组元素。
<pre>
&lt;?xml version="1.0" encoding="utf-8"?>
&lt;resources>
    &lt;!-- 定义一个Drawable数组 -->
    &lt;array name="plain_arr">
        &lt;item>@color/c1&lt;/item>
        &lt;item>@color/c2&lt;/item>
        &lt;item>@color/c3&lt;/item>
        &lt;item>@color/c4&lt;/item>
        &lt;item>@color/c5&lt;/item>
        &lt;item>@color/c6&lt;/item>
        &lt;item>@color/c7&lt;/item>
        &lt;item>@color/c8&lt;/item>
        &lt;item>@color/c9&lt;/item>
    &lt;/array>
    &lt;!-- 定义字符串数组 -->
    &lt;string-array name="string_arr">
        &lt;item>@string/c1&lt;/item>
        &lt;item>@string/c2&lt;/item>
        &lt;item>@string/c3&lt;/item>
        &lt;item>@string/c4&lt;/item>
        &lt;item>@string/c5&lt;/item>
        &lt;item>@string/c6&lt;/item>
        &lt;item>@string/c7&lt;/item>
        &lt;item>@string/c8&lt;/item>
        &lt;item>@string/c9&lt;/item>
    &lt;/string-array>
    &lt;!-- 定义字符串数组 -->
    &lt;string-array name="books">
        &lt;item>疯狂Java讲义&lt;/item>
        &lt;item>疯狂Ajax讲义&lt;/item>
        &lt;item>疯狂Android讲义&lt;/item>
    &lt;/string-array>
&lt;/resources>
</pre>
<pre>
&lt;?xml version="1.0" encoding="utf-8"?>
&lt;LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center_horizontal">
    &lt;!-- 使用字符串资源，尺度资源 -->
    &lt;TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/app_name"
        android:gravity="center"
        android:textSize="@dimen/title_font_size"
        />
    &lt;!-- 定义一个GridView组件，使用尺度资源中定义的长度来指定水平间距、垂直间距 -->
    &lt;GridView
        android:id="@+id/grid01"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:horizontalSpacing="@dimen/spacing"
        android:verticalSpacing="@dimen/spacing"
        android:numColumns="3"
        android:gravity="center">
    &lt;/GridView>
    &lt;!-- 定义ListView组件，使用了数组资源 -->
    &lt;ListView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:entries="@array/books"
        />
&lt;/LinearLayout>
</pre>
<pre>
public class MainActivity extends Activity {
    // 获取系统定义的数组资源
    String[] texts;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        texts = getResources().getStringArray(R.array.string_arr);
        // 创建一个BaseAdapter对象
        BaseAdapter ba = new BaseAdapter() {
            @Override
            public int getCount() {
                // 指定一共包含9个选项
                return texts.length;
            }

            @Override
            public Object getItem(int position) {
                // 返回指定位置的文本
                return texts[position];
            }

            @Override
            public long getItemId(int position) {
                return position;
            }

            // 重写该方法，该方法返回的View将作为的GridView的每个格子
            @Override
            public View getView(int position
                    , View convertView, ViewGroup parent) {
                TextView text = new TextView(MainActivity.this);
                Resources res = MainActivity.this.getResources();
                // 使用尺度资源来设置文本框的高度、宽度
                text.setWidth((int) res.getDimension(R.dimen.cell_width));
                text.setHeight((int) res.getDimension(R.dimen.cell_height));
                // 使用字符串资源设置文本框的内容
                text.setText(texts[position]);
                TypedArray icons = res.obtainTypedArray(R.array.plain_arr);
                // 使用颜色资源来设置文本框的背景色
                text.setBackground(icons.getDrawable(position));
                text.setTextSize(20);
                return text;
            }
        };
        GridView grid = (GridView) findViewById(R.id.grid01);
        // 为GridView设置Adapter
        grid.setAdapter(ba);
    }
}
</pre>
