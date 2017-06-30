title: Android官方培训教程6——与其他应用的交互
date: 2015-08-30 15:29:28
tags: [Android官方培训教程]
---

## 摘要
与其他应用的交互
<!--more-->

## 与其它应用交互

* 一个Android app通常都会有好几个activities。 每一个activity的界面都扮演者用户接口的角色，允许用户执行一些特殊任务（例如查看地图或者是开始拍照等）。为了让用户从一个activity跳到另外一个activity，你的app必须使用Intent来定义你的app的意图。当你使用startActivity()的方法，且参数是intent时，系统会使用这个 Intent 来定义并启动合适的app组件。使用intents甚至还可以让你的app启动另一个app里面的activity。

* 一个 Intent 可以显式的指明需要启动的模块（用一个指定的Activity实例），也可以隐式的指明自己可以处理哪种类型的动作（比如拍一张照等）。

* 这一章节会演示如何使用Intent 来做一些与其他app之间的简单交互。比如启动另外一个app，从其他app接受数据，以及使得你的app能够响应从其他发出的intent等。

## Intent的发送

Android中最重要的特征之一就是可以利用一个带有 action 的 intent 使得当前app能够跳转到其他的app。

你必须使用intent来在同一个app的两个activity之间进行切换。在那种情况下通常是定义一个显式（explicit）的intent，它指定了需要启动组件的类名。然而，当你想要叫起不同的app来执行某个动作（比如查看地图），则必须使用隐式（implicit）的intent。这节课会介绍如何为特殊的动作创建一个implicit intent，并使用它来启动另外一个app去执行intent中的action。

### 建立隐式的Intent

Implicit intents并不会声明需要启动的组件的类名，而是声明一个需要执行的动作。这个action指定了你想做的事情，例如查看，编辑，发送或者是获取什么。Intents通常会在发送action的同时附带一些数据，例如你想要查看的地址或者是你想要发送的邮件信息。数据的类型取决于你想要创建的Intent，这些数据可能是Uri，或者是其他规定的数据类型，或者根本不需要数据也是有可能的。

如果你的数据是一个Uri，会有一个简单的Intent() constructor 用来定义action与data。例如，下面是一个带有指定电话号码的intent。

	Uri number = Uri.parse("tel:5551234");
	Intent callIntent = new Intent(Intent.ACTION_DIAL, number);

当你的app通过执行startActivity()来启动这个intent时，Phone app会使用之前的电话号码来拨出这个电话。下面是一些其他intent的例子：

查看地图:

	// Map point based on address
	Uri location = Uri.parse("geo:0,0?q=1600+Amphitheatre+Parkway,+Mountain+View,+California");

	// Or map point based on latitude/longitude
	// Uri location = Uri.parse("geo:37.422219,-122.08364?z=14"); // z param is zoom level
	Intent mapIntent = new Intent(Intent.ACTION_VIEW, location);

查看网页:

	Uri webpage = Uri.parse("http://www.android.com");
	Intent webIntent = new Intent(Intent.ACTION_VIEW, webpage);

另外一些需要 extra 数据的implicit intent。你可以使用 putExtra() 方法来添加那些数据。 默认的，系统会根据Uri数据类型来决定需要哪些合适的 MIME type 。如果你没有在intent中包含一个Uri, 则通常需要使用 setType() 方法来指定intent附带的数据类型。设置MIME type 是为了指定哪些activity应该接受这个intent。例如：

发送一个带附件的email:

	Intent emailIntent = new Intent(Intent.ACTION_SEND);
	// The intent does not have a URI, so declare the "text/plain" MIME type
	emailIntent.setType(HTTP.PLAIN_TEXT_TYPE);
	emailIntent.putExtra(Intent.EXTRA_EMAIL, new String[] {"jon@example.com"}); // recipients
	emailIntent.putExtra(Intent.EXTRA_SUBJECT, "Email subject");
	emailIntent.putExtra(Intent.EXTRA_TEXT, "Email message text");
	emailIntent.putExtra(Intent.EXTRA_STREAM, Uri.parse("content://path/to/email/attachment"));
	// You can also attach multiple items by passing an ArrayList of Uris

创建一个日历事件:

	Intent calendarIntent = new Intent(Intent.ACTION_INSERT, Events.CONTENT_URI);
	Calendar beginTime = Calendar.getInstance().set(2012, 0, 19, 7, 30);
	Calendar endTime = Calendar.getInstance().set(2012, 0, 19, 10, 30);
	calendarIntent.putExtra(CalendarContract.EXTRA_EVENT_BEGIN_TIME, beginTime.getTimeInMillis());
	calendarIntent.putExtra(CalendarContract.EXTRA_EVENT_END_TIME, endTime.getTimeInMillis());
	calendarIntent.putExtra(Events.TITLE, "Ninja class");
	calendarIntent.putExtra(Events.EVENT_LOCATION, "Secret dojo");

