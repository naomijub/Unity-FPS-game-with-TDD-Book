# Chapter 12: Testing FPS Character Movement and Camera

I chose a first person shooter game over a third person shooter for the simple fact that I see a lot more content on third person games than first person games, also, because it is my prefered game style. However, understanding which character character controller to use in your game is a little harder than "just pick anyone, they are all fine" and we need to understand their differences. Also, we need to understand what a character controller actually is and how they differentiate. So, there is basically 3 options for movement controllers in Unity:
1. Build-in Character Controller.
2. RigidBody (Dynamic and Kinematic).
3. Your Custom Script.

Needless to say, that your custom script is a terrible idea to begin with and should only be used if the other solutions are really not working for you. There is one good side to a custom script, which is it is highly flexible and its complexity may be less than the power that you gain from flexibility. 

## Character Controller

A character controller is a component, or a group of components, attached to a character that is responsible for handling character movement and physics interactions, mostly collision detection. Some other important concepts related to controlling characters are gravity, jumping, drag, in-air movement, stairs, slopes, tilting, sprinting and crouching. 

### Unity's Built-in Character Controller

It is a built-in component available in the Unity editor to associate to a character, it is basically a capsule collider and a movement script developed by Unity's team, which is a wrapper over unity's physics engine controller It contains a list of properties and methods related to character movement, walking upstairs, walking through uneven grounds and slopes, and collision detection. It is usually the fastest implementation to set up character movement. 

### RigidBody

RigidBodies are components that interact with physics in real time. Although the built-in character controller has collision detection, it does now interact with other physics properties that the RigidBody interacts with, such as gravity, mass, momentum and drag. So, by adding a rigidbody we are inscripting the character into Unity's physics engine and defining that we want all physics forces to be applied by default. 

Additionally, if we add colliders to the character, the physics engine will detect collisions to all other bodies that have colliders. Rigidbodies with colliders have a key property which is `isKinematic`. When this property is unchecked, the rigidbody is considered dynamic, like the one in the previous chapter, and when it is checked it is considered kinematic. Basically a kinematic rigidbody disregards default physics forces and it is not moved by default collisions, so we are telling Unity that we will handle how our body collides and interacts with physics by our custom script. One thing that I suggest before continuing is to play with a rigidbody in both modes by throwing stuff at it, rapidly colliding with walls and adding different forces to it. 

### Differences between built-in and rigidbody

1. Rigidbodies interact with other physics objects, possibly moving them in collision, while built-in character controller does not physically interact with them.
2. Gravity is not automatically applied to the built-in controller. They only move when our code tells it to. However, the `SimpleMove` method does apply gravity.
3. Rigidbodies have drag applied to them by default. 
4. There is no momentum or drag for the movement, so there is no outside force to accelerate or decelerate the built-in controller. 
5. Built-in character controller does have a simple gravity mechanic by using the method `SimpleMove`.
6. Stairs and slopes are easily walkable for the built-in controller with the correct configuration, while it will not slide down a slope if it is located in the middle of a slope. On the other hand, rigidbodies will interact perfectly with sliding slopes, but will have a hard time interacting with stairs.
7. Built-in controller has a property that detects if it is colliding with the ground, while rigidbodies don't. This helps jumping mechanics. Including this mechanic in RigidBodies is pretty simple and it may help to deal with double jumps. 
8. Built-in character controller doesn't rotate the collider capsule, so tilting side-ways may be a problem for it.
9. Due to the skin width and overlap recovery properties, the built-in controller doesn't get stuck in walls like rigid bodies.

The conclusion is that neither of them is complete or works for every case, but they are definitely a good start for an MVP or even for a full game. The only downside that built-in character controllers have for the game we described in our GDD is tilting side-ways to shoot, so we will script this in another way. 

Understanding Unity's Built-in Character Controller
Now we will explore Unity's built-in character controller ([reference](https://docs.unity3d.com/ScriptReference/CharacterController.html), [manual](https://docs.unity3d.com/Manual/class-CharacterController.html)) which will be an explanation over its properties and most relevant methods and how to apply them. This will allow us to understand which modifications we need to do to improve our character controller so that the movement and collision feels perfect for your requirements. 

Let's create a scene to understand the character controller. Create a new built-in 3D scene, I named it `CharacterControllerScene`, and added a plane for ground collision (`Hierarchy > 3D Objects > Plane`). I augmented the x and z scale to `10`. Now add a capsule to the scene and increase its y so that it doesn't collide with the plane (`Hierarchy > 3D Objects > Capsule`, in the capsule transform, increase its y to `1.1`). Now click on the capsule game object and create a new colorful material, to make the capsule more visible. To add a character controller, go to the capsule game object and click `Add Component > Physics > Character Controller`. Note that now we have a character controller and a capsule collider (if you have another capsule collider, you may delete it). In the character controller we have a few float serialized fields that we will soon explore:
* Slope limit.
* Step offset.
* Skin width.
* Min move distance.
* Center.
* Radius.
* Height.

Also, don't forget to add some stairs and some slopes by adding cubes all around. My scene looks as simple as this:

Image x. Character controller test scene.
Center, radius and height are the capsule collider properties contained in the character controller. The common practice is to align the collider with the center of the character, the radius so that it contains all its body and the height to be equal to the distance between the feet and the head, like a capsule covering all the body. It is important to notice that for a character controller's collider you are not able to rotate the collider in x and z directions, even through code, so even if you rotate the character, you won't be able to rotate the collider. Let's understand the other properties.

**Slope limit**:

It is a simple property that determines the angle, in degrees, that our character will be able to climb when moving. Any slope with a higher value than our slope limit, our character will not be able to walk through, anything less or equal, it climbs. 

**Step offset**:

This property refers to the height of a colliding object's collider that our character is able to mount and climb. It is measured from the bottom of our character's collider to the height of the object that we are colliding with. So, if the height of the object is higher than our step offset, our character will not be able to climb it, but if it is smaller our character will be able to climb. Note that this property is usually between one fourth and two thirds of the character's height. 

**Skin width**:

This is one of the most interesting character controller properties in my opinion. It is designed to prevent our character from penetrating and getting stuck in other colliders and it varies according to the direction of collision. So, for a head-on collision, when the movement direction is perpendicular to the object's direction, it is the first actual collider, preventing our character from moving forward. However, when the movement is parallel to the colliding object's direction, the skin width is ignored. The case when we collide at an angle with an object's collider direction is a combination of both cases, so it will allow some penetration, but not a complete penetration. The documentation of the character controller suggests it should be around 10% of the radius value.

**Min move distance**:

It means minimum move distance and it sets the smallest change in distance that our character is required to do to actually cause a movement. This property usually has a very low value, commonly set to zero, but it exists to remove potential jitters from online games. Another case in which this property can be used is to manage the character's movement when the associated speed can have small variances and our character is not required to move. Imagine we add a stopping velocity force that results in a tiny negative velocity, this property would prevent the character moving more or moving backwards. 

Unity docs has move properties that are accessible by script that we can use, like `detect collision`, `is grounded`, `velocity`, `enable overlap recovery` and `collision flags`.

**Detect Collision**:

This property is a boolean that enables or disables collisions from incoming rigidbodies, like bullets, and will allow our character controller's collider to avoid impacting rigidbodies. When disabled and the character is idle, the rigid body will just pass through it, but if a move method, like `move` and `simple move`, are being called our character will just step over the incoming rigidbody. When enabled, all collisions are detected normally. We would use this property whenever our character is required to perform an animation that would conflict with its collider, like riding a car that has its own collider. 

**Enable overla recovery**:

