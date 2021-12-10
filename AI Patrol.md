# AI Patrol & Attack

## 1. Creating a New Scene 

* Start by creating a scene called **Patrol**
* Add a 3D Cube called **Player** and a 3D cube called **AI**
* Make two materials with the colors of your choice and assign them to the 3D cubes
* Add the tag ** Player ** onto your Player 3D Cube
* Go to Window -> AI -> Navigation
* Select your walkable ground
* Click on the **Object** tab and then click on the Navigation Static Checkmark
* Make sure the **Navigation Area** is set to **Walkable**
* Click on the **Bake Tab** and then on the **Bake Button**

## 2. Vision Cone

### Create a C# Script called **Vision.cs** and assign it to the **AI Game Object**

What this C# Script does, is that it adds a **Debug** Sphere and Cone to the **AI GameObject**  to give the developer an idea of what the area of detection is. 

* Declare a  **Unity Events** variable called **onSpotted**
* Declare **variable** of type **float** to calculate **viewDistance** and set it to 10.
* Declare another float variable called **FoV** to determine the angle that  the AI can detect. 
* Declare a **Transform** variable called **character** to store your character **GameObject**
* Declare another **Transform** variable for your target which is **internal**
* Lastly, Declare a **Vector3** variable called **lastKnownPosition** to store the previous position each time the **AI** goes through it.

