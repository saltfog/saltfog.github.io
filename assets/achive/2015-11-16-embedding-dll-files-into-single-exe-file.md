---
layout: post
title: Embedding .DLL files into a single .EXE file
description: Here is the trick to create a single binary .EXE file with embedded external .DLL files in C# WinForms.
keywords: embedded dll, visual studio
tags: [WinForms, CSharp]
comments: true
---

This method is unusual and rarely people do it, but it still works for me when I want to hide one or two external library binaries/assemblies like .DLL files into my single .EXE application file. I use .NET C# and Visual Studio as the IDE. Let's say I want to compile a simple application called a Mini Browser and these are external .DLL files used in my project:-

- DevExpress.BonusSkins.v13.2.dll
- DevExpress.Data.v13.2.dll
- DevExpress.Sparkline.v13.2.Core.dll
- DevExpress.Utils.v13.2.dll
- DevExpress.XtraEditors.v13.2.dll

To get started, I put them all in a folder called `lib` and include it in my project as shown in the picture below:

![Include in Project Tree](http://i.imgur.com/n3QaSRh.png)

This is one of my old project where I use C# WinForms to create a pretty simple Mini Browser application. Now, we need to change **Build Action** of each file's Properties to `Embedded Resource` as shown in the picture below:

![Properties of each file](http://i.imgur.com/rbmrZRc.png)

Next, add all of them to **References** since we're going to use them in our project as shown in the picture below:

![Add to References](http://i.imgur.com/U2PwPzY.png)

Then, we need to make sure all of our external DLL files also included in `Properties\Resources.resx` file as shown in the picture below:

![Include in Resources.resx](http://i.imgur.com/9orEOLE.png)

If nothing's there, click on **downward arrow** at the right side of **Add Resource** and select **Add Existing File...**, navigate to `lib` folder in our project, change file extension to **All File (\*.\*)** then select all of the files.

![Add Resource](http://i.imgur.com/Ok7olQ8.png)

Once we are done with this files setup, go to our main application constructor code, for example in my case is `Form1()` from _Form1.cs_ file as shown in the picture below:

![Constructor Code](http://i.imgur.com/Ap2RIJD.png)

Now, we can add this anonymous function code inside the constructor code and put it before `InitializeComponent();` and then resolve any required namespace:

```csharp
// using System.Reflection;
AppDomain.CurrentDomain.AssemblyResolve += (sender, args) =>
{
	string resourceName = new AssemblyName(args.Name).Name + ".dll";
    string resource = Array.Find(this.GetType().Assembly.GetManifestResourceNames(), element => element.EndsWith(resourceName));

    using (var stream = Assembly.GetExecutingAssembly().GetManifestResourceStream(resource))
    {
		Byte[] assemblyData = new Byte[stream.Length];
        stream.Read(assemblyData, 0, assemblyData.Length);
        return Assembly.Load(assemblyData);
	}
};
```

And compile it! We should now have our single .EXE application file without any external assembly (.DLL) files. Please note that, embedding these .DLL files into our main application file (.EXE) will result in increasing of our .EXE file size as this sums up everything.
