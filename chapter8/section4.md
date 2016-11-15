# 8.4自动朗读

*Android提供了自动朗读支持。自动朗读支持可以对指定文本内容进行朗读，从而发出声音：不仅如此，Android的自动朗读支持还允许把文本对应的音频录制成音频文件，方便以后播放。这种自动朗读支持的英文名称为TextToSpeech，简称TTS。借助TTS的支持，可以在应用程序中动态地增加音频输出，从而改善用户体验。
*Android的自动朗读支持主要通过TextToSpeech来完成，该类提供了如下一个构造器。
**TextToSpeech(Context context, TextToSpeech.OnInitListener listener)
*从上面的构造器不难看出，当创建TextToSpeech对象时，必须先提供一个OnInitListener监听器——该监听器负责监听TextToSpeech的初始化结果。
*一旦在程序中获得了TextToSpeech对象之后，接下来即可调用TextToSpeech的setLanguage(Locale loc)方法来设置该TTS发声引擎应使用的语言、国家选项。
*提示：
**由于不同的文字，在不同的语言、国家中的发音是不同的，尤其是欧美，他们所使用的都是字母文字，因此一段文本内容，使用不同语言、国家选项来朗读，发音效果是截然不同的。目前Android的TTS暂时不支持中文。
*如果调用setLanguage(Locale loc)的返回值是“TextToSpeech.LANG_COUNTRY_AVAILABLE”，则说明当前TTS系统可以支持所设置的语言、国家选项。
*对TextToSpeech设置完成后，就可调用它的方法来朗读文本了，具体方法可参考TextToSpeech的API文档。TextToSpeech类中最常见额方法就是如下两个。
**speak(ChatSequence text, int queueMode, Bundle params, String utteranceId)
**synthesizeToFile(CharSequence text, Bundle params, File file, String utteranceId)
*上面两个方法都用于把text文字内容转换为音频，区别只是speak()方法是播放转换的音频，而synthesizeToFile()方法是把转换的报的音频保存成声音文件。
*上面两个方法中的params都用于指定声音转换时的参数。speak()方法中的queueMode参数指定的TTS的发音队列模式，该参数支持如下两个常亮。
**TextToSpeech.QUEUE_FLUSH：如果指定该模式，当TTS调用speak()方法时，它会中断当前实例正在运行的任务（也可以理解为清除当前语音任务，转而执行新的语音任务）。
**TextToSpeech.QUEUE_ADD：如果指定为该模式，当TTS调用speak()方法时，会把新的发音任务添加到当前发音任务队列之后——也就是等任务队列中的发音任务执行完成后再来执行speak()方法指定的发音任务。
*当程序用完了TextToSpeech对象之后，可以在Activity的OnDestroy()方法中调用它的shutdown()来关闭TextToSpeech，释放它所占用的资源。
*归纳起来，使用TextToSpeech的步骤如下。
    1.创建TextToSpeech对象，创建时传入OnInitListener监听器监听创建是否成功。
    2.设置TextToSpeech所使用的语言、国家选项，通过返回值判断TTS是否支持该语言、国家选项。
    3.调用speak()或synthesizeToFile()方法。
    4.关闭TTS，回收资源。
*下面的程序示范了如何利用TTS来朗读用户所输入的文本内容。

```
public class MainActivity extends Activity
{
    TextToSpeech tts;
    EditText editText;
    Button speech;
    Button record;
    @Override
    public void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.mian);
        //初始化TextToSpeech对象
        tts = new TextToSpeech(this, new OnInitListener()
        {
            @Override
            public void onInit(int status)
            {   
                //如果装在TTS引擎成功
                if(status == TextToSpeech.SUCCESS)
                {   
                    //设置使用美式英语朗读
                    int result = tts.setLanguage(Locale.US);
                    //如果不支持所设置的语言
                    if(result != TextToSpeech.LANG_COUNTRY_AVAILABLE
                        && result != TextToSpeech.LANG_AVAILABLE)
                    {
                        Toast.makeText(MainActivity.this,"TTS暂时不支持这种语言的朗读",Toast.LENGTH_SHORT).show();
                    }
                }
            }
        });
        editText = (EditText) findViewById(R.id.txt);
        speech = (Button) findViewById(R.id.speech);
        record = (Button) findViewById(R.id.record);
        speech.setOnClickListener(new OnClickListener()
        {
            @Override
            public void OnClick(View arg0)
            {  
                //执行朗读
                tts.speak(editText.getText().toString(),TextToSpeech.QUEUE_ADD, null, "speech");
            }
        });
        record.setOnClickListener(new OnClickListener()
        {
            @Override
            public void OnClick(View arg0)
            {
                //将朗读文本的音频记录到指定文件
                tts.synthesizeToFile(editText.getText().toString(), null,
                    new File("/mnt/sdcard/sound.wav"), "record");
                Toast.makeText(MainActivity.this, "声音记录成功！"
                    , Toast.LENGTH_SHORT).show();
            }
        });
    }
    @Override
    public void onDestory()
    {
        //关闭TextToSpeech对象
        if(tts != null)
        {
            tts.shutdown();
        }
    }
}
```
*上面程序中的第一行粗体字代码创建了一个TextToSpeech对象，第二行粗体字代码设置使用美式英语进行朗读。接下来程序范别提供了两个按钮，一个按钮用于执行朗读，一个按钮用于将文本内容 的朗读音频保存成声音文件，分别通过调用TextToSpeech对象的speak()和synthesizeToFile()方法来完成——如上面程序中的后两行粗体字代码所示。
*运行上面的程序，将可以看到如下图所示的界面。
![](8_4.png)
*在上图所示的界面中，当用户单击“朗读”按钮后，系统将会调用TTS的speak()方法来朗读文本框中的内容，当用户单击“记录声音”按钮后，系统将会调用synthesizeToFile()方法把文本框中的文本对应的朗读音频记录到SD卡的声音文件中——单击该按钮将可以在SD卡的根目录先生成一个sound.wav文件，该文件可以被导出，在其他音频播放软件中播放。
*程序重写Activity的onDestroy()方法，并在该方法中关闭了TextToSpeech对象，回收它的资源。



