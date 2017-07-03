title: Android官方培训教程10——多媒体打印
date: 2015-09-03 11:10:07
tags: [Android官方培训教程]
categories: [Android官方培训教程]
---

## 摘要
多媒体打印
<!--more-->

## 打印

在Android 4.4（API Level 19）及更高版本的系统中，框架提供了直接从Android应用程序打印图片和文字的服务。这节将展示如何在你的应用程序中启用打印：包括打印图片，HTML页面以及创建自定义的打印文档。

### 打印照片

拍摄并分享照片是移动设备最流行的用法之一。如果你的应用拍摄了照片，并期望可以展示他们，或者允许用户共享照片，你就应该考虑让你的应用可以打印出这些照片来。Android Support Library提供了一个PrintHelper类，通过这一函数，仅仅使用很少量的代码和一些简单的打印布局配置集，就能够进行照片打印。

### 打印一幅图片

Android Support Library中的PrintHelper类提供了一种打印图片的简单方法。该类有一个单一的布局选项：setScaleMode())，它能允许你使用下面的两个选项之一：

* SCALE_MODE_FIT：这个选项会调整图像的大小，这样整个图像就会在打印有效区域内全部显示出来（等比例缩
放至长和宽都包含在纸张页面内）。
* SCALE_MODE_FILL：这个选项同样会等比例地调整图像的大小使图像充满整个打印有效区域，即让图像充满整
个纸张页面。这就意味着如果选择这个选项，那么图片的一部分（顶部和底部，或者左侧和右侧）将无法打印出
来。如果你不设置图像的打印布局选项，该模式将是默认的图像拉伸方式。

这两个setScaleMode())的图像布局选项都会保持图像原有的长宽比。下面的代码展示了如何创建一个PrintHelper类的
实例，设置布局选项，并开始打印进程：

	private void doPhotoPrint() {
		PrintHelper photoPrinter = new PrintHelper(getActivity());
		photoPrinter.setScaleMode(PrintHelper.SCALE_MODE_FIT);
		Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.droids);
		photoPrinter.printBitmap("droids.jpg - test print", bitmap);
	}

这个方法可以作为一个菜单项的Action来被调用。注意对于那些不一定被支持的菜单项（比如打印），应该放置在“更多菜单（overflow menu）”中。

在printBitmap())被调用之后，你的应用就不再需要进行其他的操作了。之后Android打印界面就会出现，允许用户选择一个打印机和它的打印选项。用户可以打印图像或者取消这一次操作。如果用户选择了打印图像，那么一个打印任务将会被创建，同时在系统的通知栏中会显示一个打印提醒通知。

如果你希望在你的打印输出中包含更多的内容，而不仅仅是一张图片，那么就必须构造一个打印文档。

### 打印HTML文档

如果要在Android上打印比一副照片更丰富的内容，我们需要将文本和图片组合在一个待打印的文档中。Android框架提供了一种使用HTML语言来构建文档并进行打印的方法，它使用的代码数量是很小的。

WebView类在Android 4.4（API Level 19）中得到了更新，使得它可以打印HTML内容。这个类允许你加载一个本地HTML资源或者从网页下载一个页面，创建一个打印任务，并把它交给Android打印服务。

### 加载一个HTML文档

用WebView打印一个HTML文档，会涉及到加载一个HTML资源，或者用一个字符串构建HTML文档。这一节将描述如何构建一个HTML的字符串并将它加载到WebView中，以备打印。

该View对象一般被用来作为一个activity布局的一部分。然而，如果你的应用当前并没有使用WebView，你可以创建一个该类的实例，以进行打印。创建该自定义View的主要步骤是：

* 在HTML资源加载完毕后，创建一个WebViewClient用来启动一个打印任务。
* 加载HTML资源至WebView对象中。

下面的代码展示了如何创建一个简单的WebViewClient并且加载一个动态创建的HTML文档：

	private WebView mWebView;
	private void doWebViewPrint() {
		// Create a WebView object specifically for printing
		WebView webView = new WebView(getActivity());
		webView.setWebViewClient(new WebViewClient() {
			public boolean shouldOverrideUrlLoading(WebView view, String url) {
				return false;
			}
			@Override
			public void onPageFinished(WebView view, String url) {
				Log.i(TAG, "page finished loading " + url);
				createWebPrintJob(view);
				mWebView = null;
			}
		});

		// Generate an HTML document on the fly:
		String htmlDocument = "<html><body><h1>Test Content</h1><p>Testing, " + "testing, testing...</p></body></html>";
		webView.loadDataWithBaseURL(null, htmlDocument, "text/HTML", "UTF-8", null);
		// Keep a reference to WebView object until you pass the PrintDocumentAdapter
		// to the PrintManager
		mWebView = webView;
	}

