# 6.4Drawable资源
* Drawable资源是android资源是android应用中使用最广泛的资源，也是 android 应用最灵活的资源，它不仅可以直接使用.png、.9.png、.jpg、.gif 等图片作为资源，也可以使用多种 XML 文件作为资源。只要一份XML文件可被系统编译成 Drawable 子类的对象，那么这份XML文本即可作为Drawable资源。
* Drawable资源通常保存在```/res/drawable```目录下，实际上可能保存在```/res/drawable-ldpi```、```/res/drawable-mdpi```、```/res/drawable-hdpi```、```/res/drawable-xhdpi```目录下。
## 图片资源
* 图片资源是最简单的Drawable资源，只要把.png、.9.png、.jpg、.gif 等格式的图片放入```/res/drawable-xxx```目录下，android SDK就会在编译应用中自动加载该图片，并在R资源清单类中生成该资源的索引。
* Java类中使用如下语法格式来访问该资源：   
```[<package_name>.]R.drawable.<file_name>```
* 在 XML 代码中则按如下语法格式来访问该资源：   
```@[<package_name>:]drawable/file_name```
* 为了在程序中获得实际的Drawable对象，Resources提供了Drawable的getDrawable(int id)方法，该方法即可根据Drawable资源在R资源清单类中的ID来获取实际的Drawable对象。
## StateListDrawable 资源（不同状态的Drawable）
* StateListDrawable 用于组织多个 Drawable 对象。当使用 StateListDrawable 作为目标组件的背景、前景图片时，StateListDrawable 对象所显示的 Drawable 对象会随目标组件状态的改变而自动切换。  
* 定义 StateListDrawable对象的XML文件的根元素为```<selector…/>```，该元素可以包含多个```<item…/>```元素，该元素可指定如下属性。
1.android:color 或 android:drawable：指定颜色或 Drawable 对象。   
2.android:state_xxx：指定一个特定状态。
* 例如如下语法格式：
<pre>
&lt;selector xmlns:android="http://schemas.android.com/apk/res/android">
    &lt;!--指定特定状态下的颜色-->
    &lt;item android:drawable="@color/btn_pressed"
          android:state_pressed="true" | "false"/>
&lt;/selector>
</pre>
## LayerDrawable 资源
定义LayerDrawable对象的XML文件的根元素```<layer-list…/>```，该元素可以包含多个```<item…/>```元素，该元素可指定如下属性。  
1.android:drawable：指定作为LayerDrawable元素之一的Drawable对象。   
2.android:id：为该Drawable对象指定一个标识。   
3.android:buttom | top | left | width：它们用于指定一个长度值，用于指定将该 Drawable 对象绘制到目标组件的指定位置。
* 例如如下语法格式：
<pre>
&lt;?xml version="1.0" encoding="utf-8"?>
&lt;layer-list xmlns:android="http://schemas.android.com/apk/res/android">

    &lt;!--指定一个 Drawable 元素-->
    &lt;item android:id="@+id/background"
        android:drawable="@drawable/background"/>

