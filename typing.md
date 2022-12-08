## still 👎
* 4 REQUIREMENT COLLECTION AND ANALYSIS 🥈
    1. Interview
    2. Persona
    3. USU
* 6 SYSTEM IMPLEMENTATION (multi-pose)
    7.	ARCore
    8.	SceneForm
    9.	OpenGL
    10.	OpenCV
    11.	TensorFlow-hub
    12.	Flusk

1.	INTRODUCTION - 7
2.	LITERATURE REVIEW - 6
3.	PROJECT MANAGEMENT 👍
4.	REQUIREMENT COLLECTION AND ANALYSIS 🥈
5.	SYSTEM DESIGN 👍
    1. live stream (Sequence/Use case) diagram 👍
6.	SYSTEM IMPLEMENTATION - 3 👍
    * on in
      1. dev:
         1. CameraX Use Case
         2. ML Kit Object detection, tracking and rendering
         3. Distance measurement
7.	TESTING - 4 👍
8.	CONCLUSION AND FUTURE WORK - 7

8 paraphrase

- alert
- actual distance



requirement collection 2w
sys design 1w
sys implementation
learning tools 6w
applications development 2w
applications testing 1w
writing report 1w
prepare presentation 5d

The project activity plan is a list of the project activities and an approximation of their start and
end date. This gives the team, a clear path to follow and an idea based on approximation on
how much time would each activity requires.

the project activity plan is how to complete a project in a certain timeframe, by dividing it into activities with approximated start and end dates. The project activity plan worked as an indicator for any potential risk, when an activity exceeded its estimated time. It helped in balance between applying the main functionality and enhancing an existing one.

# sys imp

We implemented three versions of the application. The first application differs from the other in the way that it understands the environment and measures the distance, which will be discussed in section 6.4. The difference between the other two applications is that one is a web-based application and the other is an Android application.

In order to solve the problem in the given period of time, we break the main problem into smaller problems:
1. measuring distance between the device camera and any other point in the scene
2. detect human body
3. integrate human body detector and measuring distance.  

This is first version of the application. It is an Android application. We tried to measure the distance between the camera and any point in the scene using ARCore and SceneForm, and this approach measures the distance based on a 3D understanding of the environment. 
We used the ML Kit Pose detection to detect the human body, and it is the same tool that will be used in the other versions of the application.
Integrating the ARCore part with ML Kit part will require an understanding of managing the ARcore inputs, images, and passing them to the ML Kit part. One of the approaches was to use OpenGL. We were able to implement this approach on a single image, but we could not implement it on a stream of images. One of the main characteristics of java language is that it uses a garbage collector so we have to save the image buffer into the heap. It seems that applying the detection on a stream of images is possible but will require more understanding of OpenGL. Learning OpenGL exceeded the estimated time and considering the risks effects and strategies, we adopted another approach.


# sys des
Figure X shows the four main objects used in the Measuring distance application. All four objects depend on user events. The system application is the main logic that controls the application settings, permissions, and what the user sees. The camera object is responsible for accessing and communicating with the device camera. The detector object is responsible for detecting extracted features and getting their orientation in the image. The renderer object is responsible for calculating the distance between the detected object and the camera device and draw lines on detected person.

Step 1: The user touches the screen to start the application.
Step 2: The application system opens the device camera.
Step 3: The camera gets live streaming data and processes them to match the detector input.
Step 4: If there is a detected object, the renderer gets created and receives the orientations of the detected object. 
Step 5: The renderer calculates the distance between the detected object and the camera device.
Step 6: In case the calculated distance (in step 5) is less than the safe distance, the renderer will send an alert to the application system.
Step 7: the application system shows the user an alert message.
Step 8: If there is a detected object, The renderer draws lines on the detected person.

Start Camera: The user start the camera device and it measure the distance between the detected object and the camera. The user can show lines between person poses and current distance between the detected person on this person. The user can show the detection information, like frame rate, image size and detection latency. If the current distance between the detected person and the camera is less than the safe distance, an alert will be shown.
Change Settings: The user can hide detection information, lines between person poses and the current distance that rendered on the detected person. The user can use the device GPU rather then CPU.

