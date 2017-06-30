title: Android编程权威指南笔记1
date: 2016-04-11 15:57:43
tags: [Android基础]
---

## 摘要
Android编程权威指南笔记。

<!--more-->

## 设备旋转前保存数据

设备旋转后原本的Activity会被销毁然后重建，但是这时原本的数据就会丢失。对于某些情况来说，旋转后的Activity需要加载旋转前的数据，这时就需要使用onSaveInstanceState()方法，该方法在onPause()、onStop()、onDestroy()之前调用。

方法onSaveInstanceState()默认的实现要求所有activity的视图将自身状态数据保存在Bundle对象中。

可以通过重写onSaveInstanceState()方法，将一些数据保存在Bundle中，在onCreate()方法中取出这些数据。

	@Override
	public void onSaveInstanceState(Bundle savedInstanceState){
		super.onSaveInstanceState(savedInstanceState);
		savedInstanceState.putInt("key","value");
	}

在onCreate()方法中加入检查存储的bundle信息

	String s="";
	if(savedInstanceState!=null){
		s=savedInstanceState.getString("key","");
	}

注意：在Bundle中存储和恢复的数据类型只能是基本数据类型，以及可以实现Serializable接口的对象，创建自己的定制类时，如需在onSaveInstanceState()方法中保存类对象，要记得实现Serializable接口。

## 从子activity中获取返回结果

如需要从子activity中获取返回信息时，可调用以下activity方法：startActivityForResult(Intent intent,int requestCode)。

子activity设置返回结果有以下两种方法可供调用：

	public final void setResult(int resultCode);
	public final void setResult(int resultCode,Intent data);

通常来说，参数result code可以是以下两个预定义常量中的任何一个：

	Activity.RESULT_OK;
	Activity.RESULT_CANCELED;

如果子activity不调用setResult()方法，用户后退的话父activity则会收到Activity.RESULT_CANCELED的结果代码。

处理返回结果，覆盖onActivityResult()方法获取子activity返回的数据。

	@Override
	protected void onActivityResult(int requestCode,int resultCode,Intent data){
		if(data==null){
			return;
		}
		mFlag=data.getBooleanExtra("DATABACK",false);
	}

## Android编译版本

在使用高版本的api时候需要提前检查Android设备版本。可以使用如下条件语句：

	if(Build.VERSION.SDK_INT>=Build.VERSION_CODES.HONEYCOMB)

让AndroidLint不再提示兼容性问题，使高版本的api在低版本的SKD不报错。比如：

	@TargetApi(11)

## UUID的使用

UUID含义是通用唯一识别码，创建模型层的时候添加一个UUID。

	private UUID mID；
	mID=UUID.randomUUID();

## 托管的两种方式

在activity中托管一个UI fragment，有如下两种方式：添加fragment到activity布局中，在activity代码中添加fragment。

第一种方式即使用布局fragment，这种方式灵活性不够，添加fragment到activity布局中，就等同于将fragment及其视图与activity与视图绑定在一起，且在activity的生命周期过程中，无法切换fragment视图。

第二种方式可以灵活的添加和移除fragment。实际使用方式，定义容器视图。在布局容器中添加FrameLayout，添加到activity的布局main.xml中。

	<FrameLayout android:id="@+id/fragmentContainer"
		android:layout_width="match_parent"
		android:layout_height="match_parent"
	/>

创建UI fragment，创建一个UI fragment的步骤与创建一个activity的步骤相同，具体步骤如下所示：通过定义布局文件中的组件，组装界面；创建fragment类并设置其视图为定义的布局；通过代码的方式，连接布局文件中生成的组件。

fragment的视图布局文件使用fragment_view.xml。这个文件与activity的布局文件一样。在Fragment里面使用即可。

	public class OtherFragment extends Fragment{
		@Override
		public void onCreate(Bundle savedInstanceState){
			super.onCreate(savedInstanceState);
		}

		@Override
		public View onCreateView(LayoutInflater inflater,ViewGroup parent,Bundle savedInstanceState){
			View v=inflater.inflate(R.layout.fragment_view, parent, false);
			return v;
		}
	}

添加UI Fragment到FragmentManager，也就是说将Fragment添加到Activity。FragmentManager类负责管理fragment并将它们的视图添加到activity的视图层级结构中。FragmentManager类具体管理的是：fragment队列，fragment事务的回退栈。

	public class MainActivity extends FragmentActivity{
		@Override
		public void onCreate(Bundle savedInstanceState){
			super.onCreate(savedInstanceState);
			setContentView(R.layout.main);
			FragmentManager fm=getSupportFragmentManager();
			Fragment fragment=fm.findFragmentById(R.id.fragmentContainer);

			if(fragment==null){
				fragment=new OtherFragment();
				fm.beginTransaction().add(R.id.fragmentContainer, fragment).commit(); 
			}
		}
	}

## Fragment的详细解释

fragment事务被用来添加、移除、附加、分离或替换fragment队列中的fragment。FragmentManager管理着fragment事务的回退栈。FragmentManager.beginTransaction()方法创建并返回FragmentTransaction实例。FragmentTransaction类使用了一个fluent interface接口方法，由此可得到一个FragmentTransaction队列。

这里做的事情可以理解为创建一个新的fragment事务，加入一个添加操作，然后提交该事务。add()方法有两个参数，容器视图资源ID和新创建的Fragment。容器视图资源ID有两点作用：告知FragmentManager，fragment视图应该出现在activity视图的什么地方；是FragmentManager队列中fragment的唯一标识符。