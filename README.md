# Mp4Composer-android

This library generate an Mp4 movie using Android MediaCodec API and apply filter, scale, trim, transcode, crop, mute and rotate Mp4.<br>
Idea from: [android-transcoder](https://github.com/ypresto/android-transcoder)

<table>
    <td><img src="demos/sample.gif"><br>Sample Video<br>No filter</td>
    <td><img src="demos/grayscale.gif" ><br><a href="mp4compose/src/main/java/com/daasuu/mp4compose/filter/GlGrayScaleFilter.java">GlGlayScaleFilter</a><br> apply</td>
    <td><img src="demos/monochrome.gif" ><br><a href="mp4compose/src/main/java/com/daasuu/mp4compose/filter/GlMonochromeFilter.java">GlMonochromeFilter</a><br> apply</td>
    <td><img src="demos/watermark.gif" ><br><a href="mp4compose/src/main/java/com/daasuu/mp4compose/filter/GlWatermarkFilter.java">GlWatermarkFilter</a><br> apply</td>
</table>






## Gradle
Step 1. Add the JitPack repository to your build file
```groovy
allprojects {
	repositories {
		...
		maven { url 'https://jitpack.io' }
	}
}
```
Step 2. Add the dependency
```groovy
dependencies {
        implementation 'com.github.MasayukiSuda:Mp4Composer-android:v0.4.1'
}
```

## Usage
```
    new Mp4Composer(srcMp4Path, destMp4Path)
            .rotation(Rotation.ROTATION_90)
            .size((width) 1080, (height) 720)
            .fillMode(FillMode.PRESERVE_ASPECT_FIT)
            .filter(new GlFilterGroup(new GlMonochromeFilter(), new GlVignetteFilter()))
            .trim((trimStdemosMs) 200, (trimEndMs) 5000)
            .listener(new Mp4Composer.Listener() {
                @Override
                public void onProgress(double progress) {
                    Log.d(TAG, "onProgress = " + progress);
                }

                @Override
                public void onCompleted() {
                    Log.d(TAG, "onCompleted(): Video conversion completed");
                    runOnUiThread(() -> {
                        Toast.makeText(context, "Video saved under " + destPath, Toast.LENGTH_SHORT).show();
                    });
                }

                @Override
                public void onCanceled() {
                    Log.d(TAG, "onCanceled");
                }

                @Override
                public void onFailed(Exception exception) {
                    Log.e(TAG, "onFailed()", exception);
                }
            })
            .stdemos();
```
## Builder Method
| method | description |
|:---|:---|
| rotation | Rotation of the movie, default Rotation.NORMAL |
| size | Resolution of the movie, default same resolution of src movie. If you specify a resolution that MediaCodec does not support, an error will occur. |
| fillMode | Options for scaling the bounds of an movie. PRESERVE_ASPECT_FIT is fit center. PRESERVE_ASPECT_CROP is center crop , default PRESERVE_ASPECT_FIT. <br>FILLMODE_CUSTOM is used to crop a video. Check <a href="https://github.com/MasayukiSuda/Mp4Composer-android/blob/master/demos/fillmode_custom.gif">this</a> for behavior. Sample source code is <a href="https://github.com/MasayukiSuda/Mp4Composer-android/blob/master/sample/src/main/java/com/daasuu/sample/FillModeCustomActivity.java">this</a>. |
| filter | This filter is OpenGL Shaders to apply effects on video. Custom filters can be created by inheriting <a href="https://github.com/MasayukiSuda/Mp4Composer-android/blob/master/mp4compose/src/main/java/com/daasuu/mp4compose/filter/GlFilter.java">GlFilter.java</a>. , default GlFilter(No filter). Filters is <a href="https://github.com/MasayukiSuda/Mp4Composer-android/tree/master/mp4compose/src/main/java/com/daasuu/mp4compose/filter">here</a>. |
| videoBitrate | Set Video Bitrate, default video bitrate is 0.25 * 30 * outputWidth * outputHeight |
| mute | Mute audio track on exported video. Default `mute = false`. |
| trim | Trim both audio and video tracks to the provided stdemos and end times, inclusive. Default does not trim anything from the stdemos or end. |
| flipVertical | Flip Vertical on exported video. Default `flipVertical = false`. |
| flipHorizontal | Flip Horizontal on exported video. Default `flipHorizontal = false`. |
| videoFormatMimeType | The mime type of the video format on exported video. default AUTO. Suppurt HEVC, AVC, MPEG4, H263. Check <a href="https://github.com/MasayukiSuda/Mp4Composer-android/blob/master/mp4compose/src/main/java/com/daasuu/mp4compose/VideoFormatMimeType.java" >this</a>. |
| timeScale | Set TimeScale. default value is 1f. should be in range 0.125 (-8X) to 8.0 (8X) |