> Note: 这个intent for Calendar的例子只使用于>=API Level 14。

> Note: 请尽可能的定义你的intent更加确切。例如，如果你想要使用ACTION_VIEW 的intent来显示一张图片，你
还应该指定 MIME type 为 image/* .这样能够阻止其他能够 "查看" 其他数据类型的app（比如一个地图app) 被这个
intent叫起。

### 验证是否有App去接收这个Intent

尽管Android系统会确保每一个确定的intent会被系统内置的app(such as the Phone, Email, or Calendar app)之一接收，但是你还是应该在触发一个intent之前做验证是否有App接受这个intent的步骤。**Caution: 如果你触发了一个intent，而且没有任何一个app会去接收这个intent，那么你的app会crash。**

为了验证是否有合适的activity会响应这个intent，需要执行queryIntentActivities() 来获取到能够接收这个intent的所有activity的list。如果返回的List非空，那么你才可以安全的使用这个intent。例如：

	PackageManager packageManager = getPackageManager();
	List<ResolveInfo> activities = packageManager.queryIntentActivities(intent, 0);
	boolean isIntentSafe = activities.size() > 0;

如果 isIntentSafe 是 true , 那么至少有一个app可以响应这个intent。如果是 false 则说明没有app可以handle这个intent。

### 使用Intent来启动Activity

当你创建好了intent并且设置好了extra数据，通过执行startActivity() 来发送到系统。如果系统确定有多个activity可以handle这个intent,它会显示出一个dialog，让用户选择启动哪个app。如果系统发现只有一个app可以handle这个intent，那么就会直接启动这个app。

	// Build the intent
	Uri location = Uri.parse("geo:0,0?q=1600+Amphitheatre+Parkway,+Mountain+View,+California");
	Intent mapIntent = new Intent(Intent.ACTION_VIEW, location);

	// Verify it resolves
	PackageManager packageManager = getPackageManager();
	List<ResolveInfo> activities = packageManager.queryIntentActivities(mapIntent, 0);
	boolean isIntentSafe = activities.size() > 0;

	// Start an activity if it's safe
	if (isIntentSafe) {
		startActivity(mapIntent);
	}

### 显示分享App的选择界面

请注意，当你以startActivity()的形式传递一个intent，并且有多个app可以handle时，用户可以在弹出dialog的时候，选择默认启动的app（通过勾选dialog下面的选择框，如上图所示）。这个功能对于用户有特殊偏好的时候非常有用（例如用户总是喜欢启动某个app来查看网页，总是喜欢启动某个camera来拍照）。

然而，如果用户希望每次都弹出选择界面，而且每次都不确定会选择哪个app启动，例如分享功能，用户选择分享到哪个app都是不确定的，这个时候，需要强制弹出选择的对话框。（这种情况下用户不能选择默认启动的app）。

为了显示chooser, 需要使用createChooser()来创建Intent, 列出可以响应 createChooser() 中Intent的app，并且指定了标题。

	Intent intent = new Intent(Intent.ACTION_SEND);
	...
	// Always use string resources for UI text. This says something like "Share this photo with"
	String title = getResources().getText(R.string.chooser_title);

	// Create and start the chooser
	Intent chooser = Intent.createChooser(intent, title);
	startActivity(chooser);

## 接收Activity返回的结果

启动另外一个activity并不一定是单向的。你也可以启动另外一个activity然后接受一个result回来。为了接受这个result，你需要使用startActivityForResult() ，而不是startActivity()。

例如，你的app可以启动一个camera程序并接受拍的照片作为result。或者你可以启动People程序并获取其中联系的人的详情作为result。

当然，被启动的activity需要指定返回的result。它需要把这个result作为另外一个intent对象返回，你的activity需要在onActivityResult()的回调方法里面去接收result。

> Note:在执行 startActivityForResult() 时，你可以使用explicit 或者 implicit 的intent。当你启动另外一个位于你的程序中的activity时，你应该使用explicit intent来确保你可以接收到期待的结果。

## 启动Activity

对于startActivityForResult() 方法中的intent与之前介绍的并没有什么差异，只不过是需要在这个方法里面多添加一个int类型的参数。

这个integer的参数叫做"request code"，用于标识你的请求。当你接收到result Intent时，可以从回调方法里面的参数去判断这个result是否是你想要的。例如，下面是一个启动activity来选择联系人的例子：

	static final int PICK_CONTACT_REQUEST = 1; // The request code
	...
	private void pickContact() {
		Intent pickContactIntent = new Intent(Intent.ACTION_PICK, new Uri("content://contacts"));
		pickContactIntent.setType(Phone.CONTENT_TYPE); // Show user only contacts w/ phone numbers
		startActivityForResult(pickContactIntent, PICK_CONTACT_REQUEST);
	}

## 接收Result

当用户完成了启动之后activity操作之后，系统会调用你的activity的onActivityResult() 回调方法。这个方法有三个参数：

* 你通过startActivityForResult()传递的request code。
* 第二个activity指定的result code。如果操作成功则是 RESULT_OK ，如果用户没有操作成功，而是直接点击回退或者其他什么原因，那么则是 RESULT_CANCELED
* 第三个参数则是intent,它包含了返回的result数据部分。

例如，下面是如何处理pick a contact的result的例子：对应上面的例子

	@Override
	protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		// Check which request we're responding to
		if (requestCode == PICK_CONTACT_REQUEST) {
			// Make sure the request was successful
			if (resultCode == RESULT_OK) {
				// The user picked a contact.
				// The Intent's data Uri identifies which contact was selected.
				// Do something with the contact here (bigger example below)
			}
		}
	}

在这个例子中被返回的Intent中使用Uri的形式来表示返回的联系人。为了正确的handle这些result，你必须了解那些result intent的格式。对于你自己程序里面的返回result是比较简单的。Apps都会有一些自己的api来指定特定的数据。例如，People app (Contacts app on some older versions) 总是返回一个URI来指定选择的contact，Camera app 则是在 data 数据区返回一个 Bitmap

### 读取联系人数据

上面的代码展示了如何获取联系人的返回结果，但没有说清楚如何从结果中读取数据，但如果你想知道的话，下面是一段代码，展示如何从被选的联系人中读出电话号码。

	@Override
	protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		// Check which request it is that we're responding to
		if (requestCode == PICK_CONTACT_REQUEST) {
			// Make sure the request was successful
			if (resultCode == RESULT_OK) {
				// Get the URI that points to the selected contact
				Uri contactUri = data.getData();
				// We only need the NUMBER column, because there will be only one row in the result
				String[] projection = {Phone.NUMBER};

				// Perform the query on the contact to get the NUMBER column
				// We don't need a selection or sort order (there's only one result for the given URI)
				// CAUTION: The query() method should be called from a separate thread to avoid blocking
				// your app's UI thread. (For simplicity of the sample, this code doesn't do that.)
				// Consider using CursorLoader to perform the query.
				Cursor cursor = getContentResolver().query(contactUri, projection, null, null, null);
				cursor.moveToFirst();

				// Retrieve the phone number from the NUMBER column
				int column = cursor.getColumnIndex(Phone.NUMBER);
				String number = cursor.getString(column);

				// Do something with the phone number...
			}
		}
	}

## Intent过滤

但如果你的app的功能对别的app也有用，那么你的app应该做好响应的准备。例如，如果你创建了一个social app，它可以分享messages 或者 photos 给好友，那么最好你的app能够接收 ACTION_SEND 的intent,这样当用户在其他app触发分享功能的时候，你的app能够出现在待选对话框。

为了使得其他的app能够启动你的activity，你需要在你的manifest文件的 &lt;activity> 标签下添加 &lt;intent-filter> 的属性。当你的app被安装到设备上时，系统可以识别你的intent filter并把这些信息记录下来。当其他app使用implicit intent执行startActivity() 或者 startActivityForResult()时，系统会自动查找出那些可以响应这个intent的activity。

### 添加Intent Filter

为了尽可能确切的定义你的activity能够handle哪些intent，每一个intent filter都应该尽可能详尽的定义好action与data。如果activity中的intent filter满足以下intent对象的标准，系统就能够把特定的intent发送给activity：

* Action:一个想要执行的动作的名称。通常是系统已经定义好的值，例如 ACTION_SEND 或者 ACTION_VIEW 。 在intent filt中用 &lt;action> 指定它的值，值的类型必须为字符串，而不是API中的常量(看下面的例子)
* Data:Intent附带数据的描述。在intent filter中用 &lt;data> 指定它的值，可以使用一个或者多个属性，你可以只定义MIME type或者是只指定URI prefix，也可以只定义一个URI scheme，或者是他们综合使用。

> Note: 如果你不想handle Uri 类型的数据，那么你应该指定 android:mimeType 属性。例如 text/plain or
image/jpeg.

* Category:提供一个附加的方法来标识这个activity能够handle的intent。通常与用户的手势或者是启动位置有关。系统有支持几种不同的categories,但是大多数都不怎么用的到。而且，所有的implicit intents都默认是CATEGORY_DEFAULT 类型的。在intent filter中用 &lt;category> 指定它的值。

在你的intent filter中，你可以在 &lt;intent-filter> 元素中定义对应的XML元素来声明你的activity使用何种标准。例如，这个有intent filter的activity，当数据类型为文本或图像时会处理 ACTION_SEND 的intent。

	<activity android:name="ShareActivity">
		<intent-filter>
			<action android:name="android.intent.action.SEND"/>
			<category android:name="android.intent.category.DEFAULT"/>
			<data android:mimeType="text/plain"/>
			<data android:mimeType="image/*"/>
		</intent-filter>
	</activity>

每一个发送出来的intent只会包含一个action与data类型，但是handle这个intent的activity的 &lt;intent-filter> 是可以声明多个 &lt;action> , &lt;category> 与 &lt;data> 的。如果任何的两对action与data是互相矛盾的，你应该创建不同的intent filter来指定特定的action与type。

例如，假设你的activity可以handle 文本与图片，无论是 ACTION_SEND 还是 ACTION_SENDTO 的intent。在这种情况下，你必须为两个action定义两个不同的intent filter。因为 ACTION_SENDTO intent 必须使用 Uri 类型来指定接收者使用 send 或sendto 的地址。例如：

	<activity android:name="ShareActivity">
		<!-- filter for sending text; accepts SENDTO action with sms URI schemes -->
		<intent-filter>
			<action android:name="android.intent.action.SENDTO"/>
			<category android:name="android.intent.category.DEFAULT"/>
			<data android:scheme="sms" />
			<data android:scheme="smsto" />
		</intent-filter>

		<!-- filter for sending text or images; accepts SEND action and text or image data -->
		<intent-filter>
			<action android:name="android.intent.action.SEND"/>
			<category android:name="android.intent.category.DEFAULT"/>
			<data android:mimeType="image/*"/>
			<data android:mimeType="text/plain"/>
		</intent-filter>
	</activity>

> Note:为了接受implicit intents, 你必须在你的intent filter中包含 CATEGORY_DEFAULT 的category。startActivity()和startActivityForResult()方法将所有intent视为声明了CATEGORY_DEFAULT category。如果你没有在你的intent filter中声明CATEGORY_DEFAULT，你的activity将无法对implicit intent做出响应。

### 在你的Activity中Handle发送过来的Intent

为了决定采用哪个action，你可以读取Intent的内容。你可以执行getIntent() 来获取启动你的activity的那个intent。你可以在activity生命周期的任何时候去执行这个方法，但最好是在 onCreate() 或者 onStart() 里面去执行。

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);

		// Get the intent that started this activity
		Intent intent = getIntent();
		Uri data = intent.getData();

		// Figure out what to do based on the intent type
		if (intent.getType().indexOf("image/") != -1) {
			// Handle intents with image data ...
		} else if (intent.getType().equals("text/plain")) {
			// Handle intents with text ...
		}
	}

### 返回Result

如果你想返回一个result给启动你的那个activity，仅仅需要执行setResult()，通过指定一个result code与result intent。当你的操作完成之后，用户需要返回到原来的activity，通过执行finish() 来关闭被叫起的activity。

	// Create intent to deliver some kind of result data
	Intent result = new Intent("com.example.RESULT_ACTION", Uri.parse("content://result_uri");
	setResult(Activity.RESULT_OK, result);
	finish();

你必须总是指定一个result code。通常不是 RESULT_OK 就是 RESULT_CANCELED 。你可以通过Intent 来添加需要返回的数据。

> Note:默认的result code是 RESULT_CANCELED .因此，如果用户在没有完成操作之前点击了back key，那么之前的activity接受到的result code就是"canceled"。

如果你只是纯粹想要返回一个int来表示某些返回的result数据之一，你可以设置result code为任何大于0的数值。如果你返回的result只是一个int，那么连intent都可以不需要返回了，你可以调用 setResult() 然后只传递result code如下：

	setResult(RESULT_COLOR_RED);
	finish();

> Note:我们没有必要在意你的activity是被用startActivity() 还是 startActivityForResult()方法所叫起的。系统会自动去判断该如何传递result。在不需要的result的case下，result会被自动忽略。