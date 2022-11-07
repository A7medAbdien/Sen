# ML Kit

## path

* EntryChoiceActivity
   * ChooserActivity
      1. LivePreviewActivity
      2. StillImageActivity
      3. CameraXLivePreviewActivity - cameraX is the latest and the easiest
      4. CameraXSourceDemoActivity - the smallest so it is first 😆

1. EntryChoiceActivity: Kotlin or Java. 
2. ChooserActivity: Adapter and Spinner, -> CameraXLivePreviewActivity.
3. CameraXLivePreviewActivity - learn cameraX, binds the camera use cases. 
4. AnalysisUseCase - uses imageProcessor, which is PoseDetectorProcessor in our case. 
5. `imageProcessor!!.processImageProxy(imageProxy, graphicOverlay)` is an abstract method in VisionImageProcessor Interface. 
6. VisionProcessorBase: is an abstract class, implement this interface, the only one.
7. PoseDetectorProcessor: is a child class for VisionProcessorBase, that is how step 5 is done
8. onSuccess() of detection: PoseGraphic is called

# CameraXSourceDemoActivity

1. onCreate() - XML connections and settings listeners
2. onResume() - createThenStartCameraXSource or start cameraXSource
3. onPause() & onDestroy()

## Components

1. C - preview: Custom View that displays the camera feed for CameraX's Preview use case.
2. M - createThenStartCameraXSource(): build the ML model & handel Success or Failure detection
3. M - onDetectionTaskSuccess: 
   1. ImageSourceInfo cases!! : Sets the source information of the image being processed by detectors, including size and whether it is flipped, which informs how to transform image coordinates later.
   2. Draw the detected object info in preview for each result. - `graphicOverlay!!.add(ObjectGraphic(`
   3. Graphic instance for rendering inference info (latency, FPS, resolution) in an overlay view. - `graphicOverlay!!.add(InferenceInfoGraphic(`
4. M - onDetectionTaskFailure: show why

## need to checked

1. graphicOverlay
2. ObjectGraphic
3. InferenceInfoGraphic

---

Realized that CameraXSourceDemoActivity doesn't have pose detection 🚽
---

---

# CameraXLivePreviewActivity

1. EntryChoiceActivity: Kotlin or Java. 

2. ChooserActivity: Adapter and Spinner, -> CameraXLivePreviewActivity.

3. CameraXLivePreviewActivity - learn cameraX, binds the camera use cases. 

4. AnalysisUseCase - uses imageProcessor, which is PoseDetectorProcessor in our case.
   1. Sets Processor Options via `PreferenceUtils`
   2. Create imageProcessor, and it will be instance of `ObjectDetectorProcessor` or `PoseDetectorProcessor`
   3. Build Use case, Prams (Executor, ImageAnalysis.Analyzer)
      1. `imageProcessor.processImageProxy` will use another thread to run the detection underneath,
      2. thus we can just runs the analyzer itself on main thread.

*`imageProcessor!!.processImageProxy(imageProxy, graphicOverlay)` is an abstract method in VisionImageProcessor Interface.*

---

## PreferenceUtils, Processor Options

Sets either multiple detection or not and classification or not 

## imageProcessor is instance of ObjectDetectorProcessor

ObjectDetectorProcessor is a child of **VisionProcessorBase**

onSuccess

```java
for (result in results) {
      graphicOverlay.add(ObjectGraphic(graphicOverlay, result))
      }
```

## VisionProcessorBase, what implementing processImageProxy

implements VisionImageProcessor interface



## imageProcessor.processImageProxy

1. VisionProcessorBase: is an abstract class, implement this interface, the only one.

2. PoseDetectorProcessor: is a child class for VisionProcessorBase, that is how step 5 is done

3. onSuccess() of detection: PoseGraphic is called

# Components

1. PreviewView: Custom View that displays the camera feed for CameraX's Preview use case.
2. graphicOverlay: A view which renders a series of custom graphics to be overlayed on top of an associated preview (i.e., the camera preview).
3. cameraProvider: A singleton which can be used to bind the lifecycle of cameras to any LifecycleOwner within an application's process.
4. previewUseCase: A use case that provides a camera preview stream for displaying on-screen.
5. ImageAnalysis: A use case providing CPU accessible images for an app to perform image analysis on.


