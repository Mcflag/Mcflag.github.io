title: 6、OpenGL示例
date: 2015-09-06 00:21:15
tags: [游戏框架]
categories: [游戏框架]
---

## 摘要
OpenGL示例
<!--more-->

## GLSurfaceView的代码

首先我们需要某种类型的View，它允许使用OpenGl ES绘图。它称为GLSurfaceView类，派生自SurfaceView类。为了不打断UI线程的执行，还需要一个独立的主循环线程。GLSurfaceView已经自动实现了这样的一个线程，在使用时需要做的是实现一个名为GLSurfaceView.Renderer的监听器接口。

GL10实例将命令发给OpenGL ES,EGLConfig则设置Surface的属性，例如颜色和深度等。

每一次活动暂停都将导致GLSurfaceView界面的销毁。当活动恢复时GLSurfaceView会重新实例化一个新的OpenGL ES渲染界面。并通过调用监听器的onSurfaceCreated()方法进行通知。这同时也导致丢失所有的OpenGL状态。

GLSurfaceViewTest的代码：

	public class GLSurfaceViewTest extends Activity {
		GLSurfaceView glView;

		public void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			requestWindowFeature(Window.FEATURE_NO_TITLE);
			getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN, WindowManager.LayoutParams.FLAG_FULLSCREEN);
			glView = new GLSurfaceView(this);
			glView.setRenderer(new SimpleRenderer());
			setContentView(glView);
		}

		@Override
		public void onResume() {
			super.onResume();
			glView.onResume();
		}

		@Override
		public void onPause() {
			super.onPause();
			glView.onPause();
		}

		static class SimpleRenderer implements Renderer {
			Random rand = new Random();

			@Override
			public void onSurfaceCreated(GL10 gl, EGLConfig config) {

			}

			@Override
			public void onSurfaceChanged(GL10 gl, int width, int height) {

			}

			@Override
			public void onDrawFrame(GL10 gl) {
				gl.glClearColor(rand.nextFloat(), rand.nextFloat(), rand.nextFloat(), 1);
				gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
			}
		}
	}

glClear方法指明了清理哪个缓冲区，这里设置的是GL_COLOR_BUFFER_BIT缓冲区。注意：永远不要在另一个线程中调用OpenGl ES。这是由于OpenGL ES是被设计为用在单线程中的，而且不是线程安全的。

## GLGame：实现游戏接口

实现一个GLGame类实现前面的定义的Game接口。而且OpenGL不太适合作为该Graphics接口的编程模型，替代方法是实现一个新类GLGraphics，该类记录从GLSurfaceView获得的GL10实例的变化。

	public class GLGraphics {
		GLSurfaceView glView;
		GL10 gl;

		GLGraphics(GLSurfaceView glView) {
			this.glView = glView;
		}

		public GL10 getGL() {
			return gl;
		}

		void setGL(GL10 gl) {
			this.gl = gl;
		}

		public int getWidth() {
			return glView.getWidth();
		}

		public int getHeight() {
			return glView.getHeight();
		}
	}

