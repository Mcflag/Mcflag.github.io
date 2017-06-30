title: Android官方培训教程5——数据保存
date: 2015-08-30 15:28:33
tags: [Android官方培训教程]
---

## 摘要
数据保存
<!--more-->

## 保存到Preference

如果你有一个相对较小的key-value集合需要保存，你应该使用SharedPreferences APIs。 SharedPreferences 对象指向了一个保存key-value pairs的文件，并为读写他们提供了简单的方法。每一个 SharedPreferences 文件均由framework管理，其既可以是私有的，也可以是共享的。 

Note： SharedPreferences APIs 仅仅提供了读写key-value对的功能，请不要与Preference APIs相混淆。后者可以帮助你建立一个设置用户配置的页面（尽管它实际上是使用SharedPreferences 来实现保存用户配置的)。

### 获取SharedPreference

你可以通过下面两个方法之一来创建或者访问shared preference 文件:

* getSharedPreferences() — 如果你需要多个通过名称参数来区分的shared preference文件, 名称可以通过第一个参数来指定。你可以在你的app里面通过任何一个Context 来执行这个方法。
* getPreferences() — 当你的activity仅仅需要一个shared preference文件时。因为这个方法会检索activity下的默认的shared preference文件，并不需要提供文件名称。

例如：下面的示例是在一个 Fragment 中被执行的，它会访问名为 R.string.preference_file_key 的shared preference文件，并使用private模式来打开它，这种情况下，该文件仅能被你的app访问了。

	Context context = getActivity();
	SharedPreferences sharedPref = context.getSharedPreferences(getString(R.string.preference_file_key), Context.MODE_PRIVATE);

当命名你的shared preference文件时，你应该像 "com.example.myapp.PREFERENCE_FILE_KEY" 这样来命名。当然，如果你的activity仅仅需要一个shared preference文件时，你可以使用getPreferences()方法：

	SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);

Caution: 如果你创建了一个MODE_WORLD_READABLE或者MODE_WORLD_WRITEABLE 模式的shared preference文件，那么任何其他的app只要知道文件名，就可以访问这个文件。

### 写Shared Preference

为了写 shared preferences 文件，需要通过执行edit()来创建一个 SharedPreferences.Editor。

通过类似putInt()与putString()方法来传递keys与values。然后执行commit() 来提交改变。 (**后来有建议除非是出于线程同步的需要，否则请使用apply()方法来替代commit()，因为后者有可能会卡到UI Thread。**)

	SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);
	SharedPreferences.Editor editor = sharedPref.edit();
	editor.putInt(getString(R.string.saved_high_score), newHighScore);
	editor.commit();

### 读Shared Preference

为了从shared preference中检索读取数据，可以通过类似 getInt() 与 getString()等方法来读取。在那些方法里面传递你想要获取的value对应的key，并提供一个默认的value作为查找的key不存在时函数的返回值。如下：

	SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);
	long default = getResources().getInteger(R.string.saved_high_score_default));
	long highScore = sharedPref.getInt(getString(R.string.saved_high_score), default);

## 保存到文件

Android使用与其他平台类似的基于磁盘的文件系统(disk-based file systems)。File 对象非常适合用于读写流式顺序的数据。如图片文件或是网络中交换的数据。

### 存储在内部还是外部

所有的Android设备都有两个文件存储区域："internal" 与 "external" 存储。 那两个名称来自于早先的Android系统中，当时的大多设备都内置了不可变的内存（internal storage)，然后再加上一个类似SD card（external storage）这样可以卸载的存储部件。后来有一些设备把"internal" 与 "external" 的部分都做成不可卸载的内置存储了，虽然如此，但是这一整块还是从逻辑上有被划分为"internal"与"external"的。只是现在不再以是否可以卸载来区分了。 下面列出了两者的区别：

Internal storage:

* 总是可用的
* 这里的文件默认是只能被你的app所访问的。
* 当用户卸载你的app的时候，系统会把internal里面的相关文件都清除干净。
* Internal是在你想确保不被用户与其他app所访问的最佳存储区域。

External storage:

* 并不总是可用的，因为用户有时会通过USB存储模式挂载外部存储器，当取下挂载的这部分后，就无法对其进行访问了。
* 是大家都可以访问的，因此你可能会失去保存在这里的文件的访问控制权。
* 当用户卸载你的app时，系统仅仅会删除external根目录（getExternalFilesDir()）下的相关文件。
* External是在你不需要严格的访问权限并且你希望这些文件能够被其他app所共享或者是允许用户通过电脑访问时的最佳存储区域。