# sys imp

1. CameraX Use Case

First we create a CameraX instance, `cameraProvider`, and bind it to the lifecycle of the application. The `cameraProvider` will be used to bind the CameraX use cases to the application lifecycle. We uses ViewModelProvider that provides ViewModels. ViewModel is a class that is responsible for preparing and managing the data for an Activity. Then, we use CameraXViewModel class which is responsible for interacting with CameraX tp access processCameraProvider. The processCameraProvider is a method of the CameraXViewModel class that returns the LiveData that can be observed over the application lifecycle.

```java
class CameraXLivePreviewActivity :
  AppCompatActivity(), CompoundButton.OnCheckedChangeListener {
    private var cameraProvider: ProcessCameraProvider? = null
    override fun onCreate(savedInstanceState: Bundle?) {
        ViewModelProvider(this, ViewModelProvider.AndroidViewModelFactory.getInstance(application))
            .get(CameraXViewModel::class.java)
            .processCameraProvider
            .observe(
                this,
                Observer { provider: ProcessCameraProvider? ->
                cameraProvider = provider
                bindAllCameraUseCases()
                }
            )
    }

    private fun bindAllCameraUseCases() {
        if (cameraProvider != null) {
            // As required by CameraX API, unbinds all use cases before trying to re-bind any of them.
            cameraProvider!!.unbindAll()
            bindPreviewUseCase()
            bindAnalysisUseCase()
        }
    }
}
```

We create the builder that will be used to create the preview use case. We can attach some settings to the builder like the image resolution, that can be changed by the user. The previewUseCase is of type Preview which is a camera preview stream for displaying on-screen. Then we attach to the use case a Surface Provider. We are using previewView which is a custom View that displays the camera feed for CameraX's Preview use case. The previewView class manages the Surface lifecycle, as well as the preview aspect ratio and orientation. Internally, it uses either a TextureView or SurfaceView, classes in Open Graphics Library (OpenGL) renderer, to display the camera feed. Finally, we bind the use case to CameraX instance, cameraProvider.

```java
private var previewView: PreviewView? = null
private var previewUseCase: Preview? = null
private var cameraSelector: CameraSelector? = null
private fun bindPreviewUseCase() {
    val builder = Preview.Builder()
    // attach the settings of images resolution
    previewUseCase = builder.build()
    previewUseCase!!.setSurfaceProvider(previewView!!.getSurfaceProvider())
    cameraProvider!!.bindToLifecycle(/* lifecycleOwner= */ this,
      cameraSelector!! /* determines the camera lens to use.*/,
      previewUseCase)
}
```

First we create the analyzing use case and setts the executer, main thread, and its analyzer. The analyzer is where the pose detection, tracking and rendering happens using ML Kit. The analyzer provides the input for the image processor as imageProxy. Image Proxy is a single image buffer.

```java
private var analysisUseCase: ImageAnalysis? = null
private var imageProcessor: VisionImageProcessor? = null
private fun bindAnalysisUseCase() {
    imageProcessor = PoseDetectorProcessor()
    val builder = ImageAnalysis.Builder()
    analysisUseCase = builder.build()
    analysisUseCase?.setAnalyzer(
      ContextCompat.getMainExecutor(this),
      ImageAnalysis.Analyzer {imageProxy: ImageProxy ->
        imageProcessor!!.processImageProxy(imageProxy, graphicOverlay)
      }
    )
    cameraProvider!!.bindToLifecycle(/* lifecycleOwner= */ this, cameraSelector!!, analysisUseCase)
}
```

2. OpenCV

We create an instance of OpenCV with a specified camera lens that allows accessing the current frame and processing it. After processing the frame, we save it as a buffer and yield it to the front end. we use yield since we return a value continuously while executing the function and this value is only needed once.

