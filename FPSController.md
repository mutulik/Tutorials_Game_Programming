# FPS Controller
## Creating the Scene

* Create a Scene called **FirstPersonGame**.
* Create an Empty **GameObject** and call it **Player**
* Create a **Cylinder** and make it a **Child Object** of the **Player** GameObject.
* Create an Empty GameObject and make it a Child Object of the **Player** GameObject.
* Attach the MainCamera in your Scene to the **Player GameObject**.

## Player Movement

Create a C# Script and call it **PlayerMovement.cs**. Declare the followning **variables**:
* CharacterController - controller
* float - gravity = 9.81, groundDistance, speed = 12
* Transform - groundCheck
* LayerMask - groundMask


In the **Update()** function, set a Physics Sphere Check to check the distance to the ground selected in order for it to detect how close the player is to the ground. 

Declare a float called **x** which checks the **Horizontal Inputs** which are A & D or <- & -> keys. Declare a float called **z** which checks the **Vertical Inputs**, which are W & S keys or Up & Down arrow keys.#

Declare a **Vector3** variable called **move** to get the player to move accordingly to the inputs and detects the gravity and calculates it using Time rather than frames.

## Code Snippet

    public class PlayerMovementManual : MonoBehaviour
    {
    public CharacterController controller;
    public float speed = 12f;
    public float gravity = -9.81f;
    public Transform groundCheck;
    public float groundDistance;

    public LayerMask groundMask;

    bool isGrounded;
    Vector3 velocity;
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        isGrounded = Physics.CheckSphere(groundCheck.position, groundDistance, groundMask);
        
        if(isGrounded && velocity.y < 0)
        {
            velocity.y = -2f;
        }

        float x = Input.GetAxis("Horizontal");
        float z = Input.GetAxis("Vertical");
        Vector3 move = transform.right * x + transform.forward * z;

        controller.Move(move * speed * Time.deltaTime);
        velocity.y += gravity * Time.deltaTime;

        controller.Move(velocity * Time.deltaTime);
    }
    }

## Mouse Look

Create a C# Script called **MouseLook.cs**. Declare the following **variables**:

* float - mouseSensitivity = 100
* Transform - playerBody
* float - xRotation

In the **Start()** function lock the mouse and set it to invisible.

In the **Update()** function get the **Mouse Rotation** calculating its movement through Time rather than frames. You also need to clamp the **Y-Rotation** to 90 degrees. 

## Code Snippet

    public class MouseLook : MonoBehaviour
    {
    public float mouseSensitivity = 100f;
    public Transform playerBody;
    public float xRotation;
 
    private void Start()
    {
        Cursor.lockState = CursorLockMode.Locked;
    }

    void Update()
    {
        float mouseX = Input.GetAxis("Mouse X") * mouseSensitivity * Time.deltaTime;
        float mouseY = Input.GetAxis("Mouse Y") * mouseSensitivity * Time.deltaTime;

        xRotation -= mouseY;

        
        xRotation = Mathf.Clamp(xRotation, -90f, 90f);

        transform.localRotation = Quaternion.Euler(xRotation, 0, 0);
        playerBody.Rotate(Vector3.up * mouseX);
        
    }
    }
