# 使用原始资源

- 类似声音文件及其各种类型的文件，只要 android 没有为之提供专门的支持，这种资源都被称为原始资源。android 的原始资源可以放在如下两个地方。


> 位于/res/raw 目录下，Android SDK 会处理该目录下原始资源，Android SDK 会在 R 清单类中为该目录下的资源生成一个索引项。 

> 位于/assets/目录下，该目录下的资源是更彻底的原始资源。android 应用需要通过 AssetManager 来管理该目录下的原始资源。

- Android SDK 会为位于/res/raw/目录下的资源在 R 类中生成一个索引项，在 Java 代码中则按如下语法格式来访问它：


```java
[< package_name >.]R.raw.< file_name >
```

- 在 XML 文件中可通过如下语法格式来访问它：


```xml
@[< package_name >:]raw/file_name
```

- 通过上面的索引项，android 应用就可以非常方便地访问/res/raw 目录下的原始资源，至于获取原始资源之后如何处理，则完全取决于实际项目的需要。

### AssetManager

- AssetManager 是一个专门管理/assets/目录下原始资源的管理器类


- AssetManager 提供了如下两个常用方法来访问 Assets 资源。

> InputStream open(String fileName)：根据文件名来获取原始资源对应的输入流。 

> AssetFileDescriptor openFd(String fileName)：根据文件名来获取原始资源对应的 AssetFileDescriptor。AssetFileDescriptor 代表了一项原始资源的描述，应用程序可通过 AssetFileDescriptor 来获取原始资源。

- 先在应用的/res/raw/目录下放入一个 bomb.mp3 文件—-Android SDK 会自动处理该目录下的资源，会在 R 清单类中为它生成一个索引项：R.raw.bomb。



- 接下来我们再往/assets/目录下放入一个 shot.mp3 文件—-需要通过 AssetManager 进行管理。


```java
public class MainActivity extends Activity {
MediaPlayer mediaPlayer1 = null;
MediaPlayer mediaPlayer2 = null;

@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main);
    // 直接根据声音文件的ID来创建MediaPlayer
    //获取/res/raw/目录下的原始资源文件
    mediaPlayer1 = MediaPlayer.create(this, R.raw.bomb);
    // 获取该应用的AssetManager
    AssetManager am = getAssets();
    try {
        // 获取指定文件对应的AssetFileDescriptor
      	//利用AssetManager来获取/assets/目录下的原始资源文件
        AssetFileDescriptor afd = am.openFd("shot.mp3");
        mediaPlayer2 = new MediaPlayer();
        // 使用MediaPlayer加载指定的声音文件
        mediaPlayer2.setDataSource(afd.getFileDescriptor());
        mediaPlayer2.prepare();
    } catch (IOException e) {
        e.printStackTrace();
    }
    // 获取第一个按钮，并为它绑定事件监听器
    Button playRaw = (Button) findViewById(R.id.playRaw);
    playRaw.setOnClickListener(new OnClickListener() {
        @Override
        public void onClick(View arg0) {
            // 播放声音
            mediaPlayer1.start();
        }
    });
    // 获取第二个按钮，并为它绑定事件监听器
    Button playAsset = (Button) findViewById(R.id.playAsset);
    playAsset.setOnClickListener(new OnClickListener() {
        @Override
        public void onClick(View arg0) {
            // 播放声音
            mediaPlayer2.start();
        }
    });
  }
}
```
- 上面的程序利用MediaPlayer来播放声音，MediaPlayer是Android提供的一个播放声音的类，后面还有关于该类更详细的介绍。