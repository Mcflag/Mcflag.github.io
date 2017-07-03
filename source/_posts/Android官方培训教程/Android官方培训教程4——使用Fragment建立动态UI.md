title: Android官方培训教程4——使用Fragment建立动态UI
date: 2015-08-30 15:26:54
tags: [Android官方培训教程]
categories: [Android官方培训教程]
---

## 摘要
使用Fragment建立动态UI
<!--more-->

## 使用Fragment建立动态UI

* 为了在Android上创建动态的、多窗口的用户交互体验，你需要将UI组件和Activity操作封装成模块进行使用，在activity中你可以对这些模块进行切入切出操作。你可以用Fragment来创建这些模块，Fragment就像一个嵌套的activity，拥有自己的布局（layout）以及管理自己的生命周期。
* 如果一个fragment定义了自己的布局，那么在activity中它可以与其他的fragment生成不同的组合，从而为不同的屏幕尺寸生成不同的布局（一个小的屏幕一次只放一个fragment，大的屏幕则可以两个或以上的fragment）。
* 这一章将向你展示如何用fragment来创建动态的用户体检，以及在不同屏幕尺寸的设备上优化你的APP的用户体验。像运行着android1.6这样老版本的设备，也都将继续得到支持。

## 创建一个Fragment

你可以把fragment想象成activity中一个模块化的部分，它拥有自己的生命周期，接收自己的输入事件，可以在acvitity运行过程中添加或者移除（有点像"子activity"，你可以在不同的activity里面重复使用）。这一课教你继承Support Library 中的Fragment，以使你的应用在Android1.6这样的低版本上仍能保持兼容。

### 创建一个Fragment类

* 创建一个fragment，首先需要继承Fragment类，然后在关键的生命周期方法中插入你APP的逻辑，就像activity一样。
* 其中一个区别是当你创建Fragment的时候，你必须重写onCreateView()回调方法来定义你的布局。事实上，这是使Fragment运行起来，唯一一个需要你重写的回调方法。比如，下面是一个自定义布局的示例fragment.

	import android.os.Bundle;
	import android.support.v4.app.Fragment;
	import android.view.LayoutInflater;
	import android.view.ViewGroup;
	public class ArticleFragment extends Fragment {
		@Override
		public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
			// Inflate the layout for this fragment
			return inflater.inflate(R.layout.article_view, container, false);
		}
	}

就像activity一样，当fragment从activity添加或者移除、当activity生命周期发生变化时，fragment应该是实现生命周期回调来管理它的状态。例如，当activity的onPause()被调用时，它里面的所有fragment的onPause()方法也会被触发。

### 用XML将fragment添加到activity

fragments是可以重用的，模块化的UI组件，每一个Fragment的实例都必须与一个FragmentActivity关联。你可以在activity的XML布局文件中定义每一个fragment来实现这种关联。下面是一个XML布局的例子，当屏幕被认为是large(用目录名称中的 large 字符来区分)时，它在布局中增加了两个fragment。res/layout-large/news_articles.xml

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		android:orientation="horizontal"
		android:layout_width="fill_parent"
		android:layout_height="fill_parent">
		<fragment android:name="com.example.android.fragments.HeadlinesFragment"
			android:id="@+id/headlines_fragment"
			android:layout_weight="1"
			android:layout_width="0dp"
			android:layout_height="match_parent" />
		<fragment android:name="com.example.android.fragments.ArticleFragment"
			android:id="@+id/article_fragment"
			android:layout_weight="2"
			android:layout_width="0dp"
			android:layout_height="match_parent" />
	</LinearLayout>

然后将这个布局文件用到你的activity中。

	import android.os.Bundle;
	import android.support.v4.app.FragmentActivity;
	public class MainActivity extends FragmentActivity {
		@Override
		public void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.news_articles);
		}
	}

>Note：当你用XML布局文件的方式将Fragment添加进activity时，你的Fragment是不能被动态移除的。如果你想要在用户交互的时候把fragment切入与切出，你必须在activity启动后，再将fragment添加进activity。

