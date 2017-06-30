title: Libgdx教程3——动画
date: 2015-10-13 21:52:22
tags: [libgdx]
---

## 摘要
Libgdx教程，动画。
<!--more-->

## Animation类介绍

功能：动画是由多个帧，在设定的时间间隔序列显示，比如，一个跑步的人一个动画可以通过运行时播放这些图像无限运动。Animation类用来管理动画，设置随机播放模式和播放顺序。

使用方法：animation=new Animation(float fDuration,keyFrames);

第一个参数是播放每一帧的时间，后面是一个TextureRegion。使用的图片显示一个完整的动画运行周期，被称为精灵表，每一帧按照顺序排列，animation会按顺序绘制每个精灵。

## TextureRegion[][]数组

	TextureRegion[][] tmp = TextureRegion.split(walkSheet, walkSheet.getWidth() / FRAME_COLS, walkSheet.getHeight() /FRAME_ROWS);

它将一整张动画图片分割，将获得的纹理分为一个二维数组，前提是分割的矩形大小相等。只是这样使用起来不方便。

## SetPlayMode()方法

它是Animation类自己封装的一个方法，是用来设置播放模式的，其中它提供的模式有6种：NORMAL，REVERSED，LOOP，LOOP_REVERSED，LOOP_PINGPONG，LOOP_RANDOM。

* NORMAL：就是正常的播放模式。
* REVERSED：反向播放，从后向前播放。
* LOOP：循环播放。
* LOOP_REVERSED：循环反向播放。
* LOOP_PINGPONG：先正向播放到最后一帧反向播放。
* LOOP_RANDOM：循环随机播放

## StateTime使用

代码stateTime+=Gdx.graphics.getDeltaTime()，获取一个状态下所持续的一个时间，配合系统时间使用Gdx.graphics.getDeltaTime()，获取系统渲染时间，一般默认的是0.173秒。

## 详细实现

	public class WalkAnim implements ApplicationListener {
	
		private static final int FRAME_COLS = 6;
		private static final int FRAME_ROWS = 5;
	
		Animation walkAnimation;
		Texture walkSheet;
		TextureRegion[] walkFrames;
		SpriteBatch batch;
		TextureRegion currentFrame;
	
		float stateTime;
	
		@Override
		public void create() {
			walkSheet = new Texture(Gdx.files.internal("animation_sheet.png"));
			TextureRegion[][] tmp = TextureRegion.split(walkSheet, walkSheet.getWidth() / FRAME_COLS, walkSheet.getHeight()
					/ FRAME_ROWS);
			walkFrames = new TextureRegion[FRAME_COLS * FRAME_ROWS];
			int index = 0;
			for (int i = 0; i < FRAME_ROWS; i++) {
				for (int j = 0; j < FRAME_COLS; j++) {
					walkFrames[index++] = tmp[i][j];
				}
			}
			walkAnimation = new Animation(0.025f, walkFrames);
			walkAnimation.setPlayMode(Animation.PlayMode.LOOP_PINGPONG);
			batch = new SpriteBatch();
			stateTime = 0f;
		}
	
		@Override
		public void dispose() {
	
		}
	
		@Override
		public void pause() {
	
		}
	
		@Override
		public void render() {
			Gdx.gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
			stateTime += Gdx.graphics.getDeltaTime();
			currentFrame = walkAnimation.getKeyFrame(stateTime, true);
			batch.begin();
			batch.draw(currentFrame, Gdx.graphics.getWidth() / 2, Gdx.graphics.getHeight() / 2);
			batch.end();
		}
	
		@Override
		public void resize(int arg0, int arg1) {
	
		}
	
		@Override
		public void resume() {
	
		}
	}