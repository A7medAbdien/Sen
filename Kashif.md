# Ar-Object-Detection

From the [legend](https://github.com/Kashif-E/Ar-Object-Detection), 
it does not deal with the integration of placing the 3D model on the detected object, 
it just places it in the center of the screen,
but it made it too clear that..

1. how to deal with bitmap
2. the main component for rendering in ArCore is the Frame

So we next step is...

1. change the Frame to Bitmap
2. Change the hitResult type to feature point

# Structure

* onUpdate()
  * onUpdateFrame()
    *  **copyPixelFromView()**, with each copy
       *  create instance of $ObjectDetector()$ - Needs more understanding
          *  removeOnUpdateListener() 
          *  **loadModel()**, 3D model
             *  addDrawable()
       *  $useCustomObjectDetector()$

1. copyPixelFromView()
   * Bitmap.createBitmap(hight, width, config)
   * PixelCopy.request(), here we will have a bitmap with camera view/Surface

# New Components

1. [ArFragmentBinding](https://developer.android.com/topic/libraries/view-binding#fragments) 50
2. `setOnViewCreatedListener()` 50
3. `ColorGrading.Builder()` 75
4. SurfaceView
5. Bitmap
6. Surface
7. Handler
8. `removeOnUpdateListener(OnUpdateListener onUpdateListener)` 50
9. Layout inflate
10. requireContext()
11. Context
12. setView
13. arFrame
14. hitTest



## setOnViewCreatedListener()

Invoked when the ARSceneView is created and added to the fragment.
You can use it to configure the ARSceneView and other ArFragment parameters.

## ColorGrading

ColorGrading is used to transform (either to modify or correct) the colors of the HDR buffer rendered by Filament.
Color grading transforms are applied after lighting, and after any lens effects (bloom for instance),
and include tone mapping.

## removeOnUpdateListener()

Removes a listener that will be called once per frame immediately before the Scene is updated.
Params:
onUpdateListener – the update listener to remove

when it will get here it will removeOnUpdate of the ArDisplayFragment!!

It is too important to stop onUpdate function, because this removeOnUpdateListener it will be invoked with each bitmap copied, so there is no need to dedicate any more in this bit map

# copyPixelFromView()

**inputs:** SurfaceView

**callback/return:** Bitmap

1. create bitmap, `Bitmap.createBitmap()`
2. copy pixel to the bitmap, `PixelCopy.request()`

## SurfaceView, inputs of copyPixelFromView()

Provides a dedicated drawing surface embedded inside of a view hierarchy. You can control the format of this surface and, if you like, its size; the SurfaceView takes care of placing the surface at the correct location on the screen

The surface is Z ordered so that it is behind the window holding its SurfaceView; the SurfaceView punches a hole in its window to allow its surface to be displayed. The view hierarchy will take care of correctly compositing with the Surface any siblings of the SurfaceView that would normally appear on top of it. This can be used to place overlays such as buttons on top of the Surface, though note however that it can have an impact on performance since a full alpha-blended composite will be performed each time the Surface changes.

The transparent region that makes the surface visible is based on the layout positions in the view hierarchy. If the post-layout transform properties are used to draw a sibling view on top of the SurfaceView, the view may not be properly composited with the surface.

### Bitmap, callback/result of copyPixelFromView()

1. `Bitmap.createBitmap()`
2. `PixelCopy.request()`

**Bitmap.createBitmap():**

* Returns a mutable bitmap with the specified width and height.
    Its initial density is as per getDensity.
    The newly created bitmap is in the sRGB color space.
* Params:
    * width – The width of the bitmap
    * height – The height of the bitmap
    * config – The bitmap config to create.

## `PixelCopy.request()`

1. PixelCopy (Surface -> Bitmap)
2. Surface
3. .request()

**PixelCopy:** Provides a mechanisms to issue pixel copy requests to allow for copy operations from ***Surface*** to ***Bitmap***

## Surface

Handle onto a raw buffer that is being managed by the screen compositor.

A Surface is generally created by or from a consumer of image buffers (such as a SurfaceTexture, android.media.MediaRecorder, or android.renderscript.Allocation), and is handed to some kind of producer (such as OpenGL, MediaPlayer, or CameraDevice) to draw into.

Note: A Surface acts like a ***weak reference*** to the consumer it is associated with. By itself it will not keep its parent consumer from being reclaimed.

**Weak reference:** it has something to do with the garbage collector.

```cs
Weak reference objects, which do not prevent their referents from being made finalizable, finalized, and then reclaimed. Weak references are most often used to implement canonicalizing mappings.

Suppose that the **garbage collector** determines at a certain point in time that an object is weakly reachable. At that time it will atomically clear all weak references to that object and all weak references to any other weakly-reachable objects from which that object is reachable through a chain of strong and soft references. At the same time it will declare all of the formerly weakly-reachable objects to be finalizable. At the same time or at some later time it will enqueue those newly-cleared weak references that are registered with reference queues.
```

## .request(), from `PixelCopy.request()`

Requests for the display content of a SurfaceView to be copied into a provided Bitmap. The contents of the source will be scaled to fit exactly inside the bitmap. The pixel format of the source buffer will be converted, as part of the copy, to fit the the bitmap's Bitmap.Config. The most recently queued buffer in the SurfaceView's Surface will be used as the source of the copy.

Params:
* source – The source from which to copy
* dest – The destination of the copy. The source will be scaled to match the width, height, and format of this bitmap.
* listener – Callback for when the pixel copy request completes,**what happens on succeed or failure**
* listenerThread – The callback will be invoked on this Handler when the copy is finished. **Who is doing it**

```java
public static void request(@NonNull SurfaceView source, @NonNull Bitmap dest,
            @NonNull OnPixelCopyFinishedListener listener, @NonNull Handler listenerThread) {
        request(source.getHolder().getSurface(), dest, listener, listenerThread);
    }
```

# `loadModels(it.labels[0].text)`, detected object label

1. Layout inflate
2. requireContext()
3. Context
4. setView


## requireContext()

Return the Context this fragment is currently associated with.

## Context

Interface to global information about an application environment. This is an abstract class whose implementation is provided by the Android system. It allows access to application-specific resources and classes, as well as up-calls for application-level operations such as launching activities, broadcasting and receiving intents, etc.

## setView

```java
public Builder setView(Context context, View view) {
      this.view = view;
      this.context = context;
      registryId = view;
      return this;
    }
```


# addDrawable()

1. arFrame
2. frame.hitTest()
3. hitResult = hitTest[0]
4. frame.screenCenter()

## 3.hitResult = hitTest[0]

The list is sorted by distance from the device, with the nearest intersection first. This is important because generally you can’t see objects occluded behind other objects, so most of the time the first result is the most significant.

## 4. frame.screenCenter()

it will take the width and hight of the screen divided by 2 and return it as Vector3 