```py

def generate_frames():
    while camera.isOpened():

        # read the camera frame
        success, frame = camera.read()

        # When the camera unable to read the frame
        if not success:
            break
            
        # process frame and render the landmarks
        
        # Render the frame
        buffer = cv2.imencode('.jpg', frame)[1]
        frame = buffer.tobytes()

        yield (b'--frame\r\n'
               b'Content-Type: image/jpeg\r\n\r\n' + frame + b'\r\n')

``` 

## ML Kit Object detection, tracking and rendering

We set the options of the poseDetectorProcessor in the CameraXLivePreviewActivity, then pass them to PoseDetectorProcessor. PoseDetectorProcessor is an instance of VisionProcessorBase that extracts the result of the detected poses and classifies them. The pose detection and tracking process are done in PoseDetectorProcessor and its patent VisionProcessorBase. The result is of a type Pose and it is passed to the PoseGraph file. The Pose class has a method called getAllLandmark that returns a list of all detected landmarks. The getAllLandmark method will be used to draw some landmarks between the poses that are used to measure the distance. All those tasks are done asynchronously and their results are handled using listeners.

```java
class CameraXLivePreviewActivity :
  AppCompatActivity(), CompoundButton.OnCheckedChangeListener {
    // to give the user the choice of using the CPU or GPU
    val poseDetectorOptions =
        PreferenceUtils.getPoseDetectorOptionsForLivePreview(this)
    val shouldShowInFrameLikelihood =
        PreferenceUtils.shouldShowPoseDetectionInFrameLikelihoodLivePreview(this)
    val visualizeZ = PreferenceUtils.shouldPoseDetectionVisualizeZ(this)
    val rescaleZ =
        PreferenceUtils.shouldPoseDetectionRescaleZForVisualization(this)
    val runClassification =
        PreferenceUtils.shouldPoseDetectionRunClassification(this)
    imageProcessor = PoseDetectorProcessor(
            this,
            poseDetectorOptions,
            shouldShowInFrameLikelihood,
            visualizeZ,
            rescaleZ,
            runClassification,
            /* isStreamMode = */ true
            )
  }
```

```java
class PoseDetectorProcessor(
    private val context: Context,
    options: PoseDetectorOptionsBase,
    private val showDistance: Boolean,
    private val visualizeZ: Boolean,
    private val rescaleZForVisualization: Boolean,
    private val runClassification: Boolean,
    private val isStreamMode: Boolean,
) : VisionProcessorBase<PoseDetectorProcessor.PoseWithClassification>(context) {

    private val detector: PoseDetector
    private val classificationExecutor: Executor

    private var poseClassifierProcessor: PoseClassifierProcessor? = null

    /** Internal class to hold Pose and classification results. */
    class PoseWithClassification(val pose: Pose, val classificationResult: List<String>)

    init {
        detector = PoseDetection.getClient(options)
        classificationExecutor = Executors.newSingleThreadExecutor()
    }

    override fun stop() {
        super.stop()
        detector.close()
    }

    /*
    * continueWith():
    * @param  Executor
    * @param  Continuation: A function that is called to continue execution after completion of a Task.
    * */
    override fun detectInImage(image: MlImage): Task<PoseWithClassification> {
        return detector
        .process(image)
        .continueWith(
            classificationExecutor
        ) { task ->
            val pose = task.getResult()
            var classificationResult: List<String> = ArrayList()
            if (runClassification) {
            if (poseClassifierProcessor == null) {
                poseClassifierProcessor = PoseClassifierProcessor(context, isStreamMode)
            }
            classificationResult = poseClassifierProcessor!!.getPoseResult(pose)
            }
            PoseWithClassification(pose, classificationResult)
        }
    }

    override fun onSuccess(
        results: PoseWithClassification,
        graphicOverlay: GraphicOverlay,
    ) {
        graphicOverlay.add(
        PoseGraphic(
            graphicOverlay,
            results.pose,
            showDistance,
            visualizeZ,
            rescaleZForVisualization,
            results.classificationResult
        )
        )
    //    Log.i("test", (results.pose.getPoseLandmark(0)?.position3D).toString());
    }

    override fun onFailure(e: Exception) {
        Log.e(TAG, "Pose detection failed!", e)
    }

    override fun isMlImageEnabled(context: Context?): Boolean {
        // Use MlImage in Pose Detection by default, change it to OFF to switch to InputImage.
        return true
    }

    companion object {
        private val TAG = "PoseDetectorProcessor"
    }
    }

```

