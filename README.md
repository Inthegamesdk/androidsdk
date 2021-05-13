# In The Game Android SDK

This SDK allows you to easily integrate the In The Game engagement platform in a video player in your Android app.
The repository includes an example app that shows how to use the framework.


## Installation

In your Android project, choose **File > New > New module > Import from .aar file**. 

Select the **itgframework.aar** file (included in this repo).

Add the following imports (if missing) to your app's build.gradle:

```
implementation project(":itgframework")
implementation 'androidx.localbroadcastmanager:localbroadcastmanager:1.0.0'
implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
implementation "org.jetbrains.kotlin:kotlin-stdlib-jdk7:1.3.61"
```

## Usage

To quickly show a full-screen activity with your interactive video channel (examples are in Kotlin):

```
val videoURL = "<your video url>"

val intent = Intent(this, ITGPlayerActivity::class.java)
var bundle = Bundle()
bundle.putString(ITGPlayerActivity.URL_PARAM, videoURL)
bundle.putString(ITGPlayerActivity.BROADCASTER_PARAM, "<your broadcaster name>")
intent.putExtras(bundle)
startActivity(intent)
```

To load the video channel in a view in your custom layout file: 
(add constraints or layout parameters as needed)

```
<com.inthegame.itgframework.ITGPlayerView
  android:id="@+id/playerView"
  android:layout_width="match_parent"
  android:layout_height="100dp"
  android:background="#CCCCCC"/>
```

And load it in your activity/fragment (we recommend doing it in `onResume`):

```
playerView.load("<your video url>", "<your broadcaster name>")
```

There are additional parameters for further configuration: `language`, `allowsFullScreen` and `devMode`:

```
playerView.load("<your video url>", "<your broadcaster name>", "en", false, false)
```

You can set devMode to true to use the development environment. If not specified, production environment is used as the default. 

If you want to connect ITG content with your user's account, there are other parameters in the `load()` method where you can specify your user's ID and display name. The variables are called `userBroadcasterForeignID` and `userInitialName`.

## Overlay mode

The overlay option allows for maximum flexibility - you create a view for the ITG interactive Overlay and position it over your video player as you see fit.

You can add the overlay in a view in your layout file:
```
<com.inthegame.itgframework.ITGOverlayView />
```

You can load it as:
```
overlayView.load("<your video url>", "<your broadcaster name>")
```

You will need to send playback updates manually to the overlay
(please remember to update the timer whenever you seek to a different part of the video, and to send play/pause commands whenever the video is started or paused):
```
overlayView.sendCommand(ITGOverlayView.COMMAND_PLAY)
overlayView.sendCommand(ITGOverlayView.COMMAND_PAUSE)
overlayView.sendCommand(ITGOverlayView.COMMAND_STOP)
overlayView.updateTime(10000) //in milliseconds
```

The overlay content will be sized to take the available space while fitting a specified video aspect. The default is the standard 16:9. For other video formats, you can set the aspect ratio as:
```
overlayView.setAspectRatio("4:3")
```

The listener allows you to detect when an activity is opened or closed, and to detect touches on the video area:
```
val listener = object: ITGOverlayListener {
   override fun didOpenActivity() {
   }
   override fun didCloseActivity() {
   }
   override fun didTapVideo() {
   }
}
overlayView.load("<your video url>", "<your broadcaster name>", "en", listener)
```

You can check the `OverlayActivity` in the example app for a detailed integration sample.


## Notes

Here is the activity loading code for Java:

```
Intent intent = new Intent(MainActivity.this, ITGPlayerActivity.class);
Bundle bundle = new Bundle();
bundle.putString(ITGPlayerActivity.getURL_PARAM(), videoURL);
bundle.putString(ITGPlayerActivity.getBROADCASTER_PARAM(), "<your broadcaster name>");
intent.putExtras(bundle);
startActivity(intent);
```

If you run into an Android AppCompat bug that causes webviews to crash, go to the app's build.gradle file and replace this line:
```
implementation 'androidx.appcompat:appcompat:1.1.0'
```

With:
```
implementation 'androidx.appcompat:appcompat:1.1.0-rc01'
```