A property that is enabled by default, it helps the character controller de-penetrate static game objects and colliders when an overlap is detected. This property will make Unity push out our character from the penetrated collider when, and only when, a move method is called. 

**Collision flags**:

For me, one of the more complicated properties, it tells us where the collision occurred with our character in the last movement call, so if the is no movement associated, the associated collision region flag will not be true. It uses flags to communicate where the collision was detected. Its values are None, for no collision, Above for the top of the collider, Below, for the bottom and Sides, for the middle/body. The last important note is that Sides doesn't inform us from which side the collision was detected, only that it was detected in the middle of the character.

**IsGrounded**:

This property is actually linked to the Below collision flag, and it indicates that our character is colliding with something in his bottom.

**Velocity**:

This property tells us the velocity that our character has from the character controller's movement methods, which will be zero if our velocity is coming from outside sources like moving pads or cars. 

### Movement methods

There are two movement public methods in the character controller, `Move` and `SimpleMove`. Both of them take as argument a `Vector3`, which is the direction change that your character controller is required to have. There are a few noticeable differences between them, for example:
* Simple Move is framerate independent, which means the `Vector3` we pass to it is probably multiplied by `Time.deltaTime`. It also applies gravity by default and disregards y axis movement because of that. It is not a good method for things like jumping.
* Move ins framerate dependent, so be sure to multiply it by `Time.deltaTime` to avoid weird behaviours. It doesn't apply gravity. It is a method that requires you to develop your own gravity logic and use `Time.deltaTime` to make it framerate independent. 

Another important method associated with movement methods is `OnControllerColliderHit`, which is similar to MonoBehaviour's `OnCollisionEnter` and gives you access to the object hitting the character when the movement methods are called. It allows us to program custom reactions to some collisions. Now we can start testing our character controller movement.

## Testing and First Person Camera

This is the base of the game, as its own name says, it is a first person shooter, and so, we will need a first person camera. The first step to deal with a camera is to create a new camera under the character controller object or to drag the main camera under our player, the capsule that we created previously. I also added the tag and the name player to the capsule. Avoid having two cameras on your game. I dragged the camera to the player and in the main camera transform, I clicked on the 3 vertical dots and chose reset, so that the camera is positioned within the player's position and pointing to the character's z axis direction. Another interesting thing to do is to move the camera to the top of the capsule object, as it becomes more realistic to the sight of the player, my camera's y position is now `1.0`. This will assure that the camera's position is always subordinate to the player's position. 

To understand how the look script will work, it is important to remember that everything will be handled with the mouse and the mouse can be moved into axis, x and y. If we move our player in the x axis, we want our entire player to rotate around the y axis, but if we move our mouse in y axis, we only want our camera to rotate in the x axis, not the entire player.  This allows us to rotate our player to look sideways and our player's eyes to look up and down. As our head is unable to rotate too much in the x axis, our camera rotation when looking up and down will be limited to around 180 degrees, which is called clamping. I have created a test script for this called `CameraMovementTest`, in playmode, and the first thing that we need to do is to declare a `GameObject` that has the game object containing the `Camera` component and a new script we will call `CameraMovementController`, something like this:

```C#
GameObject go;
 
[SetUp]
public void SetUp()
{
   go = new GameObject("player");
   go.AddComponent<CameraMovementController>();
}
```
So, our `CameraMovementController` needs to have a Camera. Let's create a test for that:
```
[UnityTest]
public IEnumerator CameraMovementController_WhenInstantiated_ContainsCamera()
{
   yield return null;
 
   Camera expected = go.GetComponentInChildren<Camera>();
   Camera actual = go.GetComponent<CameraMovementController>().GetCamera();
 
   Assert.IsNotNull(expected);
   Assert.AreEqual(expected, actual);
}
```

What does this test mean? It means that the camera that `CameraMovementController` is accessing is the same camera that the GameObject named player contains and they are not null. Sadly, this test just fails to compile. To fix that we can add a property `Camera` to to `CameraMovementController`:

```C#
public class CameraMovementController : MonoBehaviour
{
   public Camera Camera;
// ...
}
```

Now if we run the test in the test runner's playmode, we will see that it passes because both of them are null, which can be easily fixed by adding a new assert that checks if the expected camera is not null, `Assert.IsNotNull(expected);`. Now it fails and we can start implementing this feature. The first thing I would do, is to add a Camera component to the game object's `SetUp` method, and see that now the failing assert is `Assert.AreEqual(expected, actual)`:

```C#
[SetUp]
public void SetUp()
{
   go = new GameObject("player");
   go.AddComponent<Camera>();
   go.AddComponent<CameraMovementController>();
}
```

Note that the camera here is added as a component for the player and not as a subcomponent, this is due to the fact that the GameObject's method `GetComponentInChildren` returns the component in the GameObject or any of its children using the depth first search. This means that if no children have the component but the game object has it, the GameObjects camera will be returned. 

Our current test failing diff is:

```
Expected: <player (UnityEngine.Camera)>
But was:  null
```

Which means that the expected is retrieving the Camera component added in the `SetUp` while the property Camera is still `null`. So, in our Start method (this is why this test is in playmode), in `CameraMovementController` we will use the previously mentioned method `GetComponentInChildren` to set the Camera property to be the same as added.

```C#
public class CameraMovementTest
{
   GameObject go;
   // ...
 
   [UnityTest]
   public IEnumerator CameraMovementController_WhenInstantiated_ContainsCamera()
   {
       yield return null;
 
       Camera expected = go.GetComponentInChildren<Camera>();
       Camera actual = go.GetComponent<CameraMovementController>().Camera;
 
       Assert.IsNotNull(expected);
       Assert.AreEqual(expected, actual);
   }
}
```

And:

```C#
public class CameraMovementController : MonoBehaviour
{
   public Camera Camera;
 
   void Start()
   {
       Camera = GetComponentInChildren<Camera>();
   }
}
```

But there is something I really don't like with this test solution, which is that the Camera property is public and accessible/setable from any script. To fix that, I will change its visibility to private and create a method, `GetCamera`, that accesses the camera. Our test changes to `Camera actual = go.GetComponent<CameraMovementController>().GetCamera()` and the `CameraMovementController` is:

```C#
public class CameraMovementController : MonoBehaviour
{
   private Camera Camera;
   // ...
   public Camera GetCamera() {
       return Camera;
   }
}
```

This change gives us some protection over the camera property. We now know that we own a camera in our script, so we can start the x camera movement.

### X Axis Rotating Movement

For the mouse x camera movement we have to consider a few things. First we want it to be frame independent, so we need to consider `Time.deltaTime`. Secondly, we can have a property that determines mouse sensitivity. So we can start by having an editmode test that returns the correct mouse x rotation for the specific sensitivity and delta time. The test is simply a method call to `MouseXRotation` passing as arguments the `mouseX` value, the delta time and it will return a multiplication containing the sensitivity over x. I called this `MouseRotationTest`:

```C#
using NUnit.Framework;
using UnityEngine;
 
public class MouseRotationTest
{
   GameObject go;
 
   [SetUp]
   public void SetUp()
   {
       go = new GameObject("player");
       go.AddComponent<CameraMovementController>();
   }
 
   [Test]
   public void MouseXRotation_HasCorrectSensitivity()
   {
       float mouseX = 45f;
       float deltaTime = 0.015f;
       CameraMovementController movementController = go.GetComponent<CameraMovementController>();
 
       float xRotation = movementController.MouseXRotation(mouseX, deltaTime);
 
       Assert.AreEqual(xRotation, 67.5f);
   }
}
```

