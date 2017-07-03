title: Android官方培训教程7——Android分享操作
date: 2015-08-30 15:47:42
tags: [Android官方培训教程]
categories: [Android官方培训教程]
---

## 摘要
Android分享操作
<!--more-->

## Android分享操作

创建可以在不同的应用与设备之间进行分享的应用。

## 分享简单的数据

学习如何使得你的应用可以和其他应用进行交互。分享信息，接收信息，为用户数据提供一个简单并且可扩展的方式来执行分享操作。

Android程序中很炫的一个功能是程序之间可以互相通信。为什么要重新发明一个已经存在于另外一个程序中的功能呢，而且这个功能并非自己程序的核心部分。

### 给其他App发送简单的数据

当你构建一个intent，你必须指定这个intent需要触发的actions。Android定义了一些actions，包括ACTION_SEND，这个action表明着这个intent是用来从一个activity发送数据到另外一个activity的，甚至是跨进程之间的。

为了发送数据到另外一个activity，你需要做的是指定数据与数据的类型，系统会识别出能够兼容接受的这些数据的activity并且把这些activity显示给用户进行选择(如果有多个选择)，或者是立即启动Activity(只有一个兼容的选择)。同样的，你可以在manifest文件的Activity描述中添加接受哪些数据类型。

在不同的程序之间使用intent来发送与接受数据是在社交分享内容的时候最常用的方法。Intent使得用户用最常用的程序进行快速简单的分享信息。

注意:为ActionBar添加分享功能的最好方法是使用ShareActionProvider，它能够在API level 14以上进行使用。ShareActionProvider会在第3课中进行详细介绍。

### 分享文本内容

ACTION_SEND的最直接与最常用的是从一个Activity发送文本内容到另外一个Activity。例如，Android内置的浏览器可以把当前显示页面的URL作为文本内容分享到其他程序。这是非常有用的，通过邮件或者社交网络来分享文章或者网址给好友。下面是一段Sample Code:

	Intent sendIntent = new Intent();
	sendIntent.setAction(Intent.ACTION_SEND);
	sendIntent.putExtra(Intent.EXTRA_TEXT, "This is my text to send.");
	sendIntent.setType("text/plain");
	startActivity(sendIntent);

如果设备上有安装某个能够匹配ACTION_SEND与MIME类型为text/plain程序，那么Android系统会自动把他们都给筛选出来，并呈现Dialog给用户进行选择。如果你为intent调用了Intent.createChooser()，那么Android总是会显示可供选择。这样有一些好处：

* 即使用户之前为这个intent设置了默认的action，选择界面还是会被显示。
* 如果没有匹配的程序，Android会显示系统信息。
* 你可以指定选择界面的标题。

