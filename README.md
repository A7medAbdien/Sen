# All, Nov 12 2022

1. ARCore Measure (1 -> 3)
2. EasyLearn (3)
3. TFlite (4)
4. MLKit, using CameraX (4)
5. Kashif app (5)
6. omocha app (5)
7. ARcore in MLkit - NOT a STREAM_MODE, using OpenGL (6)
8. [MediaPipe](https://google.github.io/mediapipe/solutions/objectron), last option - IT IS NOT A OPTION

## solutions

1. ARCore Measure + EasyLearn = PlaceAndMeasure (3-4) 👍
2. ARCore in MLKit -> PlaceAndMeasure + omocha = 
   1. Capturing the image, Cant convert image form Yuv to Bitmap (7)
   2. Capturing the view, I get obj = []

# ml in ar summery

`renderer.onDrawFrame(SampleRender.this);`

* the function that I need is `draw()` in LabelRender.kt,
* used in AppRender.kt in `onDrawFrame()`, it gets its data from `arLabelAnchors`
* which is a mutable list of **ARLabelAnchor**, anchor and its label
* that comes form `anchors`
* which is a list of **ARLabelAnchor** created by mapNotNull over `objectResults`
* objectResults is a list of **DetectedObjectResult**, data class.
* objectResults is the result of `currentAnalyzer.analyze(cameraImage, imageRotation)`.
* the analyzer is an abstract method in **ObjectDetector**.kt
* the ObjectDetector.kt have also the method that Converts a YUV image to a Bitmap using YuvToRgbConverter.
* YuvToRgbConverter: is a Helper class used to efficiently convert a Media.Image object from ImageFormat.YUV_420_888 format to an RGB Bitmap object.
* back to out analyzer, it functionality will be determined based on the **`currentAnalyzer`**, which it gets determined in bindView, either MLKit or Google Cloud Vision

# MLKit CameraX

I draw it on appear, NOW I do understand the MLKit, without the graphicOverlay

## What I can DO, Back to AR in ML

1. How to create onUpdateListener as 
   1. in MLKit Task.onSuccess(), 👎 I don't have a live data, image, provider so It doesn't seems it will work 
   2. in the SceneForm 
2. Edit MLinAR to ignore OpenGL, then add SceneForm 
3. Add SceneForm on OpenGL 
4. Move all of this to a SceneForm


# Integrating MLKit CameraX with MLinAR

$Goals:$ 
1. Activate the Stream Mode in ML
2. Add Listener
3. Add SceneForm


## Activate Stream

1. change the form of injected image, ML worked AR didn't run it
2. fit the output
   1. added the trackingId to the AR

## realized that

1. I do not know if acquiring an image each time is the right way to do it
   * so i will check the display rotation to do the detection at the same time
   * or i will try to add SceneForm

Continued in [adding "MLKit in ARCore" with SceneForm](addMLtoSF.md)

## AugmentedFace ARCore feature

can not be used for my case since it [is not supported](https://github.com/google-ar/arcore-unity-sdk/issues/506) for the front camera

# all

## ml in ar

1. scan button -> onUpdate listener
2. unable to track the object - ml
3. that leads to fixed anchors - ar

1. places the anchor based on image data

## mlkit

1. I don't know its lifecycle
2. does not place an anchor at all
3. have pose detection and object detection

1. track the object


## face detection and ar, Kashif

1. places the anchor in the middle of the screen
2. I could not run it
3. more likely it does not track the object

1. easy

I need to understand what are the outputs of ml model

---

# 12 Nov

# iOS solution 

## [requirements](https://www.youtube.com/watch?v=cfKzUYH4i7A):

- Unity 2019.2
- XCode 11 beta 7
- iOS 13.1

## [Setup](https://www.youtube.com/watch?v=iRxDKCc6Z64&list=PLQMQNmwN3FvzCWfvCvq2AYh1CFnTlv2Es)

