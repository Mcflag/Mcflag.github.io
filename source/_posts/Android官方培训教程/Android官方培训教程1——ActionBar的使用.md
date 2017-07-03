title: Android官方培训教程1——ActionBar的使用
date: 2015-08-30 15:18:10
tags: [Android官方培训教程]
categories: [Android官方培训教程]
---

## 摘要
ActionBar的使用
<!--more-->

## 添加Action Bar

Action Bar是你可以为activity实现的最重要的设计元素之一。它提供了多种 UI 特性，可以让你的 app 和其他 Android app 保持较多的一致性，为用户所熟悉。核心的功能包括：

* 一个专门的空间用来显示你的app的标识，并且还可以指出目前所处在app的哪个页面。
* 以一种可预见的方式访问重要的操作（比如检索）。
* 支持导航和视图切换（通过Tabs和下拉列表）

Action bar 最基本的形式，就是为 activity 显示标题，并且在标题左边显示一个 app icon。即使在这样简单的形式下，对于所有的 activity 来说，action bar 对告知用户他们当前所处的位置十分有用，并为你的 app 维护了持续的同一标识。

只要app使用了Theme.Holo主题，所有的 activity 都显示 action bar。

## 添加Action按钮

Action bar 允许你为当前上下文中最重要的操作添加按钮。那些直接出现在 action bar 中的 icon 和文本或按钮被称作action buttons(操作按钮)。安排不下的或不足够重要的操作被隐藏在 action overflow 中，action overflow就是三个竖向点的“更多”按钮。

## 在 XML 中指定操作

所有的操作按钮和 action overflow 中其他可用的条目都被定义在 菜单资源 的 XML 文件中。通过在项目的 res/menu 目录中新增一个 XML 文件来为 action bar 添加操作。为你想添加到 action bar 中的每个条目添加一个 &lt;item> 元素。例如：res/menu/main_activity_actions.xml

	<menu xmlns:android="http://schemas.android.com/apk/res/android" >
		<!-- 搜索, 应该作为动作按钮展示-->	
		<item android:id="@+id/action_search"
			android:icon="@drawable/ic_action_search"
			android:title="@string/action_search"
			android:showAsAction="ifRoom" />
		<!-- 设置, 在溢出菜单中展示 -->
		<item android:id="@+id/action_settings"
			android:title="@string/action_settings"
			android:showAsAction="never" />
	</menu>

如果你为了兼容 Android 2.1 的版本使用了 Support 库，在 android 命名空间下 showAsAction 属性是不可用的。

## 为Action Bar添加操作

为 action bar 布局菜单条目，是通过在 activity 中实现 onCreateOptionsMenu() 回调方法来 inflate 菜单资源从而获取Menu 对象。例如：

	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		// 为ActionBar扩展菜单项
		MenuInflater inflater = getMenuInflater();
		inflater.inflate(R.menu.main_activity_actions, menu);
		return super.onCreateOptionsMenu(menu);
	}

## 为操作按钮添加响应事件

当用户按下某一个操作按钮或者action overflow中的其他条目，系统将调用activity中onOptionsItemSelected()回调方法。在该方法的实现里面调用MenuItem的getItemId()来判断哪个条目被按下-返回的ID会匹配你声明对应的&lt;item>元素中 &lt;android:id> 属性的值。

	@Override
	public boolean onOptionsItemSelected(MenuItem item) {
		// 处理动作按钮的点击事件
		switch (item.getItemId()) {
		case R.id.action_search:
			openSearch();
			return true;
		case R.id.action_settings:
			openSettings();
			return true;
		default:
			return super.onOptionsItemSelected(item);
		}
	}

## 为下级 Activity 添加向上按钮

在不是主要入口的其他所有屏中（activity 不位于主屏时），需要在 action bar 中为用户提供一个导航到逻辑父屏的up button(向上按钮)。

当运行在 Android 4.1(API level 16) 或更高版本，或者使用 Support 库中的 ActionBarActivity 时，实现向上导航需要你在 manifest 文件中声明父 activity ，同时在 action bar 中设置向上按钮可用。如何在 manifest 中声明一个 activity 的父辈，例如：

	<!-- 主 main/home 活动 (没有上级活动) -->
	<activity
		android:name="com.example.myfirstapp.MainActivity" ...>
		...
	</activity>
	<!-- 主活动的一个子活动-->
	<activity
		android:name="com.example.myfirstapp.DisplayMessageActivity"
		android:label="@string/title_activity_display_message"
		android:parentActivityName="com.example.myfirstapp.MainActivity" >
		<!-- meta-data 用于支持 support 4.0 以及以下来指明上级活动 -->
		<meta-data
			android:name="android.support.PARENT_ACTIVITY"
			android:value="com.example.myfirstapp.MainActivity" />
	</activity>

