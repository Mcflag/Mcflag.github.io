title: Libgdx教程1——图形绘制
date: 2015-10-10 16:21:38
tags: [libgdx]
---

## 摘要
Libgdx教程，图形绘制。
<!--more-->

## libgdx的环境

下载的libgdx文件包里有libgdx.so库，放在libs文件夹中，android下再放入gdx.jar和gdx-backend-android.jar两个文件即可。

项目的主要Activity需要继承自AndroidApplication，onCreate方法中可以进行初始的设定。比如：

	public class MainActivity extends AndroidApplication {
	
		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			AndroidApplicationConfiguration config = new AndroidApplicationConfiguration();
			config.useAccelerometer = false;
			config.useCompass = false;
			config.useWakelock = true;
			initialize(new FirstGame(), config);
		}
	}

里面的FirstGame类是一个显示一个屏幕的类。其逻辑实质是接收操作，并显示绘制图形到屏幕上。它需要实现一个ApplicationListener。

	public class FirstGame implements ApplicationListener {
	
		private SpriteBatch batch;

		@Override
		public void create() {
			spriteBatch = new SpriteBatch();
		}
	
		@Override
		public void dispose() {
		}
	
		@Override
		public void pause() {
		}
	
		@Override
		public void render() {
			Gdx.gl.glClear(GL10.GL_COLOR_BUFFER_BIT);//清屏
			spriteBatch.begin();
			spriteBatch.end();
		}
	
		@Override
		public void resize(int width, int height) {
		}
	
		@Override
		public void resume() {
		}
	}

## Texture类，纹理

API定义：图片从原始格式解码并上传到GPU就被称为纹理。

功能：作为容器承装获取到的目的图片，从逻辑上可以当做一张图片。

Texture一般作为参数。获取图片的方法常用方法是使用类似Gdx.files.internal("data/Potato.jpg")的方法。读取的文件必须要注明格式。

Gdx.files是libgdx的文件模块，主要提供5大功能：读取文件，写入文件，复制文件，移动文件，列出文件和目录。

* Classpath：路径相对于classpath，文件通常为只读。
* Internal：内部文件路径相对于程序根目录或者android的assets文件夹。
* External：外部文件路径是相对于SD卡根目录。
* Absolute：绝对路径。
* assets文件夹本身就是存储资源的文件夹，相比resource文件夹，它其中的资源不会生成R中的ID，用来放图片很合适。

用Gdx.files.internal("data/Potato.jpg")获取图片，调用batch.draw(texture,x,y,width,height)方法绘制图形。x，y是绘图起点坐标，左下角为原点，height和width是图形大小，绘制方向是由下向上，由左至右。

## SpriteBatch类

API定义：SpriteBatch用于绘制二维矩形参考纹理（区域），SpriteBatch类可用于批量绘图命令和优化处理的GPU。

功能用途：SpriteBatch可以把许多相同纹理一起描述并一起送入GPU，同时赋予纹理和坐标以便每个图形的绘制。使用SpriteBatch来完成纹理映射并在屏幕上显示被纹理映射的四边形的所有工作。它使得在屏幕上绘制图形极为简单，并且经过优化。由于SpriteBatch可以同时处理多个绘制请求、使用GPU进行渲染加速并序列化请求，所以只要启动它就行。

使用SpriteBatch：声明类名，然后实例化绘制时分3步，先调用begin()，然后调用draw()，最后调用end()结束。

	private Texture texture;
	private Sprite sprite;
	private SpriteBatch batch;

	@Override
	public void create(){
		float w=Gdx.graphics.getWidth();
		float h=Gdx.graphics.getHeight();

		batch=new SpriteBatch();
		texture=new Texture(Gdx.files.internal("data/Potato.jpg"));
		texture.setFilter(TextureFilter.Linear, TextureFilter.Linear);
	}

	@Override
	public void render(){
		Gdx.gl.glClearColor(1,1,1,1);//设置背景色为白色
		Gdx.gl.glClear(GL10.GL_COLOR_BUFFER_BIT);//清屏
		batch.begin();
		batch.draw(texture,x,y,w,h);
		batch.end();
	}

## TextureRegion类

API定义：定义了一个矩形区域的纹理，使用坐标系统是x轴指向右，y轴指向下，左上角是原点。

