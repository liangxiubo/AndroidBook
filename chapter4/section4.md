# 4.4Fragment详解
---
## **Fragment概述及其设计初衷**
### Fragment概述
* Fragment表现Activity中UI的一个行为或者一部分。可以把Fragment想象成Activity的一个模块化区域，有它自己的生命周期，接收属于它自己的输入事件，并且可以在Activity运行期间添加和删除（有点像一个可以在不同的Activity中重用的“子Activity”）。
* Fragment总是必须被嵌入到一个Activity中。它们的生命周期直接受其宿主Activity的生命周期影响。当一个Activity正在运行时(处于resumed状态)，我们可以独立地操作每一个Fragment，比如添加或删除它们。

### Fragment设计初衷
* 在大屏幕设备上——例如平板电脑上，支持更加动态和灵活的UI设计。
* 平板电脑的屏幕要比手机的大得多，有更多的空间来放更多的UI组件，并且这些组件之间会产生更多的交互。
* Fragment提供了这样的一种设计：不需要你亲自来管理view hierarchy的复杂变化。通过将Activity的布局分散到Fragment中，可以在运行时修改Activity的外观，并在Activity管理的back stack中保存那些变化。
* Fragment可以定义自己的布局、生命周期回调方法，因此可以将Fragment重用到多个Activity中。这允许你根据不同的屏幕尺寸或者使用场合改变Fragment组合。

## **创建fragment**
* 要创建一个fragment，必须创建一个Fragment的子类（或者是一个已存在的它的子类）。通常来说，创建Fragment需要实现如下三个方法：
	* onCreate()：当创建fragment时，系统调用此方法。在实现代码中，应当初始化想要在fragment中保持的必要组件，当fragment被暂停或者停止后可以恢复。
	* onCreateView()：fragment第一次绘制它的用户界面的时候，系统会调用此方法。为了绘制fragment的UI，此方法必须返回一个作为fragment布局的根的view。如果fragment不提供UI，可以返回null。
	* onPause()：用户将要离开fragment时，系统调用这个方法作为第一个指示（虽然它并非总是意味着fragment将被销毁）。在当前用户会话结束之前，通常应当在这里提交任何应该持久化的变化（因为用户有可能不会返回）。 

```java
public static class ExampleFragment extends Fragment {
  @Override
  public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    // Inflate the layout for this fragment
    return inflater.inflate(R.layout.example_fragment, container, false);
  }
}
```
* 传入 onCreateView() 的 container 参数是你的fragment layout将要插入的父ViewGroup（来自activity的layout）。savedInstanceState 参数是一个Bundle，如果fragment是被恢复的，它提供关于fragment的之前的实例的数据（）。
* 注意：inflate() 方法的第三个参数指示在加载期间，展开的layout是否应当附着到ViewGroup （第二个参数）。传入true会在最后的layout中创建一个多余的view group。

## **Fragment与Activity通信**
### 将Fragment添加到Activity中
* 为了在Activity中显示Fragment，还必须将Fragment添加到Activity中。将Fragment添加到Activity中有如下两种方式：
	1. 在activity的layout文件中声明fragment
	```xml
	<?xml version="1.0" encoding="utf-8"?> 
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		android:orientation="horizontal"
		android:layout_width="match_parent"
		android:layout_height="match_parent">
		<fragment android:name="com.example.news.ArticleListFragment"
			android:id="@+id/list"
			android:layout_weight="1"
			android:layout_width="0dp"
			android:layout_height="match_parent" />
			<fragment android:name="com.example.news.ArticleReaderFragment" 
				android:id="@+id/viewer"
				android:layout_weight="2"
				android:layout_width="0dp"
				android:layout_height="match_parent" />
	</LinearLayout>
	```
		* 在<fragment>中的android:name属性指定了在layout中实例化的Fragment类。当系统创建这个activity layout时，它实例化每一个在layout中指定的fragment，并调用它们的onCreateView()方法，来获取每一个fragment的layout，系统将从fragment返回的 View 直接插入到<fragment>元素所在的地方。
		* 注意：每一个fragment都需要一个唯一的标识，如果activity重启，系统可以用来恢复fragment（并且你也可以用来捕获fragment来处理事务，例如移除它。）
		* 有3种方法来为一个fragment提供一个ID：
			* 为 android:id 属性提供一个唯一ID。
			* 为 android:tag 属性提供一个唯一字符串。
			* 如果以上2个你都没有提供，系统将使用容器view的ID。

	2. 撰写代码将fragment添加到一个已存在的ViewGroup
	```java
	FragmentManager fragmentManager = getFragmentManager(); 
	FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction(); 
	ExampleFragment fragment = new ExampleFragment(); 
	fragmentTransaction.add(R.id.fragment_container, fragment); 
	fragmentTransaction.commit(); 
	```
		* add()的第一个参数是fragment要放入的ViewGroup，由resource ID指定，第二个参数是需要添加的fragment。一旦用FragmentTransaction作了改变，需要调用commit()使改变生效。
		* fragment也可以用来为activity提供后台行为而不用展现额外的UI。需要使用 add(Fragment, String) 来添加 fragment （第二个参数为fragment提供一个唯一的字符串"tag"）。这么做虽然添加了fragment，但因为它没有关联到一个activity layout中的某个view，所以不会接收到onCreateView()调用。因此不必实现此方法。