> Note： 请确保在WebViewClient)中的onPageFinished())方法内调用创建打印任务的方法。如果你没有等到页面加载完毕就进行打印，打印的输出可能会不完整或空白，甚至可能会失败。
> Note：在上面的样例代码中，保留了一个WebView对象实例的引用，这样能够确保它不会在打印任务创建之前就被垃圾回收器所回收。务必在你自己的实现中也这样做，否则打印的进程可能会无法继续执行。

如果你希望页面中包含图像，将这个图像文件放置在你的工程的“assets/”目录中，并指定一个基URL（Base URL），并将它作为loadDataWithBaseURL())方法的第一个参数，就像下面所显示的一样：

	webView.loadDataWithBaseURL("file:///android_asset/images/", htmlBody, "text/HTML", "UTF-8", null);

你也可以加载一个需要打印的网页，具体做法是将loadDataWithBaseURL())方法替换为loadUrl())，如下所示：

	// Print an existing web page (remember to request INTERNET permission!):
	webView.loadUrl("http://developer.android.com/about/index.html");

当使用WebView创建打印文档时，你要注意下面的一些限制：

* 你不能为文档添加页眉和页脚，包括页号。
* HTML文档的打印选项不包含选择打印的页数范围，例如：对于一个10页的HTMl文档，只打印2到4页是不可以的。
* 一个WebView的实例只能在同一时间处理一个打印任务。
* 若一个HTML文档包含CSS打印属性，比如一个landscape属性，这是不被支持的。
* 你不能通过一个HTML文档中的JavaScript脚本来激活打印。

> Note：一旦在布局中包含的WebView对象将文档加载完毕后，就可以打印WebView对象的内容了。

### 创建一个打印任务

在创建了WebView并加载了你的HTML内容之后，你的应用就已经几乎完成了属于它的任务。接下来，我们需要访问PrintManager，创建一个打印适配器，并在最后，创建一个打印任务。下面的代码展示了如何执行这些步骤：

	private void createWebPrintJob(WebView webView) {
		// Get a PrintManager instance
		PrintManager printManager = (PrintManager) getActivity().getSystemService(Context.PRINT_SERVICE);

		// Get a print adapter instance
		PrintDocumentAdapter printAdapter = webView.createPrintDocumentAdapter();

		// Create a print job with name and adapter instance
		String jobName = getString(R.string.app_name) + " Document";
		PrintJob printJob = printManager.print(jobName, printAdapter,
		new PrintAttributes.Builder().build());

		// Save the job object for later status checking
		mPrintJobs.add(printJob);
	}

这个例子保存了一个PrintJob对象的实例，以供我们的应用将来使用，当然这是不必须的。你的应用可以使用这个对象来跟踪打印任务执行时的进度。当你希望监控在你应用中的打印任务是否完成，是否失败或者是否被用户取消，这个方法非常有用。另外，不需要创建一个应用内置的通知，因为打印框架会自动的创建一个该打印任务的系统通知。

### 打印自定义文档

对于有些应用，比如绘图应用，页面布局应用和其它一些关注于图像输出的应用，创造出美丽的打印页面将是它的核心功能。在这种情况下，仅仅打印一幅图片或一个HTML文档就不够了。这类应用的打印输出需要精确地控制每一个会在页面中显示的对象，包括字体，文本流，分页符，页眉，页脚和一些图像元素等等。

想要创建一个完全自定义的打印文档，需要投入比之前讨论的方法更多的编程精力。你必须构建可以和打印框架相互通信的组件，调整打印参数，绘制页面元素并管理多个页面的打印。

### 连接打印管理器

当你的应用直接管理打印进程时，在收到来自用户的打印请求后，第一步要做的是连接Android打印框架并获取一个PrintManager类的实例。该类允许你初始化一个打印任务并开始打印任务的生命周期。下面的代码展示了如何获得打印管理器并开始打印进程。

	private void doPrint() {
		// Get a PrintManager instance
		PrintManager printManager = (PrintManager) getActivity().getSystemService(Context.PRINT_SERVICE);
		// Set job name, which will be displayed in the print queue
		String jobName = getActivity().getString(R.string.app_name) + " Document";
		// Start a print job, passing in a PrintDocumentAdapter implementation
		// to handle the generation of a print document
		printManager.print(jobName, new MyPrintDocumentAdapter(getActivity()), null); //
	}

上面的代码展示了如何命名一个打印任务以及如何设置一个PrintDocumentAdapter类的实例，它负责处理打印生命周期的每一步。

> Note：print())方法的最后一个参数接收一个PrintAttributes对象。你可以使用这个参数向打印框架进行一些打印设置，以及基于前一个打印周期的预设，从而改善用户体验。你也可以使用这个参数对打印内容进行一些更符合实际情况的设置，比如当打印一幅照片时，设置打印的方向与照片方向一致。