### 获取External存储的权限

为了写数据到external storage, 你必须在你的manifest文件中请求WRITE_EXTERNAL_STORAGE权限：

	<manifest ...>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	</manifest>

对于internal storage，你不需要声明任何权限，因为你的程序默认就有读写程序目录下的文件的权限。

### 保存到Internal Storage

当保存文件到internal storage时，你可以通过执行下面两个方法之一来获取合适的目录作为 FILE 的对象：

* getFilesDir() : 返回一个File，代表了你的app的internal目录。
* getCacheDir() : 返回一个File，代表了你的app的internal缓存目录。请确保这个目录下的文件在一旦不再需要的时候能够马上被删除，并对其大小进行合理限制，例如1MB 。如果系统的内部存储空间不够，会自行选择删除缓存文件。

为了在那些目录下创建一个新的文件，你可以使用File() 构造器，如下：

	File file = new File(context.getFilesDir(), filename);

同样，你也可以执行openFileOutput() 来获取一个 FileOutputStream 用于写文件到internal目录。如下：

	String filename = "myfile";
	String string = "Hello world!";
	FileOutputStream outputStream;
	try {
		outputStream = openFileOutput(filename, Context.MODE_PRIVATE);
		outputStream.write(string.getBytes());
		outputStream.close();
	} catch (Exception e) {
		e.printStackTrace();
	}

