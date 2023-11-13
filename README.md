Get started

Full sample is here: https://github.com/ZiggeoJitpackService/ExoPlayerAdsExample

This the “Hello World” tutorial with ExoPlayer https://exoplayer.dev/hello-world.html

Adding ad to sample project https://exoplayer.dev/ad-insertion.html

All the implementation is:

def exoplayer = '2.11.8'
implementation "com.google.android.exoplayer:exoplayer:$exoplayer"
implementation "com.google.android.exoplayer:exoplayer-core:$exoplayer"
implementation "com.google.android.exoplayer:exoplayer-dash:$exoplayer"
implementation "com.google.android.exoplayer:exoplayer-ui:$exoplayer"
implementation "com.google.android.exoplayer:extension-ima:$exoplayer"

So this is all test code for sample project using ExoPlayer:

main_activity.xml:

 <?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
xmlns:tools="http://schemas.android.com/tools"
android:id="@+id/main_browse_fragment"
android:layout_width="match_parent"
android:layout_height="match_parent"
tools:context=".MainActivity"
tools:deviceIds="tv"
tools:ignore="MergeRootFrame">

    <com.google.android.exoplayer2.ui.PlayerView
     android:id="@+id/video_view"
     android:layout_width="match_parent"
     android:layout_height="match_parent"/>

</FrameLayout>


MainActivity.kt:

class MainActivity : FragmentActivity() {

private val SAMPLE_VIDEO_URL = "https://storage.googleapis.com/gvabox/media/samples/stock.mp4"
private val SAMPLE_VAST_TAG_URL =
"https://pubads.g.doubleclick.net/gampad/ads?iu=/21775744923/external/single_ad_samples&sz=640x480&cust_params=sample_ct%3Dredirectlinear&ciu_szs=300x250%2C728x90&gdfp_req=1&output=vast&unviewed_position_start=1&env=vp&impl=s&correlator="

private var adsPaused = false

override fun onCreate(savedInstanceState: Bundle?) {
super.onCreate(savedInstanceState)
setContentView(R.layout.activity_main)

       var playerView = findViewById<PlayerView>(R.id.video_view)
       val videoTrackSelectionFactory: TrackSelection.Factory = AdaptiveTrackSelection.Factory()
       val trackSelector = DefaultTrackSelector(videoTrackSelectionFactory)
       val player = ExoPlayerFactory.newSimpleInstance(this, trackSelector)
       var adsLoader = ImaAdsLoader(this, Uri.parse(SAMPLE_VAST_TAG_URL))
       playerView.player = player
       adsLoader.setPlayer(player)
       val sourceFactory: DataSource.Factory = DefaultDataSourceFactory(
           this,
           Util.getUserAgent(this, "appname")
       )
       var contentSource: MediaSource?


       val contentSourceFactory = ProgressiveMediaSource.Factory(sourceFactory)
       contentSource = contentSourceFactory.createMediaSource(Uri.parse(SAMPLE_VIDEO_URL))
       contentSource =
           AdsMediaSource(contentSource, sourceFactory, adsLoader, playerView)


       player.prepare(contentSource)
       player.playWhenReady = true

}

To add any ads tap effect you need to add addAdsLoadedListener to adsLoader:

adsLoader.adsLoader.addAdsLoadedListener { adsManagerLoadedEvent: AdsManagerLoadedEvent ->
// Ads were successfully loaded, so get the AdsManager instance. AdsManager has
// events for ad playback and errors.
var adsManager = adsManagerLoadedEvent.adsManager

// Attach event listener.
adsManager.addAdEventListener(
...

           } else if (adEvent.type == AdEvent.AdEventType.TAPPED) {
               player.playWhenReady = !adsPaused
               adsPaused = !adsPaused
           } else {
           }
       }

)
...
}