```java
abstract class VisionProcessorBase<T>(context: Context) : VisionImageProcessor {

  companion object {
    const val MANUAL_TESTING_LOG = "LogTagForTest"
    private const val TAG = "VisionProcessorBase"
  }

  private var activityManager: ActivityManager =
    context.getSystemService(Context.ACTIVITY_SERVICE) as ActivityManager
  private val fpsTimer = Timer()
  private val executor = ScopedExecutor(TaskExecutors.MAIN_THREAD)

  // Whether this processor is already shut down
  private var isShutdown = false

  // Used to calculate latency, running in the same thread, no sync needed.
  private var numRuns = 0
  private var totalFrameMs = 0L
  private var maxFrameMs = 0L
  private var minFrameMs = Long.MAX_VALUE
  private var totalDetectorMs = 0L
  private var maxDetectorMs = 0L
  private var minDetectorMs = Long.MAX_VALUE

  // Frame count that have been processed so far in an one second interval to calculate FPS.
  private var frameProcessedInOneSecondInterval = 0
  private var framesPerSecond = 0

  init {
    fpsTimer.scheduleAtFixedRate(
      object : TimerTask() {
        override fun run() {
          framesPerSecond = frameProcessedInOneSecondInterval
          frameProcessedInOneSecondInterval = 0
        }
      },
      0,
      1000
    )
  }


  // ! -----------------Code for processing live preview frame from CameraX API-----------------------
  @RequiresApi(VERSION_CODES.LOLLIPOP)
  @ExperimentalGetImage
  override fun processImageProxy(image: ImageProxy, graphicOverlay: GraphicOverlay) {
    val frameStartMs = SystemClock.elapsedRealtime()
    if (isShutdown) {
      return
    }

    val  bitmap = BitmapUtils.getBitmap(image)

    if (isMlImageEnabled(graphicOverlay.context)) {
      val mlImage =
        MediaMlImageBuilder(image.image!!).setRotation(image.imageInfo.rotationDegrees).build()
      requestDetectInImage(
        mlImage,
        graphicOverlay,
        /* originalCameraImage= */ bitmap,
        /* shouldShowFps= */ true,
        frameStartMs
      )
        // When the image is from CameraX analysis use case, must call image.close() on received
        // images when finished using them. Otherwise, new images may not be received or the camera
        // may stall.
        // Currently MlImage doesn't support ImageProxy directly, so we still need to call
        // ImageProxy.close() here.
        .addOnCompleteListener { image.close() }

      return
    }

  }

  // ! --------------------------------------- InputImage.fromMediaImage passed here ------------
  private fun requestDetectInImage(
    image: MlImage,
    graphicOverlay: GraphicOverlay,
    originalCameraImage: Bitmap?,
    shouldShowFps: Boolean,
    frameStartMs: Long
  ): Task<T> {
    return setUpListener(
      detectInImage(image),
      graphicOverlay,
      originalCameraImage,
      shouldShowFps,
      frameStartMs
    )
  }

  // ! Detection Listener
  private fun setUpListener(
    task: Task<T>,
    graphicOverlay: GraphicOverlay,
    originalCameraImage: Bitmap?,
    shouldShowFps: Boolean,
    frameStartMs: Long
  ): Task<T> {
    val detectorStartMs = SystemClock.elapsedRealtime()
    return task
      .addOnSuccessListener(
        executor,
        OnSuccessListener { results: T ->
          val endMs = SystemClock.elapsedRealtime()
          val currentFrameLatencyMs = endMs - frameStartMs
          val currentDetectorLatencyMs = endMs - detectorStartMs
          if (numRuns >= 500) {
            resetLatencyStats()
          }
          numRuns++
          frameProcessedInOneSecondInterval++
          totalFrameMs += currentFrameLatencyMs
          maxFrameMs = max(currentFrameLatencyMs, maxFrameMs)
          minFrameMs = min(currentFrameLatencyMs, minFrameMs)
          totalDetectorMs += currentDetectorLatencyMs
          maxDetectorMs = max(currentDetectorLatencyMs, maxDetectorMs)
          minDetectorMs = min(currentDetectorLatencyMs, minDetectorMs)

          // Only log inference info once per second. When frameProcessedInOneSecondInterval is
          // equal to 1, it means this is the first frame processed during the current second.
          if (frameProcessedInOneSecondInterval == 1) {
            Log.d(TAG, "Num of Runs: $numRuns")
            Log.d(
              TAG,
              "Frame latency: max=" +
                maxFrameMs +
                ", min=" +
                minFrameMs +
                ", avg=" +
                totalFrameMs / numRuns
            )
            Log.d(
              TAG,
              "Detector latency: max=" +
                maxDetectorMs +
                ", min=" +
                minDetectorMs +
                ", avg=" +
                totalDetectorMs / numRuns
            )
            val mi = ActivityManager.MemoryInfo()
            activityManager.getMemoryInfo(mi)
            val availableMegs: Long = mi.availMem / 0x100000L
            Log.d(TAG, "Memory available in system: $availableMegs MB")
          }
          graphicOverlay.clear()
          // this draws the whole scene
          if (originalCameraImage != null) {
            graphicOverlay.add(CameraImageGraphic(graphicOverlay, originalCameraImage))
          }

          // ! ---------------------------------- Rendering the result ----------------------------
          this@VisionProcessorBase.onSuccess(results,graphicOverlay)

          // this render the FPS and those stuff
          if (!PreferenceUtils.shouldHideDetectionInfo(graphicOverlay.context)) {
            graphicOverlay.add(
              InferenceInfoGraphic(
                graphicOverlay,
                currentFrameLatencyMs,
                currentDetectorLatencyMs,
                if (shouldShowFps) framesPerSecond else null
              )
            )
          }

          graphicOverlay.postInvalidate()
        }
      )
      .addOnFailureListener(
        executor,
        OnFailureListener { e: Exception ->
          graphicOverlay.clear()
          graphicOverlay.postInvalidate()
          val error = "Failed to process. Error: " + e.localizedMessage
          Toast.makeText(
              graphicOverlay.context,
              """
          $error
          Cause: ${e.cause}
          """.trimIndent(),
              Toast.LENGTH_SHORT
            )
            .show()
          Log.d(TAG, error)
          e.printStackTrace()
          this@VisionProcessorBase.onFailure(e)
        }
      )
  }

  override fun stop() {
    executor.shutdown()
    isShutdown = true
    resetLatencyStats()
    fpsTimer.cancel()
  }

  private fun resetLatencyStats() {
    numRuns = 0
    totalFrameMs = 0
    maxFrameMs = 0
    minFrameMs = Long.MAX_VALUE
    totalDetectorMs = 0
    maxDetectorMs = 0
    minDetectorMs = Long.MAX_VALUE
  }

  protected abstract fun detectInImage(image: InputImage): Task<T>

  protected open fun detectInImage(image: MlImage): Task<T> {
    return Tasks.forException(
      MlKitException(
        "MlImage is currently not demonstrated for this feature",
        MlKitException.INVALID_ARGUMENT
      )
    )
  }

  protected abstract fun onSuccess(results: T, graphicOverlay: GraphicOverlay)

  protected abstract fun onFailure(e: Exception)

  protected open fun isMlImageEnabled(context: Context?): Boolean {
    return false
  }
}
```

