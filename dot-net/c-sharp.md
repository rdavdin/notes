## C# 

- decimal vs double ?
- events in a class ?
- 
- primitives are actually implemented as objects underlying
- Stateful versus stateless methods
  - stateful methods as instance methods
  - stateless methods as static methods/class methods

- **Interfaces** can have properties. This is opposite of Java
- **Delegates** are reference types that serve a purpose similar to that of function pointers in C++. They are used for event handlers and callback functions in .NET. 
  - A parameter of a delegate is compatible with the corresponding parameter of a method if **the type of the delegate parameter is more restrictive** than the type of the method parameter, because this guarantees that an argument passed to the delegate can be passed safely to the method.
  - Similarly, the return type of a delegate is compatible with the return type of a method if **the return type of the method is more restrictive** than the return type of the delegate, because this guarantees that the return value of the method can be cast safely to the return type of the delegate.
- In default, delegate in C# is **multicast**.

  - **```Object → Delegate → MulticastDelegate```**
  - A ```MulticastDelegate``` has **a linked list of delegates**, called an invocation list, consisting of one or more elements. When a multicast delegate is invoked, the delegates in **the invocation list are called synchronously in the order in which they appear**. If an error occurs during execution of the list then an exception is thrown.

- **Nested types** are similar to nested types in Java.
  - Nested types are **useful when the declaring type uses** and creates instances of the nested type, and use of the nested type is **not exposed** in public members.

- **Overloading**: Method **signatures** consist of the **method name and a  parameter list**, **not** include **return types**
- **Generic types** - refer to [generics](https://learn.microsoft.com/en-us/dotnet/standard/generics/)
  - By using **bounded type parameters**, you can ensure that the generic class or method can **only accept specific types or types that meet certain criteria**, providing additional type safety and enabling you to utilize specific methods or properties of the bounded types.

 ```
  public class Box<T> where T : IComparable
{
    private T value;

    public Box(T value)
    {
        this.value = value;
    }

    public T GetValue()
    {
        return value;
    }

    public void SetValue(T value)
    {
        this.value = value;
    }

    public bool IsGreaterThan(T other)
    {
        return value.CompareTo(other) > 0;
    }
}
  ```

  - [.NET provides a number of generic collection classes](https://learn.microsoft.com/en-us/dotnet/standard/generics/#class-library-and-language-support)

- **string**: generally speaking, ```string``` in C# is approached as ```string``` in Java. In terms of working with ```string```, we should use utilities provided by the language.
  - [Official documentation on working with strings](https://learn.microsoft.com/en-us/dotnet/csharp/how-to/#working-with-strings)
  - [programming guide - C# - strings](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/strings/)
- **abstract class**: ```virtual``` methods vs ```abstract``` methods ?
  - ```virtual``` method is as the default implementation of a base class. Derived classes can or not override them.
  - ```abstract``` method is as an interface without implementation. 
  - method **without** ```virtual``` or ```abstract``` is implicitly ```sealed```. It's got an **compilation error** if a derived class overrides it.

- **interface**: Interfaces are data structures that contain member definitions and not actual implementation. They are useful when you want to define a **contract** between members in different types that have different implementations. You can declare definitions for methods, properties, and indexers.

### CLR vs JVM

Source: [CLR vs JVM: How the Battle Between C# and Java Extends to the VM-Level](https://www.harness.io/blog/clr-vs-jvm)

- CLR: Common Language Runtime
- JVM: Java Virtual Machine

**Top similarities between the CLR and JVM include:**

- Both Virtual Machines (VMs)
- Both include garbage collection
- Both employ stack-based operations
- Both include runtime-level security
- Both have methods for exception handling

**Top differences between the CLR and JVM include:**

- CLR was designed to be language-neutral, JVM was designed to be Java-specific
- CLR was originally only Windows-compatible, JVM works with all major OSs
- CLR uses a JIT compiler, JVM uses a specialized JIT compiler called Java HotSpot
- CLR includes instructions for closures, coroutines and declaration/manipulation of pointers, the JVM does not
- JVM is compatible with more robust error resolution and production monitoring tools
- CLR allows users to define **new value-types** in the form of ```Structs```, while value-types in JVM-based languages are fixed (```byte, short, int, long, float, double, char, boolean```)

**Generic types?**

- The CLR uses **reification** to implement generic types, treating each instance as a separate concrete type. This means that at runtime, each instance of a generic type is treated as a separate concrete type. The CLR generates specialized code for each specific type used with the generic type, resulting in **improved performance but also increased memory usage**.
- The JVM uses type **erasure**, removing the generic type information at runtime and treating instances as if they were of the raw type. This approach can lead to **more efficient memory usage** but the potential of runtime errors if the code does not handle type casting correctly.

*Example:*

- In C#, you can define a generic class, such as a ```List<T>```, where T represents the type of elements in the list. When you create an instance of ```List<int>```, for example, the CLR generates specialized code specifically for ```List<int>```. This means that the runtime treats ```List<int>``` as a separate concrete type, distinct from ```List<string>``` or any other type used with ```List<T>```. This allows the CLR to optimize the code and provide better performance for each specific type.
- In Java, generic types are erased at runtime, and the JVM treats instances of generic types as if they were of the raw type. This means that the generic type information is not available at runtime. For example, if you have a ```List<Integer>```, the JVM treats it as if it were a ```List``` without any type parameter.

### [.NET Glossary](https://learn.microsoft.com/en-us/dotnet/standard/glossary)