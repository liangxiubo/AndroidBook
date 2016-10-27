# 基本界面组件
####Android系统的界面控件分为定制控件和系统控件
* 定制控件是用户独立开发的控件，或通过继承并修改系统控件后所产生的新控件。能够为用户提供特殊的功能或与众不同的显示需求方式。
* 系统控件是Android系统提供给用户已经封装的界面控件。提供在应用程序开发过程中常见功能控件。系统控件更有利于帮助用户进行快速开发，同时能够使Android系统中应用程序的界面保持一致性。

####常见的系统控件包括
* TextView、EditText、Button、ImageButton、Checkbox、RadioButton、Spinner、ListView和TabHost
#####TextView和EditText
* TextView是一种用于显示字符串的控件
* EditText则是用来输入和编辑字符串的控件，EditText是一个具有编辑功能的TextView

```
 <TextView android:id="@+id/TextView01"
	android:layout_width="wrap_content" 
	android:layout_height="wrap_content"
	android:text="TextView01" >
    </TextView>
    < EditText android:id="@+id/EditText01" 
	android:layout_width="fill_parent" 
	android:layout_height="wrap_content"
	android:text="EditText01" >
    </EditText>

```
（图片）
*第1行android:id属性声明了TextView的ID，这个ID主要用于在代码中引用这个TextView对象*
* “@+id/TextView01”表示所设置的ID值
* @表示后面的字符串是ID资源
* 加号（+）表示需要建立新资源名称，并添加到R.java文件中
* 斜杠后面的字符串（TextView01）表示新资源的名称
* 如果资源不是新添加的，或属于Android框架的ID资源，则不需要使用加号（+），但必须添加Android包的命名空间，例如android:id="@android:id/empty"

*第2行的android:layout_width属性用来设置TextView的宽度，wrap_content表示TextView的宽度只要能够包含所显示的字符串即可*

*第3行的android:layout_height属性用来设置TextView的高度*

*第4行表示TextView所显示的字符串，在后面将通过代码更改TextView的显示内容*
*第7行中“fill_content”表示EditText的宽度将等于父控件的宽度*

```
TextView textView = (TextView)findViewById(R.id.TextView01);
EditText editText = (EditText)findViewById(R.id.EditText01);
textView.setText("用户名：");
editText.setText("");

```
*第1行代码的findViewById()函数能够通过ID引用界面上的任何控件，只要该控件在XML文件中定义过ID即可*

*第3行代码的setText()函数用来设置TextView所显示的内容*

#####Button和ImageButton
* Button是一种按钮控件，用户能够在该控件上点击，并后引发相应的事件处理函数
* ImageButton用以实现能够显示图像功能的控件按钮
（图片）

```
<Button android:id="@+id/Button01" 
	android:layout_width="wrap_content" 
	android:layout_height="wrap_content"
	android:text="Button01" >
</Button>
<ImageButton android:id="@+id/ImageButton01" 
	android:layout_width="wrap_content" 
	android:layout_height="wrap_content">
</ImageButton>

```
  * *定义Button控件的高度、宽度和内容*
  * *定义ImageButton控件的高度和宽度，但是没定义显示的图像，在后面的代码中进行定义**
  
  更改Button和ImageButton内容，引入android.widget.Button和android.widget.ImageButton：
  
  ```
  Button button = (Button)findViewById(R.id.Button01);
ImageButton imageButton = (ImageButton)findViewById(R.id.ImageButton01);
button.setText("Button按钮");
imageButton.setImageResource(R.drawable.download);

  ```
  
  *第1行代码用于引用在XML文件中定义的Button控件
第2行代码用于引用在XML文件中定义的ImageButton控件
第3行代码将Button的显示内容更改为“Button按钮”
第4行代码利用setImageResource()函数，将新加入的png文件R.drawable.download传递给ImageButton*

按钮响应点击事件：添加点击事件的监听器：

