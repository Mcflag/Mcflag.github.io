title: 11SuperJump2D游戏实例
date: 2015-09-22 00:04:35
tags: [游戏框架]
---

## 摘要
Super Jump 2D游戏实例，实际上这个实例重用了前面的框架，并且使用与前面实例相同的形式。下面就直接列出代码。
<!--more-->

## Assets类

	public class Assets {
		public static Texture background;
		public static TextureRegion backgroundRegion;

		public static Texture items;
		public static TextureRegion mainMenu;
		public static TextureRegion pauseMenu;
		public static TextureRegion ready;
		public static TextureRegion gameOver;
		public static TextureRegion highScoresRegion;
		public static TextureRegion logo;
		public static TextureRegion soundOn;
		public static TextureRegion soundOff;
		public static TextureRegion arrow;
		public static TextureRegion pause;
		public static TextureRegion spring;
		public static TextureRegion castle;
		public static Animation coinAnim;
		public static Animation bobJump;
		public static Animation bobFall;
		public static TextureRegion bobHit;
		public static Animation squirrelFly;
		public static TextureRegion platform;
		public static Animation brakingPlatform;
		public static Font font;
	
		public static Music music;
		public static Sound jumpSound;
		public static Sound highJumpSound;
		public static Sound hitSound;
		public static Sound coinSound;
		public static Sound clickSound;
	
		public static void load(GLGame game) {
			background = new Texture(game, "background.png");
			backgroundRegion = new TextureRegion(background, 0, 0, 320, 480);
	
			items = new Texture(game, "items.png");
			mainMenu = new TextureRegion(items, 0, 224, 300, 110);
			pauseMenu = new TextureRegion(items, 224, 128, 192, 96);
			ready = new TextureRegion(items, 320, 224, 192, 32);
			gameOver = new TextureRegion(items, 352, 256, 160, 96);
			highScoresRegion = new TextureRegion(Assets.items, 0, 257, 300, 110 / 3);
			logo = new TextureRegion(items, 0, 352, 274, 142);
			soundOff = new TextureRegion(items, 0, 0, 64, 64);
			soundOn = new TextureRegion(items, 64, 0, 64, 64);
			arrow = new TextureRegion(items, 0, 64, 64, 64);
			pause = new TextureRegion(items, 64, 64, 64, 64);
	
			spring = new TextureRegion(items, 128, 0, 32, 32);
			castle = new TextureRegion(items, 128, 64, 64, 64);
			coinAnim = new Animation(0.2f, new TextureRegion(items, 128, 32, 32, 32), new TextureRegion(items, 160, 32, 32, 32), new TextureRegion(items, 192, 32,
					32, 32), new TextureRegion(items, 160, 32, 32, 32));
			bobJump = new Animation(0.2f, new TextureRegion(items, 0, 128, 32, 32), new TextureRegion(items, 32, 128, 32, 32));
			bobFall = new Animation(0.2f, new TextureRegion(items, 64, 128, 32, 32), new TextureRegion(items, 96, 128, 32, 32));
			bobHit = new TextureRegion(items, 128, 128, 32, 32);
			squirrelFly = new Animation(0.2f, new TextureRegion(items, 0, 160, 32, 32), new TextureRegion(items, 32, 160, 32, 32));
			platform = new TextureRegion(items, 64, 160, 64, 16);
			brakingPlatform = new Animation(0.2f, new TextureRegion(items, 64, 160, 64, 16), new TextureRegion(items, 64, 176, 64, 16), new TextureRegion(items,
					64, 192, 64, 16), new TextureRegion(items, 64, 208, 64, 16));
	
			font = new Font(items, 224, 0, 16, 16, 20);
	
			music = game.getAudio().newMusic("music.mp3");
			music.setLooping(true);
			music.setVolume(0.5f);
			if (Settings.soundEnabled)
				music.play();
			jumpSound = game.getAudio().newSound("jump.ogg");
			highJumpSound = game.getAudio().newSound("highjump.ogg");
			hitSound = game.getAudio().newSound("hit.ogg");
			coinSound = game.getAudio().newSound("coin.ogg");
			clickSound = game.getAudio().newSound("click.ogg");
		}
	
		public static void reload() {
			background.reload();
			items.reload();
			if (Settings.soundEnabled)
				music.play();
		}
	
		public static void playSound(Sound sound) {
			if (Settings.soundEnabled)
				sound.play(1);
		}
	}

