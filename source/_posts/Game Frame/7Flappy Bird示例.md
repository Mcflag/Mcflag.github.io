title: 7Flappy Bird示例
date: 2015-09-12 21:19:43
tags: [游戏框架]
---

## 摘要
Flappy Bird小游戏的实现。
<!--more-->

## 简介

项目依然使用前面写的框架。下面主要是加载资源，跳转界面以及实现游戏逻辑。

## Assets类，静态加载资源

	public class Assets {
		public static Pixmap background;
		public static Pixmap bird;
		public static Pixmap mainMenu;
		public static Pixmap buttons;
		public static Pixmap ready;
		public static Pixmap pause;
		public static Pixmap gameOver;
		public static Pixmap floor1;
		public static Pixmap floor2;
		public static Pixmap pipe1;
		public static Pixmap pipe2;

		// 十个数字
		public static Pixmap n0;
		public static Pixmap n1;
		public static Pixmap n2;
		public static Pixmap n3;
		public static Pixmap n4;
		public static Pixmap n5;
		public static Pixmap n6;
		public static Pixmap n7;
		public static Pixmap n8;
		public static Pixmap n9;

		public static Sound click;
		public static Sound eat;
		public static Sound bitten;

		public static Pixmap getNumber(int i) {
			switch (i) {
			case 1:
				return n1;
			case 2:
				return n2;
			case 3:
				return n3;
			case 4:
				return n4;
			case 5:
				return n5;
			case 6:
				return n6;
			case 7:
				return n7;
			case 8:
				return n8;
			case 9:
				return n9;
			default:
				return n0;
			}
		}
	}