For this test to compile, we have to create a method named `MouseXRotation` that returns a float, for now `0f` is enough. The first step into fixing this test is make `MouseXRotation` return `mouseX * deltaTime` that were be passed as arguments, but it still fails by a lot, so we can create a public property named sensitivity which will be the `sensitivity` in the x axis and multiply it by `mouseX * deltaTime`. This property default value will be `100f`.

```C#
public class CameraMovementController : MonoBehaviour
{
   public float sensitivityX = 100f;
   private Camera Camera;
 
   // ...
 
   public float MouseXRotation(float mouseX, float deltaTime){
       return mouseX * sensitivityX * deltaTime;
   }
}
```

Nice, but our camera is far from moving, so now we need a playmode test that assures that the player has a rotation compatible with the `MouseXRotation`. To do this, we create a test that sets the transform rotation to `Quaternion.zero`, and awaits 1 second to compare if the rotation has changed, in this case to a negative value due to how we configured the `IUnityService` fake:

```C#
[UnityTest]
public IEnumerator RotateXCamera_WhenSecondsPass_HasRotatedPlayerOnYAxis()
{
   go.transform.rotation = Quaternion.Euler(Vector3.zero);
   var originalYRotation = go.transform.rotation.y;
   yield return new WaitForSeconds(1f);
 
   var newYRotation = go.transform.rotation.y;
   float actual = newYRotation - originalYRotation;
 
   Assert.Less(actual, 0f);
}
```

There are a few changes that we need to think about to make this test pass. The first one is the dependency on `Time.deltaTime` and the second one is that we will need to access the mouse axis position, which can be done by using the same strategy we used for the `IUnityServiice` before. For this purpose, I changed the `GetInputAxis` from `FakeUnityService` a little, so now the method will return `45f` for the mouse x and `-45f` for y axis.

```C#
public float GetInputAxis(string axis)
{
   if (axis == "Horizontal") {
      return 1f;
   } else if (axis == "Vertical") {
       return -1f;
   } else if (axis == "Mouse X") {
       return 45f;
   } else { return 0f; }
}
```

`45f` is a symbolic value to simplify the test, I am not giving it any meaning for now. Also, don't forget to add the `IUnityServive unityService` to the `CameraControllerMovement`, assign it in the `Start` method with `GetComponent` and add it in the player inspector. Now we can implement a rotation method, which I called `RotationX`:

```C#
public class CameraMovementController : MonoBehaviour
{
   public float sensitivityX = 100f;
   private Camera Camera;
   private IUnityService unityService;
 
   void Start()
   {
       Camera = GetComponentInChildren<Camera>();
       unityService = GetComponent<IUnityService>();
   }
 
   void Update()
   {
       float mouseX = unityService.GetInputAxis("Mouse X");
       float deltaTime = unityService.GetDeltaTime();
       RotateX(mouseX, deltaTime);  
   }
 
   void RotateX(float mouseX, float deltaTime)
   {
       float mouseRotation = MouseXRotation(mouseX, deltaTime);
       transform.Rotate(Vector3.up * mouseRotation);
   }
   // ...
}
```

### Y Axis Rotating Movement

We could do the exact same thing for rotating the camera along the y axis. However, by doing this we would not have control over the 180 degrees rotation limit, and so we need a way to extend our rotation logic. What I will do is to copy the `MouseXRotation_HasCorrectSensitivity` test and apply it to Y with a different sensitivity, as I personally think that y sensitivity can differ from x sensitivity, by creating `MouseYRotation_HasCorrectSensitivity` in editmode test `MouseRotationTest`:

```C#
[Test]
public void MouseYRotation_HasCorrectSensitivity()
{
   float mouseY = 45f;
   float deltaTime = 0.015f;
   CameraMovementController movementController = go.GetComponent<CameraMovementController>();
 
   float yRotation = movementController.MouseYRotation(mouseY, deltaTime);
 
   Assert.AreEqual(yRotation, 54f);
}
```

As expected, the test doesn't compile, so we can go ahead and copy `MouseXRotation` by changing X to Y. Now the test fails, and to make it pass, we can just add a new variable called `sensitivityY` with the value of `80f`:

```C#
public class CameraMovementController : MonoBehaviour
{
   public float sensitivityX = 100f;
   public float sensitivityY = 80f;
   // ...
 
   public float MouseXRotation(float mouseX, float deltaTime) {
       return mouseX * sensitivityX * deltaTime;
   }
 
   public float MouseYRotation(float mouseY, float deltaTime)
   {
       return mouseY * sensitivityY * deltaTime;
   }
}
```

Well, these methods look odd, they are basically the same thing, but for x and y respectively. To deal with this, we can pass sensitivity as an argument and merge the methods by changing the name to `MouseRotation`:

```C#
public class CameraMovementController : MonoBehaviour
{
   public float sensitivityX = 100f;
   public float sensitivityY = 80f;
   // ...
   void RotateX(float mouseX, float deltaTime)
   {
       float mouseRotation = MouseRotation(mouseX, sensitivityX, deltaTime);
       transform.Rotate(Vector3.up * mouseRotation);
   }
 
   public float MouseRotation(float mouseAxis, float axisSensitivity, float deltaTime) {
       return mouseAxis * axisSensitivity * deltaTime;
   }
}
```

The test also have some changes to make their names more explicit:

```C#
public class MouseRotationTest
{
   // ...
 
   [Test]
   public void MouseRotation_OverYAxis_HasCorrectXSensitivity()
   {
       float mouseX = 45f;
       float deltaTime = 0.015f;
       CameraMovementController movementController = go.GetComponent<CameraMovementController>();
 
       float xRotation = movementController.MouseRotation(mouseX, movementController.sensitivityX, deltaTime);
 
       Assert.AreEqual(xRotation, 67.5f);
   }
 
   [Test]
   public void MouseRotation_OverXAxis_HasCorrectYSensitivity()
   {
       float mouseY = 45f;
       float deltaTime = 0.015f;
       CameraMovementController movementController = go.GetComponent<CameraMovementController>();
 
       float yRotation = movementController.MouseRotation(mouseY, movementController.sensitivityY, deltaTime);
 
       Assert.AreEqual(yRotation, 54f);
   }
}
```

Now we can make a playmode test to check the rotation over the x axis made by moving the mouse over the y axis. However, due to how our fake works, we will need to create a new playmode test that contains a new fake that doesn't move on x axis but moves on y axis because rotations can get complicated especially if the camera is rotating in the x axis but the player is rotating in the y axis. The first test is very simple, and just checks, in a different way, if the camera `localRotation` has changed positively due to an update.

```C#
public class CameraYMovementTest
{
   GameObject go;
 
   [SetUp]
   public void SetUp()
   {
       go = new GameObject("player");
       go.AddComponent<Camera>();
       go.AddComponent<CameraMovementController>();
 
       go.AddComponent<FakeYUnityService>();
   }
 
 
   [UnityTest]
   public IEnumerator RotateYCamera_WhenSecondsPass_HasRotatedPlayerOnXAxis()
   {
       var originalCameraXRotation = go.GetComponent<Camera>().transform.localRotation.x;
       yield return new WaitForSeconds(1f);
 
       var newCameraXRotation = go.GetComponent<Camera>().transform.localRotation.x;
 
       Assert.Greater(newCameraXRotation, originalCameraXRotation);
   }
}
```

And for `FakeYUnityService` now considers only `Mouse Y` value:

```C#
public class FakeYUnityService : MonoBehaviour, IUnityService
{
   public float GetDeltaTime() => 0.3f;
 
   public float GetInputAxis(string axis)
   {
       if (axis == "Horizontal") {
           return 1f;
       } else if (axis == "Vertical") {
           return -1f;
       } else if (axis == "Mouse Y") {
           return -1f;
       } else {
           return 0f;
       }
   }
}
```