然后，通过调用setDisplayHomeAsUpEnabled() 来把 app icon 设置成可用的向上按钮：

	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_displaymessage);
		getSupportActionBar().setDisplayHomeAsUpEnabled(true);
		// 如果你的minSdkVersion属性是11或更高, 应该这么用:
		// getActionBar().setDisplayHomeAsUpEnabled(true);
	}

由于系统已经知道 MainActivity 是 DisplayMessageActivity 的父 activity，当用户按下向上按钮时，系统会导航到恰当的父 activity - 你不需要去处理向上按钮的事件。

## 自定义Action Bar背景

为 activity 创建一个自定义主题，通过重写 actionBarStyle 属性来改变 action bar 的背景。actionBarStyle 属性指向另一个样式；在该样式里，通过指定一个 drawable 资源来重写 background 属性。如果你的 app 使用了 navigation tabs 或 split action bar ，你也可以通过分别设置 backgroundStacked 和 backgroundSplit 属性来为这些条指定背景。

当仅支持 Android 3.0 和更高版本时，你可以像下面这样定义 action bar 的背景：res/values/themes.xml

	<?xml version="1.0" encoding="utf-8"?>
	<resources>
		<!-- 应用于程序或者活动的主题 -->
		<style name="CustomActionBarTheme"
			parent="@android:style/Theme.Holo.Light.DarkActionBar">
			<item name="android:actionBarStyle">@style/MyActionBar</item>
		</style>
		<!-- ActionBar 样式 -->
		<style name="MyActionBar"
			parent="@android:style/Widget.Holo.Light.ActionBar.Solid.Inverse">
			<item name="android:background">@drawable/actionbar_background</item>
		</style>
	</resources>

然后，将你的主题应用到你的 app 全局或单个的 activity 之中：

	<application android:theme="@style/CustomActionBarTheme" />

当支持 Android 2.1 和更高res/values/themes.xml

	<?xml version="1.0" encoding="utf-8"?>
	<resources>
		<!-- 应用于程序或者活动的主题 -->
		<style name="CustomActionBarTheme"
			parent="@style/Theme.AppCompat.Light.DarkActionBar">
			<item name="android:actionBarStyle">@style/MyActionBar</item>
			<!-- 支持库兼容 -->
			<item name="actionBarStyle">@style/MyActionBar</item>
		</style>
		<!-- ActionBar 样式 -->
		<style name="MyActionBar"
			parent="@style/Widget.AppCompat.Light.ActionBar.Solid.Inverse">
			<item name="android:background">@drawable/actionbar_background</item>
			<!-- 支持库兼容 -->
			<item name="background">@drawable/actionbar_background</item>
		</style>
	</resources>

## 自定义Action Bar文本颜色

修改 action bar 中的文本颜色，你需要分别重写每个元素的属性：

* Action bar 的标题：创建一种自定义样式，并指定 textColor 属性；同时，在你的自定义 actionBarStyle 中为titleTextStyle 属性指定为刚才的自定义样式。
> Note：被应用到 titleTextStyle 的自定义样式应该使用 TextAppearance.Holo.Widget.ActionBar.Title 作为父样式。

* Action bar tabs：在你的 activity 主题中重写 actionBarTabTextStyle
* Action 按钮：在你的 activity 主题中重写 actionMenuTextColor

## 自定义 Tab Indicator

为 activity 创建一个自定义主题，通过重写 actionBarTabStyle 属性来改变 navigation tabs 使用的指示器。actionBarTabStyle 属性指向另一个样式资源；在该样式资源里，通过指定一个state-list drawable 来重写background 属性。

例如，这是一个状态列表 drawable，为一个 action bar tab 的多种不同状态分别指定背景图片：res/drawable/actionbar_tab_indicator.xml

	<?xml version="1.0" encoding="utf-8"?>
	<selector xmlns:android="http://schemas.android.com/apk/res/android">
	<!-- 按钮没有按下的状态 -->
		<!-- 没有焦点的状态 -->
		<item android:state_focused="false" android:state_selected="false"
			android:state_pressed="false"
			android:drawable="@drawable/tab_unselected" />
		<item android:state_focused="false" android:state_selected="true"
			android:state_pressed="false"
			android:drawable="@drawable/tab_selected" />

		<!-- 有焦点的状态 (例如D-Pad控制或者鼠标经过) -->
		<item android:state_focused="true" android:state_selected="false"
			android:state_pressed="false"
			android:drawable="@drawable/tab_unselected_focused" />
		<item android:state_focused="true" android:state_selected="true"
			android:state_pressed="false"
			android:drawable="@drawable/tab_selected_focused" />

	<!-- 按钮按下的状态D -->
		<!-- 没有焦点的状态 -->
		<item android:state_focused="false" android:state_selected="false"
			android:state_pressed="true"
			android:drawable="@drawable/tab_unselected_pressed" />
		<item android:state_focused="false" android:state_selected="true"
			android:state_pressed="true"
			android:drawable="@drawable/tab_selected_pressed" />

		<!--有焦点的状态 (例如D-Pad控制或者鼠标经过)-->
		<item android:state_focused="true" android:state_selected="false"
			android:state_pressed="true"
			android:drawable="@drawable/tab_unselected_pressed" />
		<item android:state_focused="true" android:state_selected="true"
			android:state_pressed="true"
			android:drawable="@drawable/tab_selected_pressed" />
	</selector>