下面是更新后的代码：

	Intent sendIntent = new Intent();
	sendIntent.setAction(Intent.ACTION_SEND);
	sendIntent.putExtra(Intent.EXTRA_TEXT, "This is my text to send.");
	sendIntent.setType("text/plain");
	startActivity(Intent.createChooser(sendIntent, getResources().getText(R.string.send_to));

Optionally,你可以为intent设置一些标准的附加值，例如：EXTRA_EMAIL, EXTRA_CC, EXTRA_BCC,EXTRA_SUBJECT.然而，如果接收程序没有针对那些做特殊的处理，则不会有对应的反应。你也可以使用自定义的附加值，但是除非接收的程序能够识别出来，不然没有任何效果。典型的做法是，你使用被接受程序定义的附加值。

注意:一些e-mail程序，例如Gmail,对应接收的是EXTRA_EMAIL与EXTRA_CC，他们都是String类型的，可以使用putExtra(string,string[])方法来添加到intent里面。

### 分享二进制内容

分享二进制的数据需要结合设置特定的 MIME Type ，需要在 EXTRA_STREAM 里面放置数据的URI,下面有个分享图片的例子，这个例子也可以修改用来分享任何类型的二进制数据：

	Intent shareIntent = new Intent();
	shareIntent.setAction(Intent.ACTION_SEND);
	shareIntent.putExtra(Intent.EXTRA_STREAM, uriToImage);
	shareIntent.setType("image/jpeg");
	startActivity(Intent.createChooser(shareIntent, getResources().getText(R.string.send_to)));

请注意下面的内容：

* 你可以使用 */* 这样的方式来指定MIME类型，但是这仅仅会match到那些能够处理一般数据类型的Activity(即一般的Activity无法详尽所有的MIME类型)
* 接收的程序需要有访问URI资源的权限。下面有一些方法来处理这个问题：
* 把文件写到外部存储设备上，类似SDCard，这样所有的app都可以进行读取。使用Uri.fromFile()方法来创建可以用在分享时传递到intent里面的Uri.。然而，请记住，不是所有的程序都遵循 file:// 这样格式的Uri。
* 在调用 getFileStreamPath()返回一个File之后，使用带有 MODE_WORLD_READABLE 模式的openFileOutput() 方法把数据写入到你自己的程序目录下。像上面一样，使用Uri.fromFile()创建一个 file:// 格式的Uri用来添加到intent里面进行分享。
* 媒体文件，例如图片，视频与音频，可以使用scanFile()方法进行扫描并存储到MediaStore里面。onScanCompletted()回调函数会返回一个 content:// 格式的Uri.，这样便于你进行分享的时候把这个uri放到intent里面。
* 图片可以使用 insertImage() 方法直接插入到MediaStore 系统里面。那个方法会返回一个 content:// 格式的Uri。
* 存储数据到你自己的ContentProvider里面，确保其他app可以有访问你的provider的权限。(或者使用 per-URI permissions)

### 发送多块内容

为了同时分享多种不同类型的内容，需要使用 ACTION_SEND_MULTIPLE 与指定到那些数据的URIs列表。MIME类型会根据你分享的混合内容而不同。例如，如果你分享3张JPEG的图片，那么MIME类型仍然是 image/jpeg 。如果是不同图片格式的话，应该是用 image/* 来匹配那些可以接收任何图片类型的activity。如果你需要分享多种不同类型的数据，可以使用 */* 来表示MIME。像前面描述的那样，这取决于那些接收的程序解析并处理你的数据。下面是一个例子：

	ArrayList<Uri> imageUris = new ArrayList<Uri>();
	imageUris.add(imageUri1); // Add your image URIs here
	imageUris.add(imageUri2);
	Intent shareIntent = new Intent();
	shareIntent.setAction(Intent.ACTION_SEND_MULTIPLE);
	shareIntent.putParcelableArrayListExtra(Intent.EXTRA_STREAM, imageUris);
	shareIntent.setType("image/*");
	startActivity(Intent.createChooser(shareIntent, "Share images to.."));

当然，请确保指定到数据的URIs能够被接收程序所访问(添加访问权限)。

## 接收从其他App传送来的数据

就像你的程序能够发送数据到其他程序一样，其他程序也能够方便的接收发送过来的数据。需要考虑的是用户与你的程序如何进行交互，你想要从其他程序接收哪些数据类型。例如，一个社交网络程序会希望能够从其他程序接受文本数据，像一个有趣的网址链接。

### 更新你的manifest文件

Intent filters通知了Android系统说，一个程序会接受哪些数据。像上一课一样，你可以创建intent filters来表明程序能够接收哪些action。下面是个例子，对三个activit分别指定接受单张图片，文本与多张图片。

	<activity android:name=".ui.MyActivity" >
		<intent-filter>
			<action android:name="android.intent.action.SEND" />
			<category android:name="android.intent.category.DEFAULT" />
			<data android:mimeType="image/*" />
		</intent-filter>
		<intent-filter>
			<action android:name="android.intent.action.SEND" />
			<category android:name="android.intent.category.DEFAULT" />
			<data android:mimeType="text/plain" />
		</intent-filter>
		<intent-filter>
			<action android:name="android.intent.action.SEND_MULTIPLE" />
			<category android:name="android.intent.category.DEFAULT" />
			<data android:mimeType="image/*" />
		</intent-filter>
	</activity>

当另外一个程序尝试分享一些东西的时候，你的程序会被呈现在一个列表里面让用户进行选择。如果用户选择了你的程序，相应的activity就应该被调用开启，这个时候就是你如何处理获取到的数据的问题了。

### 处理接受到的数据

为了处理从Intent带过来的数据，可以通过调用getIntent()方法来获取到Intent对象。一旦你拿到这个对象，你可以对里面的数据进行判断，从而决定下一步应该做什么。请记住，如果一个activity可以被其他的程序启动，你需要在检查intent的时候考虑这种情况(是被其他程序而调用启动的)。

	void onCreate (Bundle savedInstanceState) {
		...
		// Get intent, action and MIME type
		Intent intent = getIntent();
		String action = intent.getAction();
		String type = intent.getType();
		if (Intent.ACTION_SEND.equals(action) && type != null) {
			if ("text/plain".equals(type)) {
				handleSendText(intent); // Handle text being sent
			} else if (type.startsWith("image/")) {
				handleSendImage(intent); // Handle single image being sent
			}
		} else if (Intent.ACTION_SEND_MULTIPLE.equals(action) && type != null) {
			if (type.startsWith("image/")) {
				handleSendMultipleImages(intent); // Handle multiple images being sent
			}
		} else {
			// Handle other intents, such as being started from the home screen
		}
		...
	}

	void handleSendText(Intent intent) {
		String sharedText = intent.getStringExtra(Intent.EXTRA_TEXT);
		if (sharedText != null) {
			// Update UI to reflect text being shared
		}
	}

	void handleSendImage(Intent intent) {
		Uri imageUri = (Uri) intent.getParcelableExtra(Intent.EXTRA_STREAM);
		if (imageUri != null) {
			// Update UI to reflect image being shared
		}
	}

	void handleSendMultipleImages(Intent intent) {
		ArrayList<Uri> imageUris = intent.getParcelableArrayListExtra(Intent.EXTRA_STREAM);
		if (imageUris != null) {
			// Update UI to reflect multiple images being shared
		}
	}

请注意，因为你无法知道其他程序发送过来的数据内容是文本还是其他的数据，因此你需要避免在UI线程里面去处理那些获取到的数据。 更新UI可以像更新EditText一样简单，也可以是更加复杂一点的操作，例如过滤出感兴趣的图片。这完全取决于你的应用接下来要做些什么。

### 添加一个简便的分享功能

在ActionBar 中添加一个高效率且比较友好的Share功能，会使用到ActionProvider(在Android 4.0上才被引进)。它会handle出现share功能的appearance与hehavior。在ShareActionProvider的例子里面，你只需要提供一个share intent，剩下的就交给ShareActionProvider来做。

### 更新菜单声明

使用ShareActionProvider的第一步，在你的menu resources对应item中定义 android:actionProviderClass 属性。

	<menu xmlns:android="http://schemas.android.com/apk/res/android">
	<item android:id="@+id/menu_item_share"
		android:showAsAction="ifRoom"
		android:title="Share"
		android:actionProviderClass="android.widget.ShareActionProvider" />
		...
	</menu>

这表明了这个item的appearance与function需要与ShareActionProvider匹配。然而，你还是需要告诉provider你想分享的内容。

### 设置分享的intent（Set the Share Intent）

为了能够实现ShareActionProvider的功能，你必须提供给它一个intent。这个share intent应该像第一课讲的那样，带有 ACTION_SEND 和附加数据(例如 EXTRA_TEXT 与 EXTRA_STREAM )的。如何使用ShareActionProvider，请看下面的例子：

	private ShareActionProvider mShareActionProvider;
	...

	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		// Inflate menu resource file.
		getMenuInflater().inflate(R.menu.share_menu, menu);

		// Locate MenuItem with ShareActionProvider
		MenuItem item = menu.findItem(R.id.menu_item_share);

		// Fetch and store ShareActionProvider
		mShareActionProvider = (ShareActionProvider) item.getActionProvider();

		// Return true to display menu
		return true;
	}

	// Call to update the share intent
	private void setShareIntent(Intent shareIntent) {
		if (mShareActionProvider != null) {
			mShareActionProvider.setShareIntent(shareIntent);
		}
	}

