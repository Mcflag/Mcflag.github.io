title: Android官方培训教程9——多媒体拍照和视频
date: 2015-08-31 23:30:41
tags: [Android官方培训教程]
categories: [Android官方培训教程]
---

## 摘要
多媒体拍照和视频
<!--more-->

## 轻松拍摄照片

将会学习如何利用已有的相机应用拍摄照片，通过最简单的方式获取图片，而不是重新设计并实现一个具有相机功能的组件。幸运的是，通常来说，大多数Android设备都已经安装了至少一款相机程序。

### 请求使用相机权限

为了让别人知道你的应用需要依赖相机，在你的Manifest清单文件中添加 &lt;uses-feature> 标签:

	<uses-feature android:name="android.hardware.camera"
			android:required="true" />

如果你的程序使用相机，但相机并不是应用的正常运行所必不可少的组件，可以将 android:required 设置为 "false" 。这样的话，Google Play 也会允许没有相机的设备下载该应用。当然你有必要在使用相机之前通过调用hasSystemFeature(PackageManager.FEATURE_CAMERA)方法来检查设备上是否有相机。如果没有，你应该禁用和相机相关的功能！

### 使用相机应用程序进行拍照

利用一个描述了你想要做什么的Intent对象，Android可以将某些执行任务委托给其他应用。整个过程包含三部分：Intent 本身，一个函数调用来启动外部的 Activity，当焦点返回到你的Activity时，处理返回图像数据的代码。下面的函数通过发送一个Intent来捕获照片：

	static final int REQUEST_IMAGE_CAPTURE = 1;

	private void dispatchTakePictureIntent() {
		Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
		if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
			startActivityForResult(takePictureIntent, REQUEST_IMAGE_CAPTURE);
		}
	}

注意在调用startActivityForResult()方法之前，先调用resolveActivity()，这个方法会返回能处理该Intent的第一个Activity（译注：即检查有没有能处理这个Intent的Activity）。**执行这个检查非常重要，因为如果你调用startActivityForResult()时，没有应用能处理你的Intent，你的应用将会崩溃。**所以只要返回结果不为null，使用该Intent就是安全的。

### 获取缩略图

Android的相机应用会把拍好的照片编码为缩小的Bitmap，使用extra value的方式添加到返回的Intent当中，并传送给onActivityResult())，并且对应的Key为 "data" 。下面的代码展示的是如何获取这一图片并显示在ImageView上。

	@Override
	protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		if (requestCode == REQUEST_IMAGE_CAPTURE && resultCode == RESULT_OK) {
			Bundle extras = data.getExtras();
			Bitmap imageBitmap = (Bitmap) extras.get("data");
			mImageView.setImageBitmap(imageBitmap);
		}
	}

> Note: 这张从 "data" 中取出的缩略图适用于作为图标，但其他作用会比较有限。而处理一张全尺寸图片需要做更多的工作。

### 保存全尺寸照片

如果你提供一个File对象给Android的相机程序，它会保存这张全尺寸照片到给定的路径下。你必须提供存储图片所需要的含有后缀名形式的文件名。

一般而言，用户使用设备相机所拍摄的任何照片都应该被存放在设备的公共外部存储中，这样它们就能被所有的图片访问。将DIRECTORY_PICTURES作为参数，传递给getExternalStoragePublicDirectory())方法，可以返回适用于存储公共图片的目录。由于该方法提供的目录被所有应用共享，因此对该目录进行读写操作分别需要READ_EXTERNAL_STORAGE和WRITE_EXTERNAL_STORAGE权限。另外，因为写权限隐含了读权限，所以如果你需要外部存储的写权限，那么你仅仅需要请求一项权限就可以了：

	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

然而，如果你希望照片对你的应用而言是私有的，你可以使用getExternalFilesDir())提供的目录。在Android 4.3及以下版本的系统中，写这个目录需要WRITE_EXTERNAL_STORAGE权限。从Android 4.4开始，该目录将无法被其他应用
访问，所以该权限就不再需要了，你可以通过添加maxSdkVersion属性，声明只在低版本的Android设备上请求这个权
限。

	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"
			android:maxSdkVersion="18" />