上面GLGraphics类将在GLSurfaceView的渲染线程中使用，GLGame与AndroidGame类绝大多数相同，唯一复杂的地方是渲染线程和UI线程之间的同步。

	public abstract class GLGame extends Activity implements Game, Renderer {
		enum GLGameState {
			Initialized, Running, Paused, Finished, Idle
		}

		GLSurfaceView glView;
		GLGraphics glGraphics;
		Audio audio;
		Input input;
		FileIO fileIO;
		Screen screen;
		GLGameState state = GLGameState.Initialized;
		Object stateChanged = new Object();
		long startTime = System.nanoTime();
		WakeLock wakeLock;

		@SuppressWarnings("deprecation")
		@Override
		public void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			requestWindowFeature(Window.FEATURE_NO_TITLE);
			getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN, WindowManager.LayoutParams.FLAG_FULLSCREEN);
			glView = new GLSurfaceView(this);
			glView.setRenderer(this);
			setContentView(glView);

			glGraphics = new GLGraphics(glView);
			fileIO = new AndroidFileIO(this);
			audio = new AndroidAudio(this);
			input = new AndroidInput(this, glView, 1, 1);
			PowerManager powerManager = (PowerManager) getSystemService(Context.POWER_SERVICE);
			wakeLock = powerManager.newWakeLock(PowerManager.FULL_WAKE_LOCK, "GLGame");
		}

		@Override
		public void onResume() {
			super.onResume();
			glView.onResume();
			wakeLock.acquire();
		}

		public void onSurfaceCreated(GL10 gl, EGLConfig config) {
			glGraphics.setGL(gl);

			synchronized (stateChanged) {
				if (state == GLGameState.Initialized)
					screen = getStartScreen();
				state = GLGameState.Running;
				screen.resume();
				startTime = System.nanoTime();
			}
		}

		public void onSurfaceChanged(GL10 gl, int width, int height) {
		}

		public void onDrawFrame(GL10 gl) {
			GLGameState state = null;

			synchronized (stateChanged) {
				state = this.state;
			}

			if (state == GLGameState.Running) {
				float deltaTime = (System.nanoTime() - startTime) / 1000000000.0f;
				startTime = System.nanoTime();

				screen.update(deltaTime);
				screen.present(deltaTime);
			}

			if (state == GLGameState.Paused) {
				screen.pause();
				synchronized (stateChanged) {
					this.state = GLGameState.Idle;
					stateChanged.notifyAll();
				}
			}

			if (state == GLGameState.Finished) {
				screen.pause();
				screen.dispose();
				synchronized (stateChanged) {
					this.state = GLGameState.Idle;
					stateChanged.notifyAll();
				}
			}
		}

		@Override
		public void onPause() {
			synchronized (stateChanged) {
				if (isFinishing())
					state = GLGameState.Finished;
				else
					state = GLGameState.Paused;
				while (true) {
					try {
						stateChanged.wait();
						break;
					} catch (InterruptedException e) {
					}
				}
			}
			wakeLock.release();
			glView.onPause();
			super.onPause();
		}

		public GLGraphics getGLGraphics() {
			return glGraphics;
		}

		public Input getInput() {
			return input;
		}

		public FileIO getFileIO() {
			return fileIO;
		}

		public Graphics getGraphics() {
			throw new IllegalStateException("We are using OpenGL!");
		}

		public Audio getAudio() {
			return audio;
		}

		public void setScreen(Screen newScreen) {
			if (newScreen == null)
				throw new IllegalArgumentException("Screen must not be null");

			this.screen.pause();
			this.screen.dispose();
			newScreen.resume();
			newScreen.update(0);
			this.screen = newScreen;
		}

		public Screen getCurrentScreen() {
			return screen;
		}
	}

通过GLGame.getGLGraphics()方法得到GLGraphics方法来代替标准Graphics。所有的Screen都在渲染线程中执行。下面是一个使用GLGame的示例：

	public class GLGameTest extends GLGame {

		@Override
		public Screen getStartScreen() {
			return new TestScreen(this);
		}

		class TestScreen extends Screen {
			GLGraphics glGraphics;
			Random rand = new Random();

			public TestScreen(Game game) {
				super(game);
				glGraphics = ((GLGame) game).getGLGraphics();
			}

			@Override
			public void update(float deltaTime) {
			}

			@Override
			public void present(float deltaTime) {
				GL10 gl = glGraphics.getGL();
				gl.glClearColor(rand.nextFloat(), rand.nextFloat(), rand.nextFloat(), 1);
				gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
			}

			@Override
			public void pause() {
			}

			@Override
			public void resume() {
			}

			@Override
			public void dispose() {
			}
		}
	}

## 绘制一个红色的三角形

使用OpenGL ES绘制几何图形之前需要进行一些设置，其中有两个设置最重要，投影矩阵（以及相应的视锥）和视口。

**1、定义视口**：通过以下方法可以使用帧缓冲区的一部分或者全部。
	
	GL10.glViewport(int x, int y, int width, int height);

x和y坐标表示帧缓冲区中视口的左上方，width和height表示视口的大小，单位是像素，需要注意的是OpenGL ES假定帧缓冲区坐标系统的原点在屏幕左下角。通常情况下将x和y设置为0，width和height设置为屏幕的分辨率，也就是全屏模式。虽然看起来像是该方法设置了渲染用的2D坐标系统，但实际上并非如此。它只是定义了OpenGL用来输出最终图像的帧缓冲区部分，2D坐标系统是通过投影和模型-视图矩阵定义的。

**2、定义投影矩阵**：先只使用平行投影。

（1）矩阵模式与活动矩阵。OpenGL ES会记录3个矩阵：投影矩阵，模型视图矩阵和纹理矩阵。在使用矩阵时必须先声明要操作哪个矩阵。使用如下方法：

	GL10.glMatrixMode(int mode)