你也许在创建菜单的时候仅仅需要设置一次share intent就满足需求了，或者说你可能想先设置share intent，然后根据UI的变化来对intent进行更新。例如，当你在Gallery里面全图查看照片的时候，share intent会在你切换图片的时候进行改变。

## 分享文件

一个应用程序经常需要向其他应用程序提供一个甚至多个文件。例如，当我们用图片编辑器编辑图片时，被编辑的图片往往由图库应用程序所提供；再比如，文件管理器会允许用户在外部存储的不同区域之间复制粘贴文件。这里，我们提出一种让应用程序可以分享文件的方法：即令发送文件的应用程序对索取文件的应用程序所发出的文件请求进行响应。

在所有情况下，将文件从你的应用程序发送至其它应用程序的唯一的安全方法是向接收文件的应用程序发送这个文件的content URI，并对该URI授予临时的访问权限。具有URI临时访问权限的content URI是安全的，因为他们仅应用于接收这个URI的应用程序，并且它们会自动过期。Android的FileProvider组件提供了getUriForFile())方法来创建一个文件的content URI。

使用Android的FileProvider组件所创建的content URI来安全地在应用之间共享文件。当然，要做到这一点，同时还需要授予给接收文件的应用程序访问这些content URI的临时访问权限。

### 建立文件分享

为了将文件安全地从你的应用程序发送给其它应用程序，你需要对你的应用进行配置来提供安全的文件句柄（Content URI的形式）。Android的FileProvider组件会基于你在XML文件中的具体配置，为文件创建Content URI。

> Note:FileProvider类隶属于v4 Support Library库。

### 指定FileProvider

为你的应用程序定义一个FileProvider需要在你的Manifest清单文件中定义一个字段，这个字段指明了需要使用的创建Content URI的Authority。除此之外，还需要一个XML文件的文件名，这个XML文件指定了你的应用可以共享的目录路径。

下面的例子展示了如何在清单文件中添加 &lt;provider> 标签，来指定FileProvider类，Authority和XML文件名：

	<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
		package="com.example.myapp">
		<application
			...>

			<provider
				android:name="android.support.v4.content.FileProvider"
				android:authorities="com.example.myapp.fileprovider"
				android:grantUriPermissions="true"
				android:exported="false">

				<meta-data
					android:name="android.support.FILE_PROVIDER_PATHS"
					android:resource="@xml/filepaths" />

			</provider>
			...
		</application>
	</manifest>

这里，android:authorities属性字段指定了你希望使用的Authority，该Authority针对于FileProvider所生成的contentURI。在这个例子中，这个Authority是“com.example.myapp.fileprovider”。对于你自己的应用，要用你的应用程序包名（android:package的值）之后继续追加“fileprovider”来指定Authority。

&lt;provider> 下的子标签 &lt;meta-data> 指向了一个XML文件，该文件指定了你希望共享的目录路径。“android:resource”属性字段是这个文件的路径和名字（无“.xml”后缀）。

### 指定可共享目录路径

一旦你在你的Manifest清单文件中为你的应用添加了FileProvider，你需要指定你希望共享文件的目录路径。为了指定这个路径，我们首先在“res/xml/”下创建文件“filepaths.xml”。在这个文件中，为每一个想要共享目录添加一个XML标签。下面的是一个“res/xml/filepaths.xml”的内容样例。这个例子也说明了如何在你的内部存储区域共享一个“files/”目录的子目录：

	<paths>
		<files-path path="images/" name="myimages" />
	</paths>

在这个例子中， &lt;files-path> 标签共享的是在你的应用的内部存储中“files/”目录下的目录。“path”属性字段指出了该子目录为“files/”目录下的子目录“images/”。“name”属性字段告知FileProvider向在“files/images/”子目录中的文件的ContentURI添加路径分段（path segment）标记：“myimages”。&lt;paths> 标签可以有多个子标签，每一个子标签用来指定不同的共享目录。除了 &lt;files-path> 标签，你还可以使用 &lt;external-path> 来共享位于外部存储的目录；另外， &lt;cache-path> 标签用来共享在你的内部缓存目录下的子目录。

> Note: XML文件是你定义共享目录的唯一方式，你不可以用代码的形式添加目录。

现在你有一个完整的FileProvider声明，它在你应用程序的内部存储中“files/”目录下，或者是在“files/”中的子目录下，创建文件的Content URI。当你的应用为一个文件创建了Content URI，该Content URI将会包含下列信息：

* &lt;provider> 标签中指定的Authority（“com.example.myapp.fileprovider”）；
* 路径“myimages/”；
* 文件的名字。

例如，如果你根据这节课的例子定义了一个FileProvider，然后你需要一个文件“default_image.jpg”的ContentURI，FileProvider会返回如下URI：

	content://com.example.myapp.fileprovider/myimages/default_image.jpg

## 分享文件

分享文件的三个步骤：

* 生成文件的content URI；
* 授予URI的临时访问权限；
* 将URI发送给接收文件的应用程序。

我们对应用程序进行了配置，使得它可以使用Content URI来共享文件了，现在你可以响应其他应用程序的文件请求。一种响应这些请求的方法是在服务端应用程序提供一个文件选择接口，它可以由其他应用激活。这种方法可以允许客户端应用程序让用户从服务端应用程序选择一个文件，然后接收这个文件的Content URI。

### 接收文件请求

为了从客户端应用程序接收一个文件索取请求，然后以Content URI的形式进行响应，你的应用程序应该提供一个选择文件的Activity。客户端应用程序通过调用startActivityForResult())来启动这个Activity。该方法包含了一个Intent参数，它具有ACTION_PICK这一Action。当客户端应用程序调用了startActivityForResult())，你的应用可以向客户端应用程序返回一个结果，该结果即用户所选择的文件所对应的Content URI。

### 创建一个选择文件的Activity

