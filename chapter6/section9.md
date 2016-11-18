# 属性（Attribute）资源

### 属性资源

- 如果用户的自定义 View 组件需要指定属性，就需要属性资源的帮助了。



- 属性资源文件也放在/res/values 目录下，属性资源文件的根元素也是< resources…/ >，该元素里包含如下两个子元素。


> attr 子元素：定义一个属性。 

> declare-styleable 子元素：定义一个 styleable 对象，每个 styleable 对象就是一组 attr 属性的集合。

- 当我们使用属性文件定义了属性之后，接下来就可以在自定义组件的构造器中通过 AttributeSet 对象来获取这些属性了。



- 例如我们想开发一个默认带动画效果的图片，该图片显示时，自动从全透明变到完全不透明，为此我们需要开发一个自定义组件，但这个自定义组件需要制定一个额外 duration 属性，该属性控制动画的持续时间。



- 为了在自定义组件中使用 duration 属性，需要先定义如下属性资源文件。


```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <!-- 定义一个属性 -->
  <attr name="duration">
  </attr>
  <!-- 定义一个styleable 对象来组合多个属性 -->
  <declare-styleable name="AlphaImageView">
    <attr name="duration"/>
  </declare-styleable>
</resources>
```
- 上面的属性资源文件定义了该属性之后，至于到底在哪个 View 组件中使用该属性，该属性到底发挥什么作用，就不归属性资源文件管了。属性资源所定义的属性到底可以发挥什么作用，取决于自定义组件的代码实现。



- 例如如下自定义的 AlphaImageView，它获取了定义该组件所指定的 duration 属性之后，根据该属性来控制图片的透明度的改变。


### 温馨提示

- 在属性资源中定义< declare-styleable…/ >元素时，也可在其内部直接使用< attr…/ >定义属性，使用< attr…/ >时指定一个 format 属性即可，例如上面我们可以指定< attr name=”duratation” format=”integer”/>


```java
public class AlphaImageView extends ImageView {
  // 图像透明度每次改变的大小
  private int alphaDelta = 0;
  // 记录图片当前的透明度。
  private int curAlpha = 0;
  // 每隔多少毫秒透明度改变一次
  private final int SPEED = 300;
  Handler handler = new Handler() {
      @Override
      public void handleMessage(Message msg) {
          if (msg.what == 0x123) {
              // 每次增加curAlpha的值
              curAlpha += alphaDelta;
              if (curAlpha > 255) curAlpha = 255;
              // 修改该ImageView的透明度
              AlphaImageView.this.setAlpha(curAlpha);
          }
      }
};

public AlphaImageView(Context context, AttributeSet attrs) {
    super(context, attrs);
/*
  这里获取定义 AlphaImageView 时指定的 duration 属性，并根据该属性计算图片的透明度的变化幅度，接着程序重写了 ImageView 的 onDraw(Canvas canvas)方法，该方法启动了一个任务调度来控制图片透明度的改变。
  下面的 R.styleable.AlphaImageView、R.styleable.AlphaImageView_duration 都是 Android SDK 根据属性资源文件自动生成的。
*/
    TypedArray typedArray = context.obtainStyledAttributes(attrs,
            R.styleable.AlphaImageView);
    // 获取duration参数
    int duration = typedArray
            .getInt(R.styleable.AlphaImageView_duration, 0);
    // 计算图像透明度每次改变的大小
    alphaDelta = 255 * SPEED / duration;
}

@Override
protected void onDraw(Canvas canvas) {
    this.setImageAlpha(curAlpha);
    super.onDraw(canvas);
    final Timer timer = new Timer();
    // 按固定间隔发送消息，通知系统改变图片的透明度
    timer.schedule(new TimerTask() {
        @Override
        public void run() {
            Message msg = new Message();
            msg.what = 0x123;
            if (curAlpha >= 255) {
                timer.cancel();
            } else {
                handler.sendMessage(msg);
            }
        }
    }, 0, SPEED);
  }
}
```
### AlphaImageView

- 接着在界面布局文件使用 AlphaImageView 时可以为它指定一个 duration 属性，注意该属性位于“http://schemas.anroid.com/apk/res/ + 项目子包”命名空间下，例如本应用的包名为 org.yonga.res，那么 duration 属性就位于“http://schemas.anroid.com/apk/res/org.yonga.res”，也可以使用编辑器自动生成的命名空间。
- 程序清单：codes\06\6.10\AttrResTest\app\src\main\res\layout\main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
<!-- 导入http://schemas.android.com/apk/res/org.yonga.res命名空间，并指定该命名空间对应的短名前缀为yonga -->
  xmlns:crazyit="http://schemas.android.com/apk/res/org.yonga.res"
  android:orientation="vertical"
  android:layout_width="match_parent"
  android:layout_height="match_parent">
<!-- 使用自定义组件，并指定属性资源中定义的属性 -->
<org.crazyit.res.AlphaImageView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:src="@drawable/javaee"
<!-- 为AlphaImageView组件指定自定义属性duration的属性值为60000 -->                   
    yonga:duration="60000"/>
</LinearLayout>
```
