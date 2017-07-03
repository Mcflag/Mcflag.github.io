title: 8、OpenGL示例下
date: 2015-09-12 22:31:43
tags: [游戏框架]
categories: [游戏框架]
---

## 摘要
OpenGL示例
<!--more-->

## 纹理映射

纹理坐标：就是把纹理（一张位图）中的一个点映射到三角形的一个顶点上，纹理映射通常都是2D的。相对位置坐标称为x,y,z,纹理坐标称为u、v或者s、t。而且不论纹理图像的宽高比是多少，坐标系统总是左上角为（0，0），右下角为（1，1）。三角形的位置坐标系统和纹理坐标系统中朝向不必相同。

	int VERTEX_SIZE=(2+2)*4;
	ByteBuffer byteBuffer=ByteBuffer.allocateDirect(3*VERTEX_SIZE);
	byteBuffer.order(ByteOrder.nativeOrder());
	vertices=byteBuffer.asFloatBuffer();
	vertices.put(new float[] {	0.0f,	0.0f,	0.0f,	1.0f,
								319.0f,	0.0f,	1.0f,	1.0f,
								160.0f,	479.0f,	0.5f,	0.0f});
	vertices.flip();

为了告诉OpenGl顶点含有纹理坐标，可以使用下面的方法：

	gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
	gl.glEnableClientState(GL10.GL_TEXTURE_COORD_ARRAY);

	vertices.position(0);
	gl.glVertexPointer(2,GL10.GL_FLOAT,VERTEX_SIZE,vertices);
	vertices.position(2);
	gl.glTexCoordPointer(2,GL10.GL_FLOAT,VERTEX_SIZE,vertices);

### 上传位图

首先需要上传位图，创建一个Bitmap，然后告诉OpenGL这是一个新的纹理。

	Bitmap bitmap=BitmapFactory.decodeStream(game.getFileIO().readAsset("pic.png"));
	GL10.glGenTextures(int numTextures, int[] ids, int offset);

第一个参数指明需要多少个纹理对象，通常只需要创建一个对象，第二个参数是一个整型数组，OpenGL把生成纹理对象的ID保存到数组中，最后一个参数告诉OpenGl从何处开始保存ID。

	int textureIds[]=new int[i];
	gl.glGenTextures(1,textureIds,0);
	int textureId=textureIds[0];

现在还并没有绑定纹理对象，使用glBindTexture()方法绑定纹理对象。

	gl.glBindTexture(GL10.GL_TEXTURE_2D,textureId);
	GLUtils.texImage2D(GL10.GL_TEXTURE_2D,0,bitmap,0);

glBindTexture()方法的第二个参数一大确定，所有使用2D纹理的方法都使用这个纹理对象。GLUtils是由Android框架提供的。通常上传纹理图像任务十分复杂，该类可以提供帮助。需要做的事指明纹理类型（）、多纹理映射层次（默认为0）、需要上传的位图和一个在所有情况下都要设为0的参数。纹理对象和图像数据实际上保存在视频的RAM中，而不是通常的RAM，会丢失，每次上下文重新创建时，纹理和图像也要重新创建。

### 纹理过滤

三角形所在屏幕上占据的区域可能比纹理投影的区域大很多或小很多。这样情况下需要放大或者缩小纹理，这也称为纹理过滤。

	gl.glTexParameterf(GL10.GL_TEXTURE_2D,GL10.GL_TEXTURE_MIN_FILTER,GL10.GL_NEAREST);
	gl.glTexParameterf(GL10.GL_TEXTURE_2D,GL10.GL_TEXTURE_MAG_FILTER,GL10.GL_NEAREST);

GL10.GL_TEXTURE_MIN_FILTER和GL10.GL_TEXTURE_MAG_FILTER表示放大倍数和缩小倍数，最后一个参数指明过滤器的类型。nearest总是选择要映射到像素的纹理映射中最接近的纹元，第二种采样距待处理的三角形像素最近的4个纹元，取他们的平均值之后得到该像素的最终颜色。第一个方法可以得到像素化的图像，第二个方法可以得到平滑图像。

	gl.glBindTexture(GL10.GL_TEXTURE_2D,0);
	bitmap.recycle();

glBindTexture中的0是解除当前绑定的对象。

### 释放纹理

