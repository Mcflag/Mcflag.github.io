title: Android官方培训教程8——多媒体音频播放
date: 2015-08-31 23:21:02
tags: [Android官方培训教程]
categories: [Android官方培训教程]
---

## 摘要
多媒体音频播放
<!--more-->

## 管理音频播放

如果你的App在播放音频，显然用户能够以预期的方式来控制音频是很重要的。为了保证好的用户体验，同样App能够获取音频焦点是很重要的，这样才能确保不会在同一时刻出现多个App的声音。

能够创建对硬件音量按钮进行响应的App，当按下音量按钮的时候需要获取到当前音频的焦点，然后以适当的方式改变音量从而进行响应用户的行为。

## 控制音量与音频播放

一个好的用户体验是可预期可控的。如果App是在播放音频，那么显然我们需要做到能够通过硬件按钮，软件按钮，蓝牙耳麦等来控制音量。 同样的，我们需要能够进行play, stop, pause, skip, and previous等动作，并且进行对应的响应。

### 鉴别使用的是哪个音频流

首先需要知道的是我们的App会使用到哪些音频流。Android为播放音乐，闹铃，通知铃，来电声音，系统声音，打电话声音与DTMF频道分别维护了一个隔离的音频流。这是我们能够控制不同音频的前提。其中大多数都是被系统限制的，不能胡乱使用。除了你的App是需要做替换闹钟的铃声的操作，那么几乎其他的播放音频操作都是使用"STREAM_MUSIC"音频流。

### 使用硬件音量键来控制App的音量

默认情况下，按下音量控制键会调节当前被激活的音频流，如果你的App没有在播放任何声音，则会调节响铃的声音。如果是一个游戏或者音乐程序，需要在不管是否目前正在播放歌曲或者游戏目前是否发出声音的时候，按硬件的音量键都会有对应的音量调节。我们需要监听音量键是否被按下，Android提供了setVolumeControlStream()的方法来直接控制指定的音频流。在鉴别出App会使用哪个音频流之后，需要在Activity或者Fragment创建的时候就设置音量控制，这样能确保不管App是否可见，音频控制功能都正常工作。

	setVolumeControlStream(AudioManager.STREAM_MUSIC);

### 使用硬件的播放控制按键来控制App的音频播放

媒体播放按钮，例如play, pause, stop, skip, and previous的功能同样可以在一些线控，耳麦或者其他无线控制设备上实现。无论用户按下上面任何设备上的控制按钮，系统都会广播一个带有 ACTION_MEDIA_BUTTON 的Intent。为了响应那些操作，需要像下面一样注册一个BroadcastReceiver在Manifest文件中。

	<receiver android:name=".RemoteControlReceiver">
		<intent-filter>
			<action android:name="android.intent.action.MEDIA_BUTTON" />
		</intent-filter>
	</receiver>

Receiver需要判断这个广播是来自哪个按钮的操作，Intent在 EXTRA_KEY_EVENT 中包含了KEY的信息，同样KeyEvent类包含了一列 KEYCODE_MEDIA_ 的静态变量来表示不同的媒体按钮，例如 KEYCODE_MEDIA_PLAY_PAUSE 与 KEYCODE_MEDIA_NEXT

	public class RemoteControlReceiver extends BroadcastReceiver {
		@Override
		public void onReceive(Context context, Intent intent) {
			if (Intent.ACTION_MEDIA_BUTTON.equals(intent.getAction())) {
				KeyEvent event = (KeyEvent)intent.getParcelableExtra(Intent.EXTRA_KEY_EVENT);
				if (KeyEvent.KEYCODE_MEDIA_PLAY == event.getKeyCode()) {
					// Handle key press.
				}
			}
		}
	}

因为可能有多个程序都同样监听了那些控制按钮，那么必须在代码中特意控制当前哪个Receiver会进行响应。下面的例子显示了如何使用AudioManager来注册监听与取消监听，当Receiver被注册上时，它将是唯一响应Broadcast的Receiver。

	AudioManager am = mContext.getSystemService(Context.AUDIO_SERVICE);
	...
	// Start listening for button presses
	am.registerMediaButtonEventReceiver(RemoteControlReceiver);
	...
	// Stop listening for button presses
	am.unregisterMediaButtonEventReceiver(RemoteControlReceiver);