To solve this test we can implement the exact same solution as the rotation caused by the mouse x movement, for now. In the solution below, note that the x axis direction is `Vector.left`, this is due to the fact that the y axis is inverted in game programming, I will explain this better soon. 

```C#
public class CameraMovementController : MonoBehaviour
{
   public float sensitivityX = 100f;
   public float sensitivityY = 80f;
   // ...
 
   void Update()
   {
       float mouseX = unityService.GetInputAxis("Mouse X");
       float mouseY = unityService.GetInputAxis("Mouse Y");
       float deltaTime = unityService.GetDeltaTime();
       RotateX(mouseX, deltaTime);
       RotateY(mouseY, deltaTime);
   }
 
   void RotateX(float mouseX, float deltaTime)
   {
       float mouseRotation = MouseRotation(mouseX, sensitivityX, deltaTime);
       Camera.transform.Rotate(Vector3.up * mouseRotation);
   }
 
   void RotateY(float mouseY, float deltaTime)
   {
       float mouseRotation = MouseRotation(mouseY, sensitivityY, deltaTime);
       transform.Rotate(Vector3.left * mouseRotation);
   }
}
```

However, if you test this solution, it doesn't quite work well. This is due to two reasons:
1. Complex rotations should be done with quaternions, because they are responsible for rotating in Unity.
2. We are not clamping the rotation value between -90 degrees and 90 degrees.
So we will create a new test that will lock the rotation over the x axis between -90 and 90 degrees. This test sets the Camera `localRotation` to 89 degrees and applies a rotation to it with a 1 second update, which reaches the 90 degrees limit. By applying a new update cycle we can see that no change in rotation happens. If we get the `localRotation.eulerAngles` in this test we will see that the x angle is 90 degrees for both camera rotation tests. If you prefer to see clear angles, change it to eulerAngles:

```C#
[UnityTest]
public IEnumerator RotateYCamera_WhenSecondsPass_IsClamped()
{
   go.GetComponent<Camera>().transform.localRotation = Quaternion.Euler(89f, 0, 0);
   yield return new WaitForSeconds(1f);
 
   var newCameraXRotation = go.GetComponent<Camera>().transform.localRotation.x;
   Assert.Greater(newCameraXRotation, 0.69f);
 
   yield return new WaitForSeconds(1f);
 
   var secondNewCameraXRotation = go.GetComponent<Camera>().transform.localRotation.x;
   Assert.AreEqual(secondNewCameraXRotation, newCameraXRotation);
}
```

The solution to this test is a little bit more complex as it involves saving the the current `xRotation` and clamping it:

```C#
public class CameraMovementController : MonoBehaviour
{
   public float sensitivityX = 100f;
   public float sensitivityY = 80f;
   private float xAxisRotation;
   // ...
   void Start()
   {
       Camera = GetComponentInChildren<Camera>();
       unityService = GetComponent<IUnityService>();
       xAxisRotation = 0f;
   }
 
   void Update()
   {
       float mouseX = unityService.GetInputAxis("Mouse X");
       float mouseY = unityService.GetInputAxis("Mouse Y");
       float deltaTime = unityService.GetDeltaTime();
       RotateY(mouseY, deltaTime);
       RotateX(mouseX, deltaTime);
   }
 
   void RotateY(float mouseY, float deltaTime)
   {
       float mouseRotation = MouseRotation(mouseY, sensitivityY, deltaTime);
       xAxisRotation -= mouseRotation;
       xAxisRotation = Mathf.Clamp(xAxisRotation, -90f, 90f);
 
       Camera.transform.localRotation = Quaternion.Euler(xAxisRotation, 0f, 0f);
   }
}
```

We created a variable, `xAxisRotation`, that starts as 0f, so no rotation associated with it, then it gets the mouse rotation from `MouseRotation` and decreases it from the current `xAxisRotation`. This decrease is due to the fact that the y axis is inverted in the game screen, as point `(0, 0)` is the top left corner of your screen and the bottom left corner has an increased y value. Then we clamp the value of `xAxisRotation` to be between -90 and 90, you may change it to 70 if you prefer, so that we can transform it in a Quaternion that rotates in the x axis to the Camera's `localRotation`.

The last thing I would do is add `Cursor.lockState = CursorLockMode.Locked;` to `CameraMovementController`'s `Start` method so that the cursor is locked in the middle of the screen when starting.

```C#
public class CameraMovementController : MonoBehaviour
{
   // ...
   void Start()
   {
       Cursor.lockState = CursorLockMode.Locked;
       Cursor.visible = true;
 
       Camera = GetComponentInChildren<Camera>();
       unityService = GetComponent<IUnityService>();
       xAxisRotation = 0f;
   }
   // ...
}
```

## Testing and Scripting Movement

The objective here is to write a movement controller script associated with the character controller that will be as simple as possible while being fluid. It should move the character, have gravity applied to it, be able to jump and have ground checking, climb steps and slopes and not get stuck on walls. Unity has two axis associated with movement, *Vertical* and *Horizontal*, which are usually translated to `WASD` keys or directional keys. The method `Input.GetAxis` receives as parameter a string, which would be `"Vertical"` and `"Horizontal"` for the movement controller. Breaking down its values we identify that `W` and `Up` keys return a vertical `1f`, `S` and `Down` keys return a vertical of `-1f`, `D` and `Right` keys return a horizontal `1f` and `A` and `Left`keys return a horizontal of -1f. These inputs allow us to move our character along the z axis for vertical input and along the x axis for horizontal input. 

### Making the Player Move

Before we start developing the tests for our character movement script we can plan how we will test it. 

| Feature  	| Directional input moves the character   	| Test Type |
|---	|---	|--- |
| Frame speed forward | When axis movement is forward the resulting movement is frame independent and has the correct speed | editmode |
| Frame speed sideways | When axis movement is sideways the resulting movement is frame independent and has the correct speed | editmode |
| Frame movement sideways | When axis movement is sideways the resulting movement is frame independent and has the correct movement vector | editmode |
| Frame movement forward | When axis movement is forward the resulting movement is frame independent and has the correct movement vector | editmode |
| Character moves forward and sideways | When an update happens with positive axis movement, the character is moved to a new position | playmode |
| Movement proportions on rotating | When the player is moving and rotating the increase over x and z positions are proportionally different | playmode |
| Gravity | Player has gravity applied to it | playmode |


Now, let's go to the character movement script to implement these test cases. The first step here is to create a test script, named `MovementSpeedTest`, that will handle the movement speed on x and y axes, pressed input value and delta time. This script will contain a game object named `"player"` in the variable `go` which we will add the script `PlayerMovementController` to it:

```C#
public class MovementSpeedTest
{
   GameObject go;
 
   [SetUp]
   public void SetUp()
   {
       go = new GameObject("Player");
       go.AddComponent<PlayerMovementController>();
   }
}
```

As it already complains that this is failing, go ahead and create a script named `PlayerMovementController` in the scripts folder. Then we need to create a test that will simply return the x speed when we pass all arguments as `1f`:

```C#
[Test]
public void XMovementInFrame_WhenEverythingElseIs1_ReturnsTheXSpeed()
{
   float deltaTime = 1f;
   float horizontal = 1f;
 
   float frameSpeed = go.GetComponent<PlayerMovementController>().MovementOnX(horizontal, deltaTime);
 
   Assert.AreEqual(20f, frameSpeed);
}
```