为了配置一个选择文件的Activity，我们首先需要在Manifest清单文件中定义你的Activity，在其Intent过滤器中，匹配ACTION_PICK这一Action，以及CATEGORY_DEFAULT和CATEGORY_OPENABLE这两种Category。另外，还需要为你的应用程序设置MIME类型过滤器，来表明你的应用程序可以向其他应用程序提供哪种类型的文件。下面的这段代码展示了如何在清单文件中定义新的Activity和Intent过滤器：

	<manifest xmlns:android="http://schemas.android.com/apk/res/android">
		...
		<application>
			...
			<activity
				android:name=".FileSelectActivity"
				android:label="File Selector" >
				<intent-filter>
					<action android:name="android.intent.action.PICK"/>
					<category android:name="android.intent.category.DEFAULT"/>
					<category android:name="android.intent.category.OPENABLE"/>
					<data android:mimeType="text/plain"/>
					<data android:mimeType="image/*"/>
				</intent-filter>
			</activity>

### 在代码中定义文件选择Activity

下面，定义一个Activity子类，它用来显示在你内部存储的“files/images/”目录下可以获得的文件，然后允许用户选择期望的文件。下面的代码展示了如何定义这个Activity，并令其响应用户的选择：

	public class MainActivity extends Activity {
		// The path to the root of this app's internal storage
		private File mPrivateRootDir;
		// The path to the "images" subdirectory
		private File mImagesDir;
		// Array of files in the images subdirectory
		File[] mImageFiles;
		// Array of filenames corresponding to mImageFiles
		String[] mImageFilenames;
		// Initialize the Activity

		@Override
		protected void onCreate(Bundle savedInstanceState) {
			...
			// Set up an Intent to send back to apps that request a file
			mResultIntent =
			new Intent("com.example.myapp.ACTION_RETURN_FILE");
			// Get the files/ subdirectory of internal storage
			mPrivateRootDir = getFilesDir();
			// Get the files/images subdirectory;
			mImagesDir = new File(mPrivateRootDir, "images");
			// Get the files in the images subdirectory
			mImageFiles = mImagesDir.listFiles();
			// Set the Activity's result to null to begin with
			setResult(Activity.RESULT_CANCELED, null);
			/*
			 * Display the file names in the ListView mFileListView.
			 * Back the ListView with the array mImageFilenames, which
			 * you can create by iterating through mImageFiles and
			 * calling File.getAbsolutePath() for each File
			 */
			...
		}
		...
	}

### 响应一个文件选择

一旦用户选择了一个想要共享的文件，你的应用程序必须明确哪个文件被选择了，然后为这个文件生成一个对应的Content URI。如果我们的Activity在ListView中显示了可获得文件的清单，那么当用户点击了一个文件名时，系统会调用方法onItemClick())，在该方法中你可以获取被选择的文件。

在onItemClick())中，根据被选中文件的文件名获取一个File对象，然后将它作为参数传递给getUriForFile())，另外还需传入的参数是你在 &lt;provider> 标签中为FileProvider所指定的Authority，函数返回的Content URI包含了相应的Authority，一个对应于文件目录的路径标记（如在XML meta-data中定义的），以及包含扩展名的文件名。

下面的例子展示了如何检测选中的文件并且获得它的Content URI：

	protected void onCreate(Bundle savedInstanceState) {
		...
		// Define a listener that responds to clicks on a file in the ListView
		mFileListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
			@Override
			/*
			 * When a filename in the ListView is clicked, get its
			 * content URI and send it to the requesting app
			 */
			public void onItemClick(AdapterView<?> adapterView, View view, int position, long rowId) {
				/*
				 * Get a File for the selected file name.
				 * Assume that the file names are in the
				 * mImageFilename array.
				 */
				File requestFile = new File(mImageFilename[position]);
				/*
				 * Most file-related method calls need to be in
				 * try-catch blocks.
				 */
				// Use the FileProvider to get a content URI
				try {
					fileUri = FileProvider.getUriForFile(MainActivity.this, "com.example.myapp.fileprovider", requestFile);
				} catch (IllegalArgumentException e) {
					Log.e("File Selector", "The selected file can't be shared: " + clickedFilename);
				}
				...
			}
		});
		...
	}

记住，你能生成的那些Content URI所对应的文件，是那些在meta-data文件中包含 &lt;paths> 标签的（即你定义的）目录内的文件。如果你调用getUriForFile())方法所要获取的文件不在你指定的目录中，你会收到一个IllegalArgumentException。

### 为文件授权

现在已经有了你想要共享给其他应用程序的文件所对应的Content URI，你需要允许客户端应用程序访问这个文件。为了达到这一目的，可以通过将Content URI添加至一个Intent中，然后为该Intent设置权限标记。你所授予的权限是临时的，并且当接收文件的应用程序的任务栈终止后，会自动过期。下面的例子展示了如何为文件设置读权限：

	protected void onCreate(Bundle savedInstanceState) {
		...
		// Define a listener that responds to clicks in the ListView
		mFileListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
			@Override
			public void onItemClick(AdapterView<?> adapterView, View view, int position, long rowId) {
				...
				if (fileUri != null) {
					// Grant temporary read permission to the content URI
					mResultIntent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
				}
				...
			}
			...
		});
		...
	}

> Caution：只有调用setFlags())来为你的文件授予临时被访问权限才是唯一的，安全的方法。尽量避免对文件的
Content URI调用Context.grantUriPermission())，因为通过该方法授予的权限，你只能通过调
用Context.revokeUriPermission())来撤销。

### 与请求应用共享文件

为了向请求文件的应用程序提供其需要的文件，我们将包含了Content URI和相应权限的Intent传递给setResult())。当你定义的Activity结束后，系统会把这个包含了Content URI的Intent传递给客户端应用程序。下面的例子展示了其中的核心步骤：

	protected void onCreate(Bundle savedInstanceState) {
		...
		// Define a listener that responds to clicks on a file in the ListView
		mFileListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
			@Override
			public void onItemClick(AdapterView<?> adapterView, View view, int position, long rowId) {
				...
				if (fileUri != null) {
					...
					// Put the Uri and MIME type in the result Intent
					mResultIntent.setDataAndType(fileUri, getContentResolver().getType(fileUri));
					// Set the result
					MainActivity.this.setResult(Activity.RESULT_OK, mResultIntent);
				} else {
					mResultIntent.setDataAndType(null, "");
					MainActivity.this.setResult(RESULT_CANCELED, mResultIntent);
				}
			}
		});
	}

