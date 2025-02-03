# Beginner Game Development - Lab - Fine Tuning a 2D Platformer (Part 1)

*In this lab, we will create a **2D platformer project** in Unity and implement a **basic player controller** with movement and jumping. The controller will be structured for easy extension, allowing us to add more advanced features in future sessions.*

![Platformer Examples - Trinki by Rob Blofield](<Platformer Player Controller (Trinki).gif>)

### **Core Concept: Rigidbody2D**  
Unity's **Rigidbody2D** component allows GameObjects to interact with physics in a 2D environment. It enables movement, collisions, and forces like gravity. In this project, we use **Rigidbody2D** for our player character instead of manually adjusting the position, which allows for smooth physics-based movement and future extensions like wind resistance and velocity fall-off.

### **Core Concept: Colliders & Physics Layers**  
**Colliders** define the physical shape of a GameObject for interactions in Unity's physics system. For example, the **BoxCollider2D** on the player and **Tilemap Collider 2D** on the ground prevent objects from passing through each other. Understanding **collision layers** allows us to specify what objects should interact, such as making sure enemies and walls block movement but background elements do not.

### **Core Concept: Tags & Collision Detection**  
Unity uses **tags** to categorize GameObjects, allowing scripts to efficiently identify objects during collisions. In this lab, we tag the **ground** as `"Ground"` and check for collisions in our player script to determine when the player is standing on a surface. This is essential for implementing jumping mechanics and later advanced techniques like **coyote time** or **wall jumps**.

### **Core Concept: Input System & GetAxisRaw()**  
Unity’s **Input System** allows players to control objects using keyboard, mouse, or controller inputs. We use `Input.GetAxisRaw("Horizontal")` to detect movement direction, ensuring the player moves left or right smoothly. This method is preferable to checking individual key presses because it provides a single, scalable approach that supports **multiple input types** (e.g., keyboard and gamepad).

### **Core Concept: Velocity-Based Movement**  
Instead of modifying an object’s position directly (`transform.position`), we use `Rigidbody2D.velocity`. This ensures movement integrates naturally with Unity's physics system, allowing for smoother control and advanced mechanics like acceleration, friction, and gravity modifications.

### **Core Concept: Gravity Scale**  
The **Gravity Scale** property of a **Rigidbody2D** determines how strongly gravity affects an object. Instead of using Unity’s default gravity, we can customize **Gravity Scale** to fine-tune player physics. This will be useful when implementing mechanics like **variable jump height** and **custom gravity zones**.

### **Core Concept: Jumping & Ground Detection**  
Jumping in Unity requires detecting when the player is on the ground to prevent infinite jumps. We accomplish this by:  
1. **Tracking collisions** with the ground using `OnCollisionEnter2D()`.  
2. **Using boolean flags (`isGrounded`)** to determine when jumping is allowed.  
Later, we can extend this system with **coyote time**, **buffered jumps**, and **jump height control**.

### **Core Concept: Extensibility & Modular Code Design**  
When writing game scripts, it’s important to structure them in a way that makes adding new features easy. By separating movement logic (`HandleMovement()`) and jumping logic (`HandleJump()`), we ensure the script remains clean and modular. This approach allows us to easily introduce new mechanics like **double jumps, wall jumps, and physics effects** in future labs.