参数mode可以是GL10.GL_PROJECTION、GL10.GL_MODELVIEW或者GL10.GL_TEXTURE。该方法调用后会一直使用该矩阵，知道下次设定新的矩阵。这个矩阵模式是OpenGL的状态之一，如果应用暂停并恢复，上下文会丢失，导致此模式也会丢失。为了使后续调用都能够操作投影矩阵，可使用以下方法：

	gl.glMatrixMode(GL10.GL_PROJECTION);

（2）使用glOrthof设置正交投影。OpenGL提供以下方法设置正交投影矩阵：

	GL10.glOrthof(int left,int right,int bottom,int top, int near,int far)

这实质上是在视锥裁剪面上做了一些操作。这六个值定义了一个类似盒子的长方体，定义的是前一个面的左下角和后一个面的右上角。一般设置视锥为：

	gl.glOrthof(0,480,0,320,1,-1);

虽然视锥很薄，但是在2D模式下是没有问题的。需要注意最好不要将near和far都设为0，为了能够安全可靠的使用，也给z轴分配一些缓冲区。现在坐标的原点在左下角，对于游戏世界来说实际更加方便一些。

**3、有用的代码片段**：这段代码主要用黑色清屏，设置视口为整个帧缓冲区，设置投影矩阵，由此设置视锥。

	gl.glClearColor(0,0,0,1);
	gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	gl.glViewport(0,0,glGraphics.getWidth(),glGraphics.getHeight());
	gl.glMatrixMode(GL10.GL_PROJECTION);
	gl.glLoadIdentity();
	gl.glOrthof(0,320,0,480,1,-1);

glLoadIdentity()方法作用：实际上OpenGL提供的大多数操作活动矩阵的方法并不是直接设置活动矩阵，实际做法是根据它们携带的参数构造一个临时矩阵，并用这个矩阵与当前矩阵相乘。如果在每一帧中都调用glOrthof()方法，那么每次调用投影矩阵都会与其自身相乘。为了避免这种情况，在与投影矩阵相乘之前需要确保在合适的地方有一个干净的单位矩阵。实际意思是该方法是一个归位操作，因为每一次的矩阵变换都是基于栈顶矩阵的值，如果不归位的话会一直计算，影响下一轮的值。所以进行归位实际上是置为单位矩阵，那么哪怕是相乘也不产生影响。

**4、指定三角形**：首先要定义一个三角形的组成部分。

* 一个三角形由3个点组成。
* 每个点都称为顶点
* 一个顶点对应3D空间中的一个位置。
* 3D空间中的一个位置由3个浮点数表示，分别是x、y、z坐标。
* 一个顶点可以有一些附加属性，例如颜色或者纹理，这些属性也都由浮点数表示。

OpenGL采用数组的形式定义三角形，但是，OpenGL实际上是通过C API提供接口，因此无法使用标准JAVA数组。这里使用的替代方法是采用JAVA的NIO缓冲区，该缓冲区是一段连续字节的内存块。

为了保证完全准确，需要使用直接NIO缓冲区。这意味着这块内存不是在虚拟机的对内存中，而是在主机的堆内存中。可以使用下面的代码：

	ByteBuffer buffer=ByteBuffer.allocateDirect(NUMBER_OF_BYTES);
	buffer.order(ByteOrder.nativeOrder());

上面的代码将分配一个总长NUMBER_OF_BYTES字节的ByteBuffer，并且保证字节的顺序等于底层的CPU的字节顺序。一个NIO缓冲区有3个属性，

* Capacity：缓冲区可以容纳的元素的总个数；
* Position：下一个元素将要写入或读取的当前位置；
* Limit：最后一个元素的索引加1。

一个缓冲区的容量实际上是它的大小，Position和Limit属性可以看成是缓冲区中从位置开始到界限（不包括在内）为止定义的一个段。既然需要使用浮点数表示顶点，那么最好不用处理字节。可以将ByteBuffer实例转换成FloatBuffer实例，这样就可以使用浮点数。

	FloatBuffer floatBuffer=buffer.asFloatBuffer();

在FloatBuffer中，容量，位置和界限都是浮点型，这些缓冲区的使用方式也很简单，

	float[] vertices={...definitions of vertex positions etc...};
	floatBuffer.clear();
	floatBuffer.put(vertices);
	floatBuffer.flip();