当用户选择好文件后，我们应该向用户提供一个能够立即回到客户端应用程序的方法。一种实现的方法是向用户提供一个勾选框或者一个完成按钮。可以使用按钮的android:onClick属性字段为它关联一个方法。在该方法中，调用finish())。例如：

	public void onDoneClick(View v) {
		// Associate a method with the Done button
		finish();
	}

## 请求分享一个文件

当一个应用程序希望访问由其它应用程序所共享的文件时，请求应用程序（即客户端）经常会向其它应用程序（服务端）发送一个文件请求。在大多数情况下，这个请求会导致在服务端应用程序中启动一个Activity，在该Activity中会显示可以共享的文件。当服务端应用程序向客户端应用程序返回了文件的Content URI后，用户即选择了文件。

这节课将向你展示一个客户端应用程序应该如何向服务端应用程序请求一个文件，接收服务端应用程序发来的Content URI，然后使用这个Content URI打开这个文件。

### 发送一个文件请求

为了向服务端应用程序发送文件请求，在客户端应用程序中，需要调用startActivityForResult)方法，同时传递给这个方法一个Intent参数，它包含了客户端应用程序能处理的某个Action，比如ACTION_PICK；以及一个MIME类型。

例如，下面的代码展示了如何向服务端应用程序发送一个Intent，来启动在分享文件中提到的Activity：

	public class MainActivity extends Activity {
		private Intent mRequestFileIntent;
		private ParcelFileDescriptor mInputPFD;
		...
		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_main);
			mRequestFileIntent = new Intent(Intent.ACTION_PICK);
			mRequestFileIntent.setType("image/jpg");
			...
		}
		...
		protected void requestFile() {
			/**
			 * When the user requests a file, send an Intent to the
			 * server app.
			 * files.
			 */
			startActivityForResult(mRequestFileIntent, 0);
			...
		}
		...
	}

### 访问请求的文件

当服务端应用程序向客户端应用程序发回包含Content URI的Intent时，这个Intent会传递给客户端应用程序重写的onActivityResult())方法当中。一旦客户端应用程序有了文件的Content URI，它就可以通过获取其FileDescriptor来访问文件了。

文件的安全问题在这一过程中不用过多担心，因为客户端应用程序所收到的所有数据只有文件的Content URI而已。由于URI不包含目录路径信息，客户端应用程序无法查询出或者打开任何服务端应用程序的其他文件。客户端应用程序仅仅获取了这个文件的访问渠道以及由服务端应用程序授予的访问权限。同时访问权限是临时的，一旦这个客户端应用的任务栈结束了，这个文件将不再被除服务端应用程序之外的其他应用程序访问。

下面的例子展示了客户端应用程序应该如何处理发自服务端应用程序的Intent，以及客户端应用程序如何使用Content URI获取FileDescriptor：

	/*
	 * When the Activity of the app that hosts files sets a result and calls
	 * finish(), this method is invoked. The returned Intent contains the
	 * content URI of a selected file. The result code indicates if the
	 * selection worked or not.
	 */
	@Override
	public void onActivityResult(int requestCode, int resultCode, Intent returnIntent) {
		// If the selection didn't work
		if (resultCode != RESULT_OK) {
			// Exit without doing anything else
			return;
		} else {
			// Get the file's content URI from the incoming Intent
			Uri returnUri = returnIntent.getData();
			/*
			 * Try to open the file for "read" access using the
			 * returned URI. If the file isn't found, write to the
			 * error log and return.
			 */
			try {
				/*
				 * Get the content resolver instance for this context, and use it
				 * to get a ParcelFileDescriptor for the file.
				 */
				mInputPFD = getContentResolver().openFileDescriptor(returnUri, "r");
			} catch (FileNotFoundException e) {
				e.printStackTrace();
				Log.e("MainActivity", "File not found.");
				return;
			}
			// Get a regular file descriptor for the file
			FileDescriptor fd = mInputPFD.getFileDescriptor();
			...
		}
	}

openFileDescriptor())方法会返回一个文件的ParcelFileDescriptor对象。从这个对象中，客户端应用程序可以获取FileDescriptor对象，然后用户就可以利用这个对象读取这个文件了。

## 获取文件信息

当一个客户端应用程序拥有了文件的Content URI之后，它就可以获取该文件并进行下一步的工作了，但是在此之前，客户端应用程序还可以向服务端应用程序索取关于文件的信息，包括文件的数据类型和文件大小等等。数据类型可以帮助客户端应用程序确定自己能否处理该文件，文件大小能帮助客户端应用程序为文件设置合理的缓冲区。本节将展示如何通过查询服务端应用程序的FileProvider来获取文件的MIME类型和文件大小。

### 获取文件的MIME类型

客户端应用程序可以通过文件的数据类型判断自己应该如何处理这个文件的内容。为了得到Content URI所对应的文件数据类型，客户端应用程序可以调用ContentResolver.getType())方法。这个方法返回了文件的MIME类型。默认情况下，一个FileProvider通过文件的后缀名来确定其MIME类型。

下面的例子展示了当服务端应用程序将Content URI返回给客户端应用程序后，客户端应用程序应该如何获取文件的MIMIE类型：

	...
	/*
	 * Get the file's content URI from the incoming Intent, then
	 * get the file's MIME type
	 */
	Uri returnUri = returnIntent.getData();
	String mimeType = getContentResolver().getType(returnUri);
	...

### 获取文件名和文件大小

FileProvider类有一个query())方法的默认实现，它返回一个Cursor对象，该Cursor对象包含了Content URI所关联的文件的名称和大小。默认的实现返回下面两列信息：

* DISPLAY_NAME：文件的文件名，它是一个String类型。这个值和File.getName())所返回的值是一样的。
* SIZE：文件的大小，以字节为单位，它是一个long类型。这个值和File.length())所返回的值是一样的。

