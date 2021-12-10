# Click to Move

## Creating the Scene

* Start by creating a scene called **ClickTesting**
* Add a 3D Cube called **Player** and a 3D cube called **NPC**
* Make two materials with the colors of your choice and assign them to the 3D cubes

## Player Movement

Create a C# Script called **PlayerMovement.cs**. Declare a **NavMeshAgent** variable called **agent**. In the **Start()** function, get the Component **NavMeshAgent** and attach it to the agent **variable**.

In the **Update()** function, declare a **RaycastHit** variable called **hit**, this gets the point at which a ray is getting casted from a certain selected **GameObject**. Then declare a **Ray** variable called **ray** to declare what triggers the Raycasting. In this case the Raycast is being transmitted from the **MainCamera** with the help of the **Mouse Input**. You need an **IF Statement** to check where the **Raycast** hits and sends the player in the direction which the **Raycast** hits.

## Code Snippets
    public class PlayerMovement : MonoBehaviour
    {
        NavMeshAgent agent;
    
    void Start()
    {
        agent = GetComponent<NavMeshAgent>();
    }


    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            RaycastHit hit;
            Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
            if(Physics.Raycast(ray, out hit, Mathf.Infinity)) 
            {
                agent.SetDestination(hit.point);            
            }
        }
    }
}

## NPC Interaction

Create a C# Script called **Interaction.cs**. Declare the following variables:
* Text - message
*  LayerMask - shootable
* Reference to **PlayerMovement.cs**
* RaycastHit - hit

In the **Start()** function you need to assign the Component **PlayerMovement** to the script variable.

In the **Update()** function you need to check for **MouseInput** and if the Left Mouse Click is pressed the **Hits()** function is being called.

In the **Hits()** function, you need to do the same steps as you did with the **PlayerMovement.cs** however when the mouse Raycasts onto the target a Text will pop up and the Player will not move. 

Code Snippets

    public class Interaction : MonoBehaviour
    {
    public Text message;
    public LayerMask shootable;
    PlayerMovement script;
    RaycastHit hit;
    private void Start()
    {
        script = GetComponent<PlayerMovement>();
    }
    private void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            Hits();
            
        }
    }
    public void Hits()
    {
        Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
        if(Physics.Raycast(ray, out hit, 1000, shootable)){
            script.enabled = false;
            message.gameObject.SetActive(true);
            script.enabled = true;
        }
    }
  
}