## 建立灵活动态的UI

FragmentManager类提供了方法，让你在activity运行时能够对fragment进行添加，移除，替换，来实现动态的用户体验。

### 在activity运行时添加fragment

* 比起用 &lt;fragment> 标签在activity的布局文件中定义fragment,你也可以在activity运行时动态添加fragment，如果你在打算在activity的生命周期内替换fragment，这是必须的。
* 为了执行fragment的增加或者移除操作，你必须用 FragmentManager 创建一个FragmentTransaction对象,FragmentTransaction提供了用来增加、移除、替换以及其它一些操作的APIs。
* 如果你的activity允许fragment移除或者替换，你应该在activity的onCreate()方法中添加初始化fragment(s)。
* 运用fragment（特别是那些你在运行时添加的）的一个很重要的规则就是在布局中你必须有一个容器view，fragment的layout将会放在这个view里面。
* 在你的activity里面，用Support Library APIs调用 getSupportFragmentManager()方法获取FragmentManager 对象，然后调用 beginTransaction() 方法创建一个FragmentTransaction对象，然后调用add()方法添加一个fragment。
* 你可以使用同一个 FragmentTransaction进行多次fragment事务。当你完成这些变化操作的时候，必须调用commit()方法。

	import android.os.Bundle;
	import android.support.v4.app.FragmentActivity;
	public class MainActivity extends FragmentActivity {
		@Override
		public void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.news_articles);

			// Check that the activity is using the layout version with
			// the fragment_container FrameLayout
			if (findViewById(R.id.fragment_container) != null) {
				// However, if we're being restored from a previous state,
				// then we don't need to do anything and should return or else
				// we could end up with overlapping fragments.
				if (savedInstanceState != null) {
					return;
				}

				// Create a new Fragment to be placed in the activity layout
				HeadlinesFragment firstFragment = new HeadlinesFragment();

				// In case this activity was started with special instructions from an
				// Intent, pass the Intent's extras to the fragment as arguments
				firstFragment.setArguments(getIntent().getExtras());

				// Add the fragment to the 'fragment_container' FrameLayout
				getSupportFragmentManager().beginTransaction().add(R.id.fragment_container, firstFragment).commit();
			}
		}
	}

因为fragment是在activity运行时被添加进来时（不是在XML布局中用 &lt;fragment> 定义的），activity 可以移除这个fragment或者用另外一个来替换它。

### Fragment替换

* 替换fragment的过程与添加过程类似，只需要将add()方法替换为 replace()方法。
* 记住当你执行fragment事务的时候，例如移除或者替换，你经常要适当地让用户可以向后导航与"撤销"这次改变。为了让用户向后导航fragment事务，你必须在FragmentTransaction提交前调用addToBackStack()方法。
* **当你移除或者替换一个fragment并把它放入返回栈中时，被移除的fragment的生命周期是stopped(不是
destoryed).当用户返回重新恢复这个fragment,它的生命周期是restarts。如果你没把fragment放入返回栈中，那
么当他被移除或者替换时，它的生命周期是destoryed。**

下面是一个fragment替换的例子

	// Create fragment and give it an argument specifying the article it should show
	ArticleFragment newFragment = new ArticleFragment();
	Bundle args = new Bundle();
	args.putInt(ArticleFragment.ARG_POSITION, position);
	newFragment.setArguments(args);

	FragmentTransaction transaction = getSupportFragmentManager().beginTransaction();

	// Replace whatever is in the fragment_container view with this fragment,
	// and add the transaction to the back stack so the user can navigate back
	transaction.replace(R.id.fragment_container, newFragment);
	transaction.addToBackStack(null);

	// Commit the transaction
	transaction.commit();

addToBackStack()方法提供了一个可选的String参数为事务指定了一个唯一的名字。这个名字不是必须的，除非你打算用FragmentManager.BackStackEntry APIs来进行一些高级的fragments操作。

