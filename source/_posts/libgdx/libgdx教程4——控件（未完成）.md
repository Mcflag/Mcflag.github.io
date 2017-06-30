title: Libgdx教程4——控件
date: 2015-10-15 22:45:57
tags: [libgdx]
---

## 摘要
Libgdx教程，控件。
<!--more-->

## 演员，舞台，控件

演员是游戏设计中常用的一个对象，它接受舞台的统一管理，拥有一些公共的事件，比如触摸，点击，但是同时还有自身的响应和属性。

舞台就是容纳演员的场所，它统一管理所有演员，接受输入，同时提供一个方便的框架操作演员的时间变化。

libgdx也提供了一些自带的演员，像标签，按钮，勾选框，下拉框，图片，输入框，列表，滑动面板，滑条等。这些控件可以直接拿来用。

## 标签（Lable）

文本标签，可以自动换行。标签可以进行缩放，旋转，设置起点。Label如果实例化出来需要传入一个LabelStyle参数，否则是无法实现效果的，也可以使用Skin，但是比较复杂，先使用简单的Label和LabelStyle配套使用的方法。

LabelStyle的使用：需要先创建一个.fnt和.png文件，再加上Color就构成了一个LabelStyle。

	LabelStyle style；
	font=new BitmapFont(Gdx.files.internal("data/Potato.fnt"),Gdx.files.internal("data/Potato.png"),false);
	style=new LabelStyle(font,font.getColor());

然后将LabelStyle传入Label使用

	Label(java.lang.CharSequence text,Label.LabelStyle style)
	Label(java.lang.CharSequence text,Skin skin)

通过API可以看出，前面的参数是一个字符串，后面是一个lableStyle，然后第二个lable参数设置，第一个是字符串，第二个是一个skin类型的参数。lable自带了一些方法，比如设置旋转、拉伸、位置、大小等。由于lable控件是属于actor类，所以应该加入到舞台中去展示出来，关于舞台我会在后面详细给大家讲解，这里简单用一下舞台。

	Stage stage;
	LabelStyle style;
	BitmapFont font;

	@Override
	public void create() {
		font = new BitmapFont(Gdx.files.internal("data/Potato.fnt"),Gdx.files.internal("data/Potato.png"), false);
		style = new LabelStyle(font, font.getColor());
		stage = new Stage(480, 320, false);
		Gdx.input.setInputProcessor(stage);
		Label label1 = new Label("Hello, \n  Potato", style);
		label1.setPosition(50, 150);
		label1.setFontScale(2);
		label1.setColor(Color.GREEN);
		stage.addActor(label1);
	}

	@Override
	public void render() {
		Gdx.gl.glClearColor(1, 1, 1, 1);
		Gdx.gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
		stage.act();
		stage.draw();
	}

## Image图片控件