```java
class PoseGraphic
internal constructor(
  overlay: GraphicOverlay,
  private val pose: Pose,
  private val showDistance: Boolean,
  private val visualizeZ: Boolean,
  private val rescaleZForVisualization: Boolean,
  private val poseClassification: List<String>,
) : Graphic(overlay) {

    private var zMin = java.lang.Float.MAX_VALUE
    private var zMax = java.lang.Float.MIN_VALUE
    private val classificationTextPaint: Paint
    private val leftPaint: Paint
    private val rightPaint: Paint
    private val whitePaint: Paint
    fun draw(canvas: Canvas) {

        val landmarks = pose.allPoseLandmarks
        if (landmarks.isEmpty()) {
        return
        }

        val nose = pose.getPoseLandmark(PoseLandmark.NOSE)
        val rightShoulder = pose.getPoseLandmark(PoseLandmark.RIGHT_SHOULDER)
        val leftShoulder = pose.getPoseLandmark(PoseLandmark.LEFT_SHOULDER)
        val rightHip = pose.getPoseLandmark(PoseLandmark.RIGHT_HIP)
        val leftHip = pose.getPoseLandmark(PoseLandmark.LEFT_HIP)

        val landmarksSub: List<PoseLandmark?> =
        listOf(nose, rightShoulder, leftShoulder, rightHip, leftHip)


        // Draw all the points
        for (landmark in landmarksSub) {
        drawPoint(canvas, landmark!!, whitePaint)
        if (visualizeZ && rescaleZForVisualization) {
            zMin = min(zMin, landmark.position3D.z)
            zMax = max(zMax, landmark.position3D.z)
        }
        // Log.i("test", (landmark.position3D).toString());
        }

        drawLine(canvas, leftShoulder, rightShoulder, whitePaint)
        drawLine(canvas, leftHip, rightHip, whitePaint)

        // Left body
        drawLine(canvas, leftShoulder, leftHip, leftPaint)
        val leftSide = calculateDistance(leftShoulder!!, leftHip!!)

        // Right body
        drawLine(canvas, rightShoulder, rightHip, rightPaint)
        val rightSide = calculateDistance(rightShoulder!!, rightHip!!)

        val avgDistance = (rightSide + leftSide) / 2


        // ! ------------------------------------- Draw on nose -------------------------------------
        if (showDistance) {
        canvas.drawText(
            avgDistance.toString(),
            translateX(nose!!.position.x),
            translateY(nose.position.y),
            whitePaint
        )
        Log.d("Distance: ", avgDistance.toString())
        }
    }
}
```