## Settings类

	public class Settings {
	    public static boolean soundEnabled = true;
	    public final static int[] highscores = new int[] { 100, 80, 50, 30, 10 };
	    public final static String file = ".superjumper";
	
	    public static void load(FileIO files) {
	        BufferedReader in = null;
	        try {
	            in = new BufferedReader(new InputStreamReader(files.readFile(file)));
	            soundEnabled = Boolean.parseBoolean(in.readLine());
	            for(int i = 0; i < 5; i++) {
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
	            out = new BufferedWriter(new OutputStreamWriter(
	                    files.writeFile(file)));
	            out.write(Boolean.toString(soundEnabled));
	            out.write("\n");
	            for(int i = 0; i < 5; i++) {
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
	        for(int i=0; i < 5; i++) {
	            if(highscores[i] < score) {
	                for(int j= 4; j > i; j--)
	                    highscores[j] = highscores[j-1];
	                highscores[i] = score;
	                break;
	            }
	        }
	    }
	}

## 主活动SuperJumper类

	public class SuperJumper extends GLGame {
	    boolean firstTimeCreate = true;
	    
	    public Screen getStartScreen() {
	        return new MainMenuScreen(this);
	    }
	    
	    @Override
	    public void onSurfaceCreated(GL10 gl, EGLConfig config) {         
	        super.onSurfaceCreated(gl, config);
	        if(firstTimeCreate) {
	            Settings.load(getFileIO());
	            Assets.load(this);
	            firstTimeCreate = false;            
	        } else {
	            Assets.reload();
	        }
	    }     
	    
	    @Override
	    public void onPause() {
	        super.onPause();
	        if(Settings.soundEnabled)
	            Assets.music.pause();
	    }
	}

## Font类，点阵字体渲染类

	public class Font {
		public final Texture texture;
		public final int glyphWidth;
		public final int glyphHeight;
		public final TextureRegion[] glyphs = new TextureRegion[96];
	
		public Font(Texture texture, int offsetX, int offsetY, int glyphsPerRow, int glyphWidth, int glyphHeight) {
			this.texture = texture;
			this.glyphWidth = glyphWidth;
			this.glyphHeight = glyphHeight;
			int x = offsetX;
			int y = offsetY;
			for (int i = 0; i < 96; i++) {
				glyphs[i] = new TextureRegion(texture, x, y, glyphWidth, glyphHeight);
				x += glyphWidth;
				if (x == offsetX + glyphsPerRow * glyphWidth) {
					x = offsetX;
					y += glyphHeight;
				}
			}
		}
	
		public void drawText(SpriteBatcher batcher, String text, float x, float y) {
			int len = text.length();
			for (int i = 0; i < len; i++) {
				int c = text.charAt(i) - ' ';
				if (c < 0 || c > glyphs.length - 1)
					continue;
	
				TextureRegion glyph = glyphs[c];
				batcher.drawSprite(x, y, glyphWidth, glyphHeight, glyph);
				x += glyphWidth;
			}
		}
	}

## GLScreen

	public abstract class GLScreen extends Screen {
		protected final GLGraphics glGraphics;
		protected final GLGame glGame;
	
		public GLScreen(Game game) {
			super(game);
			glGame = (GLGame) game;
			glGraphics = ((GLGame) game).getGLGraphics();
		}
	}

## MainMenuScreen类

	public class MainMenuScreen extends GLScreen {
	    Camera2D guiCam;
	    SpriteBatcher batcher;
	    Rectangle soundBounds;
	    Rectangle playBounds;
	    Rectangle highscoresBounds;
	    Rectangle helpBounds;
	    Vector2 touchPoint;
	
	    public MainMenuScreen(Game game) {
	        super(game);
	        guiCam = new Camera2D(glGraphics, 320, 480);
	        batcher = new SpriteBatcher(glGraphics, 100);
	        soundBounds = new Rectangle(0, 0, 64, 64);
	        playBounds = new Rectangle(160 - 150, 200 + 18, 300, 36);
	        highscoresBounds = new Rectangle(160 - 150, 200 - 18, 300, 36);
	        helpBounds = new Rectangle(160 - 150, 200 - 18 - 36, 300, 36);
	        touchPoint = new Vector2();               
	    }       
	
	    @Override
	    public void update(float deltaTime) {
	        List<TouchEvent> touchEvents = game.getInput().getTouchEvents();
	        game.getInput().getKeyEvents();
	        
	        int len = touchEvents.size();
	        for(int i = 0; i < len; i++) {
	            TouchEvent event = touchEvents.get(i);                        
	            if(event.type == TouchEvent.TOUCH_UP) {
	                touchPoint.set(event.x, event.y);
	                guiCam.touchToWorld(touchPoint);
	                
	                if(OverlapTester.pointInRectangle(playBounds, touchPoint)) {
	                    Assets.playSound(Assets.clickSound);
	                    game.setScreen(new GameScreen(game));
	                    return;
	                }
	                if(OverlapTester.pointInRectangle(highscoresBounds, touchPoint)) {
	                    Assets.playSound(Assets.clickSound);
	                    game.setScreen(new HighscoreScreen(game));
	                    return;
	                }
	                if(OverlapTester.pointInRectangle(helpBounds, touchPoint)) {
	                    Assets.playSound(Assets.clickSound);
	                    game.setScreen(new HelpScreen(game));
	                    return;
	                }
	                if(OverlapTester.pointInRectangle(soundBounds, touchPoint)) {
	                    Assets.playSound(Assets.clickSound);
	                    Settings.soundEnabled = !Settings.soundEnabled;
	                    if(Settings.soundEnabled) 
	                        Assets.music.play();
	                    else
	                        Assets.music.pause();
	                }
	            }
	        }
	    }
	
	    @Override
	    public void present(float deltaTime) {
	        GL10 gl = glGraphics.getGL();        
	        gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	        guiCam.setViewportAndMatrices();
	        
	        gl.glEnable(GL10.GL_TEXTURE_2D);
	        
	        batcher.beginBatch(Assets.background);
	        batcher.drawSprite(160, 240, 320, 480, Assets.backgroundRegion);
	        batcher.endBatch();
	        
	        gl.glEnable(GL10.GL_BLEND);
	        gl.glBlendFunc(GL10.GL_SRC_ALPHA, GL10.GL_ONE_MINUS_SRC_ALPHA);               
	        
	        batcher.beginBatch(Assets.items);                 
	        
	        batcher.drawSprite(160, 480 - 10 - 71, 274, 142, Assets.logo);
	        batcher.drawSprite(160, 200, 300, 110, Assets.mainMenu);
	        batcher.drawSprite(32, 32, 64, 64, Settings.soundEnabled?Assets.soundOn:Assets.soundOff);
	                
	        batcher.endBatch();
	        
	        gl.glDisable(GL10.GL_BLEND);
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

## HelpScreen类

	public class HelpScreen extends GLScreen {
	    Camera2D guiCam;
	    SpriteBatcher batcher;
	    Rectangle nextBounds;
	    Vector2 touchPoint;
	    Texture helpImage;
	    TextureRegion helpRegion;    
	
	    public HelpScreen(Game game) {
	        super(game);
	        
	        guiCam = new Camera2D(glGraphics, 320, 480);
	        nextBounds = new Rectangle(320 - 64, 0, 64, 64);
	        touchPoint = new Vector2();
	        batcher = new SpriteBatcher(glGraphics, 1);
	    }
	
	    @Override
	    public void resume() {
	        helpImage = new Texture(glGame, "help1.png" );
	        helpRegion = new TextureRegion(helpImage, 0, 0, 320, 480);
	    }
	    
	    @Override
	    public void pause() {
	        helpImage.dispose();
	    }
	
	    @Override
	    public void update(float deltaTime) {
	        List<TouchEvent> touchEvents = game.getInput().getTouchEvents();
	        game.getInput().getKeyEvents();
	        int len = touchEvents.size();
	        for(int i = 0; i < len; i++) {
	            TouchEvent event = touchEvents.get(i);
	            touchPoint.set(event.x, event.y);
	            guiCam.touchToWorld(touchPoint);
	            
	            if(event.type == TouchEvent.TOUCH_UP) {
	                if(OverlapTester.pointInRectangle(nextBounds, touchPoint)) {
	                    Assets.playSound(Assets.clickSound);
	                    game.setScreen(new HelpScreen2(game));
	                    return;
	                }
	            }
	        }
	    }
	
	    @Override
	    public void present(float deltaTime) {
	        GL10 gl = glGraphics.getGL();        
	        gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	        guiCam.setViewportAndMatrices();
	        
	        gl.glEnable(GL10.GL_TEXTURE_2D);
	        
	        batcher.beginBatch(helpImage);
	        batcher.drawSprite(160, 240, 320, 480, helpRegion);
	        batcher.endBatch();
	        
	        gl.glEnable(GL10.GL_BLEND);
	        gl.glBlendFunc(GL10.GL_SRC_ALPHA, GL10.GL_ONE_MINUS_SRC_ALPHA);
	        
	        batcher.beginBatch(Assets.items);          
	        batcher.drawSprite(320 - 32, 32, -64, 64, Assets.arrow);
	        batcher.endBatch();
	        
	        gl.glDisable(GL10.GL_BLEND);
	    }
	
	    @Override
	    public void dispose() {
	    }
	}

## 剩下的HelpScreen类

	public class HelpScreen2 extends GLScreen {
	    Camera2D guiCam;
	    SpriteBatcher batcher;
	    Rectangle nextBounds;
	    Vector2 touchPoint;
	    Texture helpImage;
	    TextureRegion helpRegion;    
	    
	    public HelpScreen2(Game game) {
	        super(game);
	        
	        guiCam = new Camera2D(glGraphics, 320, 480);
	        nextBounds = new Rectangle(320 - 64, 0, 64, 64);
	        touchPoint = new Vector2();
	        batcher = new SpriteBatcher(glGraphics, 1);
	    }
	    
	    @Override
	    public void resume() {
	        helpImage = new Texture(glGame, "help2.png" );
	        helpRegion = new TextureRegion(helpImage, 0, 0, 320, 480);
	    }
	    
	    @Override
	    public void pause() {
	        helpImage.dispose();
	    }
	
	    @Override
	    public void update(float deltaTime) {
	        List<TouchEvent> touchEvents = game.getInput().getTouchEvents();
	        game.getInput().getKeyEvents();
	        int len = touchEvents.size();
	        for(int i = 0; i < len; i++) {
	            TouchEvent event = touchEvents.get(i);
	            touchPoint.set(event.x, event.y);
	            guiCam.touchToWorld(touchPoint);
	            
	            if(event.type == TouchEvent.TOUCH_UP) {
	                if(OverlapTester.pointInRectangle(nextBounds, touchPoint)) {
	                    Assets.playSound(Assets.clickSound);
	                    game.setScreen(new HelpScreen3(game));
	                    return;
	                }
	            }
	        }
	    }
	
	    @Override
	    public void present(float deltaTime) {
	        GL10 gl = glGraphics.getGL();        
	        gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	        guiCam.setViewportAndMatrices();
	        
	        gl.glEnable(GL10.GL_TEXTURE_2D);
	        
	        batcher.beginBatch(helpImage);
	        batcher.drawSprite(160, 240, 320, 480, helpRegion);
	        batcher.endBatch();
	        
	        gl.glEnable(GL10.GL_BLEND);
	        gl.glBlendFunc(GL10.GL_SRC_ALPHA, GL10.GL_ONE_MINUS_SRC_ALPHA);
	        
	        batcher.beginBatch(Assets.items);          
	        batcher.drawSprite(320 - 32, 32, -64, 64, Assets.arrow);
	        batcher.endBatch();
	        
	        gl.glDisable(GL10.GL_BLEND);
	    }
	
	    @Override
	    public void dispose() {
	    }
	}
	
	public class HelpScreen3 extends GLScreen {
	    Camera2D guiCam;
	    SpriteBatcher batcher;
	    Rectangle nextBounds;
	    Vector2 touchPoint;
	    Texture helpImage;
	    TextureRegion helpRegion;    
	    
	    public HelpScreen3(Game game) {
	        super(game);
	        
	        guiCam = new Camera2D(glGraphics, 320, 480);
	        nextBounds = new Rectangle(320 - 64, 0, 64, 64);
	        touchPoint = new Vector2();
	        batcher = new SpriteBatcher(glGraphics, 1);
	    }
	    
	    @Override
	    public void resume() {
	        helpImage = new Texture(glGame, "help3.png" );
	        helpRegion = new TextureRegion(helpImage, 0, 0, 320, 480);
	    }
	    
	    @Override
	    public void pause() {
	        helpImage.dispose();
	    }
	
	    @Override
	    public void update(float deltaTime) {
	        List<TouchEvent> touchEvents = game.getInput().getTouchEvents();
	        game.getInput().getKeyEvents();
	        int len = touchEvents.size();
	        for(int i = 0; i < len; i++) {
	            TouchEvent event = touchEvents.get(i);
	            touchPoint.set(event.x, event.y);
	            guiCam.touchToWorld(touchPoint);
	            
	            if(event.type == TouchEvent.TOUCH_UP) {
	                if(OverlapTester.pointInRectangle(nextBounds, touchPoint)) {
	                    Assets.playSound(Assets.clickSound);
	                    game.setScreen(new HelpScreen4(game));
	                    return;
	                }
	            }
	        }
	    }
	
	    @Override
	    public void present(float deltaTime) {
	        GL10 gl = glGraphics.getGL();        
	        gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	        guiCam.setViewportAndMatrices();
	        
	        gl.glEnable(GL10.GL_TEXTURE_2D);
	        
	        batcher.beginBatch(helpImage);
	        batcher.drawSprite(160, 240, 320, 480, helpRegion);
	        batcher.endBatch();
	        
	        gl.glEnable(GL10.GL_BLEND);
	        gl.glBlendFunc(GL10.GL_SRC_ALPHA, GL10.GL_ONE_MINUS_SRC_ALPHA);
	        
	        batcher.beginBatch(Assets.items);          
	        batcher.drawSprite(320 - 32, 32, -64, 64, Assets.arrow);
	        batcher.endBatch();
	        
	        gl.glDisable(GL10.GL_BLEND);
	    }
	
	    @Override
	    public void dispose() {
	    }
	}
	
	public class HelpScreen4 extends GLScreen {
	    Camera2D guiCam;
	    SpriteBatcher batcher;
	    Rectangle nextBounds;
	    Vector2 touchPoint;
	    Texture helpImage;
	    TextureRegion helpRegion;    
	    
	    public HelpScreen4(Game game) {
	        super(game);
	        
	        guiCam = new Camera2D(glGraphics, 320, 480);
	        nextBounds = new Rectangle(320 - 64, 0, 64, 64);
	        touchPoint = new Vector2();
	        batcher = new SpriteBatcher(glGraphics, 1);
	    }
	    
	    @Override
	    public void resume() {
	        helpImage = new Texture(glGame, "help4.png" );
	        helpRegion = new TextureRegion(helpImage, 0, 0, 320, 480);
	    }
	    
	    @Override
	    public void pause() {
	        helpImage.dispose();
	    }
	
	    @Override
	    public void update(float deltaTime) {
	        List<TouchEvent> touchEvents = game.getInput().getTouchEvents();
	        game.getInput().getKeyEvents();
	        int len = touchEvents.size();
	        for(int i = 0; i < len; i++) {
	            TouchEvent event = touchEvents.get(i);
	            touchPoint.set(event.x, event.y);
	            guiCam.touchToWorld(touchPoint);
	            
	            if(event.type == TouchEvent.TOUCH_UP) {
	                if(OverlapTester.pointInRectangle(nextBounds, touchPoint)) {
	                    Assets.playSound(Assets.clickSound);
	                    game.setScreen(new HelpScreen5(game));
	                    return;
	                }
	            }
	        }
	    }
	
	    @Override
	    public void present(float deltaTime) {
	        GL10 gl = glGraphics.getGL();        
	        gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	        guiCam.setViewportAndMatrices();
	        
	        gl.glEnable(GL10.GL_TEXTURE_2D);
	        
	        batcher.beginBatch(helpImage);
	        batcher.drawSprite(160, 240, 320, 480, helpRegion);
	        batcher.endBatch();
	        
	        gl.glEnable(GL10.GL_BLEND);
	        gl.glBlendFunc(GL10.GL_SRC_ALPHA, GL10.GL_ONE_MINUS_SRC_ALPHA);
	        
	        batcher.beginBatch(Assets.items);          
	        batcher.drawSprite(320 - 32, 32, -64, 64, Assets.arrow);
	        batcher.endBatch();
	        
	        gl.glDisable(GL10.GL_BLEND);
	    }
	
	    @Override
	    public void dispose() {
	    }
	}
	
	public class HelpScreen5 extends GLScreen {
	    Camera2D guiCam;
	    SpriteBatcher batcher;
	    Rectangle nextBounds;
	    Vector2 touchPoint;
	    Texture helpImage;
	    TextureRegion helpRegion;    
	    
	    public HelpScreen5(Game game) {
	        super(game);
	        
	        guiCam = new Camera2D(glGraphics, 320, 480);
	        nextBounds = new Rectangle(320 - 64, 0, 64, 64);
	        touchPoint = new Vector2();
	        batcher = new SpriteBatcher(glGraphics, 1);
	    }
	    
	    @Override
	    public void resume() {
	        helpImage = new Texture(glGame, "help5.png" );
	        helpRegion = new TextureRegion(helpImage, 0, 0, 320, 480);
	    }
	    
	    @Override
	    public void pause() {
	        helpImage.dispose();
	    }
	
	    @Override
	    public void update(float deltaTime) {
	        List<TouchEvent> touchEvents = game.getInput().getTouchEvents();
	        game.getInput().getKeyEvents();
	        int len = touchEvents.size();
	        for(int i = 0; i < len; i++) {
	            TouchEvent event = touchEvents.get(i);
	            touchPoint.set(event.x, event.y);
	            guiCam.touchToWorld(touchPoint);
	            
	            if(event.type == TouchEvent.TOUCH_UP) {
	                if(OverlapTester.pointInRectangle(nextBounds, touchPoint)) {
	                    Assets.playSound(Assets.clickSound);
	                    game.setScreen(new MainMenuScreen(game));
	                    return;
	                }
	            }
	        }
	    }
	
	    @Override
	    public void present(float deltaTime) {
	        GL10 gl = glGraphics.getGL();        
	        gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	        guiCam.setViewportAndMatrices();
	        
	        gl.glEnable(GL10.GL_TEXTURE_2D);
	        
	        batcher.beginBatch(helpImage);
	        batcher.drawSprite(160, 240, 320, 480, helpRegion);
	        batcher.endBatch();
	        
	        gl.glEnable(GL10.GL_BLEND);
	        gl.glBlendFunc(GL10.GL_SRC_ALPHA, GL10.GL_ONE_MINUS_SRC_ALPHA);
	        
	        batcher.beginBatch(Assets.items);          
	        batcher.drawSprite(320 - 32, 32, -64, 64, Assets.arrow);
	        batcher.endBatch();
	        
	        gl.glDisable(GL10.GL_BLEND);
	    }
	
	    @Override
	    public void dispose() {
	    }
	}

## HighscoresScreen类

	public class HighscoreScreen extends GLScreen {
	    Camera2D guiCam;
	    SpriteBatcher batcher;
	    Rectangle backBounds;
	    Vector2 touchPoint;
	    String[] highScores;  
	    float xOffset = 0;
	
	    public HighscoreScreen(Game game) {
	        super(game);
	        
	        guiCam = new Camera2D(glGraphics, 320, 480);
	        backBounds = new Rectangle(0, 0, 64, 64);
	        touchPoint = new Vector2();
	        batcher = new SpriteBatcher(glGraphics, 100);
	        highScores = new String[5];        
	        for(int i = 0; i < 5; i++) {
	            highScores[i] = (i + 1) + ". " + Settings.highscores[i];
	            xOffset = Math.max(highScores[i].length() * Assets.font.glyphWidth, xOffset);
	        }        
	        xOffset = 160 - xOffset / 2;
	    }    
	
	    @Override
	    public void update(float deltaTime) {
	        List<TouchEvent> touchEvents = game.getInput().getTouchEvents();
	        game.getInput().getKeyEvents();
	        int len = touchEvents.size();
	        for(int i = 0; i < len; i++) {
	            TouchEvent event = touchEvents.get(i);
	            touchPoint.set(event.x, event.y);
	            guiCam.touchToWorld(touchPoint);
	            
	            if(event.type == TouchEvent.TOUCH_UP) {
	                if(OverlapTester.pointInRectangle(backBounds, touchPoint)) {
	                    game.setScreen(new MainMenuScreen(game));
	                    return;
	                }
	            }
	        }
	    }
	
	    @Override
	    public void present(float deltaTime) {
	        GL10 gl = glGraphics.getGL();        
	        gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	        guiCam.setViewportAndMatrices();
	        
	        gl.glEnable(GL10.GL_TEXTURE_2D);
	        
	        batcher.beginBatch(Assets.background);
	        batcher.drawSprite(160, 240, 320, 480, Assets.backgroundRegion);
	        batcher.endBatch();
	        
	        gl.glEnable(GL10.GL_BLEND);
	        gl.glBlendFunc(GL10.GL_SRC_ALPHA, GL10.GL_ONE_MINUS_SRC_ALPHA);
	        
	        batcher.beginBatch(Assets.items);
	        batcher.drawSprite(160, 360, 300, 33, Assets.highScoresRegion);
	        
	        float y = 240;
	        for(int i = 4; i >= 0; i--) {
	            Assets.font.drawText(batcher, highScores[i], xOffset, y);
	            y += Assets.font.glyphHeight;
	        }
	        
	        batcher.drawSprite(32, 32, 64, 64, Assets.arrow);
	        batcher.endBatch();
	        
	        gl.glDisable(GL10.GL_BLEND);
	    }
	    
	    @Override
	    public void resume() {        
	    }
	    
	    @Override
	    public void pause() {        
	    }
	
	    @Override
	    public void dispose() {
	    }
	}

## 世界的模拟类

**Spring类**

	public class Spring extends GameObject {
	    public static float SPRING_WIDTH = 0.3f;
	    public static float SPRING_HEIGHT = 0.3f;
	    
	    public Spring(float x, float y) {
	        super(x, y, SPRING_WIDTH, SPRING_HEIGHT);
	    }        
	}

**Coin类**

	public class Coin extends GameObject {
	    public static final float COIN_WIDTH = 0.5f;
	    public static final float COIN_HEIGHT = 0.8f;
	    public static final int COIN_SCORE = 10;
	
	    float stateTime;
	    public Coin(float x, float y) {
	        super(x, y, COIN_WIDTH, COIN_HEIGHT);
	        stateTime = 0;
	    }
	    
	    public void update(float deltaTime) {
	        stateTime += deltaTime;
	    }
	}

**Castle类**

	public class Castle extends GameObject {
	    public static float CASTLE_WIDTH = 1.7f;
	    public static float CASTLE_HEIGHT = 1.7f;
	
	    public Castle(float x, float y) {
	        super(x, y, CASTLE_WIDTH, CASTLE_HEIGHT);
	    }
	
	}

**Squirrel类**

	public class Squirrel extends DynamicGameObject {
	    public static final float SQUIRREL_WIDTH = 1;
	    public static final float SQUIRREL_HEIGHT = 0.6f;
	    public static final float SQUIRREL_VELOCITY = 3f;
	    
	    float stateTime = 0;
	    
	    public Squirrel(float x, float y) {
	        super(x, y, SQUIRREL_WIDTH, SQUIRREL_HEIGHT);
	        velocity.set(SQUIRREL_VELOCITY, 0);
	    }
	    
	    public void update(float deltaTime) {
	        position.add(velocity.x * deltaTime, velocity.y * deltaTime);
	        bounds.lowerLeft.set(position).sub(SQUIRREL_WIDTH / 2, SQUIRREL_HEIGHT / 2);
	        
	        if(position.x < SQUIRREL_WIDTH / 2 ) {
	            position.x = SQUIRREL_WIDTH / 2;
	            velocity.x = SQUIRREL_VELOCITY;
	        }
	        if(position.x > World.WORLD_WIDTH - SQUIRREL_WIDTH / 2) {
	            position.x = World.WORLD_WIDTH - SQUIRREL_WIDTH / 2;
	            velocity.x = -SQUIRREL_VELOCITY;
	        }
	        stateTime += deltaTime;
	    }
	}

**Platform类**

	public class Platform extends DynamicGameObject {
	    public static final float PLATFORM_WIDTH = 2;
	    public static final float PLATFORM_HEIGHT = 0.5f;
	    public static final int PLATFORM_TYPE_STATIC = 0;
	    public static final int PLATFORM_TYPE_MOVING = 1;
	    public static final int PLATFORM_STATE_NORMAL = 0;
	    public static final int PLATFORM_STATE_PULVERIZING = 1;
	    public static final float PLATFORM_PULVERIZE_TIME = 0.2f * 4;
	    public static final float PLATFORM_VELOCITY = 2;
	
	    int type;
	    int state;
	    float stateTime;
	    
	    public Platform(int type, float x, float y) {
	        super(x, y, PLATFORM_WIDTH, PLATFORM_HEIGHT);
	        this.type = type;
	        this.state = PLATFORM_STATE_NORMAL;
	        this.stateTime = 0;
	        if(type == PLATFORM_TYPE_MOVING) {
	            velocity.x = PLATFORM_VELOCITY;
	        }
	    }
	
	    public void update(float deltaTime) {
	        if(type == PLATFORM_TYPE_MOVING) {
	            position.add(velocity.x * deltaTime, 0);
	            bounds.lowerLeft.set(position).sub(PLATFORM_WIDTH / 2, PLATFORM_HEIGHT / 2);
	            
	            if(position.x < PLATFORM_WIDTH / 2) {
	                velocity.x = -velocity.x;
	                position.x = PLATFORM_WIDTH / 2;
	            }
	            if(position.x > World.WORLD_WIDTH - PLATFORM_WIDTH / 2) {
	                velocity.x = -velocity.x;
	                position.x = World.WORLD_WIDTH - PLATFORM_WIDTH / 2;
	            }            
	        }
	                
	        stateTime += deltaTime;        
	    }
	
	    public void pulverize() {
	        state = PLATFORM_STATE_PULVERIZING;
	        stateTime = 0;
	        velocity.x = 0;
	    }
	}

**Bob类**

	public class Bob extends DynamicGameObject{
	    public static final int BOB_STATE_JUMP = 0;
	    public static final int BOB_STATE_FALL = 1;
	    public static final int BOB_STATE_HIT = 2;
	    public static final float BOB_JUMP_VELOCITY = 11;    
	    public static final float BOB_MOVE_VELOCITY = 20;
	    public static final float BOB_WIDTH = 0.8f;
	    public static final float BOB_HEIGHT = 0.8f;
	
	    int state;
	    float stateTime;    
	
	    public Bob(float x, float y) {
	        super(x, y, BOB_WIDTH, BOB_HEIGHT);
	        state = BOB_STATE_FALL;
	        stateTime = 0;        
	    }
	
	    public void update(float deltaTime) {     
	        velocity.add(World.gravity.x * deltaTime, World.gravity.y * deltaTime);
	        position.add(velocity.x * deltaTime, velocity.y * deltaTime);
	        bounds.lowerLeft.set(position).sub(bounds.width / 2, bounds.height / 2);
	        
	        if(velocity.y > 0 && state != BOB_STATE_HIT) {
	            if(state != BOB_STATE_JUMP) {
	                state = BOB_STATE_JUMP;
	                stateTime = 0;
	            }
	        }
	        
	        if(velocity.y < 0 && state != BOB_STATE_HIT) {
	            if(state != BOB_STATE_FALL) {
	                state = BOB_STATE_FALL;
	                stateTime = 0;
	            }
	        }
	        
	        if(position.x < 0)
	            position.x = World.WORLD_WIDTH;
	        if(position.x > World.WORLD_WIDTH)
	            position.x = 0;
	        
	        stateTime += deltaTime;
	    }
	
	    public void hitSquirrel() {
	        velocity.set(0,0);
	        state = BOB_STATE_HIT;        
	        stateTime = 0;
	    }
	    
	    public void hitPlatform() {
	        velocity.y = BOB_JUMP_VELOCITY;
	        state = BOB_STATE_JUMP;
	        stateTime = 0;
	    }
	
	    public void hitSpring() {
	        velocity.y = BOB_JUMP_VELOCITY * 1.5f;
	        state = BOB_STATE_JUMP;
	        stateTime = 0;   
	    }
	}

**World类**

	public class World {
	    public interface WorldListener {
	        public void jump();
	        public void highJump();
	        public void hit();
	        public void coin();
	    }
	
	    public static final float WORLD_WIDTH = 10;
	    public static final float WORLD_HEIGHT = 15 * 20;    
	    public static final int WORLD_STATE_RUNNING = 0;
	    public static final int WORLD_STATE_NEXT_LEVEL = 1;
	    public static final int WORLD_STATE_GAME_OVER = 2;
	    public static final Vector2 gravity = new Vector2(0, -12);
	
	    public final Bob bob;           
	    public final List<Platform> platforms;
	    public final List<Spring> springs;
	    public final List<Squirrel> squirrels;
	    public final List<Coin> coins;
	    public Castle castle;    
	    public final WorldListener listener;
	    public final Random rand;
	    
	    public float heightSoFar;
	    public int score;    
	    public int state;
	
	    public World(WorldListener listener) {
	        this.bob = new Bob(5, 1);        
	        this.platforms = new ArrayList<Platform>();
	        this.springs = new ArrayList<Spring>();
	        this.squirrels = new ArrayList<Squirrel>();
	        this.coins = new ArrayList<Coin>();        
	        this.listener = listener;
	        rand = new Random();
	        generateLevel();
	        
	        this.heightSoFar = 0;
	        this.score = 0;
	        this.state = WORLD_STATE_RUNNING;
	    }
	
	    private void generateLevel() {
	        float y = Platform.PLATFORM_HEIGHT / 2;
	        float maxJumpHeight = Bob.BOB_JUMP_VELOCITY * Bob.BOB_JUMP_VELOCITY
	                / (2 * -gravity.y);
	        while (y < WORLD_HEIGHT - WORLD_WIDTH / 2) {
	            int type = rand.nextFloat() > 0.8f ? Platform.PLATFORM_TYPE_MOVING
	                    : Platform.PLATFORM_TYPE_STATIC;
	            float x = rand.nextFloat()
	                    * (WORLD_WIDTH - Platform.PLATFORM_WIDTH)
	                    + Platform.PLATFORM_WIDTH / 2;
	
	            Platform platform = new Platform(type, x, y);
	            platforms.add(platform);
	
	            if (rand.nextFloat() > 0.9f
	                    && type != Platform.PLATFORM_TYPE_MOVING) {
	                Spring spring = new Spring(platform.position.x,
	                        platform.position.y + Platform.PLATFORM_HEIGHT / 2
	                                + Spring.SPRING_HEIGHT / 2);
	                springs.add(spring);
	            }
	
	            if (y > WORLD_HEIGHT / 3 && rand.nextFloat() > 0.8f) {
	                Squirrel squirrel = new Squirrel(platform.position.x
	                        + rand.nextFloat(), platform.position.y
	                        + Squirrel.SQUIRREL_HEIGHT + rand.nextFloat() * 2);
	                squirrels.add(squirrel);
	            }
	
	            if (rand.nextFloat() > 0.6f) {
	                Coin coin = new Coin(platform.position.x + rand.nextFloat(),
	                        platform.position.y + Coin.COIN_HEIGHT
	                                + rand.nextFloat() * 3);
	                coins.add(coin);
	            }
	
	            y += (maxJumpHeight - 0.5f);
	            y -= rand.nextFloat() * (maxJumpHeight / 3);
	        }
	
	        castle = new Castle(WORLD_WIDTH / 2, y);
	    }
	
	    public void update(float deltaTime, float accelX) {
	        updateBob(deltaTime, accelX);
	        updatePlatforms(deltaTime);
	        updateSquirrels(deltaTime);
	        updateCoins(deltaTime);
	        if (bob.state != Bob.BOB_STATE_HIT)
	            checkCollisions();
	        checkGameOver();
	    }
	
	    private void updateBob(float deltaTime, float accelX) {
	        if (bob.state != Bob.BOB_STATE_HIT && bob.position.y <= 0.5f)
	            bob.hitPlatform();
	        if (bob.state != Bob.BOB_STATE_HIT)
	            bob.velocity.x = -accelX / 10 * Bob.BOB_MOVE_VELOCITY;
	        bob.update(deltaTime);
	        heightSoFar = Math.max(bob.position.y, heightSoFar);
	    }
	
	    private void updatePlatforms(float deltaTime) {
	        int len = platforms.size();
	        for (int i = 0; i < len; i++) {
	            Platform platform = platforms.get(i);
	            platform.update(deltaTime);
	            if (platform.state == Platform.PLATFORM_STATE_PULVERIZING
	                    && platform.stateTime > Platform.PLATFORM_PULVERIZE_TIME) {
	                platforms.remove(platform);
	                len = platforms.size();
	            }
	        }
	    }
	
	    private void updateSquirrels(float deltaTime) {
	        int len = squirrels.size();
	        for (int i = 0; i < len; i++) {
	            Squirrel squirrel = squirrels.get(i);
	            squirrel.update(deltaTime);
	        }
	    }
	
	    private void updateCoins(float deltaTime) {
	        int len = coins.size();
	        for (int i = 0; i < len; i++) {
	            Coin coin = coins.get(i);
	            coin.update(deltaTime);
	        }
	    }
	
	    private void checkCollisions() {
	        checkPlatformCollisions();
	        checkSquirrelCollisions();
	        checkItemCollisions();
	        checkCastleCollisions();
	    }
	
	    private void checkPlatformCollisions() {
	        if (bob.velocity.y > 0)
	            return;
	
	        int len = platforms.size();
	        for (int i = 0; i < len; i++) {
	            Platform platform = platforms.get(i);
	            if (bob.position.y > platform.position.y) {
	                if (OverlapTester
	                        .overlapRectangles(bob.bounds, platform.bounds)) {
	                    bob.hitPlatform();
	                    listener.jump();
	                    if (rand.nextFloat() > 0.5f) {
	                        platform.pulverize();
	                    }
	                    break;
	                }
	            }
	        }
	    }
	
	    private void checkSquirrelCollisions() {
	        int len = squirrels.size();
	        for (int i = 0; i < len; i++) {
	            Squirrel squirrel = squirrels.get(i);
	            if (OverlapTester.overlapRectangles(squirrel.bounds, bob.bounds)) {
	                bob.hitSquirrel();
	                listener.hit();
	            }
	        }
	    }
	
	    private void checkItemCollisions() {
	        int len = coins.size();
	        for (int i = 0; i < len; i++) {
	            Coin coin = coins.get(i);
	            if (OverlapTester.overlapRectangles(bob.bounds, coin.bounds)) {
	                coins.remove(coin);
	                len = coins.size();
	                listener.coin();
	                score += Coin.COIN_SCORE;
	            }
	
	        }
	
	        if (bob.velocity.y > 0)
	            return;
	
	        len = springs.size();
	        for (int i = 0; i < len; i++) {
	            Spring spring = springs.get(i);
	            if (bob.position.y > spring.position.y) {
	                if (OverlapTester.overlapRectangles(bob.bounds, spring.bounds)) {
	                    bob.hitSpring();
	                    listener.highJump();
	                }
	            }
	        }
	    }
	
	    private void checkCastleCollisions() {
	        if (OverlapTester.overlapRectangles(castle.bounds, bob.bounds)) {
	            state = WORLD_STATE_NEXT_LEVEL;
	        }
	    }
	
	    private void checkGameOver() {
	        if (heightSoFar - 7.5f > bob.position.y) {
	            state = WORLD_STATE_GAME_OVER;
	        }
	    }
	}

## 最后的游戏画面GameScreen类

	public class GameScreen extends GLScreen {
	    static final int GAME_READY = 0;    
	    static final int GAME_RUNNING = 1;
	    static final int GAME_PAUSED = 2;
	    static final int GAME_LEVEL_END = 3;
	    static final int GAME_OVER = 4;
	  
	    int state;
	    Camera2D guiCam;
	    Vector2 touchPoint;
	    SpriteBatcher batcher;    
	    World world;
	    WorldListener worldListener;
	    WorldRenderer renderer;    
	    Rectangle pauseBounds;
	    Rectangle resumeBounds;
	    Rectangle quitBounds;
	    int lastScore;
	    String scoreString;    
	
	    public GameScreen(Game game) {
	        super(game);
	        state = GAME_READY;
	        guiCam = new Camera2D(glGraphics, 320, 480);
	        touchPoint = new Vector2();
	        batcher = new SpriteBatcher(glGraphics, 1000);
	        worldListener = new WorldListener() {
	            public void jump() {            
	                Assets.playSound(Assets.jumpSound);
	            }
	
	            public void highJump() {
	                Assets.playSound(Assets.highJumpSound);
	            }
	
	            public void hit() {
	                Assets.playSound(Assets.hitSound);
	            }
	
	            public void coin() {
	                Assets.playSound(Assets.coinSound);
	            }                      
	        };
	        world = new World(worldListener);
	        renderer = new WorldRenderer(glGraphics, batcher, world);
	        pauseBounds = new Rectangle(320- 64, 480- 64, 64, 64);
	        resumeBounds = new Rectangle(160 - 96, 240, 192, 36);
	        quitBounds = new Rectangle(160 - 96, 240 - 36, 192, 36);
	        lastScore = 0;
	        scoreString = "score: 0";
	    }
	
	    @Override
	    public void update(float deltaTime) {
	        if(deltaTime > 0.1f)
	            deltaTime = 0.1f;
	        
	        switch(state) {
	        case GAME_READY:
	            updateReady();
	            break;
	        case GAME_RUNNING:
	            updateRunning(deltaTime);
	            break;
	        case GAME_PAUSED:
	            updatePaused();
	            break;
	        case GAME_LEVEL_END:
	            updateLevelEnd();
	            break;
	        case GAME_OVER:
	            updateGameOver();
	            break;
	        }
	    }
	
	    private void updateReady() {
	        if(game.getInput().getTouchEvents().size() > 0) {
	            state = GAME_RUNNING;
	        }
	    }
	
	    private void updateRunning(float deltaTime) {
	        List<TouchEvent> touchEvents = game.getInput().getTouchEvents();
	        int len = touchEvents.size();
	        for(int i = 0; i < len; i++) {
	            TouchEvent event = touchEvents.get(i);
	            if(event.type != TouchEvent.TOUCH_UP)
	                continue;
	            
	            touchPoint.set(event.x, event.y);
	            guiCam.touchToWorld(touchPoint);
	            
	            if(OverlapTester.pointInRectangle(pauseBounds, touchPoint)) {
	                Assets.playSound(Assets.clickSound);
	                state = GAME_PAUSED;
	                return;
	            }            
	        }
	        
	        world.update(deltaTime, game.getInput().getAccelX());
	        if(world.score != lastScore) {
	            lastScore = world.score;
	            scoreString = "" + lastScore;
	        }
	        if(world.state == World.WORLD_STATE_NEXT_LEVEL) {
	            state = GAME_LEVEL_END;        
	        }
	        if(world.state == World.WORLD_STATE_GAME_OVER) {
	            state = GAME_OVER;
	            if(lastScore >= Settings.highscores[4]) 
	                scoreString = "new highscore: " + lastScore;
	            else
	                scoreString = "score: " + lastScore;
	            Settings.addScore(lastScore);
	            Settings.save(game.getFileIO());
	        }
	    }
	
	    private void updatePaused() {
	        List<TouchEvent> touchEvents = game.getInput().getTouchEvents();
	        int len = touchEvents.size();
	        for(int i = 0; i < len; i++) {
	            TouchEvent event = touchEvents.get(i);
	            if(event.type != TouchEvent.TOUCH_UP)
	                continue;
	            
	            touchPoint.set(event.x, event.y);
	            guiCam.touchToWorld(touchPoint);
	            
	            if(OverlapTester.pointInRectangle(resumeBounds, touchPoint)) {
	                Assets.playSound(Assets.clickSound);
	                state = GAME_RUNNING;
	                return;
	            }
	            
	            if(OverlapTester.pointInRectangle(quitBounds, touchPoint)) {
	                Assets.playSound(Assets.clickSound);
	                game.setScreen(new MainMenuScreen(game));
	                return;
	            
	            }
	        }
	    }
	
	    private void updateLevelEnd() {
	        List<TouchEvent> touchEvents = game.getInput().getTouchEvents();
	        int len = touchEvents.size();
	        for(int i = 0; i < len; i++) {                   
	            TouchEvent event = touchEvents.get(i);
	            if(event.type != TouchEvent.TOUCH_UP)
	                continue;
	            world = new World(worldListener);
	            renderer = new WorldRenderer(glGraphics, batcher, world);
	            world.score = lastScore;
	            state = GAME_READY;
	        }
	    }
	
	    private void updateGameOver() {
	        List<TouchEvent> touchEvents = game.getInput().getTouchEvents();
	        int len = touchEvents.size();
	        for(int i = 0; i < len; i++) {                   
	            TouchEvent event = touchEvents.get(i);
	            if(event.type != TouchEvent.TOUCH_UP)
	                continue;
	            game.setScreen(new MainMenuScreen(game));
	        }
	    }
	
	    @Override
	    public void present(float deltaTime) {
	        GL10 gl = glGraphics.getGL();
	        gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	        gl.glEnable(GL10.GL_TEXTURE_2D);
	        
	        renderer.render();
	        
	        guiCam.setViewportAndMatrices();
	        gl.glEnable(GL10.GL_BLEND);
	        gl.glBlendFunc(GL10.GL_SRC_ALPHA, GL10.GL_ONE_MINUS_SRC_ALPHA);
	        batcher.beginBatch(Assets.items);
	        switch(state) {
	        case GAME_READY:
	            presentReady();
	            break;
	        case GAME_RUNNING:
	            presentRunning();
	            break;
	        case GAME_PAUSED:
	            presentPaused();
	            break;
	        case GAME_LEVEL_END:
	            presentLevelEnd();
	            break;
	        case GAME_OVER:
	            presentGameOver();
	            break;
	        }
	        batcher.endBatch();
	        gl.glDisable(GL10.GL_BLEND);
	    }
	
	    private void presentReady() {
	        batcher.drawSprite(160, 240, 192, 32, Assets.ready);
	    }
	
	    private void presentRunning() {
	        batcher.drawSprite(320 - 32, 480 - 32, 64, 64, Assets.pause);
	        Assets.font.drawText(batcher, scoreString, 16, 480-20);
	    }
	
	    private void presentPaused() {        
	        batcher.drawSprite(160, 240, 192, 96, Assets.pauseMenu);
	        Assets.font.drawText(batcher, scoreString, 16, 480-20);
	    }
	
	    private void presentLevelEnd() {
	        String topText = "the princess is ...";
	        String bottomText = "in another castle!";
	        float topWidth = Assets.font.glyphWidth * topText.length();
	        float bottomWidth = Assets.font.glyphWidth * bottomText.length();
	        Assets.font.drawText(batcher, topText, 160 - topWidth / 2, 480 - 40);
	        Assets.font.drawText(batcher, bottomText, 160 - bottomWidth / 2, 40);
	    }
	
	    private void presentGameOver() {
	        batcher.drawSprite(160, 240, 160, 96, Assets.gameOver);        
	        float scoreWidth = Assets.font.glyphWidth * scoreString.length();
	        Assets.font.drawText(batcher, scoreString, 160 - scoreWidth / 2, 480-20);
	    }
	
	    @Override
	    public void pause() {
	        if(state == GAME_RUNNING)
	            state = GAME_PAUSED;
	    }
	
	    @Override
	    public void resume() {        
	    }
	
	    @Override
	    public void dispose() {       
	    }
	}

## 渲染游戏画面的WorldRenderer类

	public class WorldRenderer {
	    static final float FRUSTUM_WIDTH = 10;
	    static final float FRUSTUM_HEIGHT = 15;    
	    GLGraphics glGraphics;
	    World world;
	    Camera2D cam;
	    SpriteBatcher batcher;    
	    
	    public WorldRenderer(GLGraphics glGraphics, SpriteBatcher batcher, World world) {
	        this.glGraphics = glGraphics;
	        this.world = world;
	        this.cam = new Camera2D(glGraphics, FRUSTUM_WIDTH, FRUSTUM_HEIGHT);
	        this.batcher = batcher;        
	    }
	    
	    public void render() {
	        if(world.bob.position.y > cam.position.y )
	            cam.position.y = world.bob.position.y;
	        cam.setViewportAndMatrices();
	        renderBackground();
	        renderObjects();        
	    }
	
	    public void renderBackground() {
	        batcher.beginBatch(Assets.background);
	        batcher.drawSprite(cam.position.x, cam.position.y,
	                           FRUSTUM_WIDTH, FRUSTUM_HEIGHT, 
	                           Assets.backgroundRegion);
	        batcher.endBatch();
	    }
	
	    public void renderObjects() {
	        GL10 gl = glGraphics.getGL();
	        gl.glEnable(GL10.GL_BLEND);
	        gl.glBlendFunc(GL10.GL_SRC_ALPHA, GL10.GL_ONE_MINUS_SRC_ALPHA);
	        
	        batcher.beginBatch(Assets.items);
	        renderBob();
	        renderPlatforms();
	        renderItems();
	        renderSquirrels();
	        renderCastle();
	        batcher.endBatch();
	        gl.glDisable(GL10.GL_BLEND);
	    }
	
	    private void renderBob() {
	        TextureRegion keyFrame;
	        switch(world.bob.state) {
	        case Bob.BOB_STATE_FALL:
	            keyFrame = Assets.bobFall.getKeyFrame(world.bob.stateTime, Animation.ANIMATION_LOOPING);
	            break;
	        case Bob.BOB_STATE_JUMP:
	            keyFrame = Assets.bobJump.getKeyFrame(world.bob.stateTime, Animation.ANIMATION_LOOPING);
	            break;
	        case Bob.BOB_STATE_HIT:
	        default:
	            keyFrame = Assets.bobHit;                       
	        }
	        
	        float side = world.bob.velocity.x < 0? -1: 1;        
	        batcher.drawSprite(world.bob.position.x, world.bob.position.y, side * 1, 1, keyFrame);        
	    }
	
	    private void renderPlatforms() {
	        int len = world.platforms.size();
	        for(int i = 0; i < len; i++) {
	            Platform platform = world.platforms.get(i);
	            TextureRegion keyFrame = Assets.platform;
	            if(platform.state == Platform.PLATFORM_STATE_PULVERIZING) {                
	                keyFrame = Assets.brakingPlatform.getKeyFrame(platform.stateTime, Animation.ANIMATION_NONLOOPING);
	            }            
	                                   
	            batcher.drawSprite(platform.position.x, platform.position.y, 
	                               2, 0.5f, keyFrame);            
	        }
	    }
	
	    private void renderItems() {
	        int len = world.springs.size();
	        for(int i = 0; i < len; i++) {
	            Spring spring = world.springs.get(i);            
	            batcher.drawSprite(spring.position.x, spring.position.y, 1, 1, Assets.spring);
	        }
	        
	        len = world.coins.size();
	        for(int i = 0; i < len; i++) {
	            Coin coin = world.coins.get(i);
	            TextureRegion keyFrame = Assets.coinAnim.getKeyFrame(coin.stateTime, Animation.ANIMATION_LOOPING);
	            batcher.drawSprite(coin.position.x, coin.position.y, 1, 1, keyFrame);
	        }
	    }
	
	    private void renderSquirrels() {
	        int len = world.squirrels.size();
	        for(int i = 0; i < len; i++) {
	            Squirrel squirrel = world.squirrels.get(i);
	            TextureRegion keyFrame = Assets.squirrelFly.getKeyFrame(squirrel.stateTime, Animation.ANIMATION_LOOPING);
	            float side = squirrel.velocity.x < 0?-1:1;
	            batcher.drawSprite(squirrel.position.x, squirrel.position.y, side * 1, 1, keyFrame);
	        }
	    }
	
	    private void renderCastle() {
	        Castle castle = world.castle;
	        batcher.drawSprite(castle.position.x, castle.position.y, 2, 2, Assets.castle);
	    }
	}