To make this test compile, create a method on `PlayerMovementController` named `MovementOnX` that receives an axis value and a delta time value then returns `0f`. This test will fail and so we can create a float serialized field called `xMovementSpeed` and make the newly created method return it. The test passes. Now we create a test called `XMovementInFrame_WhenDeltaTimeIsShort_ReturnsTheXSpeedInTheFrame` which has a `deltaTime` of `0.1`:

```C#
[Test]
public void XMovementInFrame_WhenDeltaTimeIsShort_ReturnsTheXSpeedInTheFrame() {
   float deltaTime = 0.1f;
   float horizontal = 1f;
 
   float frameSpeed = go.GetComponent<PlayerMovementController>().MovementOnX(horizontal, deltaTime);
 
   Assert.AreEqual(2f, frameSpeed);
}
```

For this test to pass, we need to multiply the `xMovementSpeed` by `deltaTime` resulting in:

```C#
public float MovementOnX(float axis, float deltaTime)
{
   return xMovementSpeed * deltaTime;
}
```

The last step is to create a test that has a negative direction axis, I will name it `XMovementInFrame_WhenHorizontalAxisIsNegative_ReturnsTheXSpeedInTheFrame`:

```C#
[Test]
public void XMovementInFrame_WhenHorizontalAxisIsNegative_ReturnsTheXSpeedInTheFrame() {
   float deltaTime = 0.1f;
   float horizontal = -1f;
 
   float frameSpeed = go.GetComponent<PlayerMovementController>().MovementOnX(horizontal, deltaTime);
 
   Assert.AreEqual(-2f, frameSpeed);
}
```

To make this test pass we need `MovementOnX` to return the multiplication of `axis`, `deltaTime` and `xMovementSpeed` which would make this method exactly like the `CameraMovementController` method `MouseRotation`. Methods doing the same exact thing in different classes are a code smell, meaning that there is something wrong with the code design. To solve this code smell we can refactor our code by creating a new class `AuxFunctions` and have a static method that will multiply 3 elements:

```C#
public class AuxFunctions
{
   public static float SensitivityInFrame(float axis, float sensitivity, float deltaTime)
   {
       return axis * sensitivity * deltaTime;
   }
}
```

Now we can replace our test with this new function, include the two functions that were in `MouseRotationTest` (now deleted) and delete the `MouseRotation` method from `CameraMovementController` by replacing it with `AuxFunction.SensitivityInFrame` in all of its occurrences:

```C#
using System.Collections;
using System.Collections.Generic;
using NUnit.Framework;
using UnityEngine;
using UnityEngine.TestTools;
 
public class MovementSpeedTest
{
   GameObject go;
   PlayerMovementController moveController;
   CameraMovementController cameraController;
 
   [SetUp]
   public void SetUp()
   {
       go = new GameObject("Player");
       go.AddComponent<PlayerMovementController>();
       go.AddComponent<CameraMovementController>();
       moveController = go.GetComponent<PlayerMovementController>();
       cameraController = go.GetComponent<CameraMovementController>();
   }
 
   // A Test behaves as an ordinary method
   [Test]
   public void XMovementInFrame_WhenEverythingElseIs1_ReturnsTheXSpeed()
   {
       float deltaTime = 1f;
       float horizontal = 1f;
 
       float frameSpeed = AuxFunctions.SensitivityInFrame(horizontal, moveController.xMovementSpeed, deltaTime);
 
       Assert.AreEqual(20f, frameSpeed);
   }
 
   [Test]
   public void XMovementInFrame_WhenDeltaTimeIsShort_ReturnsTheXSpeedInTheFrame() {
       float deltaTime = 0.1f;
       float horizontal = 1f;
 
       float frameSpeed = AuxFunctions.SensitivityInFrame(horizontal, moveController.xMovementSpeed, deltaTime);
 
       Assert.AreEqual(2f, frameSpeed);
   }
 
   [Test]
   public void XMovementInFrame_WhenHorizontalAxisIsNegative_ReturnsTheXSpeedInTheFrame() {
       float deltaTime = 0.1f;
       float horizontal = -1f;
 
       float frameSpeed = AuxFunctions.SensitivityInFrame(horizontal, moveController.xMovementSpeed, deltaTime);
 
       Assert.AreEqual(-2f, frameSpeed);
   }
 
   [Test]
   public void CameraSensitivityOnX_WhenNothingIs1f_ReturnsSensitivityOnFrame()
   {
       float mouseX = 45f;
       float deltaTime = 0.015f;
 
       float xRotation = AuxFunctions.SensitivityInFrame(mouseX, cameraController.sensitivityX, deltaTime);
 
       Assert.AreEqual(xRotation, 67.5f);
   }
 
   [Test]
   public void CameraSensitivityOnY_WhenNothingIs1f_ReturnsSensitivityOnFrame()
   {
       float mouseY = 45f;
       float deltaTime = 0.015f;
 
       float yRotation = AuxFunctions.SensitivityInFrame(mouseY, cameraController.sensitivityY, deltaTime);
 
       Assert.AreEqual(yRotation, 54f);
   }
}
```

And:

```C#
public class CameraMovementController : MonoBehaviour
{
   public float sensitivityX = 100f;
   public float sensitivityY = 80f;
   // ...
 
   void RotateX(float mouseX, float deltaTime)
   {
       float mouseRotation = AuxFunctions.SensitivityInFrame(mouseX, sensitivityX, deltaTime);
       transform.Rotate(Vector3.up * mouseRotation);
   }
 
   void RotateY(float mouseY, float deltaTime)
   {
       float mouseRotation = AuxFunctions.SensitivityInFrame(mouseY, sensitivityY, deltaTime);
       xAxisRotation -= mouseRotation;
       xAxisRotation = Mathf.Clamp(xAxisRotation, -90f, 90f);
 
       Camera.transform.localRotation = Quaternion.Euler(xAxisRotation, 0f, 0f);
   }
}
```

Now that the refactor is done, `speed` becomes `sensitivity`, as it also refers to the step that we are going to take when moving, and we have deleted some code we can make a playmode test for `MovementOnX`. One last important thing on refactoring is to know when to refactor:
* When you have duplicated code.
* When your code is not clear or readable.
* When we detect a code potentially dangerous.
* Duplicate tests

Now we can make `MovementOnX` be a little more than just a multiplier function, it can return the `Vector3` associated with the change that we expect on the movement over the x axis. To do this, lets create a test on `MovementSpeed` named `MovementOnX_WhenValuesAreNot1f_ReturnsNegativeRightVector3` that will receive an axis value and a delta time return to us the x axis direction that our character should move. Note that the `axis` value is negative so the expected value to `MovementOnX` should be a `Vector.left` multiplied that the resulting multiplication from `AuxFunctions.SensitivityOnFrame`:

```C#
[Test]
public void MovementOnX_WhenValuesAreNot1f_ReturnsNegativeRightVector3()
{
   float axis = -1f;
   float deltaTime = 0.1f;
   Vector3 expected = Vector3.left * 2f;
 
   Vector3 actual = moveController.MovementOnX(axis, deltaTime);
 
   Assert.AreEqual(expected, actual);
}
```

For this test to pass we can simply make `MovementOnX` return a `Vector3.right` multiplied by the sensitivity calculated by `AuxFunctions.SensitivityInFrame`:

```C#
public Vector3 MovementOnX(float axis, float deltaTime)
{
   return Vector3.right * AuxFunctions.SensitivityInFrame(axis, xMovementSpeed, deltaTime);
}
```

Now we can have the same concept of testing for the movement on z called `MovementOnZ_WhenValuesAreNot1f_ReturnsNegativeForwardVector3`. As moving forward is relatively easier than sideways, I assume we can have the assert be equal to a `Vector3.back * 3.5f`:

