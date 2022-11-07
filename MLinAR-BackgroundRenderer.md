# AppRender

1. MainActivityView
2. DisplayRotationHelper
3. **BackgroundRenderer**
4. PointCloudRender
5. LabelRender
6. MLKitObjectDetector
7. GoogleCloudVisionDetector
8. ObjectDetector

<br>
<br>

# BackgroundRenderer

This class both renders the AR camera background and composes the a scene foreground. The camera background can be rendered as either camera image data or camera depth data. The virtual scene can be composited with or without depth occlusion.

## Structure

1. set the buffer for the coords of quad and text

# Necessarily Knowledge

[LearnOpenGL](https://learnopengl.com/Getting-started/Coordinate-Systems)

## Vertex Specifications

is the process of setting up the necessary objects for rendering with a particular shader program, as well as the process of using those objects to render.[by OpenGl](https://www.khronos.org/opengl/wiki/Vertex_Specification#Vertex_Buffer_Object)

## Vertex

A point in three-dimensional space.

## Vertex components

are one or more vertex elements that are stored contiguously (interleaved per vertex) in a single memory buffer.
A complete vertex consists of one or more components, where each component is in a separate memory buffer.[by Microsoft](https://learn.microsoft.com/en-us/previous-versions/windows/desktop/bb153361(v=vs.85))

## normalized device coordinates (NDC)

In the last chapter we learned how we can use matrices to our advantage by transforming all vertices with transformation matrices. 
OpenGL expects all the vertices, that we want to become visible, to be in normalized device coordinates after each vertex shader run. 
That is, the x, y and z coordinates of each vertex should be between -1.0 and 1.0; coordinates outside this range will not be visible. 
What we usually do, is specify the coordinates in a range (or space) we determine ourselves and in the vertex shader transform these coordinates to normalized device coordinates (NDC). 
These NDC are then given to the rasterizer to transform them to 2D coordinates/pixels on your screen. 
[by LearnOpenGL](https://learnopengl.com/Getting-started/Coordinate-Systems)

## nativeOrder

Retrieves the native byte order of the underlying platform.
This method is defined so that performance-sensitive Java code can allocate direct buffers with the same byte order as the hardware. Native code libraries are often more efficient when such buffers are used.

Returns:
The native byte order of the hardware upon which this Java virtual machine is running

## NDC QUAD

An OpenGL® quadrilateral, or quad, in computer programming and graphics is a three-dimensional (3D) shape, also called a polygon, that has four sides and four points.

## FloatBuffer.put()

Relative bulk put method  (optional operation).

This method transfers the entire content of the given source float array into this buffer. An invocation of this method of the form dst.put(a) behaves in exactly the same way as the invocation
           dst.put(a, 0, a.length) 

Params:
* src – The source array

Returns:
* This buffer

Throws:
* BufferOverflowException – If there is insufficient space in this buffer
* ReadOnlyBufferException – If this buffer is read-only


---

# Another files

## mesh

A collection of vertices, faces, and other attributes that define how to render a 3D object.
To render the mesh, use SampleRender.draw().

## mesh

A list of vertex attribute data stored GPU-side.
One or more VertexBuffers are used when constructing a Mesh to describe vertex attribute data; for example, local coordinates, texture coordinates, vertex normals, etc.

