### When a coroutine stops?

1. **Completion of Execution**
    - reach the end of its code block.
1. **When Stopped Manually**
    ```
    Coroutine myCoroutine;
    myCoroutine = StartCoroutine(MyCoroutine());
    StopCoroutine(myCoroutine);
    ```
1. **When the Object is Disabled or Destroyed**
    - In Unity, coroutines are tied to the MonoBehaviour instance, but they will **continue running as long as the GameObject itself is active**, even if the script component is disabled.
    - So, if you want a coroutine/all coroutines stop when the MonoBehavior instance (the script component) disabled, you must stop them manually in OnDisable
    ```
    private void OnDisable()
    {
        StopCoroutine(myCoroutine); //StopAllCoroutines();
    }
    ```
1. **When the Game is Exited**
    - Game stops or when switching scenes
    - If you want to ensure coroutines **continue across scenes**, they need to be managed carefully, such as using ***DontDestroyOnLoad*** for persistent game objects.
1. **Exception Thrown**
    -  If an **unhandled** exception occurs **within** the coroutine, it will stop **prematurely**.