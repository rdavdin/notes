# Coordinate Systems in Unity

### Left-handed Y-Up vs Right-handed Y-Up
- Unity: Left-handed Y-Up
    - X (red): left to right
    - Y (green): down to up
    - Z (blue): front to back

![](../imgs/dot-net/unity-axis-left-vs-right-handed.webp)
Left-handed Y-Up VS Right-handed Y-Up

### World (Universal) Space

- World space is the coordiate system for **the scene itself**. (Note: not screen)
- Its origin is the **center of the scene**.
- It is a 3D coordinates system and where all of your game objects live.
- It **never** moves.

![](../imgs/dot-net/world-cs.png)
Drag a gameObject to scene view at position (0, 0, 0) that is the origin of World Space

### Local (Relative) Space

- Local Space is the coordiate system for **the gameOjbect itself**.

![](../imgs/dot-net/local-cs.png)

### Global vs Local coordianate axes

![](../imgs/dot-net/world-vs-local.png)
Global vs Local coordianate axes
*Source:* Unity in Action, 2nd Edition, page 37

### Rotation: *pitch*, *yaw*, and *roll*
- Pitch: X-axis rotation
- Yaw: Y-axis rotation
- Roll: Z-axis rotation

![](../imgs/dot-net/pitch-yaw-roll.png)
*Source:* Unity in Action, 2nd Edition, page 36


### Screen coordinate system
- **2D**
- measured in **pixels**
- its origin (0, 0) at **lower left corner**
- go to *(Screen.width, Screen.height)*
- depends on the resolution of devices

![](../imgs/dot-net/screen-coordiante1.png)
Screen coordinate with the device resolution 480x800

![](../imgs/dot-net/screen-coordiante2.png)
Screen coordinate with the device resolution 1280x720

### GUI space
- Used by the IMGUI System ([Unity's Immediate Mode GUI System](https://docs.unity3d.com/Manual/gui-Basics.html))
- Identical to Screen coordinates except that they start at (0,0) in the **upper left** and go to (Screen.width, Screen.height) in the lower right.

### Viewport space
- **2D**
- Identical to Screen coordinates
- Start at (0,0) in the **lower left** and go to (1,1) in the upper right 
- No matter what the resolution of device and orientation. For example (0.5, 0.5) in viewport coordinates will be the center of the screen no matter what resolution or orientation.

# Transform

### position vs localPosition

- position: the position of the transform in **world space**
- localPosition: the position of the transform **relative to the parent transform**

3 game objects: 
- Cicle: 
    - position: (-1, 2, 0)
    - localPosition: (-1, 2, 0)
- Square: child of Cirle
    - localPosition: (0.5, 0, 0)
    - position: (-0.5, 2, 0)
- Circle2: child of Square
    - localPosition: (0.5, 0, 0)
    - position: (0, 2, 0)

*... Starting point*

![](../imgs/dot-net/position-vs-localposition.png)

### Transform.TransformPoint()

- Convert the position from **local space** to **world space**

```
Circle2.transform.localPosition = new Vector3(0, 0.5, 0)
 ```

*Equivalant to*

```

Circle2.transform.position = Square.transform.TransformPoint(new Vector3(0, 0.5, 0))

```

*Result transformed ...*

![](../imgs/dot-net/position-vs-localposition-2.png)

### Transform.InverseTransformPoint()

- Convert the position from **world space** to **local space**

### Transform.TransformDirection()

- Convert the direction from **local space** to **world space**

### Transform.InverseTransformDirection()

- Convert the direction from **world space** to **local space**

### Other Questions
- Camera? Camera view?
- Canvas?
- Is there a relationship between World space with Camera view?
- Main Camera vs other Cameras?
