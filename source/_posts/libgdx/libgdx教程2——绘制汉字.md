title: Libgdx教程2——绘制汉字
date: 2015-10-11 10:10:10
tags: [libgdx]
---

## 摘要
Libgdx教程，绘制汉字。
<!--more-->

## 显示汉字的基本方法

在libgdx中的汉字可以通过贴图的方式显示的，使用BitmapFont和SpriteBatch组合来完成文字的绘制。BitmapFont类是有关于文字绘制的，使用方式是：

	public BitmapFont(){
		this(Gdx.files.classpath("com/badlogic/gdx/utils/arial-15.fnt"),
			Gdx.files.classpath("com/badlogic/gdx/utils/arial-15.png"), false, true)
	}

这是默认的构造函数，可以看出它加载了两个文件arial-15.fnt和arial-15.png。libgdx的文字绘制是根据fnt文件获取对应文字的在png中的坐标位置，然后截取图片的相应部分进行绘制。那么要让libgdx支持中文思路就很简单了，我们自己构造fnt和png文件，其中包含我们要使用的中文即可。作者给我们提供了一个对应的工具：Hiero。通过应用生成两个文件，放在assets文件夹中。

	bitmapFont = new BitmapFont(Gdx.files.internal("cf.fnt"), Gdx.files.internal("cf.png"), false);
	bitmapFont.draw(spriteBatch, "FPS" + Gdx.graphics.getFramesPerSecond(), 5, Gdx.graphics.getHeight() - 10); 
	bitmapFont.draw(spriteBatch, "祝大家光棍节快乐", 0, Gdx.graphics.getHeight()/2-8);

关于多行文字可以调用：

	public TextBounds drawMultiLine(SpriteBatch spriteBatch, CharSequence str, float x, float y)

或者

	public TextBounds drawMultiLine (SpriteBatch spriteBatch, CharSequence str, float x, float y, float alignmentWidth,HAlignment alignment)

## 使用字体文件

前面显示中文时使用了BitmapFont（位图字体），这样虽然方便，但是有一个严重的问题：BitmapFont严重依赖图像，如果你需要不同尺寸那将不得不进行比例调整。解决的办法是使用libgdx的扩展，gdx-greetype。仅需要随游戏发布ttf，在运行时动态产生所需的BitmapFont，用户能够在你的游戏里使用自己希望看到的字体。

gdx-freetype作为libgdx的扩展，在android项目中使用时需要导入so库和gdx-freetype.jar包。

## FreeTypeFontGenerator类

功能：FreeTypeFontGenerator类是用来保存和解析".ttf"的字体文件然后备用。一般都配合FreeTypeBitmapFontData来使用。字体文件名称不能有中文。

	private FreeTypeFontGenerator generator;
	generator =new FreeTypeFontGenerator(Gdx.files.internal("data/bold.TTF"));

## FreeTypeBitmapFontData类

功能：负责处理FreeTypeFontGenerator的数据。

使用方法：先看代码：

	FreeTypeBitmapFontData fontData=freetypeGenerator.generateData(int size,some chinese string, false);

第一个参数是字号的大小，第二个是中文的字符串，汉字需要加引号，第三个翻转状态。

FreeTypeBitmapFontData中有个static final String DEFAULT_CHARS，相当于已经封装好了基本的英文字符，即使ttf文件中没有英文字符也可以输出英文字符。

这里的中文字符串中不允许出现重复字符，否则会出现“Key with name '****' is already in map”的错误。

## 实例

	private BitmapFont font;
	private FreeTypeFontGenerator generator;
	private FreeTypeBitmapFontData fontData;
	private SpriteBatch batch;

	@Override
	public void create() {
		generator = new FreeTypeFontGenerator(Gdx.files.internal("data/Baby.TTF"));
		fontData = generator.generateData(25, generator.DEFAULT_CHARS+ "长的帅人告白才叫，丑男那性骚扰。", false);// 这里需要把你要输出的字，全部写上，前提是不能有重复的字。
		font = new BitmapFont(fontData, fontData.getTextureRegion(), false);
		font.setColor(Color.RED);
		batch = new SpriteBatch();
	}

	@Override
	public void render() {
		Gdx.gl.glClearColor(1, 1, 1, 1);
		Gdx.gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
		batch.begin();
		font.drawMultiLine(batch,"hello: \n \n长的帅的人告白才叫告白，\n长的丑的男人告白那叫性骚扰。", 50, 220);
		batch.end();
	}