功能：作为得到纹理的一部分而进行使用的类。在一个游戏里，必然有很多需要绘制的元素，如果每个元素都要转换成一张纹理，对GPU的资源消耗是很大的，因为在绘制前，GPU都要把图片载入显存并绑定到OpenGL上，然后OpenGL再绘制需要的纹理并在不同纹理间切换，而绑定和切换的代价都是很昂贵的。TextureRgion能解决这个问题，它从一个纹理上切割出一个区域并让SpriteBatch作用于该区域。这样，一张纹理上可以包含多个需要绘制的元素，而只有与该元素对应的那部分才会绘制出来。包含多个绘制元素的纹理也称为sprite sheet。

TextureRegion一般都是截取texture，然后定义截取起点（x，y）随后再定义宽高（width，height），如果宽高是正数，那么就是沿x，y正方向截取，如果是负就是沿x，y负方向截取，方向只和宽高的正负有关。比如：

	TextureRegion textureRegion1 = new TextureRegion(texture, 0, 0, 48, 48);
	TextureRegion textureRegion2 = new TextureRegion(texture, 48, 48, -48, -48);

如果要被分割的纹理区域大小相等且没有间隔，可以用一种更简单的方式来创建TextureRegion：

	TextureRegion[][] regions = TextureRegion.split(texture, 64, 64);

仍然可以使用SpriteBatch来绘制，

	batch.draw(textureRegion,0,0,width,height);

## Sprite类

API定义：持有几何形状，颜色和纹理信息来绘制2D图形。增加了旋转缩放的动画。

功能：继承自TextureRegion，多了指定位置，颜色，旋转。

	public SpriteBatch batch;
	public Texture texture;
	public Sprite sprite;

	@Override
	public void create(){
		batch=new SpriteBatch();
		texture=new Texture(Gdx.files.internal("data/Potato.jpg"));
		TextureRegion region=new TextureRegion(texture,512,0,512,512);
		sprite=new Sprite(region);
		sprite.setSize(120,120);//设置绘制的大小
		sprite.setOrigin(sprite.getWidth()/2,sprite.getHeight()/2);//置旋转的中心点
		sprite.setRotation(50);//这个是以上面设置的中心点为中心，旋转一定角度的设置
		sprite.setPosition(150,110);//起始位置设置为（150,110）
		sprite.setColor(1,1,1,1);//设置颜色
	}

	@Override
	public void dispose() {
		batch.dispose();
		texture.dispose();
	}

	@Override
	public void render() {
		Gdx.gl.glClearColor(1, 1, 1, 1);// 设置背景颜色为白色
		Gdx.gl.glClear(GL10.GL_COLOR_BUFFER_BIT);// 清屏
		batch.begin();
		sprite.draw(batch);
		batch.end();
	}

## 清屏

OpenGL绘制纹理时需要进行清屏的操作，否则会显示前一张纹理。不过既可以在在清屏前设置背景为白色也可以不设置直接清屏。

	Gdx.gl.glClearColor(1,1,1,1);//设置清屏颜色为白色
	Gdx.gl.glClear(GL10.GL_COLOR_BUFFER_BIT);//清屏

## 关于混合的问题

默认是开启了混合的，意味着图形绘制完成时半透明的部分已经被混合了，当混合被关闭时任何已经在场景中的东西会被纹理代替，这适合绘制大背景。

	batch.disableBlending();
	backgroundSprite.draw(batch);
	batch.enableBlending();

## 关于性能优化

SpriteBatch有个构造函数可以指定最大缓冲数目，如果数值过低会造成额外的GPU调用，过高又会占用过多的内存。

在SpriteBatch有个字段为maxSpritesInBatch，可以先设置一个很高的缓冲数目，然后观察maxSpritesInBatch的值以确定合适的缓冲值。

还有一个字段renderCalls，在end被调用时，它的值表示在begin和end之间几何声明被送入GPU的次数。

还有一个构造函数可以指定缓冲大小和数量，合理的设置可以极大地提供性能。

## Texture,TextureRegion,Sprite,SpriteBatch的比较

* Texture :保存在GPU缓冲中的一张纹理。
* TextureRegion： 定义了一块矩形区域的纹理。主要作用是从一块纹理中择取需要使用的矩形区域。
* Sprite：Holds the geometry, color, and texture information for drawing 2D。这个类是继承于TextureRegion，是对TextureRegion的功能的扩展，相比增加了旋转，动画等效果。
* SpriteBatch：A SpriteBatch is used to draw 2D rectangles that reference a texture (region). 主要处理对纹理的绘画，绘画前的优化等。

从源码中可以知道：Sprite可以使用TextureRegion构造，TextureRegion可以使用Texture构造。所以SpriteBatch在底层最终操作的还是Texture。