```
final TextView textView = (TextView)findViewById(R.id.TextView01);
button.setOnClickListener(new View.OnClickListener() {
	public void onClick(View view) {
		textView.setText("Button按钮");
	}
});
imageButton.setOnClickListener(new View.OnClickListener() {
	public void onClick(View view) {
		textView.setText("ImageButton按钮");
	}
});

```
*第2行代码中button对象通过调用setOnClickListener()函数，注册一个点击（Click）事件的监听器View.OnClickListener()
第3行代码是点击事件的回调函数
第4行代码将TextView的显示内容更改为“Button按钮”
*
**View.OnClickListener()：**
* View.OnClickListener()是View定义的点击事件的监听器接口，并在接口中仅定义了onClick()函数
* 当Button从Android界面框架中接收到事件后，首先检查这个事件是否是点击事件，如果是点击事件，同时Button又注册了监听器，则会调用该监听器中的onClick()函数
* 每个View仅可以注册一个点击事件的监听器，如果使用setOnClickListener()函数注册第二个点击事件的监听器，之前注册的监听器将被自动注销
* 多个按钮注册到同一个点击事件的监听器上，代码如下：

```
Button.OnClickListener buttonListener = new Button.OnClickListener(){
	@Override
	public void onClick(View v) {
		switch(v.getId()){
			case R.id.Button01:
				textView.setText("Button按钮");
				return;
			case R.id.ImageButton01:
				textView.setText("ImageButton按钮");
				return;
		}	
	   }};
     button.setOnClickListener(buttonListener);
     imageButton.setOnClickListener(buttonListener);

```
*第1行至第12行代码定义了一个名为buttonListener的点击事件监听器
第13行代码将该监听器注册到Button上
第14行代码将该监听器注册到ImageButton上*

 #####CheckBox和RadioButton
  * CheckBox是一个同时可以选择多个选项的控件
  * RadioButton则是仅可以选择一个选项的控件
  * RadioGroup是RadioButton的承载体，程序运行时不可见，应用程序中可能包含一个或多个RadioGroup
  * 一个RadioGroup包含多个RadioButton，在每个RadioGroup中，用户仅能够选择其中一个RadioButton

CheckBox DEMO在XML文件：

```
 <TextView android:id="@+id/TextView01“
		android:layout_width="fill_parent" 
		android:layout_height="wrap_content" 
		android:text="@string/hello"/>
	<CheckBox android:id="@+id/CheckBox01"
		android:layout_width="wrap_content" 
		android:layout_height="wrap_content"
		android:text="CheckBox01" >
	</CheckBox>
	<CheckBox android:id="@+id/CheckBox02" 
		android:layout_width="wrap_content" 
		android:layout_height="wrap_content"
		android:text="CheckBox02" >
	 </CheckBox>

```
RadioBox Demo在XML文件：

```
15<RadioGroup android:id="@+id/RadioGroup01" 
		android:layout_width="wrap_content" 
		android:layout_height="wrap_content">
		<RadioButton android:id="@+id/RadioButton01" 
			android:layout_width="wrap_content" 
			android:layout_height="wrap_content“
			android:text="RadioButton01" >
		</RadioButton>
		<RadioButton android:id="@+id/RadioButton02" 
			android:layout_width="wrap_content" 
			android:layout_height="wrap_content“
			android:text="RadioButton02" >
		</RadioButton>
	</RadioGroup>

```
*第15行<RadioGroup>标签声明了一个RadioGroup*
 *在第18行和第23行分别声明了两个RadioButton，这两个RadioButton是RadioGroup的子元素*

引用CheckBox和RadioButton的方法参考下面的代码：

```
CheckBox checkBox1= (CheckBox)findViewById(R.id.CheckBox01);
RadioButton radioButton1 =(RadioButton)findViewById(R.id.RadioButton01);

```
CheckBox设置点击事件监听器的简要代码：

```
CheckBox.OnClickListener checkboxListener = new CheckBox.OnClickListener(){
	@Override
	public void onClick(View v) {
	  //过程代码
	}};
checkBox1.setOnClickListener(checkboxListener);
checkBox2.setOnClickListener(checkboxListener);

```
*与Button设置点击事件监听器中介绍的方法相似，唯一不同在于将Button.OnClickListener换成了CheckBox.OnClickListener*

RadioButton设置点击事件监听器的方法：

```
RadioButton.OnClickListener radioButtonListener = new RadioButton.OnClickListener(){
	@Override
	public void onClick(View v) {
	  //过程代码
	}}; 	
radioButton1.setOnClickListener(radioButtonListener);
radioButton2.setOnClickListener(radioButtonListener);

```





















