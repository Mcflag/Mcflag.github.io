title: 2、游戏框架中的Android基础
date: 2015-08-31 23:17:22
tags: [游戏框架]
categories: [游戏框架]
---

## 摘要
游戏框架中的Android基础
<!--more-->

## 使用ListActivity

ListAcvitity是一个默认使用ListView显示的Activity。创建一个Activity并继承ListActivity，在onCreate方法里设置Adapter。以下的例子使用ArrayAdapter和android系统默认的一个包含ListView的layout来设置Adapter。

	String tests[] = { "LifeCycleTest", "SingleTouchTest"，"AssetsTest", "ExternalStorageTest" };
	setListAdapter(new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1, tests));

然后需要实现ListView的每一个item点击事件，需要重写onListItemClick方法。在本例中点击每一个item是启动一个新的Activity。

	@Override
    protected void onListItemClick(ListView list, View view, int position, long id) {
        super.onListItemClick(list, view, position, id);
        String testName = tests[position];
        try {
            Class clazz = Class.forName("包名" + testName);
            Intent intent = new Intent(this, clazz);
            startActivity(intent);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

## StringBuilder的使用，与StringBuffer和String的比较

String是字符串常量，StringBuffer是字符串变量并且线程安全，StringBuilder是字符串变量但非线程安全。

String 类型和 StringBuffer 类型的主要性能区别其实在于 String 是不可变的对象, 因此在每次对 String 类型进行改变的时候其实都等同于生成了一个新的 String 对象，然后将指针指向新的 String 对象，所以经常改变内容的字符串最好不要用 String ，因为每次生成对象都会对系统性能产生影响，特别当内存中无引用对象多了以后， JVM 的 GC 就会开始工作，那速度是一定会相当慢的。

而如果是使用 StringBuffer 类则结果就不一样了，每次结果都会对 StringBuffer 对象本身进行操作，而不是生成新的对象，再改变对象引用。所以在一般情况下我们推荐使用 StringBuffer ，特别是字符串对象经常改变的情况下。而在某些特别情况下， String 对象的字符串拼接其实是被 JVM 解释成了 StringBuffer 对象的拼接，所以这些时候 String 对象的速度并不会比 StringBuffer 对象慢，而特别是以下的字符串对象生成中， String 效率是远要比 StringBuffer 快的：

	String S1 = “This is only a” + “ simple” + “ test”;
	StringBuffer Sb = new StringBuilder(“This is only a”).append(“ simple”).append(“ test”);
 
你会很惊讶的发现，生成 String S1 对象的速度简直太快了，而这个时候 StringBuffer 居然速度上根本一点都不占优势。其实这是 JVM 的一个把戏，在 JVM 眼里，这个

	String S1 = “This is only a” + “ simple” + “test”; 

其实就是：

	String S1 = “This is only a simple test”; 

所以当然不需要太多的时间了。但大家这里要注意的是，如果你的字符串是来自另外的 String 对象的话，速度就没那么快了，譬如：

	String S2 = “This is only a”;
	String S3 = “ simple”;
	String S4 = “ test”;
	String S1 = S2 +S3 + S4;

这时候 JVM 会规规矩矩的按照原来的方式去做。

#### 在大部分情况下StringBuffer比String效率高

StringBuffer线程安全的可变字符序列。一个类似于 String 的字符串缓冲区，但不能修改。虽然在任意时间点上它都包含某种特定的字符序列，但通过某些方法调用可以改变该序列的长度和内容。

可将字符串缓冲区安全地用于多个线程。可以在必要时对这些方法进行同步，因此任意特定实例上的所有操作就好像是以串行顺序发生的，该顺序与所涉及的每个线程进行的方法调用顺序一致。

StringBuffer 上的主要操作是 append 和 insert 方法，可重载这些方法，以接受任意类型的数据。每个方法都能有效地将给定的数据转换成字符串，然后将该字符串的字符追加或插入到字符串缓冲区中。append 方法始终将这些字符添加到缓冲区的末端；而 insert 方法则在指定的点添加字符。

例如，如果 z 引用一个当前内容是“start”的字符串缓冲区对象，则此方法调用 z.append("le") 会使字符串缓冲区包含“startle”，而 z.insert(4, "le") 将更改字符串缓冲区，使之包含“starlet”。

#### 在大部分情况下StringBuilder比StringBuffer效率高

StringBuilder一个可变的字符序列，是5.0新增的。此类提供一个与 StringBuffer 兼容的 API，但不保证同步。该类被设计用作 StringBuffer 的一个简易替换，用在字符串缓冲区被单个线程使用的时候（这种情况很普遍）。如果可能，建议优先采用该类，因为在大多数实现中，它比 StringBuffer 要快。两者的方法基本相同。

	StringBuilder builder = new StringBuilder();
	builder.append(text);
	textView.setText(builder.toString());

## 单点触摸SingleTouchTest

触摸事件需要实现OnTouchListener，并重写onTouch方法。在onTouch方法中可以处理DOWN，MOVE，CANCEL和UP事件。

	view.setOnTouchListener(new OnTouchListener() {
		@Override
		public boolean onTouch(View v, MotionEvent event) {
			switch (event.getAction()) {
       		case MotionEvent.ACTION_DOWN:
            	break;
        	case MotionEvent.ACTION_MOVE:
            	break;
        	case MotionEvent.ACTION_CANCEL:
            	break;
        	case MotionEvent.ACTION_UP:
            	break;
        	}
			return false;
		}
	});

返回值表明是否拦截触摸事件。return false表明不拦截，还会执行下层或者另外的手势Touch事件，比如onFling等等，return true表明会拦截该touch事件，不再执行另外的手势事件。

## 多点触摸MultiTouchTest

多点触摸与单点触摸基本上差别不大，就是需要多处理几个手指。同时多点触摸是在Android 2.0以上的版本里实现的，并且还需要看设备是否支持。实例如下：

	float[] x = new float[10]; //保存所有点的x坐标
    float[] y = new float[10]; //保存所有点的y坐标
    boolean[] touched = new boolean[10]; //保存所有的点是否touch

	@Override
    public boolean onTouch(View v, MotionEvent event) {
        int action = event.getAction() & MotionEvent.ACTION_MASK;
        int pointerIndex = (event.getAction() & MotionEvent.ACTION_POINTER_ID_MASK) >> MotionEvent.ACTION_POINTER_ID_SHIFT;
        int pointerId = event.getPointerId(pointerIndex);

        switch (action) {
        case MotionEvent.ACTION_DOWN:
        case MotionEvent.ACTION_POINTER_DOWN:
            touched[pointerId] = true;
            x[pointerId] = (int)event.getX(pointerIndex);
            y[pointerId] = (int)event.getY(pointerIndex);
            break;

        case MotionEvent.ACTION_UP:          
        case MotionEvent.ACTION_POINTER_UP:
        case MotionEvent.ACTION_CANCEL:
            touched[pointerId] = false; 
            x[pointerId] = (int)event.getX(pointerIndex);
            y[pointerId] = (int)event.getY(pointerIndex);
            break;

        case MotionEvent.ACTION_MOVE:
            int pointerCount = event.getPointerCount();
            for (int i = 0; i < pointerCount; i++) {
                pointerIndex = i;
                pointerId = event.getPointerId(pointerIndex);
                x[pointerId] = (int)event.getX(pointerIndex);
                y[pointerId] = (int)event.getY(pointerIndex);
            }
            break;
        }   
        return true;
    }

## 监听按键事件KeyTest

要监听键盘需要首先实现OnKeyListener，并重写onKey方法。

对于一个view，view.setFocusable设置这个view是用键盘是否能获得焦点，view.setFocusableInTouchMode设置这个view用触摸是否能获得焦点，使用键盘时最好先调用view.requestFocus()方法设置焦点。

	@Override
    public boolean onKey(View view, int keyCode, KeyEvent event) {
        switch (event.getAction()) {
        case KeyEvent.ACTION_DOWN:
            break;
        case KeyEvent.ACTION_UP:
            break;
        }
        
        if (event.getKeyCode() == KeyEvent.KEYCODE_BACK)
            return false;
        else
            return true;
    }

有两个状态，DOWN和UP。并且可以使用getKeyCode()方法得到是哪一个Key。

## 处理加速计AccelerometerTest

要使用加速度计需要先注册使用SENSOR_SERVICE，并且还需要查看系统是否存在加速计。然后重写onSensorChanged和onAccuracyChanged方法，通过SensorEvent得到x，y，z三个方向上的加速度。

	SensorManager manager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
    if (manager.getSensorList(Sensor.TYPE_ACCELEROMETER).size() == 0) {
        textView.setText("No accelerometer installed");
    } else {
        Sensor accelerometer = manager.getSensorList(Sensor.TYPE_ACCELEROMETER).get(0);
        if (!manager.registerListener(this, accelerometer, SensorManager.SENSOR_DELAY_GAME)) {
            textView.setText("Couldn't register sensor listener");
        }
    }

	@Override
    public void onSensorChanged(SensorEvent event) {
        builder.setLength(0);
        builder.append("x: ");
        builder.append(event.values[0]);
        builder.append(", y: ");
        builder.append(event.values[1]);
        builder.append(", z: ");
        builder.append(event.values[2]);
        textView.setText(builder.toString());
    }

    @Override
    public void onAccuracyChanged(Sensor sensor, int accuracy) {
        // nothing to do here
    }

## 使用assets文件夹下的资源

需要通过使用AssetManager来得到并使用assets下的资源。

	AssetManager assetManager = getAssets();
    InputStream inputStream = null;
    try {
        inputStream = assetManager.open("texts/myawesometext.txt");
        String text = loadTextFile(inputStream);
        textView.setText(text);
    } catch (IOException e) {
        textView.setText("Couldn't load file");
    } finally {
        if (inputStream != null)｛
            try {
                inputStream.close();
            } catch (IOException e) {
                textView.setText("Couldn't close file");
            }
		｝
    }

	public String loadTextFile(InputStream inputStream) throws IOException {
        ByteArrayOutputStream byteStream = new ByteArrayOutputStream();
        byte[] bytes = new byte[4096];
        int len = 0;
        while ((len = inputStream.read(bytes)) > 0)｛
            byteStream.write(bytes, 0, len);
		｝
        return new String(byteStream.toByteArray(), "UTF8");
    }

## 使用外部存储ExternalStorageTest

需要先判断外部存储是否存在，只有状态是存在的情况下才能继续使用。并且还需要在manifest文件中注册使用SD卡的权限。

	String state = Environment.getExternalStorageState();
    if (!state.equals(Environment.MEDIA_MOUNTED)) {
        textView.setText("No external storage mounted");
    } else {
        File externalDir = Environment.getExternalStorageDirectory();
        File textFile = new File(externalDir.getAbsolutePath() + File.separator + "text.txt");
        try {
            writeTextFile(textFile, "This is a test. Roger");
            String text = readTextFile(textFile);
            textView.setText(text);
            if (!textFile.delete()) {
                textView.setText("Couldn't remove temporary file");
            }
        } catch (IOException e) {
            textView.setText("something went wrong! " + e.getMessage());
        }
    }

	//下面是读文件和写文件的方法
	private void writeTextFile(File file, String text) throws IOException {
        BufferedWriter writer = new BufferedWriter(new FileWriter(file));
        writer.write(text);
        writer.close();
    }

    private String readTextFile(File file) throws IOException {
        BufferedReader reader = new BufferedReader(new FileReader(file));
        StringBuilder text = new StringBuilder();
        String line;
        while ((line = reader.readLine()) != null) {
            text.append(line);
            text.append("\n");
        }
        reader.close();
        return text.toString();
    }

## 声音的使用SoundPoolTest

一般将声音文件放在assets文件夹下，有两种方法能够使用资源。SoundPool适合短促的音效。

* InputStream open(String fileName):根据文件名来获取原始资源对应的输入流；
* AssetFileDescriptor openFd(String fileName)：根据文件名来获取原始资源对应的AssetFileDescriptor 资源描述，应用程序可以通过它来获取原始资源

获得声音资源并使用的代码如下

	setVolumeControlStream(AudioManager.STREAM_MUSIC);
    soundPool = new SoundPool(20, AudioManager.STREAM_MUSIC, 0);

	try {
        AssetManager assetManager = getAssets();
        AssetFileDescriptor descriptor = assetManager.openFd("explosion.ogg");
        explosionId = soundPool.load(descriptor, 1);
    } catch (IOException e) {
        textView.setText("Couldn't load sound effect from asset, " + e.getMessage());
    }

	//播放的方法
	if (explosionId != -1) {
        soundPool.play(explosionId, 1, 1, 0, 0, 1);
	}

#### 创建一个SoundPool （构造函数）

使用SoundPool时需要使用构造函数new一个实例出来。

	public SoundPool(int maxStream, int streamType, int srcQuality) 

参数的含义为：

* maxStream —— 同时播放的流的最大数量
* streamType —— 流的类型，一般为STREAM_MUSIC(具体在AudioManager类中列出)
* srcQuality —— 采样率转化质量，当前无效果，使用0作为默认值

#### 加载音频资源 

可以通过四种途径来加载一个音频资源：其中的priority参数目前没有效果，建议设置为1。 

* int load(AssetFileDescriptor afd, int priority)通过一个AssetFileDescriptor对象
* int load(Context context, int resId, int priority)通过一个资源ID
* int load(String path, int priority)通过指定的路径加载
* int load(FileDescriptor fd, long offset, long length, int priority)通过FileDescriptor加载

> 需要注意的是，流的加载过程是一个将音频解压为原始16位PCM数据的过程，由一个后台线程来进行处理异步，所以初始化后不能立即播放，需要等待一点时间。

#### 播放控制

有以下几个函数可用于控制播放：

	final int play(int soundID, float leftVolume, float rightVolume, int priority, int loop, float rate)

播放指定音频的音效，并返回一个streamID 。参数的含义是：

* soundID —— a soundID returned by the load() function
* leftVolume —— left volume value (range = 0.0 to 1.0)
* rightVolume —— right volume value (range = 0.0 to 1.0)
* priority —— 流的优先级，值越大优先级高，影响当同时播放数量超出了最大支持数时SoundPool对该流的处理，0是最低优先级；
* loop —— 循环播放的次数，0为值播放一次，-1为无限循环，其他值为播放loop+1次（例如，3为一共播放4次）.
* rate —— 播放的速率，范围0.5-2.0(0.5为一半速率，1.0为正常速率，2.0为两倍速率)

暂停指定播放流的音效（streamID 应通过play()返回）。

	final void pause(int streamID) 

继续播放指定播放流的音效（streamID 应通过play()返回）。

	final void resume(int streamID) 

终止指定播放流的音效（streamID 应通过play()返回）。

	final void stop(int streamID) 

这里需要注意的是

* play()函数传递的是一个load()返回的soundID——指向一个被记载的音频资源 ，如果播放成功则返回一个非0的streamID——指向一个成功播放的流 ；同一个soundID 可以通过多次调用play()而获得多个不同的streamID (只要不超出同时播放的最大数量)；
* pause()、resume()和stop()是针对播放流操作的，传递的是play()返回的streamID ;
* play()中的priority参数，只在同时播放的流的数量超过了预先设定的最大数量是起作用，管理器将自动终止优先级低的播放流。如果存在多个同样优先级的流，再进一步根据其创建事件来处理，新创建的流的年龄是最小的，将被终止；
* 无论如何，程序退出时，手动终止播放并释放资源是必要的。

#### 更多属性设置 

其实就是paly()中的一些参数的独立设置：

设置指定播放流的循环.

	final void setLoop(int streamID, int loop)

设置指定播放流的音量. 

	final void setVolume(int streamID, float leftVolume, float rightVolume) 

设置指定播放流的优先级，上面已说明priority的作用.

	final void setPriority(int streamID, int priority) 

设置指定播放流的速率，0.5-2.0

	final void setRate(int streamID, float rate) 

#### 释放资源 

可操作的函数有：

卸载一个指定的音频资源.

	final boolean unload(int soundID) 

释放SoundPool中的所有音频资源.

	final void release() 

#### SoundPool总结：

一个SoundPool可以：

* 管理多个音频资源，通过load()函数，成功则返回非0的soundID;
* 同时播放多个音频，通过play()函数，成功则返回非0的streamID;
* pause()、resume()和stop()等操作是针对streamID(播放流)的;
* 当设置为无限循环时，需要手动调用stop()来终止播放;
* 播放流的优先级(play()中的priority参数)，只在同时播放数超过设定的最大数时起作用;
* 程序中不用考虑（play触发的）播放流的生命周期，无效的soundID/streamID不会导致程序错误。

## 使用MediaPlayer来播放music和视频

MediaPlayer需要setDataSource，并且需要在播放前调用prepare()方法，然后用start(),stop(),pause(),release()控制播放和释放资源。

	setVolumeControlStream(AudioManager.STREAM_MUSIC);
    mediaPlayer = new MediaPlayer();
    try {
        AssetManager assetManager = getAssets();
        AssetFileDescriptor descriptor = assetManager.openFd("music.ogg");
        mediaPlayer.setDataSource(descriptor.getFileDescriptor(), descriptor.getStartOffset(), descriptor.getLength());
        mediaPlayer.prepare();
        mediaPlayer.setLooping(true);
    } catch (IOException e) {
        textView.setText("Couldn't load music file, " + e.getMessage());
        mediaPlayer = null;
    }

	@Override
    protected void onResume() {
        super.onResume();
        if (mediaPlayer != null) {
            mediaPlayer.start();
        }
    }

    protected void onPause() {
        super.onPause();
        if (mediaPlayer != null) {
            mediaPlayer.pause();
            if (isFinishing()) {
                mediaPlayer.stop();
                mediaPlayer.release();
            }
        }
    }

## 全屏Activity

直接使用如下两句代码即可

	requestWindowFeature(Window.FEATURE_NO_TITLE);
    getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN, WindowManager.LayoutParams.FLAG_FULLSCREEN);

## 屏幕锁定WakeLock的使用

需要在manifest文件中添加使用WakeLock的权限

	PowerManager powerManager = (PowerManager)getSystemService(Context.POWER_SERVICE);
    wakeLock = powerManager.newWakeLock(PowerManager.FULL_WAKE_LOCK, "My Lock");

	@Override
    public void onResume() {
        wakeLock.acquire();
        super.onResume();
    }
    
    public void onPause() {
        wakeLock.release();
        super.onPause();
    }

## 简单的使用canvas渲染view

自定义一个view，在其onDraw方法中进行绘制，调用invalidate()刷新页面。

	class RenderView extends View {
        Random rand = new Random();

        public RenderView(Context context) {
            super(context);
        }

        protected void onDraw(Canvas canvas) {
            canvas.drawRGB(rand.nextInt(256), rand.nextInt(256),
                    rand.nextInt(256));
            invalidate();
        }
    }

简单的在onCreate方法中调用，就能看到效果。

	setContentView(new RenderView(this));

## 简单的绘制线，圆和矩形

先设置一个paint，设置paint的属性，然后调用drawLine，drawCircle和drawRect来绘制。

	class RenderView extends View {        
        Paint paint;
        
        public RenderView(Context context) {
            super(context);
            paint = new Paint();            
        }
        
        protected void onDraw(Canvas canvas) {
            canvas.drawRGB(255, 255, 255);            
            paint.setColor(Color.RED);
            canvas.drawLine(0, 0, canvas.getWidth()-1, canvas.getHeight()-1, paint);
            
            paint.setStyle(Style.STROKE);
            paint.setColor(0xff00ff00);            
            canvas.drawCircle(canvas.getWidth() / 2, canvas.getHeight() / 2, 40, paint);
                        
            paint.setStyle(Style.FILL);
            paint.setColor(0x770000ff);
            canvas.drawRect(100, 100, 200, 200, paint);
            invalidate();
        }
    }

## 加载图片Bitmap

将文件读入到InputStream中，然后调用BitmapFactory.decodeStream()方法将其转换成Bitmap。

	class RenderView extends View {
        Bitmap bob565;
        Bitmap bob4444;
        Rect dst = new Rect();

        public RenderView(Context context) {
            super(context);

            try {
                AssetManager assetManager = context.getAssets();
                InputStream inputStream = assetManager.open("bobrgb888.png");
                bob565 = BitmapFactory.decodeStream(inputStream);
                inputStream.close();

                inputStream = assetManager.open("bobargb8888.png");
                BitmapFactory.Options options = new BitmapFactory.Options();
                options.inPreferredConfig = Bitmap.Config.ARGB_4444;
                bob4444 = BitmapFactory.decodeStream(inputStream, null, options);
                inputStream.close();

            } catch (IOException e) {
                // silently ignored, bad coder monkey, baaad!
            } finally {
                // we should really close our input streams here.
            }
        }

        protected void onDraw(Canvas canvas) {
            dst.set(50, 50, 350, 350);
            canvas.drawBitmap(bob565, null, dst, null);
            canvas.drawBitmap(bob4444, 100, 100, null);
            invalidate();
        }
    }

## 使用Font文件来改变字体

主要有两个步骤，一是通过Typeface读入font文件，然后通过setTypeface()方法将Paint设置为新的字体。

	class RenderView extends View {
        Paint paint;
        Typeface font;
        Rect bounds = new Rect();

        public RenderView(Context context) {
            super(context);
            paint = new Paint();
            font = Typeface.createFromAsset(context.getAssets(), "font.ttf");
        }

        protected void onDraw(Canvas canvas) {
            paint.setColor(Color.YELLOW);
            paint.setTypeface(font);
            paint.setTextSize(28);
            paint.setTextAlign(Paint.Align.CENTER);
            canvas.drawText("This is a test!", canvas.getWidth() / 2, 100, paint);

            String text = "This is another test o_O";
            paint.setColor(Color.WHITE);
            paint.setTextSize(18);
            paint.setTextAlign(Paint.Align.LEFT);
            paint.getTextBounds(text, 0, text.length(), bounds);
            canvas.drawText(text, canvas.getWidth() - bounds.width(), 140, paint);
            invalidate();
        }
    }

## SurfaceView的使用

几乎游戏里都是使用SurfaceView来显示界面。

	class FastRenderView extends SurfaceView implements Runnable {
        Thread renderThread = null;
        SurfaceHolder holder;
        volatile boolean running = false;
        
        public FastRenderView(Context context) {
            super(context);           
            holder = getHolder();            
        }

        public void resume() {          
            running = true;
            renderThread = new Thread(this);
            renderThread.start();         
        }
        
        public void pause() {        
            running = false;                        
            while(true) {
                try {
                    renderThread.join();
                    break;
                } catch (InterruptedException e) {
                    // retry
                }
            }
            renderThread = null;        
        }
        
        public void run() {
            while(running) {  
                if(!holder.getSurface().isValid())
                    continue;
                
                Canvas canvas = holder.lockCanvas();            
                drawSurface(canvas);                                           
                holder.unlockCanvasAndPost(canvas);            
            }
        }        
        
        private void drawSurface(Canvas canvas) {
            canvas.drawRGB(255, 0, 0);
        }
    }

在onCreate方法中调用就可以简单的使用

	FastRenderView renderView = new FastRenderView(this);
    setContentView(renderView);