### 创建一个打印适配器

打印适配器负责与Android打印框架交互并处理打印过程的每一步。这个过程需要用户在创建打印文档前选择打印机和打印选项。由于用户可以选择不同性能的打印机，不同的页面尺寸或不同的页面方向，因此这些选项可能会影响最终的打印效果。当这些选项配置好之后，打印框架会寻求适配器进行布局并生成一个打印文档，以此作为打印的前期准备。一旦用户点击了打印按钮，框架会将最终的打印文档传递给一个打印提供程序（Print Provider）供打印输出。在打印过程中，用户可以选择取消打印，所以你的打印适配器必须监听并响应取消打印的请求。

PrintDocumentAdapter抽象类负责处理打印的生命周期，它有四个主要的回调方法。你必须在你的打印适配器中实现这
些方法，以此来正确地和Android打印框架进行交互：

* onStart())：一旦打印进程开始，该方法就将被调用。如果你的应用有任何一次性的准备任务要执行，比如获取一个要打印数据的快照，那么让它们在此处执行。在你的适配器中，这个回调方法不是必须实现的。
* onLayout())：每当用户改变了影响打印输出的设置时（比如改变了页面的尺寸，或者页面的方向）该函数将会被调用，以此给你的应用一个机会去重新计算打印页面的布局。另外，该方法必须返回打印文档包含多少页面。
* onWrite())：该方法调用后，会将打印页面渲染成一个待打印的文件。该方法可以在onLayout())方法被调用后调用一次或多次。
* onFinish())：一旦打印进程结束后，该方法将会被调用。如果你的应用有任何一次性销毁任务要执行，让这些任务在该方法内执行。这个回调方法不是必须实现的。

> Note：这些适配器的回调方法会在应用的主线程上被调用。如果这些方法的实现在执行时可能需要花费大量的时间，那么应该将他们放在另一个线程里执行。例如：你可以将布局或者写入打印文档的操作封装在一个AsyncTask对象中。

### 计算打印文档信息

在实现PrintDocumentAdapter类时，你的应用必须能够指定出所创建文档的类型，计算出打印任务所需要打印的总页数，并提供打印页面的尺寸信息。在实现适配器的onLayout())方法时，我们执行这些计算，并提供与理想的输出相关的一些信息，这些信息可以在PrintDocumentInfo类中获取，包括页数和内容类型。下面的例子展示了PrintDocumentAdapter中onLayout())方法的基本实现：

	@Override
	public void onLayout(PrintAttributes oldAttributes,
				PrintAttributes newAttributes,
				CancellationSignal cancellationSignal,
				LayoutResultCallback callback,
				Bundle metadata) {
		// Create a new PdfDocument with the requested page attributes
		mPdfDocument = new PrintedPdfDocument(getActivity(), newAttributes);

		// Respond to cancellation request
		if (cancellationSignal.isCancelled() ) {
			callback.onLayoutCancelled();
			return;
		}
		// Compute the expected number of printed pages
		int pages = computePageCount(newAttributes);
		if (pages > 0) {
			// Return print information to print framework
			PrintDocumentInfo info = new PrintDocumentInfo
						.Builder("print_output.pdf")
						.setContentType(PrintDocumentInfo.CONTENT_TYPE_DOCUMENT)
						.setPageCount(pages);
						.build();
			// Content layout reflow is complete
			callback.onLayoutFinished(info, true);
		} else {
			// Otherwise report an error to the print framework
			callback.onLayoutFailed("Page count calculation failed.");
		}
	}

onLayout())方法的执行结果有三种：完成，取消或失败（计算布局无法顺利完成时会失败）。你必须通过调用PrintDocumentAdapter.LayoutResultCallback对象中的适当方法来指出这些结果中的一个。

> Note：onLayoutFinished())方法的布尔类型参数明确了这个布局内容是否和上一次打印请求相比发生了改变。恰当地设定了这个参数将避免打印框架不必要的调用onWrite())方法，缓存之前的打印文档，提升执行性能。

onLayout())的主要任务是计算打印文档的页数，并将它作为打印参数交给打印机。如何计算页数则高度依赖于你的应用是如何对打印页面进行布局的。下面的代码展示了页数是如何根据打印方向确定的：

	private int computePageCount(PrintAttributes printAttributes) {
		int itemsPerPage = 4; // default item count for portrait mode
		MediaSize pageSize = printAttributes.getMediaSize();
		if (!pageSize.isPortrait()) {
			// Six items per page in landscape orientation
			itemsPerPage = 6;
		}
		// Determine number of print items
		int printItemCount = getPrintItemCount();
		return (int) Math.ceil(printItemCount / itemsPerPage);
	}

### 将打印文档写入文件