### 与Activity通信
* fragment可以使用getActivity()获得Activity实例
* activity可以通过从FragmentManager获得一个到Fragment的引用来调用fragment中的方法，使用findFragmentById()或findFragmentByTag() 。
```java
ExampleFragment fragment = (ExampleFragment) getFragmentManager().findFragmentById(R.id.example_fragment); 
```


### 与activity分享事件
* 在一些情况下，你可能需要fragment与activity分享事件。一个好的方法是在fragment中定义一个回调的interface，并要求宿主activity实现它。当fragment接收到事件后可以通过interface调用宿主activity的实现， activity可以和在layout中的其他fragment分享信息。

## **Fragment管理与Fragment事务**
### Fragment的管理
* 要在activity中管理fragment，需要使用FragmentManager。可以通过调用activity的getFragmentManager()取得它的实例。可以通过FragmentManager做的事情包括：
	* 使用findFragmentById() （适用于在activity layout中提供UI的fragment）或findFragmentByTag() （适用于有或没有UI的fragment）获取activity中存在的fragment。
 	* 使用 popBackStack()，将fragment从后台堆栈中弹出(模拟用户的BACK 命令)。
	* 使用addOnBackStackChangeListener()注册一个监听后台堆栈变化的listener。

### 执行Fragment事务
* 根据用户的交互情况，对fragment进行添加、移除、替换、以及执行其他动作。提交给activity的每一套变化被称为一个事务，使用FragmentTransaction API执行。
```java
Fragment newFragment = new ExampleFragment();
FragmentTransaction transaction = getFragmentManager().beginTransaction();
transaction.replace(R.id.fragment_container, newFragment);
transaction.addToBackStack(null);
transaction.commit();
```
* 注意：调用addToBackStack()，可以将事务添加到一个fragment事务的back stack，这个back stack由activity管理，并允许用户通过按下 BACK 按键返回。
* 可以在一个给定的事务中设置你想执行的所有变化，使用诸如 add()、remove()、以及replace()。然后要提交事务到activity，必须调用 commit()。
* 添加变化到 FragmentTransaction的顺序并不重要，除以下例外：
	* 必须最后调用 commit()。
	* 如果添加多个fragment到同一个容器，那么添加的顺序决定了它们在view hierarchy中显示的顺序。
* 注意: 只能在activity保存它的状态（当用户离开activity）之前使用commit()提交事务。

## **提供菜单项**
* fragment可以通过实现onCreateOptionMenu()提供菜单项给activity的Options Men（以此类推，Action Bar也一样）。为了使这个方法接收调用，你必须在onCreate()期间调用setHasOptionsMenu()来指出fragment愿意添加item到选项菜单（否则，fragment将接收不到对onCreateOptionsMenu()的调用）。当一个菜单项被选择，fragment也会接收到对onOptionsItemSelected()的回调。
* 也可以通过调用registerForContextMenu()注册一个在你的fragment layout中的view来提供一个Context Menu。当用户打开Context Menu，fragment会接收到一个对onCreateContextMenu()的调用。当用户选择一个菜单项，fragment会接收到一个对onContextItemSelected()的调用。
* 当然也可以为每个菜单项提供MenuItem.OnMenuItemClickListener来处理点击事件。
* 注意：用户选择一个菜单项时，OnMenuItemClickListener会首先接收到回调，如果没有消耗事件，才会传递到activity，如果activity没有消耗事件，然后事件才会被传递到fragment的回调。