首先使用标准JAVA浮点型数组定义数据，在将数组存入缓冲区之前，需要将缓冲区清空。clear()方法只是把位置属性设为0，把界限属性设为容量。使用put()方法从缓冲区当前位置将所有的内容复制到缓冲区中，复制完后，缓冲区位置属性将加上数组的长度。接着put()方法会给复制到缓冲区的最后一个数组的数据附加其他数据，最后调用的FloatBuffer.flip()只是将位置值和界限值交换。OpenGL需要准确知道位置值和需要读取的元素数量。

有时在填充完缓冲区后，手动设置属性是很有用的。通过调用下面的方法可以实现该功能，

	FloatBuffer.position(int position);

当需要临时给一个填充过的缓冲区设置一个非零位置值。以便OpenGL将从指定的位置开始进行读取时，这个方法就派上了用场。

**5、将顶点发送到OpenGL ES**

假定坐标系统是从（0，0，1）到（320，480，-1），可以使用下面的代码：

	ByteBuffer byteBuffer=ByteBuffer.allocateDirect(3*2*4);
	byteBuffer.order(ByteOrder.nativeOrder());
	FloatBuffer vertices=byteBuffer.asFloatBuffer();
	vertices.put(new flaot[]{0.0f,0.0f,
				319.0f,0.0f,
				160.0f,479.0f});
	vertices.flip();

主要的部分是分配字节，这里有3个顶点，每个顶点是有一个给定位置的x，y坐标的位置组成的。每一个坐标值是一个浮点数，占据4个字节。3个顶点数量乘以两个坐标值数量，再乘以4个字节数量，因此这个三角形共需要24个字节。

> 只能使用x，y坐标指定顶点，OpenGL ES会自动将z坐标置为0.

在缓冲区中使用一个浮点数组保存顶点的位置。三角形从左下角（0，0）坐标开始，直到视锥/屏幕的右边缘（319，0），然后到达视锥/屏幕顶边缘的中部。作为一个使用NIO缓冲区的好习惯，在缓冲区上也使用了flip()方法。这样位置被设置为0，界限值将被设置为6（FloatBuffer的界限和位置值都是浮点型，而不是字节）。

一旦NIO缓冲区可用，OpenGL便可根据它的当前状态（即视口和投影矩阵）进行绘制。代码如下：

	gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
	gl.glVertexPointer(2,GL10.GL_FLOAT,0,vertices);
	gl.glDrawArrays(GL10.GL_TRIANGLES,0,3);

glEnableClientState()方法的调用是有点遗留问题，它告诉OpenGL这些需要绘制的顶点都有一个位置。它的问题有：

* 常量名为GL10.GL_VERTEX_ARRAY，这容易造成混淆，命名为GL10.GL_POSITION_ARRAY会更好一些。
* 因为无法绘制任何没有位置的物体，所以调用该方法有些多余。但仍然这样做的目的是为了满足OpenGL的要求。

在调用glVertexPointer()方法时，需要告诉OpenGL从何处取得顶点的位置和其他一些附加信息。第一个参数告诉OpenGL每个顶点位置是由x，y坐标组成的。如果需要制定x，y和z的值那么需要将这个值设置为3。第二个参数告诉OpenGL坐标所使用的数据类型，在此是GL10.GL_FLOAT，这说明坐标值使用的是浮点型，占用4个字节。第三个参数是步长，告诉OpenGL顶点位置值之间的字节距离，在此步长为0.说明位置值是紧密封装的(vertex1(x,y),vertex2(x,y)等)。最后一个参数是FloatBuffer，对于它有亮点：

* FloatBuffer代表原生堆中的一个内存块，并有一个起始地址。
* FloatBuffer的位置是从起始地址开始的偏移量。

OpenGL将管理该缓冲区的起始地址并将缓冲区的位置设为浮点数所在的位置。当需要绘制缓冲区中的内容时，OpenGL将从该位置读取这些顶点值。顶点指针（或者位置指针）是OpenGL中的一个状态，只要该值未改动（上下文也未丢失），OpenGL将记住并在所有需要使用顶点位置的子调用中使用它。

最后调用glDrawArrays()方法，它将绘制三角形。第一个参数指明了将要绘制的物体类型。在本例中，通过GL10.GL_TRIANGLES指明将渲染三角形。下一个参数是与顶点指针指向的第一个顶点相关量的偏移量。该偏移量以顶点为单位进行测量的，而不是以字节或浮点为单位。如果指定了多个三角形，那么可以使用这个偏移量渲染三角形列表的最后一个子集。最后一个参数告诉OpenGL渲染时使用的顶点数量。在此是3个顶点。如果绘制的是GL10.GL_TRIANGLES类型，顶点数量必须是3的倍数。