&lt;/layer-list>
</pre>
## ShapeDrawable 资源
* ShapeDrawabale用于定义一个基本的几何图形（如矩形、圆形、线条等），定义 ShapeDrawable的XML文件的根元素是< shape…/ >元素，该元素可指定如下属性。  
1.android:shape=[“rectangle” | “oval” | “line” | “ring”]：指定定义哪种类型的几何图形。
*定义ShapeDrawable对象的完整语法格式如下：
<pre>
&lt;?xml version="1.0" encoding="utf-8"?>
&lt;shape
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape=["rectangle" | "oval" | "line" | "ring"] >
    &lt;!--定义几何图形的四个角的弧度-->
    &lt;corners
        android:radius="integer"
        android:topLeftRadius="integer"
        android:topRightRadius="integer"
        android:bottomLeftRadius="integer"
        android:bottomRightRadius="integer" />
    &lt;!--定义使用渐变色填充-->
    &lt;gradient
        android:angle="integer"
        android:centerX="integer"
        android:centerY="integer"
        android:startColor="color"
        android:centerColor="color"
        android:endColor="color"
        android:gradientRadius="integer"
        android:type=["linear" | "radial" | "sweep"]
        android:useLevel=["true" | "false"] />
    &lt;!--定义几何形状的内边距-->
    &lt;padding
        android:left="integer"
        android:top="integer"
        android:right="integer"
        android:bottom="integer" />
    &lt;!--定义几何形状的大小-->
    &lt;size
        android:width="integer"
        android:height="integer" />
    &lt;!--定义使用单种颜色填充-->
    &lt;solid
        android:color="color" />
    &lt;!--定义为几何形状绘制边框-->
    &lt;stroke
        android:width="integer"
        android:color="color"
        android:dashWidth="integer"
        android:dashGap="integer" />
&lt;/shape>
</pre>
## ClipDrawable 资源
ClipDrawable代表从其他位图上截取一个 “图片片段”。在XML文件中定义 ClipDrawable对象使用 ```<clip…/>```元素，该元素的语法为：
<pre>
&lt;?xml version="1.0" encoding="utf-8"?>  
&lt;clip xmlns:android="http://schemas.android.com/apk/res/android"   
    android:drawable="@drawable/fengjing"  
    android:clipOrientation=["horizontal" | "vertical"]  
    android:gravity=["top" | "bottom" | "left" | "right" | "center_vertical" | "fill_vertical" | "center_horizontal" | "fill_horizontal" | "center" | "fill" | "clip_vertical" | "clip_horizontal"]>  
&lt;/clip>  
</pre>
* 上面的语法格式中可指定如下三个属性。  
1.android:drawable：指定截取的源Drawable对象。  
2.android:clipOrientation：指定截取方向，可设置水平截取或垂直截取。  
3.android:gravity：指定截取时的对齐方式。
* 使用 ClipDrawable 对象时可调用 setLevel(int level)方法来设置截取的区域大小，当level为0时，截取的图片片段为空；当 level为10000 时，截取整张图片。
## AnimationDrawable 资源
* AnimationDrawable代表一个动画。android 既支持传统的逐帧动画(类似电影方式，一张图片、一张图片地切换)，也支持通过平移、变换计算出来的补间动画。
* 下面以补间动画为例来介绍如何定义AnimationDrawable资源，定义补间动画的XML 资源文件以```<set…/>```元素作为根元素，该元素内可以指定如下4个元素：  
1.alpha：设置透明度的改变。   
2.scale：设置图片进行缩放改变。   
3.translate：设置图片进行位移变换。   
4.rotate：设置图片进行旋转。  
* 定义动画的XML资源应该放在```/res/anim```路径下，当使用ADT创建一个 android 应用时，默认不会包含该路径，开发者需要自行创建该路径。
* 定义补间动画的思路很简单：设置一张图片的开始状态（包括透明度、位置、缩放比、旋转度）、并设置该图片的结束状态（包括透明度、位置、缩放比、旋转度），再设置动画的持续时间，android系统会使用动画效果把这张图片从开始状态变换到结束状态。
* 设置补间动画的语法格式如下：
<pre>
&lt;?xml version="1.0" encoding="utf-8"?>
&lt;set xmlns:android="http://schemas.android.com/apk/res/android"
     android:duration="持续时间"
     android:interpolator="@[package:]anim/interpolator_resource"
     android:shareInterpolator=["true" | "false"]>
    &lt;alpha
        android:fromAlpha="float"
        android:toAlpha="float"/>
    &lt;scale
        android:fromXScale="float"
        android:fromYScale="float"
        android:pivotX="float"
        android:pivotY="float"
        android:toXScale="float"
        android:toYScale="float"/>
    &lt;translate
        android:fromXDelta="float"
        android:fromYDelta="float"
        android:toXDelta="float"
        android:toYDelta="float"/>
    &lt;rotate
        android:fromDegrees="float"
        android:pivotX="float"
        android:pivotY="float"
        android:toDegrees="float"/>