### Code Snippet

    public class Vision : MonoBehaviour
    {
        public float viewDistance = 10.0f;
        public float FoV = 90.0f;
        public UnityEvent onSpotted;
        Transform player;
        internal Transform target;
        internal float RangeToTarget;
        internal Vector3 lastKnownPosition;
    private void Awake()
    {

    }   
In your **Awake()** function you need to declare one more **GameObject** variable called **playerObject** and get it to Find the **GameObject** with Tag **"Player"**. You will also need an if statement to check whether there are any **GameObjects** attached to this variable, if there are not, you need to assign the **playerObject** variable to the **player** variable declared.

Your **Awake()** function should look like the following:

     private void Awake()
    {
        GameObject playerObject = GameObject.FindGameObjectWithTag("Player");
        if(playerObject != null)
        {
            player = playerObject.transform;
        }
    }

You need to call the function **OnDrawGizmos()** to get the **Sphere** and **Cone** to show in the **Unity Editor**. In this Function you need to set the color of the lines to red, then set the **World Matrix Transform** to **Local Matrix Transform**. Further on, you need to use **DrawFustrum()** and **DrawWireSphere()**around the area of the floats **FoV** and **viewDistance** with Vector3 set to **0**.

### Code Snippet

    private void OnDrawGizmos()
    {
        Gizmos.color = Color.red;
        Gizmos.matrix = transform.localToWorldMatrix;
        Gizmos.DrawFrustum(Vector3.zero, FoV, viewDistance, 0.5f, 1.0f);
        Gizmos.DrawWireSphere(Vector3.zero, viewDistance);
    }

Lastly, in the **Update()** function you need an **IF Statement** to check whether the **player** variable is assigned or not. If **player** is not assigned then you need to get the **distance** to the **player** GameObject. After getting the distance, you need to check whether the distance is larger than the sphere coverage or if it is in the sphere coverage, and if it is and the **AI** doesn't have a target, then the **State** changes to **Chase**.

### Code Snippet

    private void Update()
    {
        if(player != null)
        {
            Vector3 toPlayer = player.transform.position - transform.position;
            float distance = toPlayer.magnitude;
            if (distance < viewDistance && Vector3.Angle(toPlayer, transform.forward) < 0.5f * FoV)
            {
                RangeToTarget = distance;
                if(target == null)
                {
                    onSpotted.Invoke();
                }
                target = player.transform;
            }
            else
            {
                if(target != null)
                {
                    lastKnownPosition = target.transform.position;
                }
                target = null;
            }
        }
    }
## 3.  Player Chase

Create a C# Script called **Chase.cs**.
The only **Functions** you need in this script are:

* **Awake()**
* **Update()**

Before the **Awake()** function, you need to declare the following variables:
* Float - **attackRange** with value **2.0f**
* **UnityEvents** - **inRange** and **targetLost**
* **NavMeshAgent agent**
* Reference to the **Vision.cs** script

In the **Awake()** function you need to get the Components **NavMeshAgent** assigned to the **agent** variable and **Vision** script assigned to the **vision** variable.

### Code Snippet

 
    public class Chase : MonoBehaviour
    {
        public float attackRange = 2.0f;
        public UnityEvent inRange;
        public UnityEvent targetLost;
        NavMeshAgent agent;
        Vision vision;
    private void Awake()
    {
        agent = GetComponent<NavMeshAgent>();
        vision = GetComponent<Vision>();
    }
In the **Update()** function, you need an **IF Statement** to check whether the target in the **Vision.cs** script is null, if it is then the script needs to check whether the **Player** is in range. If it is **inRange** the script will **invoke** the **UnityEvent** called **inRange**. Otherwise the script will set its **destination** to the target position. If the target is not null, then the **targetLost UnityEvent** will get invoked.

### Code Snippet

     private void Update()
    {
        if (vision.target != null)
        {
            if(vision.RangeToTarget < attackRange)
            {
                inRange.Invoke();
            }
            else
            {
                agent.SetDestination(vision.target.position);
            }
        }
        else
        {
            targetLost.Invoke();
        }
    }
## 4.  Idle Patrol

Create a C# Script called **Patrol.cs** that requires a **NavMeshAgent** component. Under the class, declare the following variables:

* UnityEvent - **OnWaypointReached**
* Array - **Waypoints** wayPoints
* NavMeshAgent - agent

In the **Awake()** function you need to get the component **NavMeshAgent** into the **agent** variable. 

Declare an **OnEnable()** function, which randomizes the **wayPoints** selection and sets the **Destination** of the AI to a random Waypoint.

In the **Update()** function, you need an **IF Statement** to check whether the AI has a path set, if it does not then the **Patrol.cs** script will be disabled and **onWaypointReached UnityEvent** will be invoked.

### Code Snippet

    [RequireComponent(typeof(NavMeshAgent))]
    public class Patrol : MonoBehaviour
    {
        public UnityEvent onWaypointReached;
        public Waypoint[] wayPoints;
        NavMeshAgent agent;

        void Awake()
        {
            agent = GetComponent<NavMeshAgent>();
        }

        void OnEnable()
        {
            Waypoint wayPoint = wayPoints[Random.Range(0, wayPoints.Length)];
            agent.SetDestination(wayPoint.transform.position);
        }

        void Update()
        {
            if (!agent.hasPath)
            {
                enabled = false;
                onWaypointReached.Invoke();
            }
        }
## 5. Shooting the Player

Create a C# Script called **Shoot.cs** (can be replaced with own attack script). Declare a function called **Attack()**, which will send a **Debug** message when called.

### Code Snippet

    public class Shoot : MonoBehaviour
    {
    public void Attack()
    {
        Debug.Log("shoot");
    }
}
## 6. Attacking Detection

Create a C# Script called **Attack()**. Declare the following variables:

* float - **attackRange**
* UnityEvent - **attack, lostTarget, OutOfRange**
* Reference to **Vision.cs**

In the Awake() function you need to get the component **Vision.cs** attached to the **vision** variable.

In the Update() function you need to check whether the target in the **vision** variable is null or if it is set. If the target is null, then **lostTarget** will be invoked, if **RangeToTarget** is bigger than **attackRange** then **OutOfRange** will be Invoked and if none of the conditions above are met, the **attack** variable will be invoked.

### Code Snippet

    public class Attack : MonoBehaviour
    {
    public float attackRange;
    public UnityEvent attack;
    public UnityEvent lostTarget;
    public UnityEvent OutofRange;
    Vision vision;
    private void Awake()
    {
        vision = GetComponent<Vision>();

    }
    private void Update()
    {
        if(vision.target == null)
        {
            lostTarget.Invoke();
        }
        else if(vision.RangeToTarget > attackRange)
        {
            OutofRange.Invoke();
        }
        else
        {
            attack.Invoke();
        }
    }
## 7. AI State Change

Create a C# Script called **AI.cs**. Declare a List of Behaviours called States and declare a function called **ChangeState()** declaring a new Behaviour variable called **newState** in which you check each state and enable each one in the order of the state that is declared. 

### Code Snippet

    public class AI : MonoBehaviour
    {
    public List<Behaviour> states = new List<Behaviour>();
    public void ChangeState(Behaviour newState)
    {
        states.ForEach((state) => state.enabled = (state == newState));
    }
    }

To see this component in action please refer to **Example.scene** from **Tutorial 1** folder situated in this **Unity Package**.

## How to Setup the Scripts in the Editor

All references to Behaviour States need to be called through the **AI.cs** script.
### Vision


For the **OnSpotted** Unity Event you need to import the **Chase** state through the use of **AI.ChangeState**
### AI

The four states that need importing into the **AI** component are:
1. Patrol
2. Chase
3. Attack
4. Investigate

### Chase

Set the attack range to 2.

* When In Range set the State to Attack.
* When Target Lost set the State to Investigate.
### Attack


Set Attack Range to 2.

* On **Attack** State set the State to **Shoot.Attack**
* When **Lost Target** Set State to Investigate
* When **Out of Range** Set State to Chase

### Investigate


* When **On Target Not Found** Change State to Patrol
### Patrol


Set Waypoints to the Waypoints you have created

When **On Waypoint Reached** change to Patrol State. 

## Note: In order to set obstacles in the scene, you will need to follow the last 5 steps from Step 1 but instead of setting Navigation Area to Walkable, you will need to set it to Not Walkable.