客户端应用可以通过将query())的除了Content URI之外的其他参数都设置为“null”，来同时获取文件的名称（DISPLAY_NAME）和大小（SIZE）。例如，下面的代码获取一个文件的名称和大小，然后在两个TextView中将他们显示出来：

	...
	/*
	 * Get the file's content URI from the incoming Intent,
	 * then query the server app to get the file's display name
	 * and size.
	 */
	Uri returnUri = returnIntent.getData();
	Cursor returnCursor =
	getContentResolver().query(returnUri, null, null, null, null);
	/*
	 * Get the column indexes of the data in the Cursor,
	 * move to the first row in the Cursor, get the data,
	 * and display it.
	 */
	int nameIndex = returnCursor.getColumnIndex(OpenableColumns.DISPLAY_NAME);
	int sizeIndex = returnCursor.getColumnIndex(OpenableColumns.SIZE);
	returnCursor.moveToFirst();
	TextView nameView = (TextView) findViewById(R.id.filename_text);
	TextView sizeView = (TextView) findViewById(R.id.filesize_text);
	nameView.setText(returnCursor.getString(nameIndex));
	sizeView.setText(Long.toString(returnCursor.getLong(sizeIndex)));
	...

## 使用NFC分享文件

Android允许你通过Android Beam文件传输功能在设备之间传送大文件。这个功能具有简单的API，同时，它允许用户仅需要进行一些简单的触控操作就能启动文件传输过程。Android Beam会自动地将文件从一台设备拷贝至另一台设备中，并在完成时告知用户。

Android Beam文件传输API可以用来处理规模较大的数据，而在Android4.0（API Level 14）引入的Android BeamNDEF传输API则用来处理规模较小的数据，比如：URI或者消息数据等。另外，Android Beam仅仅是Android NFC框架提供的众多特性之一，它允许你从NFC标签中读取NDEF消息。

## 发送文件给其他设备

这节课将展示如何通过Android Beam文件传输向另一台设备发送大文件。要发送文件，首先需要声明使用NFC和外部存储的权限，你需要测试一下你的设备是否支持NFC，这样，你才能够将文件的URI提供给Android Beam文件传输。使用Android Beam文件传输功能必须满足下列要求：

* 用Android Beam文件传输功能传输大文件只能在Android 4.1（API Level 16）及以上版本的Android系统中使用。
* 你希望传送的文件必须放置于外部存储。学习更多关于外部存储的知识，可以阅读：Using the External Storage。
* 每个你希望传送的文件必须是全局可读的。你可以通过File.setReadable(true,false))来为文件设置相应的读权限。
* 你必须提供待传输文件的File URI。Android Beam文件传输无法处理由FileProvider.getUriForFile)生成的Content URI。

### 在清单文件中声明

首先，编辑你的Manifest清单文件来声明你的应用程序所需要的权限和功能。为了允许你的应用程序使用Android Beam文件传输控制NFC从外部存储发送文件，你必须在应用程序的Manifest清单文件中声明下面的权限：

NFC：允许你的应用程序通过NFC发送数据。为了声明该权限，添加下面的标签作为一个 &lt;manifest> 标签的子标签：

	<uses-permission android:name="android.permission.NFC" />

READ_EXTERNAL_STORAGE：允许你的应用读取外部存储。为了声明该权限，添加下面的标签作为一个 &lt;manifest> 标签的子标签：

	<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

> Note：对于Android 4.2.2（API Level 17）及之前版本的系统，这个权限不是必需的。在后续版本的系统中，若
应用程序需要读取外部存储，可能会需要申明该权限。为了保证将来程序稳定性，建议在该权限申明变成必需的
之前，就在清单文件中声明好。

### 指定NFC功能

指定你的应用程序使用NFC，添加 &lt;uses-feature> 标签作为一个 &lt;manifest> 标签的子标签。设置 android:required 属性字段为 true ，这样可以使得你的应用程序只有在NFC可以使用时，才能运行。下面的代码展示了如何指定 &lt;uses-feature> 标签：

	<uses-feature 
		android:name="android.hardware.nfc"
		android:required="true" />

注意，如果你的应用程序将NFC作为一个可选的功能，但期望在NFC不可使用时程序还能继续执行，你就应该设置 android:required 属性字段为 false ，然后在代码中测试NFC的可用性。

### 指定Android Beam文件传输

由于Android Beam文件传输只能在Android 4.1（API Level 16）及以上的平台使用，如果你的应用将Android Beam文件传输作为一个不可缺少的核心模块，那么你必须指定 &lt;uses-sdk> 标签为：android:minSdkVersion="16"。或者，你可以将android:minSdkVersion设置为其它值，然后在代码中测试平台版本，这部分内容将在下一节中展开。

### 测试设备是否支持Android Beam文件传输

为了在Manifest清单文件中，指定NFC是可选的，你应该使用下面的标签：

	<uses-feature android:name="android.hardware.nfc" android:required="false" />

如果设置了android:required="false"，那么你必须要在代码中测试设备是否支持NFC和Android Beam文件传输。

为了在代码中测试Android Beam文件传输，我们先通过PackageManager.hasSystemFeature())和参数FEATURE_NFC，来测试设备是否支持NFC。下一步，通过SDK_INT的值测试系统版本是否支持Android Beam文件传输。如果设备支持Android Beam文件传输，那么获得一个NFC控制器的实例，它能允许你与NFC硬件进行通信，如下所示：

	public class MainActivity extends Activity {
		...
		NfcAdapter mNfcAdapter;
		// Flag to indicate that Android Beam is available
		boolean mAndroidBeamAvailable = false;
		...
		@Override
		protected void onCreate(Bundle savedInstanceState) {
			...
			// NFC isn't available on the device
			if (!PackageManager.hasSystemFeature(PackageManager.FEATURE_NFC)) {
				/*
				 * Disable NFC features here.
				 * For example, disable menu items or buttons that activate
				 * NFC-related features
				 */
				...
				// Android Beam file transfer isn't supported
			} else if (Build.VERSION.SDK_INT < Build.VERSION_CODES.JELLY_BEAN_MR1) {
				// If Android Beam isn't available, don't continue.
				mAndroidBeamAvailable = false;
				/*
				 * Disable Android Beam file transfer features here.
				 */
				...
				// Android Beam file transfer is available, continue
			} else {
				mNfcAdapter = NfcAdapter.getDefaultAdapter(this);
				...
			}
		}
		...
	}