> Note: 所有存储在getExternalFilesDir())提供的目录中的文件会在用户卸载你的app后被删除。

一旦你选定了存储文件的目录，你需要设计一个保证文件名不会冲突的命名规则。也许你还希望将路径存储在一个成员变量里以备在将来使用。下面的例子使用日期时间戳为新照片生成唯一的文件名：

	String mCurrentPhotoPath;
	private File createImageFile() throws IOException {
		// Create an image file name
		String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
		String imageFileName = "JPEG_" + timeStamp + "_";
		File storageDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
		File image = File.createTempFile(
				imageFileName, /* prefix */
				".jpg", /* suffix */
				storageDir /* directory */
		);
		// Save a file: path for use with ACTION_VIEW intents
		mCurrentPhotoPath = "file:" + image.getAbsolutePath();
		return image;
	}

有了上面的方法，我们就可以给新照片创建文件对象了，现在你可以像这样创建并触发一个Intent：

	static final int REQUEST_TAKE_PHOTO = 1;
	private void dispatchTakePictureIntent() {
		Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
		// Ensure that there's a camera activity to handle the intent
		if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
			// Create the File where the photo should go
			File photoFile = null;
			try {
				photoFile = createImageFile();
			} catch (IOException ex) {
				// Error occurred while creating the File
				...
			}
			// Continue only if the File was successfully created
			if (photoFile != null) {
				takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT,
				Uri.fromFile(photoFile));
				startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO);
			}
		}
	}

### 将照片添加到相册中

当你通过Intent创建了一张照片，你应该知道你的图片在哪，因为是你决定将它存储在哪里的。对其他人来说，也许查看你的照片最简单的方式是通过系统的Media Provider。

> Note: 如果你将你的图片存储在getExternalFilesDir())提供的目录中，媒体扫描器不能访问到你的文件，因为它们属于你的应用的私有数据。

下面的例子演示了如何触发系统的Media Scanner将你的照片添加到Media Provider的数据库中，这样就可以使得
Android相册程序与其他程序能够读取到这些照片。

	private void galleryAddPic() {
		Intent mediaScanIntent = new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE);
		File f = new File(mCurrentPhotoPath);
		Uri contentUri = Uri.fromFile(f);
		mediaScanIntent.setData(contentUri);
		this.sendBroadcast(mediaScanIntent);
	}

### 解码解码一幅缩放图片

在有限的内存下，管理许多全尺寸的图片会很棘手。如果你发现你的应用在展示了少量图片后消耗了所有内存，你可以通过缩放图片到目标视图尺寸，之后再载入到内存中的方法，来显著降低内存的使用，下面的例子演示了这个技术：

	private void setPic() {
		// Get the dimensions of the View
		int targetW = mImageView.getWidth();
		int targetH = mImageView.getHeight();

		// Get the dimensions of the bitmap
		BitmapFactory.Options bmOptions = new BitmapFactory.Options();
		bmOptions.inJustDecodeBounds = true;
		BitmapFactory.decodeFile(mCurrentPhotoPath, bmOptions);
		int photoW = bmOptions.outWidth;
		int photoH = bmOptions.outHeight;

		// Determine how much to scale down the image
		int scaleFactor = Math.min(photoW/targetW, photoH/targetH);

		// Decode the image file into a Bitmap sized to fill the View
		bmOptions.inJustDecodeBounds = false;
		bmOptions.inSampleSize = scaleFactor;
		bmOptions.inPurgeable = true;

		Bitmap bitmap = BitmapFactory.decodeFile(mCurrentPhotoPath, bmOptions);
		mImageView.setImageBitmap(bitmap);
	}

## 使用相机程序来录制视频