如果，你需要缓存一些文件，你可以使用createTempFile()。例如：下面的方法从URL中抽取了一个文件名，然后再在程序的internal缓存目录下创建了一个以这个文件名命名的文件。

	public File getTempFile(Context context, String url) {
		File file;
		try {
			String fileName = Uri.parse(url).getLastPathSegment();
			file = File.createTempFile(fileName, null, context.getCacheDir());
		catch (IOException e) {
			// Error while creating file
		}
		return file;
	}

Note: 你的app的internal storage 目录是以你的app的包名作为标识存放在Android文件系统的特定目录下[data/data/com.example.xx]。 从技术上讲，如果你设置文件为可读的，那么其他app就可以读取你的internal文件。然而，其他app需要知道你的包名与文件名。若是你没有设置为可读或者可写，其他app是没有办法读写的。因此只要你使用MODE_PRIVATE ，那么这些文件就不可能被其他app所访问。

### 保存文件到External Storage

因为external storage可能是不可用的，比如遇到SD卡被拔出等情况时。因此你应该在访问之前去检查其是否可用。你可以通过执行 getExternalStorageState()来查询external storage的状态。如果返回的状态是MEDIA_MOUNTED, 那么你可以读写。示例如下：

	/* Checks if external storage is available for read and write */
	public boolean isExternalStorageWritable() {
		String state = Environment.getExternalStorageState();
		if (Environment.MEDIA_MOUNTED.equals(state)) {
			return true;
		}
		return false;
	}
	/* Checks if external storage is available to at least read */
	public boolean isExternalStorageReadable() {
		String state = Environment.getExternalStorageState();
		if (Environment.MEDIA_MOUNTED.equals(state) || Environment.MEDIA_MOUNTED_READ_ONLY.equals(state)) {
			return true;
		}
		return false;
	}

尽管external storage对于用户与其他app是可修改的，那么你可能会保存下面两种类型的文件。

* Public files :这些文件对与用户与其他app来说是public的，当用户卸载你的app时，这些文件应该保留。例如，那些被你的app拍摄的图片或者下载的文件。
* Private files: 这些文件应该是被你的app所拥有的，它们应该在你的app被卸载时删除掉。尽管由于存储在externalstorage，那些文件从技术上而言可以被用户与其他app所访问，但实际上那些文件对于其他app是没有意义的。因此，当用户卸载你的app时，系统会删除你的app的private目录。例如，那些被你的app下载的缓存文件。

如果你想要保存文件为public形式的，请使用**getExternalStoragePublicDirectory()**方法来获取一个 File 对象来表示存储在external storage的目录。这个方法会需要你带有一个特定的参数来指定这些public的文件类型，以便于与其他public文件进行分类。参数类型包括DIRECTORY_MUSIC 或者 DIRECTORY_PICTURES. 如下:

	public File getAlbumStorageDir(String albumName) {
		// Get the directory for the user's public pictures directory.
		File file = new File(Environment.getExternalStoragePublicDirectory(
		Environment.DIRECTORY_PICTURES), albumName);
		if (!file.mkdirs()) {
			Log.e(LOG_TAG, "Directory not created");
		}
		return file;
	}

如果你想要保存文件为私有的方式，你可以通过执行**getExternalFilesDir()** 来获取相应的目录，并且传递一个指示文件类型的参数。每一个以这种方式创建的目录都会被添加到external storage封装你的app目录下的参数文件夹下（如下则是albumName）。这下面的文件会在用户卸载你的app时被系统删除。如下示例：

	public File getAlbumStorageDir(Context context, String albumName) {
		// Get the directory for the app's private pictures directory.
		File file = new File(context.getExternalFilesDir(
		Environment.DIRECTORY_PICTURES), albumName);
		if (!file.mkdirs()) {
			Log.e(LOG_TAG, "Directory not created");
		}
		return file;
	}

如果刚开始的时候，没有预定义的子目录存放你的文件，你可以在 getExternalFilesDir()方法中传递 null . 它会返回你的app在external storage下的private的根目录。

请记住，getExternalFilesDir() 方法会创建的目录会在app被卸载时被系统删除。如果你的文件想在app被删除时仍然保留，请使用getExternalStoragePublicDirectory()。

不管你是使用 getExternalStoragePublicDirectory() 来存储可以共享的文件，还是使用 getExternalFilesDir() 来储存那些对与你的app来说是私有的文件，有一点很重要，那就是你要使用那些类似 DIRECTORY_PICTURES 的API的常量。那些目录类型参数可以确保那些文件被系统正确的对待。例如，那些以 DIRECTORY_RINGTONES 类型保存的文件就会被系统的media scanner认为是ringtone而不是音乐。

### 查询剩余空间

如果你事先知道你想要保存的文件大小，你可以通过执行getFreeSpace() or getTotalSpace() 来判断是否有足够的空间来保存文件，从而避免发生IOException。那些方法提供了当前可用的空间还有存储系统的总容量。

然而，系统并不能保证你可以写入通过 getFreeSpace() 查询到的容量文件， 如果查询的剩余容量比你的文件大小多几MB，或者说文件系统使用率还不足90%，这样则可以继续进行写的操作，否则你最好不要写进去。

> Note：你并没有被强制要求在写文件之前一定有要去检查剩余容量。你可以尝试先做写的动作，然后通过捕获IOException 。这种做法仅适合于你事先并不知道你想要写的文件的确切大小。例如，如果在把PNG图片转换成JPEG之前，你并不知道最终生成的图片大小是多少。

### 删除文件

你应该在不需要使用某些文件的时候，删除它。删除文件最直接的方法是直接执行文件的 delete() 方法。

	myFile.delete();

如果文件是保存在internal storage，你可以通过 Context 来访问并通过执行 deleteFile() 进行删除

	myContext.deleteFile(fileName);

> Note: 当用户卸载你的app时，android系统会删除以下文件：所有保存到internal storage的文件。所有使用getExternalFilesDir()方式保存在external storage的文件。然而，通常来说，你应该手动删除所有通过 getCacheDir() 方式创建的缓存文件，以及那些不会再用到的文件。

还有一点需要注意的是如果要删除的file是一个路径，也就是文件夹的话，使用delete()只能删除空文件夹。如果里面还有内容需要使用递归的方法来清空整个文件夹，最后才能删除目标file。

	public static void delete(File file) {
		if (file.isFile()) {
			file.delete();
			return;
		}

		if (file.isDirectory()) {
			File[] childFiles = file.listFiles();
			if (childFiles == null || childFiles.length == 0) {
				file.delete();
				return;
			}

			for (int i = 0; i < childFiles.length; i++) {
				delete(childFiles[i]);
			}
			file.delete();
		}
	}

## 保存到数据库

对于重复或者结构化的数据（如联系人信息）等保存到DB是个不错的主意。

### 定义Schema与Contract

SQL中一个中重要的概念是schema：一种DB结构的正式声明。schema是从你创建DB的SQL语句中生成的。你可能会发现创建一个伴随类（companion class）是很有益的，这个类称为合约类（contract class）,它用一种系统化并且自动生成文档的方式，显示指定了你的schema样式。

Contract Clsss是一些常量的容器。它定义了例如URIs，表名，列名等。这个contract类允许你在同一个包下与其他类使用同样的常量。 它让你只需要在一个地方修改列名，然后这个列名就可以自动传递给你整个code。

一个组织你的contract类的好方法是在你的类的根层级定义一些全局变量，然后为每一个table来创建内部类。

> Note：通过实现 BaseColumns 的接口，你的内部类可以继承到一个名为_ID的主键，这个对于Android里面的一
些类似cursor adaptor类是很有必要的。这么做不是必须的，但这样能够使得你的DB与Android的framework能够
很好的相容。

例如，下面的例子定义了表名与这个表的列名：

	public final class FeedReaderContract {
		// To prevent someone from accidentally instantiating the contract class,
		// give it an empty constructor.
		public FeedReaderContract() {}

		/* Inner class that defines the table contents */
		public static abstract class FeedEntry implements BaseColumns {
			public static final String TABLE_NAME = "entry";
			public static final String COLUMN_NAME_ENTRY_ID = "entryid";
			public static final String COLUMN_NAME_TITLE = "title";
			public static final String COLUMN_NAME_SUBTITLE = "subtitle";
			...
		}
	}

### 使用SQL Helper创建DB

当你定义好了你的DB的结构之后，你应该实现那些创建与维护db与table的方法。下面是一些典型的创建与删除table的语句。

	private static final String TEXT_TYPE = " TEXT";
	private static final String COMMA_SEP = ",";
	private static final String SQL_CREATE_ENTRIES =
		"CREATE TABLE " + FeedReaderContract.FeedEntry.TABLE_NAME + " (" +
		FeedReaderContract.FeedEntry._ID + " INTEGER PRIMARY KEY," +
		FeedReaderContract.FeedEntry.COLUMN_NAME_ENTRY_ID + TEXT_TYPE + COMMA_SEP +
		FeedReaderContract.FeedEntry.COLUMN_NAME_TITLE + TEXT_TYPE + COMMA_SEP +
		... // Any other options for the CREATE command
		" )";

	private static final String SQL_DELETE_ENTRIES =
		"DROP TABLE IF EXISTS " + TABLE_NAME_ENTRIES;

就像保存文件到设备的internal storage 一样，Android会保存db到你的程序的private的空间上。你的数据是受保护的，因为那些区域默认是私有的，不可被其他程序所访问。

在SQLiteOpenHelper类中有一些很有用的APIs。当你使用这个类来做一些与你的db有关的操作时，系统会对那些有可能比较耗时的操作（例如创建与更新等）在真正需要的时候才去执行，而不是在app刚启动的时候就去做那些动作。你所需要做的仅仅是执行getWritableDatabase()或者getReadableDatabase()。

> Note：因为那些操作可能是很耗时的，请确保你在background thread（AsyncTask or IntentService）里面去执行 getWritableDatabase() 或者 getReadableDatabase()。

为了使用 SQLiteOpenHelper, 你需要创建一个子类并重写onCreate(), onUpgrade()与onOpen()等callback方法。你也许还需要实现onDowngrade(), 但是这并不是必需的。例如，下面是一个实现了SQLiteOpenHelper 类的例子：

	public class FeedReaderDbHelper extends SQLiteOpenHelper {
		// If you change the database schema, you must increment the database version.
		public static final int DATABASE_VERSION = 1;
		public static final String DATABASE_NAME = "FeedReader.db";

		public FeedReaderDbHelper(Context context) {
			super(context, DATABASE_NAME, null, DATABASE_VERSION);
		}

		public void onCreate(SQLiteDatabase db) {
			db.execSQL(SQL_CREATE_ENTRIES);
		}

		public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
			// This database is only a cache for online data, so its upgrade policy is
			// to simply to discard the data and start over
			db.execSQL(SQL_DELETE_ENTRIES);
			onCreate(db);
		}

		public void onDowngrade(SQLiteDatabase db, int oldVersion, int newVersion) {
			onUpgrade(db, oldVersion, newVersion);
		}
	}