### 创建一个提供文件的回调函数

一旦你确认了设备支持Android Beam文件传输，那么可以添加一个回调函数，当Android Beam文件传输监测到用户希望向另一个支持NFC的设备发送文件时，系统会调用它。在该回调函数中，返回一个Uri对象数组，Android Beam文件传输将URI对应的文件拷贝给要接收这些文件的设备。

要添加这个回调函数，我们需要实现NfcAdapter.CreateBeamUrisCallback接口，和它的方法：createBeamUris())，下面是一个例子：

	public class MainActivity extends Activity {
		...
		// List of URIs to provide to Android Beam
		private Uri[] mFileUris = new Uri[10];
		...
		/**
		* Callback that Android Beam file transfer calls to get
		* files to share
		*/
		private class FileUriCallback implements NfcAdapter.CreateBeamUrisCallback {
			public FileUriCallback() {

			}
			/**
			 * Create content URIs as needed to share with another device
			 */
			@Override
			public Uri[] createBeamUris(NfcEvent event) {
				return mFileUris;
			}
		}
		...
	}

一旦你实现了这个接口，通过调用setBeamPushUrisCallback())将回调函数提供给Android Beam文件传输。下面是一个例子：

	public class MainActivity extends Activity {
		...
		// Instance that returns available files from this app
		private FileUriCallback mFileUriCallback;
		...
		@Override
		protected void onCreate(Bundle savedInstanceState) {
			...
			// Android Beam file transfer is available, continue
			...
			mNfcAdapter = NfcAdapter.getDefaultAdapter(this);
			/*
			 * Instantiate a new FileUriCallback to handle requests for
			 * URIs
			 */
			mFileUriCallback = new FileUriCallback();
			// Set the dynamic callback for URI requests.
			mNfcAdapter.setBeamPushUrisCallback(mFileUriCallback,this);
			...
		}
		...
	}

> Note：你也可以将Uri对象数组通过应用程序的NfcAdapter实例，直接提供给NFC框架。如果你能在NFC触碰事件发生之前，定义这些URI，那么你可以选择使用这个方法。要学习关于这个方法的知识，可以阅读：NfcAdapter.setBeamPushUris())。

### 定义要发送的文件

为了将一个或更多个文件发送给其他支持NFC的设备，需要为每一个文件获取一个File URI（一个具有文件格式（file scheme）的URI），然后将它们添加至一个Uri对象数组中。要传输一个文件，你必须也有读该文件的权限。例如，下面的例子展示的是你如何根据文件名获取它的File URI，然后将URI添加至数组当中：

	/*
	 * Create a list of URIs, get a File,
	 * and set its permissions
	 */
	private Uri[] mFileUris = new Uri[10];
	String transferFile = "transferimage.jpg";
	File extDir = getExternalFilesDir(null);
	File requestFile = new File(extDir, transferFile);
	requestFile.setReadable(true, false);
	// Get a URI for the File and add it to the list of URIs
	fileUri = Uri.fromFile(requestFile);
	if (fileUri != null) {
		mFileUris[0] = fileUri;
	} else {
		Log.e("My Activity", "No File URI available for file.");
	}

## 接收其他设备的文件

Android Beam文件传输将文件拷贝至接收设备上的一个特殊目录。同时使用Android Media Scanner扫描拷贝的文件，并在MediaStore provider中为媒体文件添加对应的条目记录。这节课将向你展示当文件拷贝完成时要如何响应，以及在接收设备上应该如何定位拷贝的文件。

### 响应请求并显示数据

当Android Beam文件传输将文件拷贝至接收设备后，它会发布一个通知，该通知包含有一个Intent，该Intent拥有：ACTION_VIEW这一Action，首个被传输文件的MIME类型，以及一个指向第一个文件的URI。当用户点击了这个通知后，Intent会被发送至系统。为了让你的应用程序能够响应这个Intent，我们需要为响应的Activity所对应的 &lt;activity> 标签添加一个 &lt;intent-filter> 标签，在 &lt;intent-filter> 标签中，添加下面的子标签：

	<action android:name="android.intent.action.VIEW" />

该标签用来匹配从通知发出的Intent，这些Intent具有ACTION_VIEW这一Action。

	<category android:name="android.intent.category.CATEGORY_DEFAULT" />

该标签用来匹配不含有显式Category的Intent对象。

	<data android:mimeType="mime-type" />

匹配一个MIME类型。仅仅指定那些你的应用能够处理的类型。例如，下面的例子展示了如何添加一个intent filter来激活你的activity：

	<activity
		android:name="com.example.android.nfctransfer.ViewActivity"
		android:label="Android Beam Viewer" >
		...
		<intent-filter>
			<action android:name="android.intent.action.VIEW"/>
			<category android:name="android.intent.category.DEFAULT"/>
			...
		</intent-filter>
	</activity>

> Note：不仅仅只有Android Beam文件传输会发送含有ACTION_VIEW这一Action的Intent。在接收设备上的其它应用也有可能会发送含有该Action的intent。我们马上会进一步讨论这一问题。

### 请求文件读权限

如果要读取Android Beam文件传输所拷贝到设备上的文件，需要READ_EXTERNAL_STORAGE权限。例如：

	<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

如果你希望将文件拷贝至应用程序自己的存储区，那么需要的权限改为WRITE_EXTERNAL_STORAGE，另外WRITE_EXTERNAL_STORAGE权限包含了READ_EXTERNAL_STORAGE权限。

> Note：对于Android 4.2.2（API Level 17）及之前版本的系统，READ_EXTERNAL_STORAGE权限仅在用户选择要读文件时才是强制需要的。而在今后的版本中会在所有情况下都需要该权限。为了保证未来应用程序在的稳定性，建议在Manifest清单文件中声明该权限。