We load the Movnet model using TensorFlow-hub API. Movenet Multi-pose Lightning model is a convolutional neural network model that predicts the human pose locations of people in the image. The model was designed to be run in the browse (Tfhub.dev, 2022). Then we process the image to fit the Movnet model input specification using TensorFlow API. After passing the frame to the Movnet detector we get the orientation of the detected poses for each person in the frame. We use those orientations to draw and render points and landmarks on the detected person using OpenCV API.

```py
def process_image(frame):
    # Resize image
    img = frame.copy()
    img = tf.image.resize_with_pad(tf.expand_dims(img, axis=0), 384, 640)
    input_img = tf.cast(img, dtype=tf.int32)

    results = movenet(input_img)
    keypoints_with_scores = results['output_0'].numpy()[
        :, :, :51].reshape((6, 17, 3))

    # Render keypoints and landmarks
    loop_through_people(frame, keypoints_with_scores, EDGES, 0.1)
    
def loop_through_people(frame, keypoints_with_scores, edges, confidence_threshold):
    for person in keypoints_with_scores:
        draw_connections(frame, person, edges, confidence_threshold)
        draw_keypoints(frame, person, confidence_threshold)

# Draw Keypoints


def draw_keypoints(frame, keypoints, confidence_threshold):
    y, x, c = frame.shape
    shaped = np.squeeze(np.multiply(keypoints, [y, x, 1]))

    for kp in shaped:
        ky, kx, kp_conf = kp
        if kp_conf > confidence_threshold:
            cv2.circle(frame, (int(kx), int(ky)), 6, (0, 255, 0), -1)


def draw_connections(frame, keypoints, edges, confidence_threshold):
    y, x, c = frame.shape
    shaped = np.squeeze(np.multiply(keypoints, [y, x, 1]))

    for edge, color in edges.items():
        p1, p2 = edge
        y1, x1, c1 = shaped[p1]
        y2, x2, c2 = shaped[p2]

        if (c1 > confidence_threshold) & (c2 > confidence_threshold):
            cv2.line(frame, (int(x1), int(y1)),
                     (int(x2), int(y2)), (0, 0, 255), 4)
            # Measure the distance
```