```C#
[Test]
public void MovementOnZ_WhenValuesAreNot1f_ReturnsNegativeForwardVector3()
{
   float axis = -1f;
   float deltaTime = 0.1f;
   Vector3 expected = Vector3.back * 3.5f;
 
   Vector3 actual = moveController.MovementOnZ(axis, deltaTime);
 
   Assert.AreEqual(expected, actual);
}
```

So now we need to create a function `MovementOnZ` on `PlayerMovementController` that can return a simple `Vector3.forward`. As it doesn't pass we can multiply the `Vector3.forward` by the same value we used on `MovementOnX`, `AuxFunctions.SensitivityInFrame(axis, xMovementSpeed, deltaTime)`. This improved the result but it is not quite right, so to make it right we create a `yMovementSpeed` serialized property with the value of `35f` and replace `xMovementSpeed` by it:

```C#
public class PlayerMovementController : MonoBehaviour
{
   [SerializeField]
   public float xMovementSpeed = 20f;
   [SerializeField]
   public float yMovementSpeed = 35f;
 
   public Vector3 MovementOnX(float axis, float deltaTime)
   {
       return Vector3.right * AuxFunctions.SensitivityInFrame(axis, xMovementSpeed, deltaTime);
   }
 
   public Vector3 MovementOnZ(float axis, float deltaTime)
   {
       return Vector3.forward * AuxFunctions.SensitivityInFrame(axis, yMovementSpeed, deltaTime);
   }
}
```

Ok, our movement seems to be working fine. So, let's test if our character actually moves and to do that we will create a playmode that checks if the new position is different from the original position and checks if they have changed to the correct values. To create this test we need a playmod script test named `CharacterMovementTest` and a test in it that will check if the position has changed. Note that the game object has A `FakeUnityService`, as it will required `Time.deltaTime` and `Input.GetAxis`, and a `CharacterController`, which is the Unity's built-in character controller script:

```C#
using System.Collections;
using System.Collections.Generic;
using NUnit.Framework;
using UnityEngine;
using UnityEngine.TestTools;
 
public class CharacterMovementTest
{
  
   [UnityTest]
   public IEnumerator CharacterMovement_When1SecondPassed_MovesCharacter()
   {
       GameObject go = new GameObject("Player");
       go.AddComponent<PlayerMovementController>();
       go.AddComponent<CharacterController>();
       go.AddComponent<FakeUnityService>();
       Vector3 original = go.transform.position;
 
       yield return new WaitForSeconds(1f);
 
       Vector3 actual = go.transform.position;
 
       Assert.AreNotEqual(original, actual);
   }
}
```

And, as expected, the test fails because there is nothing being executed on the update method. Let's fix that by adding a `CharacterController` property and a `IUnityService` property, attaching a component to them and using `CharacterController.Move` to move the character to a new direction based on the sum of `MovementOnX` and `MovementOnZ`:

```C#
public class PlayerMovementController : MonoBehaviour
{
   [SerializeField]
   public float xMovementSpeed = 20f;
   [SerializeField]
   public float yMovementSpeed = 35f;
 
   CharacterController controller;
   IUnityService unityService;
 
   void Start()
   {
       controller = GetComponent<CharacterController>();
       unityService = GetComponent<IUnityService>();
   }
 
   void Update()
   {
       float horizontal = unityService.GetInputAxis("Horizontal");
       float vertical = unityService.GetInputAxis("Vertical");
       float deltaTime = unityService.GetDeltaTime();
 
       Vector3 movement = MovementOnX(horizontal, deltaTime) + MovementOnZ(vertical, deltaTime);
 
       controller.Move(movement);
   }
 
   public Vector3 MovementOnX(float axis, float deltaTime)...
 
   public Vector3 MovementOnZ(float axis, float deltaTime)...
}
```

Now we should test this script in a real scene to have a feeling of how it is working. To do that make sure to add a `CharacterController` component and the `PlaymerMovementController` to your player, also, don;t forget what we have already added for camera, `UnityService`, `CameraMovementController` and a camera children to our player. Hit play and try to move a little. If you move, you will notice two problems with our code:
1. Whenever we move upstairs or up a slope, the player doesn't fall. Which is because the `Move` method doesn't apply gravity to our component.
2. The player seems to be always moving in the same direction, no matter where we are looking at.

To solve the first problem, let's create a test in `CharacterMovementTest` that executes a rotation and moves our player. I named the test `MovementAndRotation_OnRotatingView_HasZXIncreasedInDifferentProportions` and it will have two yields in it. The first one assures minimal movement while the second one assures a larger movement. The idea here is to check if the proportion of movement on x and z is different because rotation would make the player deviate from a proportional path. To assist with this test I created a new `IUnityService` which  I called it `FakeUnityServicePlayTest`:

```C#
public class FakeUnityServicePlayTest : MonoBehaviour, IUnityService
{
   public float GetDeltaTime() => 0.03f;
 
   public float GetInputAxis(string axis)
   {
       if (axis == "Horizontal")
       {
           return 0f;
       }
       else if (axis == "Vertical")
       {
           return 1f;
       }
       else if (axis == "Mouse X")
       {
           return 0.5f;
       }
       else
       {
           return 0f;
       }
   }
}
```

And the test:

```C#
[UnityTest]
public IEnumerator MovementAndRotation_OnRotatingView_HasZXIncreasedInDifferentProportions()
{
   GameObject player = new GameObject("TestPlayer");
   player.AddComponent<PlayerMovementController>();
   player.AddComponent<CharacterController>();
   player.AddComponent<FakeUnityService>();
   player.AddComponent<Camera>();
   player.AddComponent<CameraMovementController>();
 
   yield return new WaitForSeconds(0.1f);
   Vector3 origin = player.transform.position;
 
   yield return new WaitForSeconds(0.3f);
 
   Vector3 actual = player.transform.position;
   int xProportion = Mathf.RoundToInt(actual.x / origin.x);
   int zProportion = Mathf.RoundToInt(actual.z / origin.z);
 
   Assert.AreNotEqual(xProportion, zProportion);
}
```

By executing this test we will observe that the error says that both proportions were equal to the same integer, while the `zProportion` should be, at least a little bit, larger than the `xProportion`. To solve this we should fix our `MonvementOn_` methods. They are using `Vector3.forward` and ``Vector3.right` to determine the direction of movement, but we need it to be relative to the game object's transform, so we change the methods to:

```C#
public Vector3 MovementOnX(float axis, float deltaTime)
{
   return transform.right * AuxFunctions.SensitivityInFrame(axis, xMovementSpeed, deltaTime);
}
 
public Vector3 MovementOnZ(float axis, float deltaTime)
{
   return transform.forward * AuxFunctions.SensitivityInFrame(axis, yMovementSpeed, deltaTime);
}
```

And now our test is passing. However, to make our test more robust and less flaky, I would change the assert to:

```C#
float xProportion = actual.x / origin.x;
float zProportion = actual.z / origin.z;
 
Assert.Greater(zProportion, xProportion);
```

Also, the editmode test continues to pass because it does not include any rotation. Now we can address the first issue that we found, which is that gravity is not applied.

## Applying Gravity

The last step for a character controller is applying gravity to the character. Gravity visual effect is that the player moves down a little each frame, this is done because gravity is a force applied over time downwards (meaning where the body with the largest mass, in this case the ground, is located). The other effect of gravity being applied over time is that the speed downwards will increase over time, as the equation `V = Vo + g.t` defines, being `Vo` the initial velocity, `g` gravity, `t` delta time and `V` the resulting velocity.  With this in mind our first test should be very simple, We need only to test if our player velocity has decreased over time in `y` axis:

```cs
public class MovementSpeedTest
{
    GameObject go;
    PlayerMovementController moveController;
    CameraMovementController cameraController;

