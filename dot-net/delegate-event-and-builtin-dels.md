### Delegates

[Official Reference](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/)

#### Basic Properties

1. A **type** that references to a method with **a specific signature and return type**.
2. Similar to function pointers in C and C++, but delegates are **object-oriented**, type safe, and secure.

3. Allow methods to be passed as parameters
4. Can be used to define callback methods
5. Can be chained together; for example, multiple methods can be called on a single event. It's called multicast delegate that a delegate can **hold references to multiple methods**.

#### Why do delegates have those properties?

- Generally speaking, under the hook, when a delegate is declared, the compiler will generate a class with the name of the delegate, and it inherits from class System.Delegate.
- From this view, the generated class allows instances of the delegate to reference methods that match its signature.

***Note***: The signature of a delegate ***includes return type***. This is different from the signature in the context of method overloading that the signature is only its parameters.

**How Delegates Work Under the Hood**
1. **Declaration:**
    ```
    public delegate void MyDelegate();
    ```
    The C# compiler creates a class that represents this delegate (*MyDelegate*). This class will inherit from the System.Delegate class.

1. **Inheritance:** The generated delegate class will have several properties and methods that allow you to:
    - **Store references** to one or more methods (known as the invocation list).
    - **Invoke** the methods it references.
    - **Support multicast delegates** (i.e., a delegate that can reference multiple methods).

1. **Invocation**: When you invoke a delegate, the underlying class handles calling each of the methods in its invocation list in the order they were added.
1. **Type Safety**: Delegates provide type safety since they can only reference methods that **match their signature**, meaning the return type and parameters must match exactly.

#### How to use *delegates* basically?

1. **Declare** a delegate with its signature and return type
    ```
        [modifier] delegate [return type] [delegate-name] ([parameters]);
    ```
1. **Use** the delegate
    
    ```
    i.e.
    public class DelEx{
        public delegate void YourDelegate();

        public static void YourMethod1(){
            //do sth
        }

        public static void YourMethod2(){
            //do sth
        }

        public static void Main(){
            YourDelegate del = YourMethod1; 
            del += YourMethod2; 
            del?.Invoke(); 
        }
    }
    ```

### Events

[Official Reference](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/)

- Events are built on top of delegates.
- **Delegates** define the signature of methods that can be called when the event is triggered.
- **Events** ensure that the delegate methods are only invoked in controlled circumstances, meaning only the class (*publisher*) that defines the event can raise it (trigger it), while other classes (*subscriber*) can only subscribe to or unsubscribe from the event.

#### How to use *events*?

1. **Declare a delegate**: The event must have a delegate that defines the method signature.
1. **Declare an event**: Events are declared with the event keyword.
1. **Subscribe to the event**: Methods that should respond to the event are "subscribed" using +=.
1. **Raise the event**: The event is raised (triggered) by invoking the delegate when the specific condition occurs.

```
using System;

public class Program
{
    // Step 1: Declare a delegate that defines the signature for the event handler methods.
    public delegate void Notify();  // Delegate signature

    // Step 2: Declare an event using the delegate.
    public event Notify OnProcessCompleted;

    // Method to trigger/raise the event
    public void Process()
    {
        Console.WriteLine("Process started...");

        // Step 4: Raise the event (this will call all the subscribed methods).
        OnProcessCompleted?.Invoke();
    }

    public static void Main()
    {
        Program program = new Program();

        // Step 3: Subscribe methods to the event.
        program.OnProcessCompleted += ProcessCompletedHandler;

        // Start the process
        program.Process();
    }

    // This is the method that will be called when the event is raised.
    public static void ProcessCompletedHandler()
    {
        Console.WriteLine("Process Completed!");
    }
}
```

### Built-in Delegates (Action, Func, Predicate, Comparison)
**Summary of Built-In Delegates:**

|Delegate|	Purpose	|Parameters|	Return Type|
|-|-|-|-|
|Action|	Executes a method that does not return a value|	0 to 16	|void|
|Func|	Executes a method and returns a value|	0 to 16 (last is return type)| Any (last type)|
|Predicate|	Evaluates a condition and returns a boolean|	1 parameter	|bool|
|Comparison|	Compares two objects and returns an integer|	2 parameters|	int|