### Additional Notes/Documentation
* [Unity Documentation - Manual - Unity 2D](https://docs.unity3d.com/6000.0/Documentation/Manual/Unity2D.html)
* [Unity Documentation - Unity 2D Development Resources](https://docs.unity3d.com/6000.0/Documentation/Manual/2d-game-development-landing.html)
* [Unity Documentation - Unity 2D Renderer Sorting](https://docs.unity3d.com/6000.0/Documentation/Manual/2d-renderer-sorting.html)

---

## Section 1 - Setting Up the Project

### Step 1: Create a New 2D Unity Project
1. Open **Unity Hub** and create a new **2D Core** project.
2. Name it `"2DPlatformerLab"`.
3. Choose a location and click **Create**.

### Step 2: Configure the Scene
1. In the **Hierarchy**, right-click and create a `2D > Sprite` object, name it `"Player"`.
2. Create a **Tilemap**:
   - `GameObject > 2D Object > Tilemap > Rectangular Tilemap`
   - Rename the **Grid** to `"Level"`.
   - Select `"Tilemap"` inside it and rename it `"Ground"`.
3. Create a **Main Camera Setup**:
   - Select the **Main Camera**.
   - Set the **Projection** to **Orthographic**.
   - Adjust **Size** (e.g., 5) to frame the playable area.

### Step 3: Add Required Components
1. Select `"Player"`, add the following components:
   - **Sprite Renderer** (Assign a placeholder sprite)
   - **Rigidbody2D** (Set Gravity Scale to `1`, Interpolate to `Interpolate`)
   - **BoxCollider2D** (Adjust size to fit sprite)

2. Select `"Ground"`, add:
   - **Tilemap Collider 2D**
   - **Rigidbody2D** (Set **Body Type** to *Static*)

---

## 2. Creating the Player Controller

We will structure the player movement system to allow easy modifications in the future.

### Step 4: Create the Player Controller Script
1. In the **Project window**, create a folder `"Scripts"`.
2. Inside `"Scripts"`, create a C# script named `"PlayerController"` and attach it to the `"Player"` GameObject.

---

### Step 5: Implement the PlayerController Script

```csharp
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    [Header("Movement Settings")]
    public float moveSpeed = 5f;
    public float jumpForce = 10f;
    
    [Header("References")]
    private Rigidbody2D rb;
    
    private bool isGrounded;

    void Awake()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        HandleMovement();
        HandleJump();
    }

    void HandleMovement()
    {
        float moveInput = Input.GetAxisRaw("Horizontal"); // Get movement input (-1, 0, 1)
        rb.velocity = new Vector2(moveInput * moveSpeed, rb.velocity.y); // Maintain vertical velocity
    }

    void HandleJump()
    {
        if (Input.GetKeyDown(KeyCode.Space) && isGrounded)
        {
            rb.velocity = new Vector2(rb.velocity.x, jumpForce);
        }
    }

    private void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("Ground"))
        {
            isGrounded = true;
        }
    }

    private void OnCollisionExit2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("Ground"))
        {
            isGrounded = false;
        }
    }
}
```

---

## 3. Testing the Player Controller

1. **Assign the script** to the `Player` GameObject.
2. **Tag the Ground**:
   - Select `"Ground"` and set its **Tag** to `"Ground"`.
3. **Play the game**:
   - Use **A/D or Left/Right Arrow keys** to move.
   - Press **Space** to jump.

---

## Section 2 - Lab Task

Now that we have a basic player controller, The next step is to extend the system. In your lab group discuss how you might track what type of surface your player is standing on (e.g `Normal`, `Slippery`, `Sticky` etc). Based On these variable scenarios, write new methods/variables that will manipulate the player controller.
#### Minimum requirements
- Speed change when running on different surfaces
- Debug Log Message confirming the current surface
#### Desirable requirements
- Acceleration/Deceleration of player movement
- Variable top speed based on running on different surfaces
- Variable acceleration/deceleration based on different surfaces
- variable jump height based on different surfaces
#### Extension task (Optional Homework - for eager beavers! 🤓)
- Refactor acceleration/deceleration using `Animation Curves`
- Sprite animations for Walk/Run/Slip/Stick/Jump/Fall/Land
- Implement animations changes

---
## Next Steps

In future labs, we will enhance the **player physics** by adding:
- **Better Jump Control (Coyote Time, Variable Jump Height)**
- **Wall Sliding and Wall Jumping**
- **Custom Gravity and Wind Resistance**

---

## Summary

✅ Created a **new 2D Unity project**  
✅ Set up a **player object** with physics  
✅ Implemented **basic movement & jumping**  