## LoadingScreen类，加载资源

	public class LoadingScreen extends Screen {
		public LoadingScreen(Game game) {
			super(game);
		}

		@Override
		public void update(float deltaTime) {
			Graphics g = game.getGraphics();
			Assets.background = g.newPixmap("flappybird/bg1.png", PixmapFormat.RGB565);
			Assets.bird = g.newPixmap("flappybird/b1.png", PixmapFormat.ARGB4444);
			Assets.mainMenu = g.newPixmap("flappybird/mainmenu.png", PixmapFormat.ARGB4444);
			Assets.buttons = g.newPixmap("flappybird/buttons.png", PixmapFormat.ARGB4444);

			Assets.ready = g.newPixmap("flappybird/ready.png", PixmapFormat.ARGB4444);
			Assets.pause = g.newPixmap("flappybird/pausemenu.png", PixmapFormat.ARGB4444);
			Assets.gameOver = g.newPixmap("flappybird/gameover.png", PixmapFormat.ARGB4444);

			Assets.floor1 = g.newPixmap("flappybird/d1.png", PixmapFormat.ARGB4444);
			Assets.floor2 = g.newPixmap("flappybird/d2.png", PixmapFormat.ARGB4444);
			Assets.pipe1 = g.newPixmap("flappybird/g2.png", PixmapFormat.ARGB4444);
			Assets.pipe2 = g.newPixmap("flappybird/g1.png", PixmapFormat.ARGB4444);

			Assets.n0 = g.newPixmap("flappybird/n0.png", PixmapFormat.ARGB4444);
			Assets.n1 = g.newPixmap("flappybird/n1.png", PixmapFormat.ARGB4444);
			Assets.n2 = g.newPixmap("flappybird/n2.png", PixmapFormat.ARGB4444);
			Assets.n3 = g.newPixmap("flappybird/n3.png", PixmapFormat.ARGB4444);
			Assets.n4 = g.newPixmap("flappybird/n4.png", PixmapFormat.ARGB4444);
			Assets.n5 = g.newPixmap("flappybird/n5.png", PixmapFormat.ARGB4444);
			Assets.n6 = g.newPixmap("flappybird/n6.png", PixmapFormat.ARGB4444);
			Assets.n7 = g.newPixmap("flappybird/n7.png", PixmapFormat.ARGB4444);
			Assets.n8 = g.newPixmap("flappybird/n8.png", PixmapFormat.ARGB4444);
			Assets.n9 = g.newPixmap("flappybird/n9.png", PixmapFormat.ARGB4444);

			Assets.click = game.getAudio().newSound("flappybird/click.ogg");
			Assets.eat = game.getAudio().newSound("flappybird/eat.ogg");
			Assets.bitten = game.getAudio().newSound("flappybird/bitten.ogg");
			Settings.load(game.getFileIO());
			game.setScreen(new MainMenuScreen(game));
		}

		@Override
		public void present(float deltaTime) {

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

## MainMenuScreen类，主界面

	public class MainMenuScreen extends Screen{
		public MainMenuScreen(Game game) {
        	super(game);               
    	}   

    	@Override
    	public void update(float deltaTime) {
        	Graphics g = game.getGraphics();
        	List<TouchEvent> touchEvents = game.getInput().getTouchEvents();
        	game.getInput().getKeyEvents();       
        
        	int len = touchEvents.size();
        	for(int i = 0; i < len; i++) {
            	TouchEvent event = touchEvents.get(i);
            	if(event.type == TouchEvent.TOUCH_UP) {
                	if(inBounds(event, 0, g.getHeight() - 64, 64, 64)) {
                    	Settings.soundEnabled = !Settings.soundEnabled;
                    	if(Settings.soundEnabled)
                        	Assets.click.play(1);
                	}
                	if(inBounds(event, 64, 220, 192, 42) ) {
                    	game.setScreen(new GameScreen(game));
                    	if(Settings.soundEnabled)
                        	Assets.click.play(1);
                    	return;
                	}
                	if(inBounds(event, 64, 220 + 42, 192, 42) ) {
                    	game.setScreen(new HighscoreScreen(game));
                    	if(Settings.soundEnabled)
                        	Assets.click.play(1);
                    	return;
                	}
            	}
        	}
    	}
    
    	private boolean inBounds(TouchEvent event, int x, int y, int width, int height) {
        	if(event.x > x && event.x < x + width - 1 && event.y > y && event.y < y + height - 1) 
            	return true;
        	else
            	return false;
    	}

    	@Override
    	public void present(float deltaTime) {
        	Graphics g = game.getGraphics();
        
        	g.drawPixmap(Assets.background, 0, 0);
        	g.drawPixmap(Assets.mainMenu, 64, 220);
        	if(Settings.soundEnabled)
            	g.drawPixmap(Assets.buttons, 0, 416, 0, 0, 64, 64);
        	else
            	g.drawPixmap(Assets.buttons, 0, 416, 64, 0, 64, 64);
    	}

    	@Override
    	public void pause() {        
        	Settings.save(game.getFileIO());
    	}

    	@Override
    	public void resume() {

    	}

    	@Override
    	public void dispose() {

    	}
	}

## HighscoreScreen类

	public class HighscoreScreen extends Screen {
		String lines[] = new String[5];

		public HighscoreScreen(Game game) {
			super(game);

			for (int i = 0; i < 5; i++) {
				lines[i] = "" + (i + 1) + ". " + Settings.highscores[i];
			}
		}

		@Override
		public void update(float deltaTime) {
			List<TouchEvent> touchEvents = game.getInput().getTouchEvents();
			game.getInput().getKeyEvents();

			int len = touchEvents.size();
			for (int i = 0; i < len; i++) {
				TouchEvent event = touchEvents.get(i);
				if (event.type == TouchEvent.TOUCH_UP) {
					if (event.x < 64 && event.y > 416) {
						if (Settings.soundEnabled)
							Assets.click.play(1);
						game.setScreen(new MainMenuScreen(game));
						return;
					}
				}
			}
		}

		@Override
		public void present(float deltaTime) {
			Graphics g = game.getGraphics();

			g.drawPixmap(Assets.background, 0, 0);
			g.drawPixmap(Assets.mainMenu, 64, 20, 0, 42, 196, 42);

			int y = 100;
			for (int i = 0; i < 5; i++) {
				drawText(g, lines[i], 20, y);
				y += 50;
			}

			g.drawPixmap(Assets.buttons, 0, 416, 64, 64, 64, 64);
		}

		public void drawText(Graphics g, String line, int x, int y) {
			int len = line.length();
			for (int i = 0; i < len; i++) {
				char character = line.charAt(i);

				if (character == ' ') {
					x += 20;
					continue;
				}

				int srcX = 0;
				int srcWidth = 0;
				if (character == '.') {
					// srcX = 200;
					// srcWidth = 10;
					x += 20;
					continue;
				} else {
					srcX = (character - '0') * 20;
					srcWidth = 20;
				}
				int name = 0;
				if ((character - '0') >= 0 && (character - '0') <= 9) {
					name = character - '0';
				}
				g.drawPixmap(Assets.getNumber(name), x, y);

				// g.drawPixmap(Assets.numbers, x, y, srcX, 0, srcWidth, 32);
				x += srcWidth;
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

## Settings类

	public class Settings {
		public static boolean soundEnabled = false;
		public static int[] highscores = new int[] { 10, 8, 5, 3, 1 };

		public static void load(FileIO files) {
			BufferedReader in = null;
			try {
				in = new BufferedReader(new InputStreamReader(files.readFile(".bird")));
				soundEnabled = Boolean.parseBoolean(in.readLine());
				for (int i = 0; i < 5; i++) {
					highscores[i] = Integer.parseInt(in.readLine());
				}
			} catch (IOException e) {
				// :( It's ok we have defaults
			} catch (NumberFormatException e) {
				// :/ It's ok, defaults save our day
			} finally {
				try {
					if (in != null)
						in.close();
				} catch (IOException e) {
				}
			}
		}

		public static void save(FileIO files) {
			BufferedWriter out = null;
			try {
				out = new BufferedWriter(new OutputStreamWriter(files.writeFile(".bird")));
				out.write(Boolean.toString(soundEnabled));
				out.write("\n");
				for (int i = 0; i < 5; i++) {
					out.write(Integer.toString(highscores[i]));
					out.write("\n");
				}

			} catch (IOException e) {
			} finally {
				try {
					if (out != null)
						out.close();
				} catch (IOException e) {
				}
			}
		}

		public static void addScore(int score) {
			for (int i = 0; i < 5; i++) {
				if (highscores[i] < score) {
					for (int j = 4; j > i; j--)
						highscores[j] = highscores[j - 1];
					highscores[i] = score;
					break;
				}
			}
		}
	}

## FlappyBirdGame类，游戏启动的入口

	public class FlappyBirdGame extends AndroidGame{
		@Override
		public Screen getStartScreen() {
			return new LoadingScreen(this);
		}
	}

## 游戏世界的抽象

Bird类：

	public class Bird {

		public static final int width = 34;
		public static final int height = 24;

		public int x;
		public int y;
		public Rect birdRect;

		public Bird(int x, int y) {
			this.x = x;
			this.y = y;
			birdRect = new Rect(x, y, x + width, y + height);
		}

		public void moveUpAndDown(int deltaY) {
			y = y + deltaY;
			birdRect = new Rect(x, y, x + width, y + height);
		}

		public boolean checkOverScreen() {
			if (y <= 0 || y >= 365) {
				return true;
			}
			return false;
		}
	}

Pipe类：

	public class Pipe {

		public int left;
		public int gapY;
		public int gapHeight;

		public Rect upRect;
		public Rect downRect;

		public boolean isScore = false;
		public boolean first = true;

		public Pipe(int x, int gapY, int gapHeight) {
			left = x;
			this.gapY = gapY;
			this.gapHeight = gapHeight;

			upRect = new Rect(left, 0, left + 52, gapY);
			downRect = new Rect(left, gapY + gapHeight, left + 52, 480);
		}

		public void update(int x) {
			left = left - x;
			upRect.set(left, 0, left + 52, gapY);
			downRect.set(left, gapY + gapHeight, left + 52, 480);
			checkIsScore();
		}

		public void checkIsScore() {
			if (left + 52 < 80 && first) {
				isScore = true;
			}
		}

		public void scoreOver() {
			isScore = false;
			first = false;
		}
	}

Floor类：

	public class Floor {

		private int state = 1;

		private int x;
		private int y;

		public Floor(int x, int y) {
			this.x = x;
			this.y = y;
		}

		public void setState(int state) {
			this.state = state;
		}

		public int getState() {
			return state;
		}

		public int getX() {
			return x;
		}

		public void setX(int x) {
			this.x = x;
		}

		public int getY() {
			return y;
		}

	}

## World类：整合世界

	public class World {
		static final int SCORE_INCREMENT = 1;

		public static final int startX = 80;
		public static final int startY = 200;
		public static final int floorStartX = 0;
		public static final int floorStartY = 384;
		public static final int pipeStartX = 250;

		private static final float TICK_INITIAL = 0.08f;

		public Bird bird;
		public Floor floor;
		public ArrayList<Pipe> pipes;
		public boolean gameOver = false;
		public int score = 0;
		public int deltaY = 0;
		public float gravity = 0;
		public static float velocityY = 0;
		public static int pipeVelocity = 10;

		float tickTime = 0;
		static float tick = TICK_INITIAL;
		Random random;
		int randNumY = 0;
		int randNumHeight = 0;

		public World(float g) {
			bird = new Bird(startX, startY);
			floor = new Floor(floorStartX, floorStartY);
			pipes = new ArrayList<Pipe>();
			gravity = g;
			Random random = new Random();
			randNumHeight = random.nextInt(151) + 50;
			randNumY = random.nextInt(278 - randNumHeight) + 45;
			pipes.add(new Pipe(pipeStartX, randNumY, randNumHeight));
		}

		public void addNewPipe() {

		}

		public void setGravity(float gravity) {
			this.gravity = gravity;
		}

		public void update(float deltaTime) {
			if (gameOver)
				return;

			tickTime += deltaTime;

			while (tickTime > tick) {
				tickTime -= tick;

				velocityY += gravity * tick;
				deltaY = (int) (velocityY * tick);
				bird.moveUpAndDown(deltaY);
				if (floor.getState() == 1) {
					floor.setState(2);
				} else if (floor.getState() == 2) {
					floor.setState(1);
				}
				if (bird.checkOverScreen()) {
					gameOver = true;
					return;
				}
				for (int i = 0; i < pipes.size(); i++) {
					pipes.get(i).update(pipeVelocity);
					if (i == 0) {
						if (pipes.get(i).left <= -52) {
							pipes.remove(i);
						}
					}
					if (i == pipes.size() - 1) {
						if (pipes.get(i).left <= 60) {
							Random random = new Random();
							randNumHeight = random.nextInt(151) + 50;
							randNumY = random.nextInt(278 - randNumHeight) + 45;
							pipes.add(new Pipe(320, randNumY, randNumHeight));
						}
					}
					if (pipes.get(i).isScore) {
						score += 1;
						pipes.get(i).scoreOver();
					}
					if (checkBirdPipeCollision(bird.birdRect, pipes.get(i).upRect) || checkBirdPipeCollision(bird.birdRect, pipes.get(i).downRect)) {
						gameOver = true;
						return;
					}
				}
			}
		}

		private boolean checkBirdPipeCollision(Rect a, Rect b) {
			if (a.right < b.left || a.left > b.right || a.top > b.bottom || a.bottom < b.top) {
				// 没有重合
				return false;
			} else {
				// 有重合
				return true;
			}
		}

		public void reset() {
			velocityY = 0;
			score = 0;
		}
	}

## GameScreen类，实现主循环

	public class GameScreen extends Screen {

		public static final float GRAVITY = 140.0f;

		enum GameState {
			Ready, Running, Paused, GameOver
		}

		GameState state = GameState.Ready;
		World world;
		int oldScore = 0;
		String score = "0";

		public GameScreen(Game game) {
			super(game);
			world = new World(GRAVITY);
		}

		@Override
		public void update(float deltaTime) {
			List<TouchEvent> touchEvents = game.getInput().getTouchEvents();
			game.getInput().getKeyEvents();

			if (state == GameState.Ready)
				updateReady(touchEvents);
			if (state == GameState.Running)
				updateRunning(touchEvents, deltaTime);
			if (state == GameState.Paused)
				updatePaused(touchEvents);
			if (state == GameState.GameOver)
				updateGameOver(touchEvents);
		}

		private void updateReady(List<TouchEvent> touchEvents) {
			if (touchEvents.size() > 0) {
				world.reset();
				state = GameState.Running;
			}
		}
	
		private void updateRunning(List<TouchEvent> touchEvents, float deltaTime) {
			int len = touchEvents.size();
			for (int i = 0; i < len; i++) {
				TouchEvent event = touchEvents.get(i);
				if (event.type == TouchEvent.TOUCH_UP) {
					if (event.x < 64 && event.y < 64) {
						if (Settings.soundEnabled)
							Assets.click.play(1);
						state = GameState.Paused;
						return;
					}
					world.setGravity(GRAVITY);
				} else if (event.type == TouchEvent.TOUCH_DOWN) {
					if (event.x < 64 && event.y < 64) {
	
					} else {
						world.setGravity(-GRAVITY);
					}
				}
			}

			world.update(deltaTime);
			if (world.gameOver) {
				if (Settings.soundEnabled)
					Assets.bitten.play(1);
				state = GameState.GameOver;
			}
			if (oldScore != world.score) {
				oldScore = world.score;
				score = "" + oldScore;
				if (Settings.soundEnabled)
					Assets.eat.play(1);
			}
		}

		private void updatePaused(List<TouchEvent> touchEvents) {
			int len = touchEvents.size();
			for (int i = 0; i < len; i++) {
				TouchEvent event = touchEvents.get(i);
				if (event.type == TouchEvent.TOUCH_UP) {
					if (event.x > 80 && event.x <= 240) {
						if (event.y > 100 && event.y <= 148) {
							if (Settings.soundEnabled)
								Assets.click.play(1);
							state = GameState.Running;
							return;
						}
						if (event.y > 148 && event.y < 196) {
							if (Settings.soundEnabled)
								Assets.click.play(1);
							game.setScreen(new MainMenuScreen(game));
							return;
						}
					}
				}
			}
		}

		private void updateGameOver(List<TouchEvent> touchEvents) {
			int len = touchEvents.size();
			for (int i = 0; i < len; i++) {
				TouchEvent event = touchEvents.get(i);
				if (event.type == TouchEvent.TOUCH_UP) {
					if (event.x >= 128 && event.x <= 192 && event.y >= 200 && event.y <= 264) {
						if (Settings.soundEnabled)
							Assets.click.play(1);
						game.setScreen(new MainMenuScreen(game));
						return;
					}
				}
			}
		}

		@Override
		public void present(float deltaTime) {
			Graphics g = game.getGraphics();
	
			g.drawPixmap(Assets.background, 0, 0);
			drawWorld(world);
			if (state == GameState.Ready)
				drawReadyUI();
			if (state == GameState.Running)
				drawRunningUI();
			if (state == GameState.Paused)
				drawPausedUI();
			if (state == GameState.GameOver)
				drawGameOverUI();
	
			drawText(g, score, g.getWidth() / 2 - score.length() * 20 / 2, g.getHeight() - 42);
		}

		private void drawWorld(World world) {
			Graphics g = game.getGraphics();
			Bird bird = world.bird;
			Floor floor = world.floor;
	
			Pixmap birdPixmap = null;
			birdPixmap = Assets.bird;
			g.drawPixmap(birdPixmap, bird.x, bird.y);
			for (int i = 0; i < world.pipes.size(); i++) {
				if (world.pipes.get(i).left < 0) {
					g.drawPixmap(Assets.pipe1, //
							0, //
							0, //
							-world.pipes.get(i).left, //
							320 - world.pipes.get(i).gapY, //
							52 + world.pipes.get(i).left, //
							world.pipes.get(i).gapY //
					);
					g.drawPixmap(Assets.pipe2, //
							0, //
							world.pipes.get(i).downRect.top, //
							-world.pipes.get(i).left, //
							0, //
							52 + world.pipes.get(i).left, //
							321 //
					);
	
				} else {
					g.drawPixmap(Assets.pipe1, //
							world.pipes.get(i).left, //
							0, //
							0, //
							320 - world.pipes.get(i).gapY, //
							52,//
							world.pipes.get(i).gapY //
					);
					g.drawPixmap(Assets.pipe2, //
							world.pipes.get(i).left, //
							world.pipes.get(i).downRect.top, //
							0, //
							0, //
							52, //
							321 //
					);
				}
			}
	
			if (floor.getState() == 1) {
				g.drawPixmap(Assets.floor1, 0, 384, 0, 0, 320, 112);
			} else if (floor.getState() == 2) {
				g.drawPixmap(Assets.floor2, 0, 384, 0, 0, 320, 112);
			}
		}
	
		private void drawReadyUI() {
			Graphics g = game.getGraphics();
	
			g.drawPixmap(Assets.ready, 47, 100);
		}

		private void drawRunningUI() {
			Graphics g = game.getGraphics();
	
			g.drawPixmap(Assets.buttons, 0, 0, 64, 128, 64, 64);
		}
	
		private void drawPausedUI() {
			Graphics g = game.getGraphics();
	
			g.drawPixmap(Assets.pause, 80, 100);
		}
	
		private void drawGameOverUI() {
			Graphics g = game.getGraphics();
	
			g.drawPixmap(Assets.gameOver, 62, 100);
			g.drawPixmap(Assets.buttons, 128, 200, 0, 128, 64, 64);
		}
	
		public void drawText(Graphics g, String line, int x, int y) {
			int len = line.length();
			for (int i = 0; i < len; i++) {
				char character = line.charAt(i);
	
				if (character == ' ') {
					x += 20;
					continue;
				}
	
				int srcX = 0;
				int srcWidth = 0;
				if (character == '.') {
					x += 20;
					continue;
				} else {
					srcX = (character - '0') * 20;
					srcWidth = 20;
				}
				int name = 0;
				if ((character - '0') >= 0 && (character - '0') <= 9) {
					name = character - '0';
				}
				g.drawPixmap(Assets.getNumber(name), x, y);
				x += srcWidth;
			}
		}
	
		@Override
		public void pause() {
			if (state == GameState.Running)
				state = GameState.Paused;
	
			if (world.gameOver) {
				Settings.addScore(world.score);
				Settings.save(game.getFileIO());
			}
		}
	
		@Override
		public void resume() {
	
		}
	
		@Override
		public void dispose() {
	
		}
	}			