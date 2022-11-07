# Resources

1. [Recognize text and facial features with ML Kit: Android](https://codelabs.developers.google.com/codelabs/mlkit-android#0)
2. [New features in ML Kit: Text Recognition V2 & Pose Detection](https://www.youtube.com/watch?v=9EKQ0UC04S8)


# ml kit

have a pose detection will be used

# issues

## tf lite pose_estimate

[NDK](https://stackoverflow.com/questions/64372383/ndk-at-library-android-sdk-ndk-bundle-did-not-have-a-source-properties-file/70068917#70068917)

---

# Choices

1. TF, too mush to understand
2. MLkit -> remove extras, the more likely one after watching the videos
3. video -> simple, must to watc

# Problem

get ML Kit to work with ArCore

1. use the face detection app - he does not place the anchor on the detected object, place it in the center
2. uses the Ar in ML, last topic in ARcore documents 
3. use ai-ar app
4. do by myself: readit article
   1. understand the cameraGL
   2. how to change the hit test
   3. bitmap to ?? in the ArCore

## 1. face detection app

### can not load the custom module

1. understand mlkit custom module
2. try different modules
3. ask the app owner

eventually i will need to understand the use of mlkit

# Massage

I am trying to create an application that measures the distance between the camera and any detected person using AR technology...

I have been searching for four weeks and this what I reached for..

I splatted the problem into three parts:

1. application that places an anchor and measures its distance (done and understood)
2. application that detect the human body (done and did not understood)
3. integration of both (just recourse and I cant run it)

## First part: Place and measure

I used ArCore and Sceneform on Android Studio, and I think I understand most of it. I followed [Shibui Yusuke](https://shibuiyusuke.medium.com/measuring-distance-with-arcore-6eb15bf38a8f) and  [Etienne Caron](https://www.youtube.com/watch?v=Ct1asuSts94)

## Second part: Person detection

I used ML Kit pos estimation API. I followed [New features in ML Kit video](https://www.youtube.com/watch?v=9EKQ0UC04S8) and [building use cases playlist](https://www.youtube.com/watch?v=a-HaDCGJw0o&list=PLRX9Q8jR15s1ygC_apa-iTftCqrcdPIkF). I tried to understand [googlesamples/mlkit](https://github.com/googlesamples/mlkit/tree/master/android/vision-quickstart) and [tensorflow/examples](https://github.com/tensorflow/examples/tree/master/lite/examples/pose_estimation/android) but I barely understand, where the define the processor, and I loos it when it comes to the cameraSource file. eventually I created a runnable application.

## Third Part: Integration

Based on what I understood from the last part, that the camera deals with bitmaps, and ArCore I don't know how it deal with camera, my knowledge starts from the hit result. So search for two days here and there, I found [Kashif-E/Ar-Object-Detection](https://github.com/Kashif-E/Ar-Object-Detection), but as mush I try I cant run it on any model.

I tried to understand how to use custom ML Kit models, but no progress. But I will use pose inference so I will try to understand how he deals with the bitmap, then i will try the pose model.

However, I road this post, [Integrating Google ARCore with Google MLKit](https://www.reddit.com/r/Arcore/comments/e1sdiw/integrating_google_arcore_with_google_mlkit/), so I decided to look for help, which is something that I clearly need.

## Summary

I would like to know how to integrate them, to measures the distance between the camera and any detected person. Any guide, advice or anything is appropriated. Also, How do you learn form the documentations? maybe keep this for later.

Those are the applications that I want to combine/integrate:

   1. [Place and Measure](https://github.com/A7medAbdien/PlaceAndMeasure)
   2. [Pose Detection](https://github.com/A7medAbdien/poseDetec)


I reached this in four weeks, It is my first time to deal with applications. I am taking application development course this semester, and it is about ionic!!

Lastly, I could not to find the rules of the community, so sorry if I did violate any.

# Another massage

Trying to use STREAM_MODE, in the arcore-ml-sample


Hello there,

I would like to know if there is a version of arcore-ml-sample that uses stream_mode? if not i would like to ask how may i do such thing if its possible, and why if its not.

kind regards,

a7med

I am trying to do the same application using SceneForm, using the MLKit Stream_mode
