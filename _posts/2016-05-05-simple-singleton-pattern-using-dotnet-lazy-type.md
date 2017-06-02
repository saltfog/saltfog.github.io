---
layout: post
title: Simple singleton pattern using .NET 4's Lazy type
description: Here is a simple singleton pattern to turn your class into a singleton class. This singleton pattern is very simple and perform well, best use with time-consuming operation class in C# programming.
keywords: singleton pattern, .net 4, laziness, lambda expression
tags: [Singleton, CSharp]
comments: true
---

Recently, most of Microsoft .NET based applications are using .NET 4 or higher. There are debates around saying either singleton is an anti-pattern or not and mostly said yes when it is overused. However, there are times where it is useful too. So, here is my favorite way of implementing the singleton, which is using the `System.Lazy<T>` type. All you need to do is pass a delegate to the constructor which calls the single constructor, which is done most easily with a lambda expression.

Let say I have a class called `SayHello` with the singleton pattern implemented:

```csharp
using System;

namespace SingletonExample
{
    public sealed class SayHello
    {
        private static readonly Lazy<SayHello> lazy = new Lazy<SayHello>(() => new SayHello());
        public static SayHello Instance { get { return lazy.Value; } }

        private SayHello()
        {
        }

        public void Test()
        {
            Console.WriteLine("Hello hello hello!");
        }
    }
}
```

Here is my main program that will call an instance of method `Test()` from `SayHello` class:

```csharp
using System;

namespace SingletonExample
{
    class Program
    {
        static void Main(string[] args)
        {
            SayHello.Instance.Test();

            Console.ReadLine();
        }
    }
}
```

Example output:

![SingletonExample](http://i.imgur.com/Vnbsea3.png)

This singleton pattern is simple and perform well, best use with time-consuming operation class.