为了访问你的db，需要实例化你的 SQLiteOpenHelper的子类：

	FeedReaderDbHelper mDbHelper = new FeedReaderDbHelper(getContext());

### 添加信息到DB

通过传递一个 ContentValues 对象到insert()方法：

	// Gets the data repository in write mode
	SQLiteDatabase db = mDbHelper.getWritableDatabase();

	// Create a new map of values, where column names are the keys
	ContentValues values = new ContentValues();
	values.put(FeedReaderContract.FeedEntry.COLUMN_NAME_ENTRY_ID, id);
	values.put(FeedReaderContract.FeedEntry.COLUMN_NAME_TITLE, title);
	values.put(FeedReaderContract.FeedEntry.COLUMN_NAME_CONTENT, content);

	// Insert the new row, returning the primary key value of the new row
	long newRowId;
	newRowId = db.insert(
		FeedReaderContract.FeedEntry.TABLE_NAME,
		FeedReaderContract.FeedEntry.COLUMN_NAME_NULLABLE,
		values);

insert() 方法的第一个参数是table名，**第二个参数会使得系统自动对那些 ContentValues 没有提供数据的列填充数据为 null** ，如果第二个参数传递的是null，那么系统则不会对那些没有提供数据的列进行填充。

### 从DB中读取信息

