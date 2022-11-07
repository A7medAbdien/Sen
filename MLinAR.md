# ML in Ar

# Structure of $MainActivity.kt$

we have a one provider, $MainActivity$, and many observers. [More about Lifecycle-Aware Components](https://developer.android.com/topic/libraries/architecture/lifecycle#lco)

1. add Session as an observer to the main activity, using ARCoreSessionLifecycleHelper
   * CameraPermissionHelper, used inside the helper but i didn't want into the details
2. add AppRender as an observer to the main activity??

## Understood

1. ARCoreSessionLifecycleHelper
   * *CameraPermissionHelper*
2. AppRender
   * MainActivityView
   * DisplayRotationHelper
   * BackgroundRenderer
   * PointCloudRender
   * LabelRender
   * MLKitObjectDetector
   * GoogleCloudVisionDetector
   * ObjectDetector 

# 1. ARCoreSessionLifecycleHelper

## Outline

1. inheritance, `DefaultLifecycleObserver`
2. handel installation of ARCore, `tryCreateSession()` - private method
3. onResume()
4. onPause(), `sessionCache?.pause()`, function that Pause the current session.
5. onDestroy(), `sessionCache?.close(); sessionCache = null`, function that Explicitly close ARCore Session to release native resources.
6. handel if the camera permission denied, `onRequestPermissionsResult()` - used for permission

## what it does

Manages an ARCore Session using the Android Lifecycle API.
Before starting a Session, this class requests an install of ARCore, if necessary,
and asks the user for permissions, if necessary.

**Android Lifecycle:** Lifecycle-aware components perform actions in response to a change in the lifecycle status of another component, such as activities and fragments. These components help you produce better-organized, and often lighter-weight code, that is easier to maintain.

**Activity Lifecycle:** [link](https://developer.android.com/guide/components/activities/activity-lifecycle)

All Those function took the Activity/Observable as a parameter

* onCreate	called when activity is first created.
* onStart	called when activity is becoming visible to the user.
* onResume	called when activity will start interacting with the user.
* onPause	called when activity is not visible to the user.
* onStop	called when activity is no longer visible to the user.
* onRestart	called after your activity is stopped, prior to start.
* onDestroy	called before the activity is destroyed.

![activity-lifecycle](imgs/activity_lifecycle.png)

## inputs

1. Activity
2. Set of Session.features

## inherited from $DefaultLifecycleObserver$

DefaultLifecycleObserver: 
Callback **interface** for listening to LifecycleOwner state changes.
If a class implements both this interface and LifecycleEventObserver, then methods of DefaultLifecycleObserver will be called first, and then followed by the call of LifecycleEventObserver.onStateChanged(LifecycleOwner, Lifecycle.Event)
If a class implements this interface and in the same time uses OnLifecycleEvent, then annotations will be ignored.

$So:$ Now the ARCoreSessionLifecycleHelper/or the activity will be passed to it will be an **observed**, have the status of lifecycle

## $onResume()$

After creating a session, but before Session.resume is called is the perfect time to setup a session.

So we call 
```java
    beforeSessionResume?.invoke(session)
    session.resume()
    sessionCache = session
```
Now we have 
1. the current session in `sessionCache`
2. function that can access the session `beforeSessionResume`

---
---

# onCreate()

## Session

Manages AR system state and handles the session lifecycle. This class is the main entry point to the ARCore API. This class allows the user to create a session, configure it, start or stop it and, most importantly, receive frames that allow access to camera image and device pose. [more](https://developers.google.com/ar/reference/java/com/google/ar/core/Session)

## configure(Config config)
Configures the session and verifies that the enabled features in the specified session config are supported with the currently set camera config.

### Config
Holds settings that are used to configure the session. To apply a configuration, use Session.configure(Config).

---

```java
val filter = CameraConfigFilter(session)
    .setFacingDirection(CameraConfig.FacingDirection.BACK)
// Gets the list of supported camera configs that satisfy the provided filter settings.
val configs = session.getSupportedCameraConfigs(filter)
// Sorting
val sort = compareByDescending<CameraConfig> { it.imageSize.width }
    .thenByDescending { it.imageSize.height }
session.cameraConfig = configs.sortedWith(sort)[0]
```

## CameraConfigFilter
Allows the application to select the camera config filters it needs to enable/disable so that it can obtain the list of camera configs that are supported on the device camera.

### setFacingDirection
Sets camera facing direction filter.
The default value is CameraConfig.FacingDirection.BACK.

## Sorting CameraConfig!!

### compareByDescending<CameraConfig>

Creates a descending comparator using the function to transform value to a Comparable instance for comparison.

Sample:

```java
val list = listOf("aa", "b", "bb", "a")

val sorted = list.sortedWith(compareByDescending { it.length })

println(sorted) // [aa, bb, b, a]
```

### thenByDescending
Creates a descending comparator using the primary comparator and the function to transform value to a Comparable instance for comparison.

Sample:

```java
val list = listOf("aa", "b", "bb", "a")

val lengthComparator = compareBy<String> { it.length }
println(list.sortedWith(lengthComparator)) // [b, a, aa, bb]

val lengthThenStringDesc = lengthComparator.thenByDescending { it }
println(list.sortedWith(lengthThenStringDesc)) // [b, a, bb, aa]
```

---

## `lifecycle.addObserver(arCoreSessionHelper)`

A class can monitor the component's lifecycle status by implementing DefaultLifecycleObserver and overriding corresponding methods such as onCreate, onStart, etc. Then you can add an observer by calling the addObserver() method of the Lifecycle class and passing an instance of your observer. [More about Lifecycle-Aware Components](https://developer.android.com/topic/libraries/architecture/lifecycle#lco)

### `lifecycle` equivalent to `getLifecycle()`

Returns the Lifecycle of the provider.

### `addObserver()`

Adds a LifecycleObserver that will be notified when the LifecycleOwner changes state.

The given observer will be brought to the current state of the LifecycleOwner. For example, if the LifecycleOwner is in Lifecycle.State.STARTED state, the given observer will receive Lifecycle.Event.ON_CREATE, Lifecycle.Event.ON_START events.

Params: 
observer – The observer to notify.



















# AppRender

1. lifecycle-Aware functions
2. bindView - binds the view with the corresponding xml elements

## 1. lifecycle-Aware functions

onResume(): call the displayRotationHelper.onResume() to set the displayManager.registerDisplayListener()

onPause(): to unregister this listener


# IDK

1. lateinit
2. `var exceptionCallback: ((Exception) -> Unit)? = null`, [link](https://stackoverflow.com/questions/42646016/what-does-the-arrow-operator-do-in-kotlin)
3. .invoke(), `exceptionCallback?.invoke(e)`, [link](https://stackoverflow.com/questions/45173677/invoke-operator-operator-overloading-in-kotlin)
4. An enum is a special "class" that represents a group of constants (unchangeable variables, like final variables).
