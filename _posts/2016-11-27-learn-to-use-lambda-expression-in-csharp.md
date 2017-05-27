---
layout: post
title: Learn to use Lambda Expression in C#
description: Lambda expression helps me to write coding faster and shorter in C#. Let's learn how we can use lambda expressions, delegates, predicates and closures.
keywords: lambda expressions, c# programming, anonymous functions, lambda operator, c# delegates, c# predicates, c# closures
tags: [Lambda Expression, CSharp]
comments: true
---

### Lambda expression

Basically, a lambda expression is an anonymous function that we construct with the lambda operator `=>`.

The following example will create a function that always return the integer 7:

```csharp
Func<int> getSeven = () => 7;
int numberSeven = getSeven();
// Variable 'numberSeven' will now contain 7
```

We also can pass parameters to the function by declaring them inside the parenthesis:

```csharp
Func<int, int, int> addXandY = (x, y) => x + y; // x + y is expression
int total = addXandY(5, 4);
// Variable 'total' will now contain 9
```

If we have more extensive function, we can wrap the function body with curly brackets. This is called a statement lambda:

```csharp
Func<string> getUnit = () => "kg";
Func<int, int, string, string> getTotalAndUnit = (a, b, unit) =>
{
    int _total = a + b;
    return string.Format("Total: {0} {1}", _total, unit);
};
string result = getTotalAndUnit(5, 10, getUnit());
// Variable 'result' will now contain "Total: 15 kg"
```

When we are using the extensive form of lambda expression and want to return a value, we will need to use the return statement just like a ordinary method.

I usually use the statement lambda for my event handler if there is just a simple code operation, for example:

```csharp
// Let's say I have a manager with singleton pattern
// that contains a public event property like this:
public event EventHandler<bool> OnDataPointsUpdated;

// Then, somewhere in my other class
// I subscribe the event at the class constructor.
// Here I just create a statement lambda for the event handler:
AppManager.Instance.OnDataPointsUpdated += (obj, e) =>
{
    if (e)
    {
        // e == true, update the chart.
        UpdateChart();
    }
}
```

### Delegate

A delegate is a type that represents a method signature (including parameter list and return value).

The following example will first create a delegate type (MathOperation), it will then create a method that match the delegate's method signature (Add). When the method Calculate is called, it will first instantiate the delegate with the newly created method. Finally the delegate instantiation will get invoked. Thus, running the `Add` method:

```csharp
private delegate double MathOperation(double a, double b);

private double Add(double a, double b)
{
    return a + b;
}

private void Calculate()
{
    MathOperation adder = Add;
    Console.WriteLine("The result: " + adder(3445.33, 1000.65));
    // Will print "The result: 4445.98"
}
```

Delegates can be used together with anonymous methods, i.e. the method that matches the delegate's method signature is created as we instantiate the delegate like so:

```csharp
private delegate double MathOperation(double a, double b);

private void Calculate()
{
    MathOperation adder = delegate(double a, double b)
    {
        return a + b;
    };
    Console.WriteLine("The result: " + adder(3445.33, 1000.65));
    // Will print "The result: 4445.98"
}
```

The anonymous method can be replaced with a lambda expression:

```csharp
MathOperation adder = (double a, double b) =>
{
    return a + b;
}
```

### Predicate

A predicate is a premade delegate that takes a type as well as an instance of said type and returns a Boolean value:

```csharp
public delegate bool Predicate<in T>(T obj);
```

It's used for evaluate a condition and return True/False accordingly:

```csharp
Predicate<int> isEven = (int a) => a % 2 == 0;
Console.WriteLine("Is 3223 even: " + isEven(3223));
// Will print "Is 3223 even: False"
```

We will often see it in LINQ-statements where it is used for filter a sequence of values (the predicate is defined as a parameter to the `Where` method):

```csharp
var integers = new List<int> {1, 2, 3, 4, 5, 6};
var evenNumbers = integers.Where(p => p % 2 == 0);
evenNumbers.ToList().ForEach(p => Console.Write(p + " "));
// Will print "2 4 6 "
```

### Closure

In essence, a closure is a block of code which can be executed at a later time, but which maintains the environment in which it was first created, i.e. it can still use the local variables of the method which created it, event after that method has finished executing:

```csharp
var foo = "Hello";
Func<string> myClosure = () => { return foo; };
var bar = myClosure();
Console.WriteLine(bar);
// Will print "Hello"
```

As we can see in the example above, the `foo` variable defined in the local scope is accessible to the lambda expression, thus creating a closure.
