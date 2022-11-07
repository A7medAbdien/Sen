Hello everyone

I would like to know if there is a version of Google Machine Learning in ARCore app that uses stream_mode?

if not i would like to ask how may i do such thing if its possible, and why if its not.

kinde regards,

a7med

if not i would like to ask how may i do such thing if its possible, and why if its not.

kinde regards,

a7med

---

I tried to implement [Google Machine Learning in ARCore](https://github.com/googlesamples/arcore-ml-sample), 
but using SceneForm rather than OpenGL, and on STREAM_MODE rather than on SINGLE_IMAGE_MODE.

I tried two approaches to get the camera scene (view/scene/cameraLiveData - I don't know what to call it):

1. acquiring image
    ```java
    private fun onUpdateFrame(frameTime: FrameTime?) {
        // ! --------------------------- get the recent frame
        val frame = arFragment!!.arSceneView.arFrame
        if (frame == null) {
        return
        }

        val cameraImage = frame!!.tryAcquireCameraImage()
        if (cameraImage != null) {
        Log.d("my_test",cameraImage.toString())
        // -------------------------- pass to the MLKit analyzer
        analyzer.analyze(cameraImage,180)
        cameraImage.close()}
    }
    ```

2. capturing arSceneView
   ```java
    private fun onUpdateFrame(frameTime: FrameTime?) {
        val view: ArSceneView = arFragment!!.arSceneView
        if (view.width > 0 && view.height > 0) {
            var bitmap = Bitmap.createBitmap(
                view!!.width,
                view!!.height,
                Bitmap.Config.ARGB_8888
            )
            PixelCopy.request(view, bitmap, { copyResult ->
                if (copyResult == PixelCopy.SUCCESS) {
                    Log.d("my_frame", "Copying ArFragment view.")
                    // -------------------------- pass to the MLKit analyzer
                    analyzer.analyze(bitmap, 180)
                } else {
                    Log.e("my_frame", "Failed to copy ArFragment view.")
                }
            }, callbackHandler)
        }
    }
   ```

## Analyzers

### The analyzer of the first approach, acquiring image:

```java
fun analyze(image: Image, imageRotation: Int) {
    val inputImage = InputImage.fromMediaImage(image,180)
    detector.process(inputImage).addOnSuccessListener { obj ->
        Log.d("my_obj", obj.size.toString())
        for (result in obj) {
        Log.d(
          "my_re",
          (result.boundingBox.exactCenterX().toInt() to result.boundingBox.exactCenterY()
            .toInt()).toString()
        )
      }
    }.addOnFailureListener { e ->
      Log.e("my_dete",e.toString())
    }
}
```

it shows me an error that,

`com.google.mlkit.common.MlKitException: Internal error has occurred when executing ML Kit tasks`


### The analyzer of the second approach, Capturing arSceneView:

```java
fun analyze(image: Bitmap, imageRotation: Int) {
    val inputImage = InputImage.fromBitmap(image, 0)
    Log.d("my_dete",detector.toString())
    detector.process(inputImage).addOnSuccessListener { obj ->
        // ------------------------ shows a size of 0 all the times
        Log.d("my_obj", obj.size.toString())
        for (result in obj) {
            Log.d(
                "my_re",
                (result.boundingBox.exactCenterX().toInt() to result.boundingBox.exactCenterY()
                    .toInt()).toString()
            )
        }
    }.addOnFailureListener { e ->
        Log.d("my_dete", e.toString())
    }
}
```

nothing got detected, I got this approach form [another source](https://github.com/shibuiwilliam/ai-ar-omocha) than [Google ARCore in Machine Learning sample](https://github.com/googlesamples/arcore-ml-sample)

## Summary

I get an error because of the formatting, in the first approach.

In the [Google Machine Learning in ARCore](https://github.com/googlesamples/arcore-ml-sample) they uses **YuvToRgbConverter** then pass the image as a bitmap, but it need the use of OpenGL, and I can not implement the Streaming mode on it.

In the second approach, capturing arSceneView, I don't know why I am not getting any results


---

# 11 Nov 2022

Morning Dear Dr.Ali,
Please whenever you have a chance for a half hour meeting, to discuss the progress of the project, please let me know.
All my regards,
Ahmed

- How to add onUpdateListener to OpenGL

---

# 12 Nov

When I call it "AR Experience"?

In my case I am trying to build an application that measure the distance between the camera and any detected human body, exactly like [this](https://www.youtube.com/watch?v=wTRt96sCR5E).

I started with android platform, the best match was [Use ARCore as input for Machine Learning models](https://developers.google.com/ar/develop/java/machine-learning), but I have no clue to how to change it to on Stream_mode - BTW I don't mean to mention to my question about this.

After losing hope on android, I found that I can use [MedeaPipe pose detection](https://google.github.io/mediapipe/solutions/pose.html) to detect the human body and by measuring the distance between two poses I can estimate how far the person is. But I know that ARCore uses what called hitTest, which it uses depth api to measure the distance.

Also, there is a [MedeaPipeUnityPlugin](https://github.com/homuler/MediaPipeUnityPlugin). 

So my questions are:

- Does MedeaPipe provide an AR Experience, if it used as I mentioned? and If there is another way to use it, please let me know.
- Do we call it AR Experience, even if we do not have a 3D understanding of the environment? 