利用一个描述了你想要做什么的Intent对象，Android可以将某些执行任务委托给其他应用。整个过程包含三部分：Intent 本身，一个函数调用来启动外部的 Activity，当焦点返回到你的Activity时，处理返回图像数据的代码。下面的函数将会发送一个Intent来录制视频

	static final int REQUEST_VIDEO_CAPTURE = 1;
	private void dispatchTakeVideoIntent() {
		Intent takeVideoIntent = new Intent(MediaStore.ACTION_VIDEO_CAPTURE);
		if (takeVideoIntent.resolveActivity(getPackageManager()) != null) {
			startActivityForResult(takeVideoIntent, REQUEST_VIDEO_CAPTURE);
		}
	}

### 查看视频

Android的相机程序会把指向视频存储地址的Uri添加到Intent中，并传送给onActivityResult())方法。下面的代码获取该视频并显示到一个VideoView当中：

	@Override
	protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		if (requestCode == REQUEST_VIDEO_CAPTURE && resultCode == RESULT_OK) {
			Uri videoUri = intent.getData();
			mVideoView.setVideoURI(videoUri);
		}
	}

## 控制相机硬件

通过使用框架的API来直接控制相机硬件。直接控制设备的相机，比起向已有的相机应用请求图片或视频，要复杂一些。然而，如果你想要创建一个特殊的相机应用或将相机整合在你的应用当中，这节会演示如何做到。

### 打开相机对象

获取一个 Camera 对象是直接控制相机的第一步。正如Android自带的相机程序一样，访问相机推荐的方式是在onCreate())方法里面另起一个线程来打开相机。这种办法可以避免因为启动时间较长导致UI线程被阻塞。还有一种更好的方法，可以把打开相机的操作延迟到onResume())方法里面去执行，这样使得代码更容易重用，并且保持控制流程简单。

当相机正在被另外一个程序使用的时候去执行Camera.open())会抛出一个Exception，利用 try 语句块进行捕获：

	private boolean safeCameraOpen(int id) {
		boolean qOpened = false;
		try {
			releaseCameraAndPreview();
			mCamera = Camera.open(id);
			qOpened = (mCamera != null);
		} catch (Exception e) {
			Log.e(getString(R.string.app_name), "failed to open Camera");
			e.printStackTrace();
		}
		return qOpened;
	}
	private void releaseCameraAndPreview() {
		mPreview.setCamera(null);
		if (mCamera != null) {
			mCamera.release();
			mCamera = null;
		}
	}

自从API Level 9开始，相机框架可以支持多个相机。如果你使用旧的API，在调用open())时不传入参数 ，那么你会获取后置摄像头。

### 创建相机预览界面

拍照通常需要向用户提供一个预览界面来显示待拍摄的事物。你可以使用SurfaceView来展现照相机采集的图像。

为了显示一个预览界面，你需要一个Preview类。这个类需要实现 android.view.SurfaceHolder.Callback 接口，用这个接口把相机硬件获取的数据传递给程序。

	class Preview extends ViewGroup implements SurfaceHolder.Callback {
		SurfaceView mSurfaceView;
		SurfaceHolder mHolder;
		Preview(Context context) {
			super(context);
			mSurfaceView = new SurfaceView(context);
			addView(mSurfaceView);
			// Install a SurfaceHolder.Callback so we get notified when the
			// underlying surface is created and destroyed.
			mHolder = mSurfaceView.getHolder();
			mHolder.addCallback(this);
			mHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
		}
		...
	}

这个Preview类必须在实时图像预览开始之前传递给Camera对象。正如下一节所描述的：设置和启动Preview

