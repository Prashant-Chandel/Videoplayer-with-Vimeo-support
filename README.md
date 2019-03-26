# Videoplayer-with-Vimeo-support
Android Vimeo /normal video player 
# DEMO Screen shots



![alt text](https://github.com/Prashant-Chandel/Videoplayer-with-Vimeo-support/blob/master/Screenshot/Screenshot_20190310-160858.png)
![alt text](https://github.com/Prashant-Chandel/Videoplayer-with-Vimeo-support/blob/master/Screenshot/Screenshot_20190310-160922.png)
![alt text](https://github.com/Prashant-Chandel/Videoplayer-with-Vimeo-support/blob/master/Screenshot/Screenshot_20190310-161047.png)
![alt text](https://github.com/Prashant-Chandel/Videoplayer-with-Vimeo-support/blob/master/Screenshot/Screenshot_20190310-161052.png)
![alt text](https://github.com/Prashant-Chandel/Videoplayer-with-Vimeo-support/blob/master/Screenshot/Screenshot_20190310-162217.png)

# Usage
For a working implementation of this project see the sample app.
# Gradle dependency 
step 1:Add it in your root build.gradle at the end of repositories:

	allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}
Step 2. Add the dependency

	dependencies {
	        implementation 'com.github.Prashant-Chandel:Videoplayer-with-Vimeo-support:138257e15c'
	}
            
# For Maven dependency use 

<repositories>
		<repository>
		    <id>jitpack.io</id>
		    <url>https://jitpack.io</url>
		</repository>
	</repositories>
  
Step 2. Add the dependency

	<dependency>
	    <groupId>com.github.Prashant-Chandel</groupId>
	    <artifactId>Videoplayer-with-Vimeo-support</artifactId>
	    <version>138257e15c</version>
	</dependency>            
            
  
  # Code Implementation
  
Include the UniversalVideoView and UniversalMediaController widget in your layout. This should usually be placed in the same parent ViewGroup, which makes sense when in full screen state.
            <FrameLayout
                android:id="@+id/video_layout"
                android:layout_width="fill_parent"
                android:layout_height="200dp"
                android:background="@android:color/black">

                <com.universalvideoview.UniversalVideoView
                    android:id="@+id/videoView"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_gravity="center"
                    app:uvv_autoRotation="true"
                    app:uvv_fitXY="false" />

                <com.universalvideoview.UniversalMediaController
                    android:id="@+id/media_controller"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    app:uvv_scalable="true" />

            </FrameLayout>
	    
In your onCreate method, set the UniversalMediaController to the UniversalVideoView and implements the

UniversalVideoView.VideoViewCallback Callback.
            View mBottomLayout;
            View mVideoLayout;
            UniversalVideoView mVideoView;
            UniversalMediaController mMediaController;

            mVideoView = (UniversalVideoView) findViewById(R.id.videoView);
            mMediaController = (UniversalMediaController) findViewById(R.id.media_controller);
            mVideoView.setMediaController(mMediaController);

            mVideoView.setVideoViewCallback(new UniversalVideoView.VideoViewCallback() {
                @Override
                public void onScaleChange(boolean isFullscreen) {
                    this.isFullscreen = isFullscreen;
                    if (isFullscreen) {
                        ViewGroup.LayoutParams layoutParams = mVideoLayout.getLayoutParams();
                        layoutParams.width = ViewGroup.LayoutParams.MATCH_PARENT;
                        layoutParams.height = ViewGroup.LayoutParams.MATCH_PARENT;
                        mVideoLayout.setLayoutParams(layoutParams);
                        //GONE the unconcerned views to leave room for video and controller
                        mBottomLayout.setVisibility(View.GONE);
                    } else {
                        ViewGroup.LayoutParams layoutParams = mVideoLayout.getLayoutParams();
                        layoutParams.width = ViewGroup.LayoutParams.MATCH_PARENT;
                        layoutParams.height = this.cachedHeight;
                        mVideoLayout.setLayoutParams(layoutParams);
                        mBottomLayout.setVisibility(View.VISIBLE);
                    }
                }

                @Override
                public void onPause(MediaPlayer mediaPlayer) { // Video pause
                    Log.d(TAG, "onPause UniversalVideoView callback");
                }

                @Override
                public void onStart(MediaPlayer mediaPlayer) { // Video start/resume to play
                    Log.d(TAG, "onStart UniversalVideoView callback");
                }

                @Override
                public void onBufferingStart(MediaPlayer mediaPlayer) {// steam start loading
                    Log.d(TAG, "onBufferingStart UniversalVideoView callback");
                }

                @Override
                public void onBufferingEnd(MediaPlayer mediaPlayer) {// steam end loading
                    Log.d(TAG, "onBufferingEnd UniversalVideoView callback");
                }

            });
            
            
            
Work with vimeo Executor:-To get an vimeo playable link from vimeo client
Video information can be extracted from an identifier:
```java
VimeoExtractor.getInstance().fetchVideoWithIdentifier("1234", null, new OnVimeoExtractionListener() {
            @Override
            public void onSuccess(VimeoVideo video) {
                String hdStream = video.getStreams().get("1080p");
                //...
            }

            @Override
            public void onFailure(Throwable throwable) {
                //Error handling here
            }
});}
```

Or alternatively from a full video url:
```java
VimeoExtractor.getInstance().fetchVideoWithURL("https://vimeo.com/1234", null, new OnVimeoExtractionListener() {
            @Override
            public void onSuccess(VimeoVideo video) {
                String hdStream = video.getStreams().get("1080p");
                //...
            }

            @Override
            public void onFailure(Throwable throwable) {
                //Error handling here
            }
});
```

# Example with vimeo url is for further refrance please check sample code:-
```java
 VimeoExtractor.getInstance().fetchVideoWithURL(Constants.VIDEO_Vimeo_URL, null, new OnVimeoExtractionListener() {
            @Override
            public void onSuccess(final VimeoVideo video) {
                String hdStream = video.getStreams().get("1080p");

              
               if (hdStream != null) {
                    getActivity().runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            // Start the MediaController
                            mVideoView.setMediaController(mMediaController);
                            // Get the URL from String VideoURL
                            Uri video = Uri.parse(hdStreamuRL);

                            mVideoView.setVideoURI(video);

                        }
                    });
                }
            }
```

Please build and read the documentation for a better oversight of the information available from each video.

# Private Videos
When requesting information on private videos - pass the referrer parameter as null, this will ensure the default header is sent and should avoid any HTTP 403 Forbidden errors.

            
# Note
Support Android Kitkat v4.4(API Level 19 and above).

UniversalVideoView does not retain its full state when going into the background. You should save or restore the state and take care of the Activity Lifecycle.
You may need to set the android:configChanges="orientation|keyboardHidden|screenSize" for your Activity in AndroidManifest.xml to prevent the system from recreate the Activity while phone rotation.

Customization
UniversalVideoView attribute
uvv_fitXY, Video scale to fill the VideoView's dimension or keep Aspect Ratio (default) likes Android framework VideoView.
uvv_autoRotation, auto switch to landscape(fullscreen) or portrait mode according to the orientation sensor.
UniversalMediaController attribute
uvv_scalable, show or hide the scale button by coding or in xml. if you will not play the video in fullscreen.

License

Copyright 2019  Prashant chandel <chandela.prashant14@gmail.com>

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