    [SetUp]
    public void SetUp()
    {
        go = new GameObject("Player");
        go.AddComponent<PlayerMovementController>();
        go.AddComponent<CameraMovementController>();
        moveController = go.GetComponent<PlayerMovementController>();
        cameraController = go.GetComponent<CameraMovementController>();
    }
// …
    [Test]
    public void CalculateGravityVelocity_WhenPlayerIsNotMoving_DecreasesPlayerYVelocity()
    {
        Vector3 playerOriginalVelocity = moveController.velocity;

        moveController.CalculateGravityVelocity();

        Assert.Less(moveController.velocity.y, playerOriginalVelocity.y);
        Assert.AreEqual(moveController.velocity.x, playerOriginalVelocity.x);
        Assert.AreEqual(moveController.velocity.z, playerOriginalVelocity.z);
        Assert.AreEqual(moveController.velocity.z, 0f);
    }

}
```

As this is a very simple test, we can start with a very simple implementation to check if it fails and do nothing. Then we can improve our solution to calculate the new velocity applied to our object due to gravity:

```cs
public class PlayerMovementController : MonoBehaviour
{
    [SerializeField]
    public float xMovementSpeed = 20f;
    [SerializeField]
    public float yMovementSpeed = 35f;
    [SerializeField]
    public Vector3 velocity = Vector3.zero;
    float gravity = -9.8f;

// …
public void CalculateGravityVelocity()
    {
        velocity.y += gravity;
    }

}
```

The test works well, but it is not time dependent, which is something that we need to consider when applying forces. To solve that a new test can be added considering time dependency (remember to add `deltaTime` to the previous test):

```cs
[Test]
public void CalculateGravityVelocity_WhenPlayerIsNotMoving_DecreasesPlayerYVelocity()
{
    float deltaTime = 1f;
    Vector3 playerOriginalVelocity = moveController.velocity;

    moveController.CalculateGravityVelocity(deltaTime);

    Assert.Less(moveController.velocity.y, playerOriginalVelocity.y);
    Assert.AreEqual(moveController.velocity.x, playerOriginalVelocity.x);
    Assert.AreEqual(moveController.velocity.z, playerOriginalVelocity.z);
    Assert.AreEqual(moveController.velocity.z, 0f);
}

[Test]
public void CalculateGravityVelocity_WhenPlayerIsNotMovingAndDeltaTimeIsConsidered_DecreasesPlayerYVelocity()
{
    float deltaTime = 0.3f;
    Vector3 playerOriginalVelocity = moveController.velocity;

    moveController.CalculateGravityVelocity(deltaTime);

    Vector3 playerGravityAppliedVelocity = moveController.velocity;

    Assert.That(Mathf.Approximately(playerGravityAppliedVelocity.y, -2.94f));
    Assert.AreEqual(playerOriginalVelocity.y, 0f);
}
```

And this can be fixed by multiplying gravity by `deltaTime`:

```cs
public void CalculateGravityVelocity(float deltaTime)
{
    velocity.y += gravity * deltaTime;
}
```

### Applying gravity to gameplay

If we try to play the game with the current changes we will see that nothing happens, because we haven't actually applied the gravity effect to our character. To do this we must first create a playmode test that checks if player position has decreased over time:

```cs
public class CharacterMovementTest
{

// …
   [UnityTest]
    public IEnumerator ApplyGravity_AfterSeconds_MovesCubeDown()
    {
        GameObject go = new GameObject("Player");
        go.AddComponent<PlayerMovementController>();
        go.AddComponent<CharacterController>();
        go.AddComponent<UnityService>();
        Vector3 original = go.transform.position;
        PlayerMovementController moveController = go.GetComponent<PlayerMovementController>();

        yield return new WaitForSeconds(0.05f);
        Vector3 actual = go.transform.position;

        Assert.AreNotEqual(original, actual);
        Assert.Less(actual.y, -0.5f);
        Assert.Greater(actual.y, -2.5f);
    }

}
```

Note that as we are using `UnityService` and not `FakeUnityService` the actual position is actually a range, which does make the test flaky, but is something nice to demonstrate.Now, when we execute this test we will notice that if fails, so we implement an update function that applies gravity (`ApplyGravity`), which will be just calling `Move` on the character controller for the gravity velocity:

```cs
public class PlayerMovementController : MonoBehaviour
{
    [SerializeField]
    public float xMovementSpeed = 20f;
    [SerializeField]
    public float yMovementSpeed = 35f;
    [SerializeField]
    public Vector3 velocity = Vector3.zero;
    float gravity = -9.8f;

 // …

    void Update()
    {
        float horizontal = unityService.GetInputAxis("Horizontal");
        float vertical = unityService.GetInputAxis("Vertical");
        float deltaTime = unityService.GetDeltaTime();

        Vector3 movement = MovementOnX(horizontal, deltaTime) + MovementOnZ(vertical, deltaTime);

        controller.Move(movement);
        ApplyGravity(deltaTime);
    }

    void ApplyGravity(float deltaTime)
    {
        CalculateGravityVelocity(deltaTime);
        controller.Move(velocity);
    }

    public void CalculateGravityVelocity(float deltaTime)
    {
        velocity.y += gravity * deltaTime;
    }
	// …
}
```

Now we need to remember that although `V = Vo + g.t`, we are actually not calculating the relative motion of the character, `ΔV = V - Vo`, but how much we are going to move our player to a new position, `Δy`,  and this is a different function which is described as `Δy = ½ g . tˆ2`. This function means that the difference of position (`Δy`) is equal to the gravity (`g`) multiplied by delta time squared (`tˆ2`). This is due to the fact that although character controller move function receives a `motion` as argument, it is actually applying an [absolute movement delta](https://docs.unity3d.com/ScriptReference/CharacterController.Move.html). Considering this, one nice thing to have in this test is something to make sure that the distance is increasing with the square of time, you can simply do that by reducing the accepted position interval and adding the following block, that checks if the `Δy` in the second block is larger than twice the `Δy` in the first block, to the previous test:

```cs
[UnityTest]
public IEnumerator ApplyGravity_AfterSeconds_MovesCubeDown()
{
    GameObject go = new GameObject("Player");
    go.AddComponent<PlayerMovementController>();
    go.AddComponent<CharacterController>();
    go.AddComponent<UnityService>();
    Vector3 original = go.transform.position;
    PlayerMovementController moveController = go.GetComponent<PlayerMovementController>();

    yield return new WaitForSeconds(0.1f);
    Vector3 actual = go.transform.position;

    Assert.AreNotEqual(original, actual);
    Assert.Less(actual.y, -0.05f);
    Assert.Greater(actual.y, -0.08f);

    yield return new WaitForSeconds(0.1f);
    Vector3 actual2 = go.transform.position;

    Assert.AreNotEqual(actual, actual2);
    Assert.Less(actual2.y, -0.2f);
    Assert.Greater(actual2.y, -0.28f);
}
```

Note the increased wait time, now at `0.1`, and the increased range for the first `Δy`, between `0.05` and `0.08`, which is less than half of the second `Δy`, demonstrating the quadratic relationship.

If we play the game now, gravity is applied but something seems weird as the speed in which we go down seems to be increasing even when we are touching the ground. To fix that, a new test should be added verifying that when the character controller touches the ground, its speed is set to zero. The following test can help us test that:

```cs
[UnityTest]
public IEnumerator ApplyGravity_WehnHitsGround_VelocityIsZero()
{
    LayerMask groundMask = new LayerMask
    {
        value = 3
    };

    GameObject go = new GameObject("Player");
    go.AddComponent<PlayerMovementController>();
    go.AddComponent<CharacterController>();
    go.AddComponent<UnityService>();
    PlayerMovementController moveController = go.GetComponent<PlayerMovementController>();
    moveController.groundLayerMask = groundMask;
    moveController.groundPosition = (new GameObject("Ground position")).transform;

    Vector3 original = go.transform.position;

    GameObject ground = new GameObject("Ground");
    ground.transform.localScale = new Vector3(100f, 1f, 100f);
    ground.transform.position = Vector3.down * 3;
    ground.AddComponent<BoxCollider>();
    ground.layer = 3;

    yield return new WaitForSeconds(3f);
    Vector3 actual = go.transform.position;
    Assert.Less(actual.y, original.y);
    Assert.That(Mathf.Approximately(actual.y, -1.42f));
    Assert.Less(-1.3f, moveController.velocity.y);
}
```

This test consists of a bunch of changes in `PlayerMovementController`. Let's start by the ones reflected on the test. The first one is defining a position in the lower part of the character. The second one is  defining a layer mask for detecting when the player collides with the ground, then we need a "ground" game object to collide. After yielding 3 seconds there are 3 checks:
Current position is lower than the original position, which means the object has fallen.
Actual position is approximately floor position plus a detection sphere.
When the player hits the ground, the velocity in `y` is less than a minimal velocity (-1f) added by `Time.deltaTime`.

Considering the current Apply gravity scenario, when the test is executed the velocity in y axis is something around `-30f` and if `ground.AddComponent<BoxCollider>()`  is removed the position of `actual.y` is way less than `-10f`. To solve this issue a detection sphere should be added to the bottom of the player capsule which will set an `isGrounded` flag to true when hits the ground layer mask, then the if the velocity is less than `0f` and `isGrounded` is true, the velocity is set to near `0f`:

```cs
public class PlayerMovementController : MonoBehaviour
{
    // …
    [SerializeField]
    public Vector3 velocity = Vector3.zero;
    [SerializeField]
    public Transform groundPosition;
    public LayerMask groundLayerMask;