## Fragments之间的交互

* 为了重用Fragment UI组件，你应该把每一个fragment都构建成完全的自包含的、模块化的组件，定义他们自己的布局与行为。当你定义好这些模块化的Fragment，你就可以让他们关联activity，使他们与application的逻辑结合起来，实现全局的复合的UI。
* 通常，你想fragment之间能相互交互，比如基于用户事件改变fragment的内容。所有fragment之间的交互需要通过他们关联的activity，两个fragment之间不应该直接交互。

### 定义一个接口

为了让fragment与activity交互，你可以在Fragment 类中定义一个接口，并且在activity中实现这个接口。Fragment在他们生命周期的onAttach()方法中获取接口的实现，然后调用接口的方法来与Activity交互。

	public class HeadlinesFragment extends ListFragment {
		OnHeadlineSelectedListener mCallback;

		// Container Activity must implement this interface
		public interface OnHeadlineSelectedListener {
			public void onArticleSelected(int position);
		}

		@Override
		public void onAttach(Activity activity) {
			super.onAttach(activity);
			// This makes sure that the container activity has implemented
			// the callback interface. If not, it throws an exception
			try {
				mCallback = (OnHeadlineSelectedListener) activity;
			} catch (ClassCastException e) {
				throw new ClassCastException(activity.toString()+ " must implement OnHeadlineSelectedListener");
			}
		}
		...
	}

现在Fragment就可以通过调用 OnHeadlineSelectedListener 接口实例的 mCallback 中的 onArticleSelected() （也可以是其它方法）方法与activity传递消息。举个例子，在fragment中的下面的方法在用户点击列表条目时被调用，fragment 用回调接口来传递事件给父Activity.

	@Override
	public void onListItemClick(ListView l, View v, int position, long id) {
		// Send the event to the host activity
		mCallback.onArticleSelected(position);
	}

### 实现接口

为了接收回调事件，宿主activity必须实现在Fragment中定义的接口。举个例子，下面的activity实现了上面例子中的接口。

	public static class MainActivity extends Activity implements HeadlinesFragment.OnHeadlineSelectedListener{
		...
		public void onArticleSelected(int position) {
			// The user selected the headline of an article from the HeadlinesFragment
			// Do something here to display that article
		}
	}

### 传消息给Fragment

宿主activity通过findFragmentById()方法来获取fragment的实例，然后直接调用Fragment的public方法来向fragment传递消息。例如，假设上面所示的activity可能包含另外一个fragment,这个fragment用来展示从上面的回调方法中返回的指定的数据。在这种情况下，activity可以把从回调方法中接收到的信息传递给这个展示数据的Fragment。

	public static class MainActivity extends Activity implements HeadlinesFragment.OnHeadlineSelectedListener{
		...
		public void onArticleSelected(int position) {
			// The user selected the headline of an article from the HeadlinesFragment
			// Do something here to display that article
			ArticleFragment articleFrag = (ArticleFragment)
			getSupportFragmentManager().findFragmentById(R.id.article_fragment);
			if (articleFrag != null) {
				// If article frag is available, we're in two-pane layout...
				// Call a method in the ArticleFragment to update its content
				articleFrag.updateArticleView(position);
			} else {
				// Otherwise, we're in the one-pane layout and must swap frags...
				// Create fragment and give it an argument for the selected article
				ArticleFragment newFragment = new ArticleFragment();
				Bundle args = new Bundle();
				args.putInt(ArticleFragment.ARG_POSITION, position);
				newFragment.setArguments(args);
				FragmentTransaction transaction = getSupportFragmentManager().beginTransaction();
				// Replace whatever is in the fragment_container view with this fragment,
				// and add the transaction to the back stack so the user can navigate back
				transaction.replace(R.id.fragment_container, newFragment);
				transaction.addToBackStack(null);
				// Commit the transaction
				transaction.commit();
			}
		}
	}