由于你的应用对于其自身的内部存储区域具有控制权，所以若要将文件拷贝至应用程序自身的的内部存储区域，写权限是不需要声明的。

### 获取拷贝文件的目录

Android Beam文件传输一次性将所有文件拷贝到目标设备的一个目录中，Android Beam文件传输通知所发出的Intent中包含有URI，该URI指向了第一个被传输的文件。然而，你的应用程序也有可能接收到除了Android Beam文件传输之外的某个来源所发出的含有ACTION_VIEW这一Action的Intent。为了明确你应该如何处理接收的Intent，你需要检查它的Scheme和Authority。可以调用Uri.getScheme())获得URI的Scheme，下面的代码展示了如何确定Scheme并对URI进行相应的处理：

	public class MainActivity extends Activity {
		...
		// A File object containing the path to the transferred files
		private File mParentPath;
		// Incoming Intent
		private Intent mIntent;
		...
		/*
		 * Called from onNewIntent() for a SINGLE_TOP Activity
		 * or onCreate() for a new Activity. For onNewIntent(),
		 * remember to call setIntent() to store the most
		 * current Intent
		 *
		 */
		private void handleViewIntent() {
			...
			// Get the Intent action
			mIntent = getIntent();
			String action = mIntent.getAction();
			/*
			 * For ACTION_VIEW, the Activity is being asked to display data.
			 * Get the URI.
			 */
			if (TextUtils.equals(action, Intent.ACTION_VIEW)) {
				// Get the URI from the Intent
				Uri beamUri = mIntent.getData();
				/*
				 * Test for the type of URI, by getting its scheme value
				 */
				if (TextUtils.equals(beamUri.getScheme(), "file")) {
					mParentPath = handleFileUri(beamUri);
				} else if (TextUtils.equals(beamUri.getScheme(), "content")) {
					mParentPath = handleContentUri(beamUri);
				}
			}
			...
		}
		...
	}

### 从File URI中获取目录

如果接收的Intent包含一个File URI，则该URI包含了一个文件的绝对文件名，它包括了完整的路径和文件名。对于Android Beam文件传输来说，目录路径指向了其它被传输文件的位置（如果有其它传输文件的话），要获得这个目录路径，需要取得URI的路径部分（URI中除去“file:”前缀的部分），根据路径创建一个File对象，然后获取这个File的父目录：

	public String handleFileUri(Uri beamUri) {
		// Get the path part of the URI
		String fileName = beamUri.getPath();
		// Create a File object for this filename
		File copiedFile = new File(fileName);
		// Get a string containing the file's parent directory
		return copiedFile.getParent();
	}
	...

### 从Content URI获取目录

如果接收的Intent包含一个Content URI，这个URI可能指向的是存储于MediaStore Content Provider的目录和文件名。你可以通过检测URI的Authority值来判断它是否是来自于MediaStore的Content URI。一个MediaStore的Content URI可能来自Android Beam文件传输也可能来自其它应用程序，但不管怎么样，你都能根据该Content URI获得一个目录路径和文件名。

你也可以接收一个含有ACTION_VIEW这一Action的Intent，它包含的Content URI针对于Content Provider，而不是MediaStore，在这种情况下，这个Content URI不包含MediaStore的Authority，且这个URI一般不指向一个目录。

> Note：对于Android Beam文件传输，接收在含有ACTION_VIEW的Intent中的Content URI时，如果第一个接收的文件，其MIME类型为“audio/”，“image/”或者“video/*”，Android Beam文件传输会在它存储传输文件的目录内运行Media Scanner，以此为媒体文件添加索引。同时Media Scanner将结果写入MediaStore的Content Provider，之后它将第一个文件的Content URI回递给Android Beam文件传输。这个Content URI就是你在通知Intent中所接收到的。要获得第一个文件的目录，你需要使用该Content URI从MediaStore中获取它。

### 确定Content Provider

为了明确你能从Content URI中获取文件目录，你可以通过调用Uri.getAuthority())获取URI的Authority，以此确定与该URI相关联的Content Provider。其结果有两个可能的值：

* MediaStore.AUTHORITY:表明这个URI关联了被MediaStore记录的一个文件或者多个文件。可以从MediaStore中获取文件的全名，目录名就自然可以从文件全名中获取。
* 其他值:来自其他Content Provider的Content URI。可以显示与该Content URI相关联的数据，但是不要尝试去获取文件目录。

要从MediaStore的Content URI中获取目录，我们需要执行一个查询操作，它将Uri参数指定为收到的ContentURI，将MediaColumns.DATA列作为投影（Projection）。返回的Cursor对象包含了URI所代表的文件的完整路径和文件名。该目录路径下还包含了由Android Beam文件传输传送到该设备上的其它文件。下面的代码展示了你要如何测试Content URI的Authority，并获取传输文件的路径和文件名：

	...
	public String handleContentUri(Uri beamUri) {
		// Position of the filename in the query Cursor
		int filenameIndex;
		// File object for the filename
		File copiedFile;
		// The filename stored in MediaStore
		String fileName;
		// Test the authority of the URI
		if (!TextUtils.equals(beamUri.getAuthority(), MediaStore.AUTHORITY)) {
			/*
			 * Handle content URIs for other content providers
			 */
			// For a MediaStore content URI
		} else {
			// Get the column that contains the file name
			String[] projection = { MediaStore.MediaColumns.DATA };
			Cursor pathCursor = getContentResolver().query(beamUri, projection,null, null, null);
			// Check for a valid cursor
			if (pathCursor != null && pathCursor.moveToFirst()) {
				// Get the column index in the Cursor
				filenameIndex = pathCursor.getColumnIndex(MediaStore.MediaColumns.DATA);
				// Get the full file name including path
				fileName = pathCursor.getString(filenameIndex);
				// Create a File object for the filename
				copiedFile = new File(fileName);
				// Return the parent directory of the file
				return new File(copiedFile.getParent());
			} else {
				// The query didn't work; return null
				return null;
			}
		}
	}
	...