一个Camera实例与它相关的Preview必须以特定的顺序来创建，其中Camera对象首先被创建。在下面的示例中，初始化Camera的动作被封装了起来，这样，无论用户想对Camera做出任何改变，Camera.startPreview())都会被 setCamera() 调用。另外，Preview对象必须在 surfaceChanged() 这一回调方法里面重新启用（restart）。

	public void setCamera(Camera camera) {
		if (mCamera == camera) { return; }
		stopPreviewAndFreeCamera();
		mCamera = camera;
		if (mCamera != null) {
			List<Size> localSizes = mCamera.getParameters().getSupportedPreviewSizes();
			mSupportedPreviewSizes = localSizes;
			requestLayout();
			try {
				mCamera.setPreviewDisplay(mHolder);
			} catch (IOException e) {
				e.printStackTrace();
			}
			// Important: Call startPreview() to start updating the preview
			// surface. Preview must be started before you can take a picture.
			mCamera.startPreview();
		}
	}

### 修改相机设置

相机设置可以改变拍照的方式，从缩放级别到曝光补偿等。下面的例子仅仅演示了如何改变预览大小，更多设置请参考相机应用的源代码。

	public void surfaceChanged(SurfaceHolder holder, int format, int w, int h) {
		// Now that the size is known, set up the camera parameters and begin
		// the preview.
		Camera.Parameters parameters = mCamera.getParameters();
		parameters.setPreviewSize(mPreviewSize.width, mPreviewSize.height);
		requestLayout();
		mCamera.setParameters(parameters);
		// Important: Call startPreview() to start updating the preview surface.
		// Preview must be started before you can take a picture.
		mCamera.startPreview();
	}

### 设置预览方向

大多数相机程序会锁定预览为横屏状态，因为该方向是相机传感器的自然方向。当然这一设定并不会阻止你去拍竖屏的照片，因为设备的方向信息会被记录在EXIF头中。setCameraDisplayOrientation())方法可以让你在不影响照片拍摄过程的情况下，改变预览的方向。然而，对于Android API Level 14及以下版本的系统，在改变方向之前，你必须先停止你的预览，然后再去重启它。

### 拍摄照片

只要预览开始之后，可以使用Camera.takePicture())方法拍摄照片。你可以创建Camera.PictureCallback与Camera.ShutterCallback对象并将他们传递到Camera.takePicture())中。如果你想要进行连拍，你可以创建一个Camera.PreviewCallback并实现onPreviewFrame())方法。你可以拍摄选中的预览帧，或是为调用takePicture())建立一个延迟

### 重启Preview

在拍摄好图片后，你必须在用户拍下一张图片之前重启预览。在下面的示例中，利用快门按钮来实现重启。

	@Override
	public void onClick(View v) {
		switch(mPreviewState) {
		case K_STATE_FROZEN:
			mCamera.startPreview();
			mPreviewState = K_STATE_PREVIEW;
			break;
		default:
			mCamera.takePicture( null, rawCallback, null);
			mPreviewState = K_STATE_BUSY;
		} // switch
		shutterBtnConfig();
	}

### 停止预览并释放相机

当你的程序在使用相机完毕后，有必要做清理的动作。特别地，你必须释放Camera对象，不然可能会引起其他应用崩溃，包括你自己应用的新实例。

那么何时应该停止预览并释放相机呢？在预览的Surface被摧毁之后，可以做停止预览与释放相机的动作。如下面Preview类中的方法所示：

	public void surfaceDestroyed(SurfaceHolder holder) {
		// Surface will be destroyed when we return, so stop the preview.
		if (mCamera != null) {
			// Call stopPreview() to stop updating the preview surface.
			mCamera.stopPreview();
		}
	}
	/**
	 * When this function returns, mCamera will be null.
	 */
	private void stopPreviewAndFreeCamera() {
		if (mCamera != null) {
			// Call stopPreview() to stop updating the preview surface.
			mCamera.stopPreview();
			// Important: Call release() to release the camera for use by other
			// applications. Applications should release the camera immediately
			// during onPause() and re-open() it during onResume()).
			mCamera.release();
			mCamera = null;
		}
	}

在这节的前部分中，这一些系列的动作也是 setCamera() 方法的一部分，因此初始化一个相机的动作，总是从停止预览开始的。