通常，App需要在Receiver没有激活或者不可见的时候（比如在onStop的方法里面）取消注册监听。但是在媒体播放的时候并没有那么简单，实际上，我们需要在后台播放歌曲的时候同样需要进行响应。 一个比较好的注册与取消监听的方法是当程序获取与失去音频焦点的时候进行操作。

### 管理音频焦点

由于有多个应用可能播放音频，考虑他们应该如何交互非常重要。为了防止多个音乐app同时播放音频，Android使用音频焦点[audio focus]来控制音频的播放 — 只有获取到audio focus的App才能够播放音频。

在你的App开始播放音频之前，它需要经过发出请求[request]->接受请求[receive]->音频焦点锁定[Audio Focus]的过程。同样，它需要知道如何监听失去音频焦点[lose of audio focus]的事件并进行合适的响应。

### 请求获取音频焦点

在你的App开始播放音频之前，它需要获取它将要使用的音频流的音频焦点。通过使用 requestAudioFocus()) 方法来获取你想要获取到的音频流焦点。如果请求成功这个方法会返回 AUDIOFOCUS_REQUEST_GRANTED 。

你必须指定正在使用哪个音频流，而且需要确定请求的是短暂的还是永久的audio focus。

* 短暂的焦点锁定：当期待播放一个短暂的音频的时候（比如播放导航指示）
* 永久的焦点锁定：当计划播放可预期到的较长的音频的时候（比如播放音乐）

下面是一个在播放音乐的时候请求永久的音频焦点的例子，我们必须在开始播放之前立即请求音频焦点，比如在用户点击播放或者游戏程序中下一关开始的片头音乐。

	AudioManager am = mContext.getSystemService(Context.AUDIO_SERVICE);
	...
	// Request audio focus for playback
	int result = am.requestAudioFocus(afChangeListener,
			// Use the music stream.
			AudioManager.STREAM_MUSIC,
			// Request permanent focus.
			AudioManager.AUDIOFOCUS_GAIN);

	if (result == AudioManager.AUDIOFOCUS_REQUEST_GRANTED) {
		am.registerMediaButtonEventReceiver(RemoteControlReceiver);
		// Start playback.
	}

一旦结束了播放，需要确保调用 abandonAudioFocus())方法。这样会通知系统说你不再需要获取焦点并且取消注册AudioManager.OnAudioFocusChangeListener的监听。在释放短暂音频焦点的情况下，这会允许任何被你打断的App继续播放。

	// Abandon audio focus when playback complete
	am.abandonAudioFocus(afChangeListener);

当请求短暂音频焦点的时候，我们可以选择是否开启“ducking”。Ducking是一个特殊的机制使得允许音频间歇性的短暂播放。 通常情况下，一个好的App在失去音频焦点的时候它会立即保持安静。如果我们选择在请求短暂音频焦点的时候开启了ducking，那意味着其它App可以继续播放，仅仅是在这一刻降低自己的音量，在短暂重新获取到音频焦点后恢复正常音量(也就是说：不用理会这个请求短暂焦点的请求，这并不会导致目前在播放的音频受到牵制，比如在播放音乐的时候突然出现一个短暂的短信提示声音，这个时候仅仅是把播放歌曲的音量暂时调低，好让短信声能够让用户听到，之后立马恢复正常播放)。

	// Request audio focus for playback
	int result = am.requestAudioFocus(afChangeListener,
			// Use the music stream.
			AudioManager.STREAM_MUSIC,
			// Request permanent focus.
			AudioManager.AUDIOFOCUS_GAIN_TRANSIENT_MAY_DUCK);

	if (result == AudioManager.AUDIOFOCUS_REQUEST_GRANTED) {
		// Start playback.
	}

### 处理失去音频焦点

如果A程序可以请求获取音频焦点，那么在B程序请求获取的时候，A获取到的焦点就会失去。显然我们需要处理失去焦点的事件。

在音频焦点的监听器里面，当接受到描述焦点改变的事件时会触发onAudioFocusChange())回调方法。对应于获取焦点的三种类型，我们同样会有三种失去焦点的类型 - 永久失去，短暂失去，允许Ducking的短暂失去。