    float gravity = -9.8f;
    float detectionSphereRadius = 0.5f;
    bool isGrounded;

    void Start()
    {
        controller = GetComponent<CharacterController>();
        unityService = GetComponent<IUnityService>();
        groundPosition.position = transform.position - (Vector3.up * transform.localScale.y / 2);
    }

    void Update()
    {
        float horizontal = unityService.GetInputAxis("Horizontal");
        float vertical = unityService.GetInputAxis("Vertical");
        float deltaTime = unityService.GetDeltaTime();

        Vector3 movement = MovementOnX(horizontal, deltaTime) + MovementOnZ(vertical, deltaTime);

        controller.Move(movement);
        ApplyGravity(deltaTime);
    }

    void ApplyGravity(float deltaTime)
    {
        CalculateGravityVelocity(deltaTime);
        controller.Move(velocity * deltaTime);
    }

    public void CalculateGravityVelocity(float deltaTime)
    {
        isGrounded = Physics.CheckSphere(groundPosition.position, detectionSphereRadius, groundLayerMask);

        if (isGrounded && velocity.y < 0f)
        {
            velocity.y = -1f;
        } else
        {
            velocity.y += gravity * deltaTime;
        }
    }
// …
}
```

Note that due to this changes, the other tests in `CharacterMovementTest` need to change as well:

```cs
[UnityTest]
public IEnumerator CharacterMovement_When1SecondPassed_MovesCharacter()
{
    LayerMask groundMask = new LayerMask
    {
        value = 0
    };
    GameObject go = new GameObject("Player");
    go.AddComponent<PlayerMovementController>();
    go.AddComponent<CharacterController>();
    go.AddComponent<FakeUnityService>();
    PlayerMovementController moveController = go.GetComponent<PlayerMovementController>();
    moveController.groundLayerMask = groundMask;
    moveController.groundPosition = (new GameObject("Ground position")).transform;


    Vector3 original = go.transform.position;

    yield return new WaitForSeconds(0.1f);

    Vector3 actual = go.transform.position;

    Assert.AreNotEqual(original, actual);
}

[UnityTest]
public IEnumerator MovementAndRotation_OnRotatingView_HasZXIncreasedInDifferentProportions()
{
    LayerMask groundMask = new LayerMask
    {
        value = 0
    };
    GameObject go = new GameObject("Player");
    go.AddComponent<PlayerMovementController>();
    go.AddComponent<CharacterController>();
    go.AddComponent<FakeUnityService>();
    go.AddComponent<Camera>();
    go.AddComponent<CameraMovementController>();
    PlayerMovementController moveController = go.GetComponent<PlayerMovementController>();
    moveController.groundLayerMask = groundMask;
    moveController.groundPosition = (new GameObject("Ground position")).transform;

    yield return new WaitForSeconds(0.1f);
    Vector3 origin = go.transform.position;

    yield return new WaitForSeconds(0.3f);

    Vector3 actual = go.transform.position;
    float xProportion = actual.x / origin.x;
    float zProportion = actual.z / origin.z;

    Assert.Greater(zProportion, xProportion);

}

[UnityTest]
public IEnumerator ApplyGravity_AfterSeconds_MovesCubeDown()
{
    LayerMask groundMask = new LayerMask
    {
        value = 0
    };
    GameObject go = new GameObject("Player");
    go.AddComponent<PlayerMovementController>();
    go.AddComponent<CharacterController>();
    go.AddComponent<UnityService>();
    PlayerMovementController moveController = go.GetComponent<PlayerMovementController>();
    moveController.groundLayerMask = groundMask;
    moveController.groundPosition = (new GameObject("Ground position")).transform;

    Vector3 original = go.transform.position;

    yield return new WaitForSeconds(0.1f);
    Vector3 actual = go.transform.position;

    Assert.AreNotEqual(original, actual);
    Assert.Less(actual.y, -0.05f);
    Assert.Greater(actual.y, -0.12f);

    yield return new WaitForSeconds(0.1f);
    Vector3 actual2 = go.transform.position;

    Assert.AreNotEqual(actual, actual2);
    Assert.Less(actual2.y, -0.22f);
    Assert.Greater(actual2.y, -0.33f);
}

[UnityTest]
public IEnumerator ApplyGravity_WehnHitsGround_VelocityIsZero()
{
    LayerMask groundMask = new LayerMask
    {
        value = 3
    };

    GameObject go = new GameObject("Player");
    go.AddComponent<PlayerMovementController>();
    go.AddComponent<CharacterController>();
    go.AddComponent<UnityService>();
    PlayerMovementController moveController = go.GetComponent<PlayerMovementController>();
    moveController.groundLayerMask = groundMask;
    moveController.groundPosition = (new GameObject("Ground position")).transform;

    Vector3 original = go.transform.position;

    GameObject ground = new GameObject("Ground");
    ground.transform.localScale = new Vector3(100f, 1f, 100f);
    ground.transform.position = Vector3.down * 3;
    ground.AddComponent<BoxCollider>();
    ground.layer = 3;

    yield return new WaitForSeconds(3f);
    Vector3 actual = go.transform.position;
    Assert.Less(actual.y, original.y);
    Assert.That(Mathf.Approximately(actual.y, -1.42f));
    Assert.Less(-1.3f, moveController.velocity.y);
}
```

Next step is to enable jumping with the `CharacterController` and `Gravity`.