当仅支持 Android 3.0 和更高时，你的样式 XML 文件应该是这样的：res/values/themes.xml

	<?xml version="1.0" encoding="utf-8"?>
	<resources>
		<!-- 应用于程序或活动的主题 -->
		<style name="CustomActionBarTheme"
			parent="@style/Theme.Holo">
			<item name="android:actionBarTabStyle">@style/MyActionBarTabs</item>
		</style>
		<!-- ActionBar tabs 标签样式 -->
		<style name="MyActionBarTabs"
			parent="@style/Widget.Holo.ActionBar.TabView">
			<!-- 标签指示器 -->
			<item name="android:background">@drawable/actionbar_tab_indicator</item>
		</style>
	</resources>

当使用 Support 库支持 Android 2.1 和更高时，你的样式 XML 文件应该是这样的：res/values/themes.xml

	<?xml version="1.0" encoding="utf-8"?>
	<resources>
		<!-- 应用于程序或活动的主题 -->
		<style name="CustomActionBarTheme"
			parent="@style/Theme.AppCompat">
			<item name="android:actionBarTabStyle">@style/MyActionBarTabs</item>
			<!-- 支持库兼容 -->
			<item name="actionBarTabStyle">@style/MyActionBarTabs</item>
		</style>
		<!-- ActionBar tabs 样式 -->
		<style name="MyActionBarTabs"
			parent="@style/Widget.AppCompat.ActionBar.TabView">
			<!-- 标签指示器 -->
			<item name="android:background">@drawable/actionbar_tab_indicator</item>
			<!-- 支持库兼容 -->
			<item name="background">@drawable/actionbar_tab_indicator</item>
		</style>
	</resources>

## ActionBar的覆盖叠加

默认情况下，action bar 显示在 activity 窗口的顶部，会稍微地减少其他布局的有效空间。如果在用户交互过程中你要隐藏和显示 action bar，可以通过调用 ActionBar 中的 hide()和show()来实现。但是，这将会导致 activity 基于新尺寸重新计算与绘制布局。

为了避免在 action bar 隐藏和显示过程中调整布局的大小，可以为 action bar 启用叠加模式(overlay mode)。在叠加模式下，所有可用的空间都会被用来布局就像ActionBar不存在一样，并且 action bar 会叠加在你的布局之上。这样布局顶部就会有点被遮挡，但当 action bar 隐藏或显示时，系统不再需要调整布局而是无缝过渡。

要为 action bar 启用叠加模式，需要自定义一个主题，该主题继承于已经存在的 action bar 主题，并设置android:windowActionBarOverlay 属性的值为 true 。

如果 minSdkVersion 为 11 或更高，自定义主题必须继承 Theme.Holo 主题（或者它的子主题）。例如：

	<resources>
		<!-- 为程序或者活动应用的主题样式 -->
		<style name="CustomActionBarTheme"
			parent="@android:style/Theme.Holo">
			<item name="android:windowActionBarOverlay">true</item>
		</style>
	</resources>

如果为了兼容运行在 Android 3.0 以下版本的设备而使用了 Support 库，自定义主题必须继承 Theme.AppCompat 主题（或者它的子主题）。例如：

	<resources>
		<!-- 为程序或者活动应用的主题样式 -->
		<style name="CustomActionBarTheme"
			parent="@android:style/Theme.AppCompat">
			<item name="android:windowActionBarOverlay">true</item>
			<!-- 兼容支持库 -->
			<item name="windowActionBarOverlay">true</item>
		</style>
	</resources>

请注意，这主题包含两种不同的 windowActionBarOverlay 式样定义：一个带 android: 前缀，另一个不带。带前缀的适用于包含该式样的 Android 系统版本，不带前缀的适用于通过从 Support 库中读取式样的旧版本。

指定布局的顶部边距，当 action bar 启用叠加模式时，它可能会遮挡住本应保持可见状态的布局。为了确保这些布局始终位于 action bar 下部，可以使用 actionBarSize 属性来指定顶部margin或padding的高度来到达。例如：

	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
		android:layout_width="match_parent"
		android:layout_height="match_parent"
		android:paddingTop="?android:attr/actionBarSize">
		...
	</RelativeLayout>

如果在 action bar 中使用 Support 库，需要移除 android: 前缀。例如：

	<!-- 兼容支持库 -->
	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
		android:layout_width="match_parent"
		android:layout_height="match_parent"
		android:paddingTop="?attr/actionBarSize">
		...
	</RelativeLayout>

在这种情况下，不带前缀的 ?attr/actionBarSize 适用于包括Android 3.0 和更高的所有版本。