为了从DB中读取数据，需要使用query()方法， 传递你需要查询的条件。查询后会返回一个 Cursor 对象。

	SQLiteDatabase db = mDbHelper.getReadableDatabase();

	// Define a projection that specifies which columns from the database
	// you will actually use after this query.
	String[] projection = {
		FeedReaderContract.FeedEntry._ID,
		FeedReaderContract.FeedEntry.COLUMN_NAME_TITLE,
		FeedReaderContract.FeedEntry.COLUMN_NAME_UPDATED,
		...
		};

	// How you want the results sorted in the resulting Cursor
	String sortOrder =	FeedReaderContract.FeedEntry.COLUMN_NAME_UPDATED + " DESC";

	Cursor c = db.query(
		FeedReaderContract.FeedEntry.TABLE_NAME, // The table to query
		projection, // The columns to return
		selection, // The columns for the WHERE clause
		selectionArgs, // The values for the WHERE clause
		null, // don't group the rows
		null, // don't filter by row groups
		sortOrder // The sort order
		);

要查询在cursor中的行，使用cursor的其中一个move方法，但必须在读取值之前调用。一般来说你应该先调用 moveToFirst() 函数，将读取位置置于结果集最开始的位置。对每一行，你可以使用cursor的其中一个get方法比如 getString() 或 getLong() 获取列的值。对于每一个get方法必须传递你想要获取的列的索引位置(index position)，索引位置可以通过调用 getColumnIndex() 或 getColumnIndexOrThrow() 获得。

下面是演示如何从course对象中读取数据信息：

	cursor.moveToFirst();
	long itemId = cursor.getLong(
		cursor.getColumnIndexOrThrow(FeedReaderContract.FeedEntry._ID)
	);

### 删除DB中的信息

和查询信息一样，删除数据同样需要提供一些删除标准。DB的API提供了一个防止SQL注入的机制来创建查询与删除标准。

> SQL Injection：(随着B/S模式应用开发的发展，使用这种模式编写应用程序的程序员也越来越多。但是由于程序员的水平及经验也参差不齐，相当大一部分程序员在编写代码的时候，没有对用户输入数据的合法性进行判断，使应用程序存在安全隐患。用户可以提交一段数据库查询代码，根据程序返回的结果，获得某些他想得知的数据，这就是所谓的SQL Injection，即SQL注入)

这个机制把查询语句划分为选项条款与选项参数两部分。条款部分定义了查询的列的特征，参数部分用来测试是否符合前面的条款。(原文，The clause defines the columns to look at, and also allows you to combine column tests. The arguments are values to test against that are bound into the clause.) 因为处理的结果与通常的SQL语句不同，这样可以避免SQL注入问题。

	// Define 'where' part of query.
	String selection = FeedReaderContract.FeedEntry.COLUMN_NAME_ENTRY_ID + " LIKE ?";
	// Specify arguments in placeholder order.
	String[] selelectionArgs = { String.valueOf(rowId) };
	// Issue SQL statement.
	db.delete(table_name, mySelection, selectionArgs);

### 更新数据

当你需要修改DB中的某些数据时，使用 update() 方法。更新操作结合了插入与删除的语法。

	SQLiteDatabase db = mDbHelper.getReadableDatabase();

	// New value for one column
	ContentValues values = new ContentValues();
	values.put(FeedReaderContract.FeedEntry.COLUMN_NAME_TITLE, title);

	// Which row to update, based on the ID
	String selection = FeedReaderContract.FeedEntry.COLUMN_NAME_ENTRY_ID + " LIKE ?";
	String[] selectionArgs = { String.valueOf(rowId) };

	int count = db.update(
		FeedReaderDbHelper.FeedEntry.TABLE_NAME,
		values,
		selection,
		selectionArgs);