# Self-Typing Text
## Creating the Scene

* Start by creating a scene called **TextTyping**
* Create a **Text** GameObject and center it to the middle of the screen
* Set the Font Size to 40.
* Set the Font Color to **Red**
## Text Typing Animation

Create a C# Script called **AutoType.cs**. Declare the following **variables**:
* Text - typer
* string - TextToType
* Float - **delay = 1.0f, commaPause = 0.1f, stopPause**

In the **Start()** function Start the **TypeText()** Coroutine.

Declare an **IEnumerator** function called **TypeText()**. In this function, you have to get each of the character from the **TextToType** string and show it into the **Textbox** every half second.

## Code Snippet

    public class AutoType : MonoBehaviour
    {
    public Text typer;
    [Multiline(5)] public string TextToType;
    public float delay = 1.0f;
    public float commaPause = 0.1f;
    public float stopPause;
    private void Start()
    {
        StartCoroutine(TypeText());
    }
    IEnumerator TypeText()
    {
        foreach (char c in TextToType)
        {
            typer.text += c;
        if(c == ',')
            {
                yield return new WaitForSeconds(commaPause);
            }
        else if (c == ',')
            {
                yield return new WaitForSeconds(stopPause);
            }
            else
            {
                yield return new WaitForSeconds(delay);
            }
        }
        
    }
}

Attach the **AutoType.cs** script to your **Text** GameObject.
