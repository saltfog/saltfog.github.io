---
layout: post
title: C# common practices
description: Here are some sort of C# common coding practices I have been using in my coding style at work.
keywords: csharp common practices, self-documented code, code optimization, good coding practices
tags: [CSharp, Best Practices]
comments: true
---

There are a lot of articles about C# good common practices available online, some may written in any programming book. Just _google_ it, the magic is there! Previously, I don't really care about the good common practices in my C# programming, as long as my application works. Just a couple of years ago I started working in team of developers, in big software company, then the coding standards have been practiced widely among software developers.

So, here are some of my common coding practices I have been using for C# programming. If you're a beginner or new .NET developer, these coding practices might be useful for you. Let's begin!

### Use of proper naming conventions

Use of proper naming conventions ensure you an easy way to maintain your code, especially when the software is big or has thousand lines of code. You can use Camel case or Pascal when declaring any variable or property. Camel case is a word with the first letter lowercase, while others are capitalized. Pascal case is a word the first letter capitalized and followed by others.

Example:

```csharp
// Declaring variables using camelCase
private double numberOfSamples;
private int primeNumber;
private object myObject

// Declaring public properties using PascalCase
public double Distance { get; set; }
public int NumberOfChannels { get; }
public object MyObject { get; private set; }

// Private methods using camelCase
private bool isValid(double value) { ... }
private void getUrlFromServer() { ... }

// Public methods using PascalCase
public double GetNumberOfSamples() { ... }
public bool IsUrlWorking(string url) { ... }
```

#### Private vs Public

```csharp
// Example of my naming conventions I usually
// use for private members, can be camelCase or PascalCase
// but with a prefix of "_" character.
private string _studentName;
private int _BigNumber;
private void _generateRandom() { ... }

// For public members I usually use PascalCase.
public string TeacherName { get; set; }
public void ShowExample(bool display) { ... }
```

#### Constant variables

I usually use all uppercase when declaring constant variables.

Example:

```csharp
public const int INVALID_VALUE = -1;
public const string ERROR_MSG = "Application crashed. Please check the log file."
```

### Good naming practices

- Recommended to use prefix like `Is`, `Has` or `Can` for **Boolean** properties, e.g. `IsVisible`, `HasChildren`, `CanExecute`. These will give proper meaning to the properties.
- Never use a name that begins with a numeric character. For example, instead of `1Person`, you can define as `OnePerson`.
- Always use meaningful names for class, property, method, variable, etc. Make it easily understandable. Good code is self-documented. For example, instead of `Meas` class name, best to put in a complete term as `Measurement`.
- Always use `I` as a prefix for Interface. This is a common practice for declaring interfaces.

Example:

```csharp
public interface IMyInterface
{
  void MethodToImplement(); // Abstract method signature
  double MyProperties { get; }
}

public class MyInterfaceImplementer : IMyInterface
{
  // Constructor
  public MyInterfaceImplementer()
  {
    MyInterfaceImplementer obj = new MyInterfaceImplementer();
    obj.MethodToImplement();
  }

  public void MethodToImplement()
  {
    // Abstract method implementation here
  }
}
```

#### Class naming practices

I always add `Exception` as suffix to my custom exception class name. This will give better visibility to my exception class.

Example:

```csharp
public class LoginException : System.Exception
{
  // TODO stuffs here...
}
```

I never prefix or suffix the class name to its property names.

Example:

```csharp
public class Person
{
  public string PersonFirstname { get; set; } // [❌] I DON'T DO THIS
  public string Firstname { get; set; } // [✓] I DO THIS INSTEAD
}

static void Main()
{
  // Because I can easily identify it from that class directly.
  Person person = new Person();
  Console.WriteLine(person.Firstname); // <--
}
```

### Other coding practices

I would use **Nullable Data Type** whenever required.

Example:

```csharp
int index = 0; // Simple declaration of int
int? index = null; // Nullable data type declaration
```