可以使用recycle()来释放。

	gl.glBindTexture(GL10.GL_TEXTURE_2D,0);
	int textuerids={textureid};
	gl.glDeleteTextures(1,textureIds,0);

删除纹理对象之前，需要确定它没有被绑定。

### 启用纹理

纹理映射是否使用还需要通过以下代码设置：

	GL10.glEnable(GL10.GL_TEXTURE_2D);
	GL10.giDisable(GL10.GL_TEXTURE_2D);

### TexturedTriangleTest类，使用纹理的示例

	public class TextureTriangleTest extends GLGame {

		@Override
		public Screen getStartScreen() {
			return new TexturedTriangleScreen(this);
		}

		class TexturedTriangleScreen extends Screen {

			final int VERTEX_SIZE = (2 + 2) * 4;
			GLGraphics glGraphics;
			FloatBuffer vertices;
			int textureId;

			public TexturedTriangleScreen(Game game) {
				super(game);
				glGraphics = ((GLGame) game).getGLGraphics();
				ByteBuffer byteBuffer = ByteBuffer.allocateDirect(VERTEX_SIZE * 3);
				byteBuffer.order(ByteOrder.nativeOrder());
				vertices = byteBuffer.asFloatBuffer();
				vertices.put(new float[] { 0.0f, 0.0f, 0.0f, 1.0f, 319.0f, 0.0f, 1.0f, 1.0f, 160.0f, 479.0f, 0.5f, 0.0f });
				vertices.flip();
				textureId = loadTexture("glbasic/bobrgb8888.png");
			}

			public int loadTexture(String fileName) {
				try {
					Bitmap bitmap = BitmapFactory.decodeStream(game.getFileIO().readAsset(fileName));
					GL10 gl = glGraphics.getGL();
					int textureIds[] = new int[1];
					gl.glGenTextures(1, textureIds, 0);
					int textureId = textureIds[0];
					gl.glBindTexture(GL10.GL_TEXTURE_2D, textureId);
					GLUtils.texImage2D(GL10.GL_TEXTURE_2D, 0, bitmap, 0);
					gl.glTexParameterf(GL10.GL_TEXTURE_2D, GL10.GL_TEXTURE_MIN_FILTER, GL10.GL_NEAREST);
					gl.glTexParameterf(GL10.GL_TEXTURE_2D, GL10.GL_TEXTURE_MAG_FILTER, GL10.GL_NEAREST);
					gl.glBindTexture(GL10.GL_TEXTURE_2D, 0);
					bitmap.recycle();
					return textureId;

				} catch (IOException e) {
					throw new RuntimeException("couldn't load asset '" + fileName + "'");
				}
			}

			@Override
			public void update(float deltaTime) {

			}

			@Override
			public void present(float deltaTime) {
				GL10 gl=glGraphics.getGL();
				gl.glViewport(0, 0, glGraphics.getWidth(), glGraphics.getHeight());
				gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
				gl.glMatrixMode(GL10.GL_PROJECTION);
				gl.glLoadIdentity();
				gl.glOrthof(0, 320, 0, 480, 1, -1);
			
				gl.glEnable(GL10.GL_TEXTURE_2D);
				gl.glBindTexture(GL10.GL_TEXTURE_2D, textureId);
			
				gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
				gl.glEnableClientState(GL10.GL_TEXTURE_COORD_ARRAY);
			
				vertices.position(0);
				gl.glVertexPointer(2, GL10.GL_FLOAT, VERTEX_SIZE, vertices);
				vertices.position(2);
				gl.glTexCoordPointer(2, GL10.GL_FLOAT, VERTEX_SIZE, vertices);
			
				gl.glDrawArrays(GL10.GL_TRIANGLES, 0, 3);
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

有一点需要记住，所有加载的位图的宽度和高度都必须是2的次幂。在示例中使用的图像是128*128的。另一个问题是颜色深度，即使没有设置，GLUtils.texImage2D()也能够得到正确的图像数据。

## Texture类

在框架中专门写一个类来辅助处理纹理对象。并且封装一些易用的方法来绑定和清理纹理。

	public class Texture {
		GLGraphics glGraphics;
		FileIO fileIO;
		String fileName;
		int textureId;
		int minFilter;
		int magFilter;
		int width;
		int height;

		public Texture(GLGame glGame, String fileName) {
			this.glGraphics = glGame.getGLGraphics();
			this.fileIO = glGame.getFileIO();
			this.fileName = fileName;
			load();
		}

		private void load() {
			GL10 gl = glGraphics.getGL();
			int[] textureIds = new int[1];
			gl.glGenTextures(1, textureIds, 0);
			textureId = textureIds[0];

			InputStream in = null;
			try {
				in = fileIO.readAsset(fileName);
				Bitmap bitmap = BitmapFactory.decodeStream(in);
				width = bitmap.getWidth();
				height = bitmap.getHeight();
				gl.glBindTexture(GL10.GL_TEXTURE_2D, textureId);
				GLUtils.texImage2D(GL10.GL_TEXTURE_2D, 0, bitmap, 0);
				setFilters(GL10.GL_NEAREST, GL10.GL_NEAREST);
				gl.glBindTexture(GL10.GL_TEXTURE_2D, 0);
			} catch (IOException e) {
				throw new RuntimeException("Couldn't load texture '" + fileName + "'");
			} finally {
				if (in != null) {
					try {
						in.close();
					} catch (IOException e) {
					}
				}
			}
		}

		public void reload() {
			load();
			bind();
			setFilters(minFilter, magFilter);
			glGraphics.getGL().glBindTexture(GL10.GL_TEXTURE_2D, 0);
		}

		public void setFilters(int minFilter, int magFilter) {
			this.minFilter = minFilter;
			this.magFilter = magFilter;
			GL10 gl = glGraphics.getGL();
			gl.glTexParameterf(GL10.GL_TEXTURE_2D, GL10.GL_TEXTURE_MIN_FILTER, minFilter);
			gl.glTexParameterf(GL10.GL_TEXTURE_2D, GL10.GL_TEXTURE_MAG_FILTER, magFilter);
		}

		public void bind() {
			GL10 gl = glGraphics.getGL();
			gl.glBindTexture(GL10.GL_TEXTURE_2D, textureId);
		}

		public void dispose() {
			GL10 gl = glGraphics.getGL();
			gl.glBindTexture(GL10.GL_TEXTURE_2D, textureId);
			int[] textureIds = { textureId };
			gl.glDeleteTextures(1, textureIds, 0);
		}
	}

在上下文丢失时可以使用reload()方法，同时只有在实际绑定了纹理之后，setFilter()方法才会有效。

## 索引顶点，重用的好处

如果有多个三角形的情况下，有可能多个三角形会有共用一些顶点。而且其实渲染一个矩形可以定义为两个三角形。下面先使用旧的方法，即将一个矩形当成两个三角形，其中两个顶点具有相同的位置，颜色和纹理坐标。需要很明确的告诉OpenGL每个三角形使用哪些顶点。例如第一个三角形使用索引值 0，1，2，第二个三角形使用索引值2，3，0。

	short[] indices={0,1,2,2,3,0};

OpenGL要求索引值使用短整型，或者使用字节。但是并不能够将一个短整型数组传给OpenGL，需要使用ShortBuffer。

	ByteBuffer byteBuffer=ByteBuffer.allocate(indices.length*2);
	byteBuffer.order(ByteOrder.nativeOrder());
	ShortBuffer shortBuffer=byteBuffer.asShortBuffer();
	shortBuffer.put(indices);
	shortBuffer.flip();

一个短整型需要2个字节内存空间。如果使用两个三角形绘制成矩形，需要采用如下方式定义顶点。

	ByteBuffer byteBuffer=ByteBuffer.allocateDirect(4*VERTEX_SIZE);
	byteBuffer.order(ByteOrder.nativeOrder());
	vertices=byteBuffer.asFloatBuffer();
	vertices.put(new float[]{100.0f, 100.0f, 0.0f, 1.0f,
							228.0f, 100.0f, 1.0f, 1.0f,
							228.0f, 229.0f, 1.0f, 0.0f,
							100.0f, 228.0f, 0.0f, 0.0f});
	vertices.flip();

同样需要调用glEnableClientState(),glVertexPointer()或glTexCoordPointer()方法，不同之处在于这个实际绘制的方法：

	gl.glDrawElements(GL10.GL_TRIANGLES,6,GL10.GL_UNSIGNED_SHORT,indices);

第一个参数指明了渲染基本类型，第二个参数指明顶点数，第三个参数指明索引类型，第四个是保存6个索引的ShortBuffer。

## IndexedScreen类，绘制两个索引三角形

	public class indexedTest extends GLGame {
		public Screen getStartScreen() {
			return new IndexedScreen(this);
		}

		class IndexedScreen extends Screen {
			final int VERTEX_SIZE = (2 + 2) * 4;
			GLGraphics glGraphics;
			FloatBuffer vertices;
			ShortBuffer indices;
			Texture texture;

			public IndexedScreen(Game game) {
				super(game);
				glGraphics = ((GLGame) game).getGLGraphics();

				ByteBuffer byteBuffer = ByteBuffer.allocateDirect(4 * VERTEX_SIZE);
				byteBuffer.order(ByteOrder.nativeOrder());
				vertices = byteBuffer.asFloatBuffer();
				vertices.put(new float[] { 100.0f, 100.0f, 0.0f, 1.0f, 228.0f, 100.0f, 1.0f, 1.0f, 228.0f, 228.0f, 1.0f, 0.0f, 100.0f, 228.0f, 0.0f, 0.0f });
				vertices.flip();

				byteBuffer = ByteBuffer.allocateDirect(6 * 2);
				byteBuffer.order(ByteOrder.nativeOrder());
				indices = byteBuffer.asShortBuffer();
				indices.put(new short[] { 0, 1, 2, 2, 3, 0 });
				indices.flip();

				texture = new Texture((GLGame) game, "bobrgb888.png");
			}

			@Override
			public void present(float deltaTime) {
				GL10 gl = glGraphics.getGL();
				gl.glViewport(0, 0, glGraphics.getWidth(), glGraphics.getHeight());
				gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
				gl.glMatrixMode(GL10.GL_PROJECTION);
				gl.glLoadIdentity();
				gl.glOrthof(0, 320, 0, 480, 1, -1);

				gl.glEnable(GL10.GL_TEXTURE_2D);
				texture.bind();

				gl.glEnableClientState(GL10.GL_TEXTURE_COORD_ARRAY);
				gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);

				vertices.position(0);
				gl.glVertexPointer(2, GL10.GL_FLOAT, VERTEX_SIZE, vertices);
				vertices.position(2);
				gl.glTexCoordPointer(2, GL10.GL_FLOAT, VERTEX_SIZE, vertices);

				gl.glDrawElements(GL10.GL_TRIANGLES, 6, GL10.GL_UNSIGNED_SHORT, indices);
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

## Vertices类

为了更易于使用，将会创建一个Vertices类，类有一个最大顶点数，并且可以使用索引进行渲染。可以启用所有渲染需要使用的状态，并在渲染结束后清理这些状态。

	public class Vertices {
		final GLGraphics glGraphics;
		final boolean hasColor;
		final boolean hasTexCoords;
		final int vertexSize;
		final FloatBuffer vertices;
		final ShortBuffer indices;

		public Vertices(GLGraphics glGraphics, int maxVertices, int maxIndices, boolean hasColor, boolean hasTexCoords) {
			this.glGraphics = glGraphics;
			this.hasColor = hasColor;
			this.hasTexCoords = hasTexCoords;
			this.vertexSize = (2 + (hasColor ? 4 : 0) + (hasTexCoords ? 2 : 0)) * 4;

			ByteBuffer buffer = ByteBuffer.allocateDirect(maxVertices * vertexSize);
			buffer.order(ByteOrder.nativeOrder());
			vertices = buffer.asFloatBuffer();

			if (maxIndices > 0) {
				buffer = ByteBuffer.allocateDirect(maxIndices * Short.SIZE / 8);
				buffer.order(ByteOrder.nativeOrder());
				indices = buffer.asShortBuffer();
			} else {
				indices = null;
			}
		}

		public void setVertices(float[] vertices, int offset, int length) {
			this.vertices.clear();
			this.vertices.put(vertices, offset, length);
			this.vertices.flip();
		}

		public void setIndices(short[] indices, int offset, int length) {
			this.indices.clear();
			this.indices.put(indices, offset, length);
			this.indices.flip();
		}

		public void draw(int primitiveType, int offset, int numVertices) {
			GL10 gl = glGraphics.getGL();

			gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
			vertices.position(0);
			gl.glVertexPointer(2, GL10.GL_FLOAT, vertexSize, vertices);

			if (hasColor) {
				gl.glEnableClientState(GL10.GL_COLOR_ARRAY);
				vertices.position(2);
				gl.glColorPointer(4, GL10.GL_FLOAT, vertexSize, vertices);
			}

			if (hasTexCoords) {
				gl.glEnableClientState(GL10.GL_TEXTURE_COORD_ARRAY);
				vertices.position(hasColor ? 6 : 2);
				gl.glTexCoordPointer(2, GL10.GL_FLOAT, vertexSize, vertices);
			}

			if (indices != null) {
				indices.position(offset);
				gl.glDrawElements(primitiveType, numVertices, GL10.GL_UNSIGNED_SHORT, indices);
			} else {
				gl.glDrawArrays(primitiveType, offset, numVertices);
			}

			if (hasTexCoords) {
				gl.glDisableClientState(GL10.GL_TEXTURE_COORD_ARRAY);
			}

			if (hasColor) {
				gl.glDisableClientState(GL10.GL_COLOR_ARRAY);
			}
		}
	}

并且现在可以这么使用Vertices类来减少代码：

	Vertices vertices=new Vertices(glGraphics,4,6,false,true);
	vertices.setVertices(new Float[]{	100.0f,	100.0f,	0.0f,	1.0f,
										228.0f,	100.0f,	1.0f,	1.0f,
										228.0f,	228.0f,	1.0f,	0.0f,
										100.0f,	228.0f,	0.0f,	0.0f},0,16);
	vertices.setIndices(new short[]{0,1,2,2,3,0}, 0, 6);

同样可以使用简单调用来代替设置顶点属性数组和渲染工作的调用：

	vertices.draw(GL10.GL_TRIANGLES,0,6);

## 半透明混合处理

在OpenGL中启用半透明混合处理，需要调用两个方法：

	gl.glEnable(GL10.GL_BLEND);
	gl.glBlendFunc(GL10.GL_SRC_ALPHA, GL10.GL_ONE_MINUS_SRC_ALPHA);

第一个方法通知OpenGL从现在开始对渲染的所有三角形启用半透明混合处理。第二个方法指明来源色和目的色如何混合。

BlendingTest类的代码

	public class BlendingTest extends GLGame {
		public Screen getStartScreen() {
			return new BlendingScreen(this);
		}

		class BlendingScreen extends Screen {
	    	GLGraphics glGraphics;
	    	Vertices vertices;
	    	Texture textureRgb;
	    	Texture textureRgba;
	    
	    	public BlendingScreen(Game game) {
	        	super(game);
	        	glGraphics = ((GLGame)game).getGLGraphics();
	        
	        	textureRgb = new Texture((GLGame)game, "glbasic/bobrgb888.png");
	        	textureRgba = new Texture((GLGame)game, "glbasic/bobargb8888.png");
	        
	        	vertices = new Vertices(glGraphics, 8, 12, true, true);
	        	float[] rects = new float[] {
	                100, 100, 1, 1, 1, 0.5f, 0, 1,
	                228, 100, 1, 1, 1, 0.5f, 1, 1,
	                228, 228, 1, 1, 1, 0.5f, 1, 0,
	                100, 228, 1, 1, 1, 0.5f, 0, 0,
	                
	                100, 300, 1, 1, 1, 1, 0, 1,
	                228, 300, 1, 1, 1, 1, 1, 1,
	                228, 428, 1, 1, 1, 1, 1, 0,
	                100, 428, 1, 1, 1, 1, 0, 0                    
	        	};
	        	vertices.setVertices(rects, 0, rects.length);
	        	vertices.setIndices(new short[] {0, 1, 2, 2, 3, 0,
	                                         4, 5, 6, 6, 7, 4 }, 0, 12);                        
	    	}
	    
	    	@Override
			public void present(float deltaTime) {
	        	GL10 gl = glGraphics.getGL();
	        	gl.glViewport(0, 0, glGraphics.getWidth(), glGraphics.getHeight());
	        	gl.glClearColor(1,0,0,1);
	        	gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	        	gl.glMatrixMode(GL10.GL_PROJECTION);
	        	gl.glLoadIdentity();
	        	gl.glOrthof(0, 320, 0, 480, 1, -1);
	        
	        	gl.glEnable(GL10.GL_BLEND);
	        	gl.glBlendFunc(GL10.GL_SRC_ALPHA, GL10.GL_ONE_MINUS_SRC_ALPHA);
	        
	        	gl.glEnable(GL10.GL_TEXTURE_2D);
	        	textureRgb.bind();
	        	vertices.draw(GL10.GL_TRIANGLES, 0, 6 );
	        
	        	textureRgba.bind();
	        	vertices.draw(GL10.GL_TRIANGLES, 6, 6 );
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

混合的开销很大，只有在确实需要的时候才使用混合。

## 2D变换：操作模型视图矩阵

平移，旋转和缩放操作矩阵。需要使用模型视图矩阵来进行变换。

	gl.glMatrixMode(GL10.GL_MODELVIEW);
	gl.glLoadIdentity();
	gl.glTranslatef(200,300,0);
	vertices.draw(GL10.GL_TRIANGLES,0,6);

通过常量GL10.GL_MODELVIEW指定模型视图矩阵，glTranslatef()有3个输入参数，平移发生在x、y和z轴。

## 使用平移的示例

**Bob类**

	class Bob {
    	static final Random rand = new Random();
    	public float x, y;
    	float dirX, dirY;

    	public Bob() {
        	x = rand.nextFloat() * 320;
        	y = rand.nextFloat() * 480;
        	dirX = 50;
        	dirY = 50;
    	}

    	public void update(float deltaTime) {
        	x = x + dirX * deltaTime;
        	y = y + dirY * deltaTime;

        	if (x < 0) {
            	dirX = -dirX;
            	x = 0;
       		}

        	if (x > 320) {
            	dirX = -dirX;
            	x = 320;
        	}

        	if (y < 0) {
            	dirY = -dirY;
            	y = 0;
        	}

        	if (y > 480) {
            	dirY = -dirY;
            	y = 480;
        	}
    	}
	}

可以使用下列代码渲染

	gl.glMatrixMode(GL10.GL_MODELVIEW);
	for(int i=0;i<100;i++){
		bob.update(deltaTime);
		gl.glLoadIdentity();
		gl.glTranslatef(bobs[i].x,bobs[i].y,0);
		bobModel.render(GL10.GL_TRIANGLES,0,6);
	}

**BobTest类：100个移动的Bob**

	public class BobTest extends GLGame {
    	public Screen getStartScreen() {
        	return new BobScreen(this);
    	}
    
    	class BobScreen extends Screen {
        	static final int NUM_BOBS = 100;
        	GLGraphics glGraphics;
        	Texture bobTexture;
        	Vertices bobModel;
        	Bob[] bobs;

        	public BobScreen(Game game) {
            	super(game);
            	glGraphics = ((GLGame)game).getGLGraphics();
            
            	bobTexture = new Texture((GLGame)game, "bobrgb888.png");
            
            	bobModel = new Vertices(glGraphics, 4, 12, false, true);
            	bobModel.setVertices(new float[] { -16, -16, 0, 1,  
                                                16, -16, 1, 1, 
                                                16,  16, 1, 0,
                                               -16,  16, 0, 0, }, 0, 16);
            	bobModel.setIndices(new short[] {0, 1, 2, 2, 3, 0}, 0, 6);

            
            	bobs = new Bob[100];
            	for(int i = 0; i < 100; i++) {
                	bobs[i] = new Bob();
            	}            
        	}

        	@Override
        	public void update(float deltaTime) {         
            	game.getInput().getTouchEvents();
            	game.getInput().getKeyEvents();
            
            	for(int i = 0; i < NUM_BOBS; i++) {
                	bobs[i].update(deltaTime);
            	}
        	}

        	@Override
        	public void present(float deltaTime) {
            	GL10 gl = glGraphics.getGL();
            	gl.glClearColor(1,0,0,1);
            	gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
            	gl.glMatrixMode(GL10.GL_PROJECTION);
            	gl.glLoadIdentity();
            	gl.glOrthof(0, 320, 0, 480, 1, -1);
            
            	gl.glEnable(GL10.GL_TEXTURE_2D);
            	bobTexture.bind();
            
            	gl.glMatrixMode(GL10.GL_MODELVIEW);
            	for(int i = 0; i < NUM_BOBS; i++) {
                	gl.glLoadIdentity();
                	gl.glTranslatef(bobs[i].x, bobs[i].y, 0);
                	gl.glRotatef(45, 0, 0, 1);
                	gl.glScalef(2, 0.5f, 0);
                	bobModel.draw(GL10.GL_TRIANGLES, 0, 6);
            	}            
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

**更多的变换**

除了glTranslate()，glRotatef()，glScalef()。

	GL10.glRotatef(float angle,float axisX,float axisY,float axisZ);

第一个参数是顶点需要旋转的角度，单位是度，后三个参数定义了一个旋转的轴。比如调用gl.glRotatef(45,0,0,1)，表示使用z轴作为旋转轴。使用glScalef()方法缩放图片。

	GL10.glScalef(float x,float y,float z);

该方法表示在x轴，y轴和z轴上的缩放。

所有的方法glTranslate(),glScalef(),glRotatef(),glOrthof()。

> 需要特别注意，对矩阵的操作，**最后指定的矩阵最先使用**。

## 性能优化

测量帧率

	public class FPSCounter {
    	long startTime = System.nanoTime();
    	int frames = 0;
    
    	public void logFrame() {
        	frames++;
        	if(System.nanoTime() - startTime >= 1000000000) {
            	Log.d("FPSCounter", "fps: " + frames);
            	frames = 0;
            	startTime = System.nanoTime();
        	}
    	}
	}

## 造成OpenGl渲染慢的原因

对OpenGL不好的：

* 在每一帧中改变状态很多次(例如混合、启用/关闭纹理映射等);
* 在每一帧中改变矩阵很多次；
* 在每一帧中绑定纹理很多次；
* 在每一帧中改变顶点、颜色和纹理坐标指针很多次。

由于glDrawElements()或glDrawArrays()方法被调用后并不是立即执行，这些命令被放在一个缓冲区中，由GPU异步执行。

* 改变当前绑定的纹理的代价是昂贵的，命令缓冲区中使用了纹理且还没有被处理的三角形必须首先进行渲染，管道将会停止。
* 改变顶点、颜色和纹理坐标指针的是昂贵的，命令缓冲区中使用就指针且还没有被渲染的三角形必须首先渲染，管道将会停止。
* 改变混合状态的代价是昂贵的，命令缓冲区中需要/不需要混合且还未被渲染的三角形必须首先渲染。管道将会停止。
* 改变模型视图或者投影矩阵的代价是昂贵的，命令缓冲区中应当应用旧矩阵且还未被处理的三角形必须首先渲染。管道将会停止。

主要的优化方式就是移除不必要的状态改变。减小纹理大小意味着需要获取更少的像素，减少OpenGL/JNI方法的调用，绑定顶点的概念。

将Vertices改为可绑定的模式，减少对OpenGL的调用

	public class BindableVertices {
		final GLGraphics glGraphics;
		final boolean hasColor;
		final boolean hasTexCoords;
		final int vertexSize;
		final FloatBuffer vertices;
		final ShortBuffer indices;

		public BindableVertices(GLGraphics glGraphics, int maxVertices, int maxIndices, boolean hasColor, boolean hasTexCoords) {
			this.glGraphics = glGraphics;
			this.hasColor = hasColor;
			this.hasTexCoords = hasTexCoords;
			this.vertexSize = (2 + (hasColor ? 4 : 0) + (hasTexCoords ? 2 : 0)) * 4;

			ByteBuffer buffer = ByteBuffer.allocateDirect(maxVertices * vertexSize);
			buffer.order(ByteOrder.nativeOrder());
			vertices = buffer.asFloatBuffer();

			if (maxIndices > 0) {
				buffer = ByteBuffer.allocateDirect(maxIndices * Short.SIZE / 8);
				buffer.order(ByteOrder.nativeOrder());
				indices = buffer.asShortBuffer();
			} else {
				indices = null;
			}
		}

		public void setVertices(float[] vertices, int offset, int length) {
			this.vertices.clear();
			this.vertices.put(vertices, offset, length);
			this.vertices.flip();
		}

		public void setIndices(short[] indices, int offset, int length) {
			this.indices.clear();
			this.indices.put(indices, offset, length);
			this.indices.flip();
		}

		public void bind() {
			GL10 gl = glGraphics.getGL();

			gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
			vertices.position(0);
			gl.glVertexPointer(2, GL10.GL_FLOAT, vertexSize, vertices);

			if (hasColor) {
				gl.glEnableClientState(GL10.GL_COLOR_ARRAY);
				vertices.position(2);
				gl.glColorPointer(4, GL10.GL_FLOAT, vertexSize, vertices);
			}

			if (hasTexCoords) {
				gl.glEnableClientState(GL10.GL_TEXTURE_COORD_ARRAY);
				vertices.position(hasColor ? 6 : 2);
				gl.glTexCoordPointer(2, GL10.GL_FLOAT, vertexSize, vertices);
			}
		}

		public void draw(int primitiveType, int offset, int numVertices) {
			GL10 gl = glGraphics.getGL();

			if (indices != null) {
				indices.position(offset);
				gl.glDrawElements(primitiveType, numVertices, GL10.GL_UNSIGNED_SHORT, indices);
			} else {
				gl.glDrawArrays(primitiveType, offset, numVertices);
			}
		}

		public void unbind() {
			GL10 gl = glGraphics.getGL();
			if (hasTexCoords)
				gl.glDisableClientState(GL10.GL_TEXTURE_COORD_ARRAY);

			if (hasColor)
				gl.glDisableClientState(GL10.GL_COLOR_ARRAY);
		}
	}

同时移除了不必要的状态改变后以及使用可绑定的BindableVertices类的示例如下：

	public class OptimizedBobTest extends GLGame {
		public Screen getStartScreen() {
			return new OptimizedBobScreen(this);
		}

		class OptimizedBobScreen extends Screen {
			static final int NUM_BOBS = 100;
			GLGraphics glGraphics;
			Texture bobTexture;
			BindableVertices bobModel;
			Bob[] bobs;
			FPSCounter fpsCounter;

			public OptimizedBobScreen(Game game) {
				super(game);
				glGraphics = ((GLGame) game).getGLGraphics();

				bobTexture = new Texture((GLGame) game, "bobrgb888-32x32.png");

				bobModel = new BindableVertices(glGraphics, 4, 12, false, true);
				bobModel.setVertices(new float[] { -16, -16, 0, 1, 16, -16, 1, 1, 16, 16, 1, 0, -16, 16, 0, 0, }, 0, 16);
				bobModel.setIndices(new short[] { 0, 1, 2, 2, 3, 0 }, 0, 6);

				bobs = new Bob[100];
				for (int i = 0; i < 100; i++) {
					bobs[i] = new Bob();
				}

				fpsCounter = new FPSCounter();
			}

			@Override
			public void update(float deltaTime) {
				game.getInput().getTouchEvents();
				game.getInput().getKeyEvents();

				for (int i = 0; i < NUM_BOBS; i++) {
					bobs[i].update(deltaTime);
				}
			}

			@Override
			public void resume() {
				GL10 gl = glGraphics.getGL();
				gl.glViewport(0, 0, glGraphics.getWidth(), glGraphics.getHeight());
				gl.glClearColor(1, 0, 0, 1);
				gl.glMatrixMode(GL10.GL_PROJECTION);
				gl.glLoadIdentity();
				gl.glOrthof(0, 320, 0, 480, 1, -1);

				gl.glMatrixMode(GL10.GL_MODELVIEW);

				bobTexture.reload();
				gl.glEnable(GL10.GL_TEXTURE_2D);
				bobTexture.bind();
			}

			@Override
			public void present(float deltaTime) {
				GL10 gl = glGraphics.getGL();
				gl.glClear(GL10.GL_COLOR_BUFFER_BIT);

				bobModel.bind();
				for (int i = 0; i < NUM_BOBS; i++) {
					gl.glLoadIdentity();
					gl.glTranslatef((int) bobs[i].x, (int) bobs[i].y, 0);
					bobModel.draw(GL10.GL_TRIANGLES, 0, 6);
				}
				bobModel.unbind();

				fpsCounter.logFrame();
			}

			@Override
			public void pause() {
			}

			@Override
			public void dispose() {
			}
		}
	}
