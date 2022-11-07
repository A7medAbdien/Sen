# ObjectGraphic

1. just pose and object detection
2. how the box is rendered
3. try to add some execution logic, log.e
4. get the position of the box/detected object
5. bitmap to ar frame, ar frame to bitmap, i think this might led to the ml in ar

## how the box is rendered

```java
    val rect = RectF(detectedObject.boundingBox)
    val x0 = translateX(rect.left)
    val x1 = translateX(rect.right)
    rect.left = min(x0, x1)
    rect.right = max(x0, x1)
    rect.top = translateY(rect.top)
    rect.bottom = translateY(rect.bottom)
    canvas.drawRect(rect, boxPaints[colorID])
```

### detectedObject.boundingBox is type Rect, 

Rect holds four integer coordinates for a rectangle. The rectangle is represented by the coordinates of its 4 edges (left, top, right bottom). These fields can be accessed directly. Use width() and height() to retrieve the rectangle's width and height. Note: most methods do not check to see that the coordinates are sorted correctly (i.e. left <= right and top <= bottom).

RectF holds four float coordinates for a rectangle. The rectangle is represented by the coordinates of its 4 edges (left, top, right, bottom). These fields can be accessed directly. Use width() and height() to retrieve the rectangle's width and height. Note: most methods do not check to see that the coordinates are sorted correctly (i.e. left <= right and top <= bottom).

Using Rect you define its edges using integers and using RectF they are defined as floats.
[differences](https://stackoverflow.com/questions/4913643/rect-and-rectf-in-android-sdk) 

# GraphicOverlay

## translateX()

Adjusts the x coordinate from the image's coordinate system to the view coordinate system.

```java
    val x0 = translateX(rect.left) // used in ObjectGraphic

    public float translateX(float x) {
      if (overlay.isImageFlipped) {
        return overlay.getWidth() - (scale(x) - overlay.postScaleWidthOffset);
      } else {
        return scale(x) - overlay.postScaleWidthOffset;
      }
    }
```

1. scale(x)
2. what is $overlay$
3. overlay.postScaleWidthOffset
4. overlay.scaleFactor, used in the scale function

## scale(x)

```java
/** Adjusts the supplied value from the image scale to the view scale. */
    public float scale(float imagePixel) {
      return imagePixel * overlay.scaleFactor;
    }
```

# overlay

Contain class Graphic, Draw the graphic on the supplied canvas.

The Canvas class holds the "draw" calls. 
To draw something, you need 4 basic components: '
1. A Bitmap to hold the pixels
2. a Canvas to host the draw calls (writing into the bitmap)
3. a drawing primitive (e.g. Rect, Path, text, Bitmap)
4. a paint (to describe the colors and styles for the drawing).

---
realized that it deals with the camera system as 2D not 3D

remembered a project that render the name of the object using AR, to be checked 4:00