&lt;/set>
</pre>
* 上面语法格式中包含了大量的fromXxx、toXxx属性，这些属性就用于定义图片的开始状态、结束状态。除此之外，当进行缩放变换（scale）、旋转（rotate）变换时，还需要指定如下pivotX、pivotY两个属性，这两个属性用于指定变换的“中心点”—-比如进行旋转变换时，需要指定“旋轴点”；进行缩放变换时，需要指定“中心点”。
* 除此之外，上面```<set…/>```、```<alpha…/>```、```<scale…/>```、```<translate…/>```、```<rotate…/>```都可指定一个 android:interpolator 属性，该属性指定动画的变化速度，可以实现匀速、正加速、负加速、无规则变加速等，android 系统的R.anim类中包含了大致常量，它们定义了不同的动画速度，例如：  
1.linear_interpolator：匀速变换。  
2.accelerate_interpolator：加速变换。  
3.decelerate_interpolator：减速变换。  
* 如果程序想让```<set…/>```元素下所有的变换效果使用相同的动画速度，则可指定 android:shareInterpolator=”true”。
<pre>
&lt;?xml version="1.0" encoding="UTF-8"?>
&lt;set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/linear_interpolator"
    android:duration="5000">
    &lt;!-- 定义缩放变换 -->
    &lt;scale android:fromXScale="1.0"
        android:toXScale="1.4"
        android:fromYScale="1.0"
        android:toYScale="0.6"
        android:pivotX="50%"
        android:pivotY="50%"
        android:fillAfter="true"
        android:duration="2000"/>
    &lt;!-- 定义位移变换 -->
    &lt;translate android:fromXDelta="10"
        android:toXDelta="130"
        android:fromYDelta="30"
        android:toYDelta="-80"
        android:duration="2000"/>
&lt;/set>
</pre>
*　在 Java 代码中则按如下语法格式来访问它：  
```[< package >.]R.anim.<file_name>```  
在 XML 文件中按如下语法格式来访问它：  
```@[<package_name>:]anim/file_name```  
*　为了在 Java 代码中获取实际的 Animation 对象，则可调用AnimationUtils的如下方法：  
```loadAnimation(Context ctx,int resId)``` 
<pre>
public class MainActivity extends Activity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        final ImageView image = (ImageView) findViewById(R.id.image);
        // 加载动画资源
        final Animation anim = AnimationUtils.loadAnimation(this,
                R.anim.my_anim);
        // 设置动画结束后保留结束状态
        anim.setFillAfter(true);  // ①
        Button bn = (Button) findViewById(R.id.bn);
        bn.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View arg0) {
                // 开始动画
                image.startAnimation(anim);
            }
        });
    }
}
</pre>
<pre>
&lt;?xml version="1.0" encoding="UTF-8"?>
&lt;set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/linear_interpolator"
    android:duration="5000">
    &lt;!-- 定义缩放变换 -->
    &lt;scale android:fromXScale="1.0"
        android:toXScale="1.4"
        android:fromYScale="1.0"
        android:toYScale="0.6"
        android:pivotX="50%"
        android:pivotY="50%"
        android:fillAfter="true"
        android:duration="2000"/>
    &lt;!-- 定义位移变换 -->
    &lt;translate android:fromXDelta="10"
        android:toXDelta="130"
        android:fromYDelta="30"
        android:toYDelta="-80"
        android:duration="2000"/>
&lt;/set>
</pre> 
* X 轴向右为正，Y 轴向下为正  
* 在这些属性里面还可以加上%和p,例如:  
1.android:fromXDelta=”100%”,表示自身的100%,也就是从View自己的位置开始  
2.android:toXDelta=”80%p”,表示父层View的80%,是以它父层View为参照的　　