* 失去短暂焦点：通常在失去这种焦点的情况下，我们会暂停当前音频的播放或者降低音量，同时需要准备在重新获取到焦点之后恢复播放。
* 失去永久焦点：假设另外一个程序开始播放音乐等，那么我们的程序就应该有效的结束自己。实用的做法是停止播放，移除button监听，允许新的音频播放器独占监听那些按钮事件，并且放弃自己的音频焦点。这种情况下，重新播放你的音频之前，你需要确保用户重新点击了你的App的播放按钮等。

	OnAudioFocusChangeListener afChangeListener = new OnAudioFocusChangeListener() {
		public void onAudioFocusChange(int focusChange) {
			if (focusChange == AUDIOFOCUS_LOSS_TRANSIENT) {
				// Pause playback
			} else if (focusChange == AudioManager.AUDIOFOCUS_GAIN) {
				// Resume playback
			} else if (focusChange == AudioManager.AUDIOFOCUS_LOSS) {
				am.unregisterMediaButtonEventReceiver(RemoteControlReceiver);
				am.abandonAudioFocus(afChangeListener);
				// Stop playback
			}
		}
	};

### Duck机制

Ducking是一个特殊的机制使得允许音频间歇性的短暂播放。在Ducking的情况下，正常播放的歌曲会降低音量来凸显这个短暂的音频声音，这样既让这个短暂的声音比较突出，又不至于打断正常的声音。下面的代码片段让我们的播放器在暂时失去音频焦点时降低音量，并在重新获得音频焦点之后恢复原来音量。

	OnAudioFocusChangeListener afChangeListener = new OnAudioFocusChangeListener() {
		public void onAudioFocusChange(int focusChange) {
			if (focusChange == AUDIOFOCUS_LOSS_TRANSIENT_CAN_DUCK) {
				// Lower the volume
			} else if (focusChange == AudioManager.AUDIOFOCUS_GAIN) {
				// Raise it back to normal
			}
		}
	};

监听失去音频焦点是最重要的广播之一，但不是唯一的方法。系统广播了一系列的intent来警示你去改变用户的音频使用体验。

## 兼容音频输出设备

用户在播放音乐的时候有多个选择，可以使用内置的扬声器，有线耳机或者是支持A2DP的蓝牙耳机。

### 检测目前正在使用的硬件设备

选择的播放设备会影响App的行为。可以使用AudioManager来查询某个音频输出到扬声器，有线耳机还是蓝牙上。

	if (isBluetoothA2dpOn()) {
		// Adjust output for Bluetooth.
	} else if (isSpeakerphoneOn()) {
		// Adjust output for Speakerphone.
	} else if (isWiredHeadsetOn()) {
		// Adjust output for headsets
	} else {
		// If audio plays and noone can hear it, is it still playing?
	}

### 处理音频输出设备的改变

当有线耳机被拔出或者蓝牙设备断开连接的时候，音频流会自动输出到内置的扬声器上。假设之前播放声音很大，这个时候突然转到扬声器播放会显得非常嘈杂。

幸运的是，系统会在那种事件发生时会广播带有ACTION_AUDIO_BECOMING_NOISY的intent。无论何时播放音频去注册一个BroadcastReceiver来监听这个intent会是比较好的做法。

在音乐播放器下，用户通常希望发生那样事情的时候能够暂停当前歌曲的播放。在游戏里，通常会选择减低音量。

	private class NoisyAudioStreamReceiver extends BroadcastReceiver {
		@Override
		public void onReceive(Context context, Intent intent) {
			if (AudioManager.ACTION_AUDIO_BECOMING_NOISY.equals(intent.getAction())) {
				// Pause the playback
			}
		}
	}

	private IntentFilter intentFilter = new IntentFilter(AudioManager.ACTION_AUDIO_BECOMING_NOISY);
	private void startPlayback() {
		registerReceiver(myNoisyAudioStreamReceiver(), intentFilter);
	}
	private void stopPlayback() {
		unregisterReceiver(myNoisyAudioStreamReceiver);
	}