## Distance measurement

In order to calculate the distance between the camera and detected person, we calculate the distance between the shoulders and hips. When the user will be closer to the camera this distance will be bigger and as the user gets farther the distance between the shoulder and hips will get smaller. The image shows how the distance between two point on an object changes compare to its position in the real world.

The railroad ties appear to get smaller as they get further away from us.If we measured the apparent size of each railroad tie, their measured size would decrease in proportion to their distance from our eyes. The same thing happens to the distance between shoulders and hips. 
In order to calculate the distance between the camera and detected person, we calculate the distance between the shoulders and hips. We could use the pupillary distance (PD), the distance between the centers of your pupils, since it is fixed as the detected person moves, but it will be affected if the detected person did not face the camera. We used the distance between the shoulders and hips, considering if the detected person rotated.
We get the average of the distance right side and left one, then using a polynomial equation we get how far the detected person is from the camera. The coefficients of the polynomial equation calculate by measuring the size of landmarks, distance between shoulders and hips, to how far a person form the device camera.

# Testing

## Unit testing

1. the application asks for permissions
2. the user can start the camera
3. the user can see a stream of camera images
4. the application can detect pose body
5. the application can render landmarks
6. the application can show the alert massage
7. the application can measure how far the detected person is from the camera
8. the user can access settings
9. edit the safe distance
10. the user can render the distance on detected person
11. the user can stop rendering landmarks
12. the user can use GPU instead of CPU

## 

asks for permissions
start the camera
stream camera images
detect a person
render landmarks
render the current distance on detected person
show the alert massage
access settings
edit the safe distance
render the distance on detected person
stop rendering landmarks
use GPU instead of CPU
close the camera


The application asks for camera and internet permissions, it will shows a pop-up window to get the user approval.
when the user click on start the application camera, the application will take the user to the CameraX activity.
The CameraX activity will show a stream of images of the current scene.
the application shows a massage when a person got detected
the application renders landmarks between shoulders and hips on the detected person
the application renders the current distance between the detected person and the camera
the application shows an alert massage when the person violate the safe distance
when the user clicks on settings icon, the application will take the user to the settings activity
the user should be able to change the safe distance
the user should be able to control showing landmarks
the user should be able to control showing the current distance on detected person
the user should be able to control using the device GPU instead of CPU.
when the user clicks on close the camera, the application will take the user back to the entry activity


# Project Management

The project was proposed and given a total of 6 months to be implemented. As such, a Spiral Software Development Life Cycle model was chosen to be followed throughout the project’s development. Each cycle of the Spiral Model is divided into four phases: Planning, Design, construction and evaluation. The reason for adopting the spiral model is that the tools for implementing this project were unclear and the spiral approach, is a risk-driven process model (Boehm and Hansen, 2000). 

We implemented the project over five cycles if the spiral model. The first cycle covered the low fidelity prototype and the second cycle covered the high fidelity prototype, both low and high fidelity prototypes will be discussed in chapter 4. the third cycle covered an application that detect on single image, this application will be discussed in the next section. The last cycle covered an application that detection on image stream.


1. Not on schedule
2. Unmet requirements
3. Data loss
4. Underestimating the time required to learn the needed tools
5. Tools are not integrable
6. Tools are no longer supported


broken down into small iterative increments called sprints. During each sprint, the team comes together to collaborate, implement a part of the solution, then introduce new functionalities to the system or change them. 