当需要将打印内容输出到一个文件时，Android打印框架会调用PrintDocumentAdapter类的onWrite())方法。这个方法的参数指定了哪些页面要被写入以及要使用的输出文件。该方法的实现必须将每一个请求页的内容渲染成一个含有多个页面的PDF文件。当这个过程结束以后，你需要调用callback对象的onWriteFinished())方法。

> Note： Android打印框架可能会在每次调用onLayout())后，调用onWrite())方法一次甚至更多次。在这节课当中，有一件非常重要的事情是当打印内容的布局没有变化时，将onLayoutFinished())方法的布尔参数设置为“false”，以此避免对打印文档进行不必要的重写操作。

> Note：onLayoutFinished())方法的布尔类型参数明确了这个布局内容是否和上一次打印请求相比发生了改变。恰当地设定了这个参数将避免打印框架不必要的调用onLayout())方法，缓存之前的打印文档，提升执行性能。

下面的代码展示了使用PrintedPdfDocument类创建了PDF文件的基本原理：

	@Override
	public void onWrite(final PageRange[] pageRanges,
				final ParcelFileDescriptor destination,
				final CancellationSignal cancellationSignal,
				final WriteResultCallback callback) {
		// Iterate over each page of the document,
		// check if it's in the output range.
		for (int i = 0; i < totalPages; i++) {
			// Check to see if this page is in the output range.
			if (containsPage(pageRanges, i)) {
				// If so, add it to writtenPagesArray. writtenPagesArray.size()
				// is used to compute the next output page index.
				writtenPagesArray.append(writtenPagesArray.size(), i);
				PdfDocument.Page page = mPdfDocument.startPage(i);
				// check for cancellation
				if (cancellationSignal.isCancelled()) {
					callback.onWriteCancelled();
					mPdfDocument.close();
					mPdfDocument = null;
					return;
				}
				// Draw page content for printing
				drawPage(page);
				// Rendering is complete, so page can be finalized.
				mPdfDocument.finishPage(page);
			}
		}

		// Write PDF document to file
		try {
			mPdfDocument.writeTo(new FileOutputStream(destination.getFileDescriptor()));
		} catch (IOException e) {
			callback.onWriteFailed(e.toString());
			return;
		} finally {
			mPdfDocument.close();
			mPdfDocument = null;
		}

		PageRange[] writtenPages = computeWrittenPages();
		// Signal the print framework the document is complete
		callback.onWriteFinished(writtenPages);
		...
	}

代码中将PDF页面递交给了drawPage()方法，就布局而言，onWrite())方法的执行可以有三种结果：完成，取消或者失败(内容无法被写入）。你必须通过调用PrintDocumentAdapter.WriteResultCallback对象中的适当方法来指明这些结果中的一个。

> Note：渲染打印文档是一个可能耗费大量资源的操作。为了避免阻塞应用的主UI线程，你应该考虑将页面的渲染和写操作放在另一个线程中执行，比如在AsyncTask中执行。关于更多异步任务线程的知识，可以阅读：Processes and Threads。

### 绘制PDF页面内容

当你的应用进行打印时，你的应用必须生成一个PDF文档并将它传递给Android打印框架以进行打印。你可以使用任何PDF生成库来协助完成这个操作。本节将展示如何使用PrintedPdfDocument类将你的打印内容生成为PDF页面。

PrintedPdfDocument类使用一个Canvas对象来在PDF页面上绘制元素，这一点和在activity布局上进行绘制很类似。你可以在打印页面上使用Canvas类提供的相关绘图方法绘制页面元素。下面的代码展示了如何使用这些方法在PDF页面上绘制一些简单的元素：

	private void drawPage(PdfDocument.Page page) {
		Canvas canvas = page.getCanvas();
		// units are in points (1/72 of an inch)
		int titleBaseLine = 72;
		int leftMargin = 54;
		Paint paint = new Paint();
		paint.setColor(Color.BLACK);
		paint.setTextSize(36);
		canvas.drawText("Test Title", leftMargin, titleBaseLine, paint);
		paint.setTextSize(11);
		canvas.drawText("Test paragraph", leftMargin, titleBaseLine + 25, paint);
		paint.setColor(Color.BLUE);
		canvas.drawRect(100, 100, 172, 172, paint);
	}

当使用Canvas在一个PDF页面上绘图时，元素通过单位“点（point）”来指定大小，一个点相当于七十二分之一英寸。确保你使用这个测量单位来指定页面上的元素大小。在定位绘制的元素时，坐标系的原点（即（0,0）点）在页面的最左上角。

Tip：虽然Canvas对象允许你将打印元素放置在一个PDF文档的边缘，但许多打印机无法在纸张的边缘打印。所以当你使用这个类构建一个打印文档时，确保你考虑了那些无法打印的边缘区域。