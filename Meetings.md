# 7 Nov


## 1. Basics of ARCore and SceneForm (1-4)

1. ARCore measure
2. EasyLearn

PlaceAndMeasure -- working

---

## 2. Machine Learning (5-7)

1. MLKit + CameraX
2. Machine learning with ARCore

tracking image over the applications -- MLKit application

---

## 3. Integration (7 - now)

1. Can not use CameraX with ARCore
2. Basics of OpenGL, and get back to Machine learning with ARCore

3. Used SceneForm rather than OpenGl
   1. capture image - need a converter
   2. capture arSceneView - no result 
4. Using OpenGL
   1. Unable to add onUpdateListener, since it uses onDrawFrame

## 4. last Hope on Android

I could measure the distance based on scan button

-- [STOPPED](https://github.com/google-ar/arcore-android-sdk/issues/1275)

<br>
<br>

# 4. Alternatives for Android

1. using Unity Foundation - require apple products
2. using Python, MediaPipe -I don't know if it is an AR Experience or not


## 1. Unity Foundation

Full AR Experience, but requires Apple products

## 2. MediaPipe, Python

1. Use openCV
2. Detect Pose, and measure the distance between hip and shoulder, to avoid Z-axis
3. get coordinates of the polynomial
4. Create Kivy applications
5. use WSL to use Buildozer, to convert Kivy to Android


---

## Total Tools used tile now

1. ARcore - intermediate
2. SceneForm - intermediate
3. Android, Kotlin - intermediate
4. MLKit - NOT Text, sound, custom
5. CameraX - two use cases
6. OpenGL - just an Intro, 50 page of a book
7. Kivy - basics
8. Buildozer - just basics