一旦发出了glVertexPointer()命令，OpenGL将把顶点位置传输到GPU并进行存储一共后续渲染命令使用。每次渲染顶点时读取的顶点数据是最近一次使用glVertexPointer()指定的。每个顶点可能拥有多个属性。如果不设置颜色，绘制时会使用默认值。这只颜色的方法为：

	GL10.glColor4f(float r,float g,float b,float a);

方法取值区间为0.0~1.0，默认颜色是（1，1，1，1），从完全不透明的白色开始。

这里是使用OpenGl绘制三角形所使用的16行代码，完成了清屏，设置视口与投影矩阵，创建存储顶点位置的NIO缓冲区和绘制三角形。

## 绘制三角形的示例代码

	public class FirstTriangleTest extends GLGame {
		public Screen getStartScreen() {
			return new FirstTriangleScreen(this);
		}

		class FirstTriangleScreen extends Screen {
			GLGraphics glGraphics;
			FloatBuffer vertices;

			public FirstTriangleScreen(Game game) {
				super(game);
				glGraphics = ((GLGame) game).getGLGraphics();

				ByteBuffer byteBuffer = ByteBuffer.allocateDirect(3 * 2 * 4);
				byteBuffer.order(ByteOrder.nativeOrder());
				vertices = byteBuffer.asFloatBuffer();
				vertices.put(new float[] { 0.0f, 0.0f, 319.0f, 0.0f, 160.0f, 479.0f });
				vertices.flip();
			}

			@Override
			public void present(float deltaTime) {
				GL10 gl = glGraphics.getGL();
				gl.glViewport(0, 0, glGraphics.getWidth(), glGraphics.getHeight());
				gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
				gl.glMatrixMode(GL10.GL_PROJECTION);
				gl.glLoadIdentity();
				gl.glOrthof(0, 320, 0, 480, 1, -1);

				gl.glColor4f(1, 0, 0, 1);
				gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
				gl.glVertexPointer(2, GL10.GL_FLOAT, 0, vertices);
				gl.glDrawArrays(GL10.GL_TRIANGLES, 0, 3);
			}

			@Override
			public void update(float deltaTime) {
				game.getInput().getTouchEvents();
				game.getInput().getKeyEvents();
			}

			@Override
			public void pause() {
			}

			@Override
			public void resume() {
			}

			@Override
			public void dispose() {
			}
		}
	}

在这个类中，有两个实例，GLGraphics实例和用来存储三角形3个顶点的2D位置的FloatBuffer。从OpenGl的最佳实践来看，之前的代码中做错了几件事：

* 在没有需求的地方，一遍一遍地将同一状态设为相同值，在OpenGL中改变状态的开销是巨大的，因此在单一帧中应当尽力减少状态改变的次数。
* 视口和投影矩阵一经设置就不会再改变，应当将这些代码移到resume()中，该方法只在OpenGL界面每次重新创建的时候被调用；这样也处理了OpenGL的上下文丢失。
* 应当将设置颜色的代码移到resume()中，这些代码设置的颜色用来清除或设置默认的顶点颜色，这两种颜色也不会改变。
* 应当将glEnableClientState()和glVertexPointer()方法移动到resume()中。
* 唯一需要在每一帧中调用的方法是glClear()和glDrawArray()。这两种方法都使用OpenGL的当前状态，只要我们不修改，并且不会因为活动暂停和恢复而丢失上下文，当前的状态就将保持不变。

如果实际中使用了这些优化方法，那么在主循环中只需要两个OpenGL调用。

> 这里还有一个小问题，三角形的右下角实际上丢失了一个像素，这实际上是因为OpenGL光栅化(绘制像素)三角形时所采用的方式造成的。有一个特定的三角形光栅处理规则导致了这个问题。但是实际上更多的是渲染2D矩形（由两个三角形组成），其中不会存在这个问题。

## 指定每个顶点的颜色

上一节使用glColor4f()方法设置顶点颜色，但是可以通过更细度的控制方式（例如，为每一个顶点设置颜色）。

	int VERTEX_SIZE=(2+4)*4;
	ByteBuffer byteBuffer=ByteBuffer.allocateDirect(3*VERTEX_SIZE);
	byteBuffer.order(ByteOrder.nativeOrder());
	FloatBuffer vertices=byteBuffer.asFloatBuffer();
	vertices.put(new Float[]{0.0f,0.0f,1,0,0,1,
				319.0f,0.0f,0,1,0,1,
				160.0f,479.0f,0,0,1,1});
	vertices.flip();