**I usually use runtime constants over compile time constants.** Runtime constants are those being evaluated at the runtime and declared with the keyword `readonly`. On the other side, compile time constants are `static`, evaluated at the time of compilation and declared with the keyword `const`;

Example:

```csharp
public readonly string LOG_FILENAME = "app.log"; // Runtime constants
public const string LOG_FILENAME = "app.log"; // Compile time constants
```

So, what is the need to prefer `readonly` over `const` variables? Compile time constants (`const`) must be initialized at the time of declaration and can’t be changed later. Also, they are limited to only numbers and strings. The IL replaces the `const` variable with the value of it over the whole code and thus it is a bit faster. Whereas, the Runtime constants (`readonly`) are initialized in the constructor and can be changed at different initialization time. The IL references the `readonly` variable and not the original value. So, when you have some critical situation, use `const` to make the code run faster. When you need a reliable code, always prefer `readonly` variables.

**I usually use `string.Format()` or `StringBuilder` for string concatenation.** It's because of better memory management! `string.Format()` will not create multiple objects and `StringBuilder` is an immutable object which will not create separate memory for each operation.

**I would properly utilize try-catch-finally blocks.** If I write code that may throw some Exception, I use the `try-catch` block for that piece of code to handle the exception. Unnecessary surrounding lines of code with `try-catch` will slow down my application. I use it only when required. Sometimes, I use the `finally` block to clean up any resources after the call, e.g. if I'm doing any database call, I have to close the connection in this block.

Example:

```csharp
void ReadFile(int index)
{
  // To run this code, substitute a valid path from your local machine
  string path = @"c:\users\public\test.txt";
  System.IO.StreamReader file = new System.IO.StreamReader(path);
  char[] buffer = new char[10];
  try
  {
    file.ReadBlock(buffer, index, buffer.Length);
  }
  catch (System.IO.IOException e)
  {
    Console.WriteLine("Error reading from {0}. Message = {1}", path, e.Message);
  }
  finally
  {
    if (file != null)
    {
      file.Close();
    }
  }
  // Do something with buffer...
}
```

**I would use IDisposable interface for any resource consumption operation.** `IDisposable` interface is to free all the resources from the memory. Once I implement `IDisposable` interface in my class, I will get a `Dispose()` method there. So, I write the code there to free the resources.

Example:

```csharp
public class User : IDisposable
{
  public int id { get; protected set; }
  public string name { get; protected set; }
  public string pass { get; protected set; }

  public User(int userID)
  {
    id = userID;
  }
  public User(string Username, string Password)
  {
    name = Username;
    pass = Password;
  }

  // Other functions go here...

  public void Dispose()
  {
    Dispose(true);
    GC.SuppressFinalize(this);
  }

  protected virtual void Dispose(bool disposing)
  {
    if (disposing)
    {
      // free managed resources
    }
    // free native resources if there are any.
  }

}
```

**Note:** If you're using `using(...) { ... }` block, it will call the `Dispose()` method automatically to free up the class resources. You will not have to call the `Dispose()` explicitly for the class.

**I usually split my logic in several small and simple methods.** If methods are too long, sometimes it's difficult to handle them. This is why I need to break them in separate methods and in future I may need to call one part, it will be easier to call rather than replicating the code. This is also easier to do unit testing for the small chunks rather than a big code. Based upon that, I extract my code in small simple methods and call them from wherever I want. In general, a method should never be more than 10-15 lines long.

Example:

```csharp
// Assuming I'm going to calculate the force due to
// gravitational acting on two bodies of mass A kg and B kg
// separated by the distance X cm?
//
// Formula, F = (G * m1 * m2) / r^2
//

public double GetDividend(double gravityConstant, double mass1, double mass2)
{
  // Return G * m1 * m2
}

public double GetDivisor(double distance)
{
  // Convert distance from cm to m
  // Return distance with square
}

public double GetForce(double dividend, double divisor)
{
  // Return result of calculation = dividend / divisor
}

static void Main()
{
  var result = GetForce(GetDividend() / GetDivisor());
  Console.WriteLine(result.ToString());
}
```
