title: Android官方培训教程2——兼容不同的设备
date: 2015-08-30 15:24:22
tags: [Android官方培训教程]
categories: [Android官方培训教程]
---

## 摘要
兼容不同的设备
<!--more-->

## 适配不同的语言

把UI中的字符串存储在外部文件，通过代码提取，这是一种很好的做法。Android可以通过工程中的资源目录轻松实现这一功能。

如果你使用Android SDK Tools来创建工程，则在工程的根目录会创建一个 res/ 的目录，目录中包含所有资源类型的子目录。其中包含工程的默认文件比如 res/values/strings.xml ，用来保存你的字符串值。

为了支持多国语言，在 res/ 中创建一个额外的 values 目录以连字符和ISO国家代码结尾命名，比如 values-es/ 是为语言代码为"es"的区域设置的简单的资源文件的目录。Android会在运行时根据设备的区域设置，加载相应的资源。详见Providing Alternative Resources。若你决定支持某种语言，则需要创建资源子目录和字符串资源文件，例如:

	MyProject/
		res/
			values/
				strings.xml
			values-es/
				strings.xml
			values-fr/
				strings.xml

添加不同区域语言的字符串值到相应的文件。在运行时，Android系统会根据用户设备当前的区域设置，使用相应的字符串资源。例如下面列举了几个不同语言对应不同的字符串资源文件。英语(默认区域语言)，/values/strings.xml :

	<?xml version="1.0" encoding="utf-8"?>
	<resources>
		<string name="title">My Application</string>
		<string name="hello_world">Hello World!</string>
	</resources>

西班牙语， /values-es/strings.xml :

	<?xml version="1.0" encoding="utf-8"?>
	<resources>
		<string name="title">Mi Aplicación</string>
		<string name="hello_world">Hola Mundo!</string>
	</resources>

法语， /values-fr/strings.xml :

	<?xml version="1.0" encoding="utf-8"?>
	<resources>
		<string name="title">Mon Application</string>
		<string name="hello_world">Bonjour le monde !</string>
	</resources>

## 适配不同的屏幕

Android将设备屏幕归类为两种常规属性：尺寸和分辨率。你应该想到你的app会被安装在各种屏幕尺寸和分辨率的设备中。这样，你的app就应该包含一些可选资源，针对不同的屏幕尺寸和分辨率，来优化你的app外观。

* 有4种普遍尺寸：小(small)，普通(normal)，大(large)，超大(xlarge)
* 4种普遍分辨率：低精度(ldpi), 中精度(mdpi), 高精度(hdpi), 超高精度(xhdpi)

声明针对不同屏幕所用的layout和bitmap，你必须把这些可选资源放置在独立的目录中，与你适配不同语言时的做法类似。同样要注意屏幕的方向(横向或纵向)也是一种需要考虑的屏幕尺寸变化，所以许多app会修改layout，来针对不同的屏幕方向优化用户体验。

### 创建不同的layout

为了针对不同的屏幕去优化用户体验，你需要对每一种将要支持的屏幕尺寸，创建唯一的XML文件。每一种layout需要保存在相应的资源目录中，目录以 -&lt;screen_size> 为后缀命名。例如，对大尺寸屏幕(large screens)，一个唯一的layout文件应该保存在 res/layout-large/ 中。

>Note:为了匹配合适的屏幕尺寸Android会自动地测量你的layout文件。所以你不需要因不同的屏幕尺寸去担心UI元素的大小，而应该专注于layout结构对用户体验的影响。(比如关键视图相对于同级视图的尺寸或位置)

例如，这个工程包含一个默认layout和一个适配大屏幕的layout：

	MyProject/
		res/
			layout/
				main.xml
			layout-large/
				main.xml

layout文件的名字必须完全一样，为了对相应的屏幕尺寸提供最优的UI，文件的内容不同。系统会根据你的app所运行的设备屏幕尺寸，在与之对应的layout目录中加载layout。另一个例子，这一个工程中有为适配横向屏幕的layout:

	MyProject/
		res/
			layout/
				main.xml
			layout-land/
				main.xml

默认的， layout/main.xml 文件用作竖屏的layout。如果你想给横屏提供一个特殊的layout，也适配于大屏幕，那么你需要使用 large 和 land 修饰符。

	MyProject/
		res/
			layout/ # default (portrait)
				main.xml
			layout-land/ # landscape
				main.xml
			layout-large/ # large (portrait)
				main.xml
			layout-large-land/ # large landscape
				main.xml

### 创建不同的bitmap

你应该为4种普遍分辨率:低，中，高，超高精度，都提供相适配的bitmap资源。这能帮助你在所有屏幕分辨率中都能有良好的画质和效果。要生成这些图像，你应该从原始的矢量图像资源着手，然后根据下列尺寸比例，生成各种密度下的图像。

* xhdpi: 2.0
* hdpi: 1.5
* mdpi: 1.0 (基准)
* ldpi: 0.75

这意味着，如果你针对xhdpi的设备生成了一张200x200的图像，同样的你应该为hdpi生成150x150,为mdpi生成100x100, 和为ldpi生成75x75的图片资源。然后，将这些文件放入相应的drawable资源目录中:

	MyProject/
		res/
			drawable-xhdpi/
				awesomeimage.png
			drawable-hdpi/
				awesomeimage.png
			drawable-mdpi/
				awesomeimage.png
			drawable-ldpi/
				awesomeimage.png

## 适配不同的系统版本

指定最小和目标API级别,AndroidManifest.xml文件中描述了你的app的细节，并且标明app支持哪些Android版本。具体来说， &lt;uses-sdk> 元素中的 minSdkVersion 和 targetSdkVersion 属性，标明在设计和测试app时，最低兼容API的级别和最高适用的API级别(这个最高的级别是需要通过你的测试的)。例如：

	<uses-sdk android:minSdkVersion="4" android:targetSdkVersion="15" />

Android在Build常量类中提供了对每一个版本的唯一代号，在你的app中使用这些代号可以建立条件，保证依赖于高级别的API的代码，只会在这些API在当前系统中可用时，才会执行。

	private void setUpActionBar() {
		// Make sure we're running on Honeycomb or higher to use ActionBar APIs
		if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
			ActionBar actionBar = getActionBar();
			actionBar.setDisplayHomeAsUpEnabled(true);
		}
	}

使用平台风格和主题。Android提供了用户体验主题，为app提供基础操作系统的外观和体验。这些主题可以在manifest文件中被应用于你的app中.通过使用内置的风格和主题，你的app自然地随着Android新版本的发布，自动适配最新的外观和体验。

使你的activity看起来像对话框:

	<activity android:theme="@android:style/Theme.Dialog">

使你的activity有一个透明背景:

	<activity android:theme="@android:style/Theme.Translucent">

应用在 /res/values/styles.xml 中定义的自定义主题:

	<activity android:theme="@style/CustomTheme">

使整个app应用一个主题(全部activities)在元素中添加 android:theme 属性:

	<application android:theme="@style/CustomTheme">