总共3个顶点，每个顶点需要两个坐标和4个颜色分量。因此共需要6个浮点数，每个浮点数占用4个字节。要使用这些数值，需要渲染它就必须告诉OpenGL不但有位置信息，还有颜色属性。

	gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
	gl.glEnableClientState(GL10.GL_COLOR_ARRAY);

还需要告诉OpenGL哪里可以得到这些信息：

	vertices.position(0);
	gl.glVertexPointer(2,GL10.GL_FLOAT,VERTEX_SIZE,vertices);
	verteices.position(2);
	gl.glColorPointer(4,GL10.GL_FLOAT,VERTEZ_SIZE,vertiecs);

这里需要指明顶点的大小。使用VERTEX_SIZE常量。接下来，设置第一个顶点的R颜色分量在缓冲区的位置并调用glColorPointer()方法，第一个参数是每个颜色的分量数，一直都是4，因为包含R、G、B和A四个分量。第二个参数指明分量类型，第三个参数是顶点颜色间的步长。与顶点位置间的步长一致，最后一个参数是顶点缓冲区。在调用glColorPointer()方法之前先调用vertices.position(2)，OpenGL知道第一个顶点的颜色从第三个浮点数开始。

为了绘制三角形，需要调用glDrawElements()方法，该方法告诉OpenGL使用FloatBuffer中的前3个顶点绘制一个三角形。

	gl.glDrawElements(GL10.GL_TRIANGLES,0,3);

由于设置了顶点和顶点颜色，那么OpenGL会自动忽略默认颜色。

**ColoredTriangleTest类的代码**

	public class ColoredTriangleTest extends GLGame {
		public Screen getStartScreen() {
			return new ColoredTriangleScreen(this);
		}

		class ColoredTriangleScreen extends Screen {
	    	final int VERTEX_SIZE = (2 + 4) * 4;
	    	GLGraphics glGraphics;
	    	FloatBuffer vertices;        

	    	public ColoredTriangleScreen(Game game) {
	        	super(game);
	        	glGraphics = ((GLGame) game).getGLGraphics();

	        	ByteBuffer byteBuffer = ByteBuffer.allocateDirect(3 * VERTEX_SIZE);
	        	byteBuffer.order(ByteOrder.nativeOrder());
	        	vertices = byteBuffer.asFloatBuffer();
	        	vertices.put( new float[] {   0.0f,   0.0f, 1, 0, 0, 1,
	                                    	319.0f,   0.0f, 0, 1, 0, 1,
	                                    	160.0f, 479.0f, 0, 0, 1, 1});
	        	vertices.flip();
	   		}

	    	@Override
	    	public void present(float deltaTime) {
	        	GL10 gl = glGraphics.getGL();
	        	gl.glViewport(0, 0, glGraphics.getWidth(), glGraphics.getHeight());
	        	gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	        	gl.glMatrixMode(GL10.GL_PROJECTION);
	        	gl.glLoadIdentity();
	        	gl.glOrthof(0, 320, 0, 480, 1, -1);
	        
	        	gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
	        	gl.glEnableClientState(GL10.GL_COLOR_ARRAY);
	        
	        	vertices.position(0);
	        	gl.glVertexPointer(2, GL10.GL_FLOAT, VERTEX_SIZE, vertices);
	        	vertices.position(2);            
	        	gl.glColorPointer(4, GL10.GL_FLOAT, VERTEX_SIZE, vertices);
	        
	        	gl.glDrawArrays(GL10.GL_TRIANGLES, 0, 3);
	    	}

			@Override
			public void update(float deltaTime) {
			}

			@Override
			public void pause() {
			}

			@Override
			public void resume() {
			}

			@Override
			public void dispose() {
			}
		}
	}

现在也可以关闭GL10.GL_COLOR_ARRAY，这样OpenGL可以使用默认的颜色了。可以采用之前的方式使用glColor4f()方法设置。关闭GL10.GL_COLOR_ARRAY可以使用下面方法：

	gl.glDisableClientState(GL10.GL_COLOR_ARRAY);

但是这样只是关闭从FloatBuffer读取颜色的功能，如果通过glColorPointer()设置了颜色指针，就会一直使用颜色指针。