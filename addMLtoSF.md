# Adding MLKit to SceneForm

1. get image
2. pass it to the processor
3. get coordinates
4. create anchor
5. set listener

## 1. get image

1. capture frame, image
2. capture arSceneView, bitmap

## 2. pass it to the processor

- image formatting
  - frame -> YuvToRgbConverter not working
  - arSceneView -> bitmap -> byteBuffer
- analyzer - result []
  1. change the input of ARinML
  2. learn OpenGL, if button working all time


## 3. get coordinates



## 4. create anchor



## 5. set listener

`arFragment.arSceneView.scene.addOnUpdateListener(this::onUpdateFrame)` this rather than `setOnTapArPlaneListener`


# what i did

1. get session and frame, got them from MLinAR and ai-ar
2. acquire image, add tryAcquireCameraImage function
3. displayRotationHelper, add **displayRotationHelper** from MLinAR 👎- cant do it and i think it is not important for now
4. Create Analyzer:
   1. added analyzer 👍
   2. format input image 👍
   3. detect image


# Now - add to PlaceAndMeasure

## acquire image - NOT WORKING 👎

```java
private fun onUpdateFrame(frameTime: FrameTime?) {
  // ! --------------------------- get the recent frame
    val frame = arFragment!!.arSceneView.arFrame
    val cameraImage = frame!!.tryAcquireCameraImage()
    if (cameraImage != null) {
      Log.d("my_cam","hi")
      cameraImage.close()
    }
  }
```

or 

```java
    val session = arFragment!!.arSceneView.session
    if (frame == null) {
        return
    }

    val frame = try {
        session!!.update()
    } catch (e: CameraNotAvailableException) {
        Log.e(TAG, "Camera not available during onDrawFrame", e)
        return
    }

    if (cameraImage != null) {
        Log.d("my_cam","hi")
        cameraImage.close()
    }
```

## capture arSceneView

```java
Log.d("my_frame", view!!.width.toString() + " " + view.height.toString())
if (view.width > 0 && view.height > 0) {
    var bitmap = Bitmap.createBitmap(
        view!!.width,
        view!!.height,
        Bitmap.Config.ARGB_8888
    )
    PixelCopy.request(view, bitmap, { copyResult ->
        if (copyResult == PixelCopy.SUCCESS) {
            Log.d("my_frame", "Copying ArFragment view.")
            analyzer.analyze(bitmap, 0)
            Log.d("my_frame", bitmap.toString())

        } else {
            Log.e("my_frame", "Failed to copy ArFragment view.")
        }
    }, callbackHandler)
}
```

then formate it to ImageInput

1. as it is bitmap
2. turns bitmap to bufferBytes

both NOT_WORKing 👎, no detected result

# Now 

1. PlaceAndMeasure, 
   1. added MLKit
   2. capture arSceneView
   3. use handler
   4. formate arSceneView
      - image -> converter didn't work + obj = []
      - view -> obj = []

2. move SceneForm to ARinML - onUpdateFrame doesn't work with await syntax
   1. kill openGL
   2. add SceneForm
   3. figure out away to use await syntax in onUpdateFrame

3. try to remove button
   1. Yes - OpenGL 👎
   2. No - SceneForm, why object not working



