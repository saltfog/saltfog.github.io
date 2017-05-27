---
layout: post
title: Developing WPF application using MVVM design pattern
description: Basic tutorial on how you can start developing .NET C# WPF application using Model-View-ViewModel (MVVM) design pattern.
keywords: wpf application, mvvm design pattern, password hashing tool
tags: [MVVM, WPF, CSharp]
comments: true
---

In this tutorial, I will use an example application called Password Hashing Tool. It is a simple .NET C# WPF application created using VS2013.

![Password Hashing Tool](http://i.imgur.com/EQgW18i.png)

[Full source code on GitHub](https://github.com/heiswayi/SimpleOneWayHashing)

tldr;

### WPF

WPF stands for **Windows Presentation Foundation**, a graphical subsystem for rendering UI in Windows-based applications by Microsoft for software development. It's now the current platform for developing Windows desktop applications. It's modern, advanced, hardware-accelerated framework for developing applications that maintains separation of concerns. WPF supports advanced rendering of 2D vector and 3D graphics, providing an immense range of capabilities for building rich, interactive and quality user interfaces.

### MVVM design pattern

Model View ViewModel (MVVM) is an architectural pattern used in software development that originated from [Microsoft](https://msdn.microsoft.com/en-us/library/hh848246.aspx), which is from the WPF creator itself. It's a variation of [Martin Fowler's Presentation Model](http://www.martinfowler.com/eaaDev/PresentationModel.html) design pattern and it's based on a derivation of the [Model-View-Controller (MVC)](https://msdn.microsoft.com/en-us/library/ff649643.aspx) pattern.

MVVM facilitates a separation of the development of the GUI from the development of the business logic or back-end logic (the data model). It's targeted at modern UI development platforms (WPF and Silverlight) in which there is a UX developer who has different requirements than a more "traditional" developer. With MVVM, it allows you to keep your code clean and easy to maintain. MVVM is a way of creating client applications that leverages core features of the WPF platform while allows for simple unit testing of application functionality and helps developers and designers work together with less technical difficulties.

* **Model** - A Model is a simple class object that hold data. It should only contain properties and property validation. It's _not responsible_ for getting data, saving data, click events, complex calculations, business rules, or any of that stuff.

* **View** - A View is the UI used to display data defined in XAML and should not have any logic in the code-behind. In most cases, it can be DataTemplates which is simply a template that tells the application how to display a class. It binds to the ViewModel by only using data-binding. It's OK to put code behind your view _if_ that code is related to the View only, such as setting focus or running animations.

* **ViewModel** - A ViewModel or VM in short is where the magic happens. This is where the majority of your code-behind goes such as data access, click events, complex calculations, business rules validation, etc. It's typically built to reflect the View. It's an abstraction of the View that exposes public properties and commands.

#### Advantages of MVVM design pattern

* **Collaboration** - Programmers and non-programmers (designers) can work together easily.
* **Reliability** - The code is testable (unit testing) to maintain the consistency of code quality.
* **Flexibility** - It's much easier to change view without messing with the rest of the code.
* **Code Friendly** - With a good separation between Model, UI and logic (code-behind), it makes easier for other people to understand overall process in order to take over the project, make improvements or debug it.

### Let's have a look on "Password Hashing Tool" project

Okay, here is the tree view for directories and files looked like inside the project named as "SimpleOneWayHashing":

```
Solution 'SimpleOneWayHashing' (1 project)
└── SimpleOneWayHashing
    ├── Properties
    ├── References
    ├── Commands
    |   ├── CommandReference.cs
    |   └── DelegateCommand.cs
    ├── Models
    |   └── HashingModel.cs
    ├── Themes
    |   └── Metro
    |       ├── Metro.MSControls.Core.Implicit.xaml
    |       ├── Metro.MSControls.Toolkit.Implicit.xaml
    |       ├── Styles.Shared.xaml
    |       ├── Styles.WPF.xaml
    |       └── Theme.Colors.xaml
    ├── ViewModels
    |   ├── HashingViewModel.cs
    |   └── ViewModelBase.cs
    ├── Views
    |   └── HasherView.xaml
    |       └── HasherView.xaml.cs
    ├── App.config
    ├── App.xaml
    |   └── App.xaml.cs
    └── ClassDiagram1.cd
```

As you can see from the tree view above, there are basically the folders and files structure. **Models**, **ViewModels** and **Views** are the primary folders usually used to separate between GUI and logics. This is to ensure our project is organized properly and clean. I created another folder called **Commands**, inside this folder, there are helper classes related to `ICommand` - let's skip this one first.

#### Let's start with the Model first

```
    ├── Models
    |   └── HashingModel.cs
```

This is how the code looked like inside _HashingModel.cs_ file.

```csharp
using System;
using System.Collections.Generic;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace SimpleOneWayHashing.Models
{
    public class HashingModel
    {
        #region Private Members
        private string appTitle = "One-Way Password Hashing";
        private string result;
        #endregion

        #region Constructor
        public HashingModel(string _plainText, string _salt)
        {
            PlainText = _plainText;
            Salt = _salt;
            result = string.Empty;
        }
        public HashingModel()
        {
            PlainText = string.Empty;
            Salt = string.Empty;
            result = string.Empty;
        }
        #endregion

        #region Public Properties and Methods
        public string AppTitle { get { return appTitle; } }
        public string PlainText { get; set; }
        public string Salt { get; set; }
        public string Result { get { return result; } }

        public void ComputingResult()
        {
            UTF8Encoding utf8 = new UTF8Encoding();
            byte[] textWithSaltBytes = utf8.GetBytes(string.Concat(PlainText, Salt));
            HashAlgorithm hasher = new SHA1CryptoServiceProvider();
            byte[] hashedBytes = hasher.ComputeHash(textWithSaltBytes);
            hasher.Clear();
            result = Convert.ToBase64String(hashedBytes);
        }
        #endregion
    }
}
```

The class contains private members, public properties and methods. As you can see, `ComputingResult()` is a method I used to compute a hash from `PlainText` and `Salt` properties, then return the result to `result` property. This class uses its own namespace called `SimpleOneWayHashing.Models`.

#### The ViewModel or VM

```
    ├── ViewModels
    |   ├── HashingViewModel.cs
    |   └── ViewModelBase.cs
```

Inside _ViewModels_ folder, there are two files; _HashingViewMode.cs_ and _ViewModelBase.cs_. Inside _ViewModelBase.cs_, there is a helper class which inherits `INotifyPropertyChanged` interface.

```csharp
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Text;
using System.Threading.Tasks;

namespace SimpleOneWayHashing.ViewModels
{
    /// <summary>
    /// Provides common functionality for ViewModel class
    /// </summary>
    public abstract class ViewModelBase : INotifyPropertyChanged
    {
        public event PropertyChangedEventHandler PropertyChanged;

        protected void OnPropertyChanged(string propertyName)
        {
            PropertyChangedEventHandler handler = PropertyChanged;

            if (handler != null)
            {
                handler(this, new PropertyChangedEventArgs(propertyName));
            }
        }

    }
}
```

_HashingViewMode.cs_ is where it contains all the binding properties for this "Password Hashing Tool" program to work with the View.

```csharp
using System;
using System.Collections.Generic;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Input;

using SimpleOneWayHashing.Commands;
using SimpleOneWayHashing.Models;

namespace SimpleOneWayHashing.ViewModels
{
    public class HashingViewModel : ViewModelBase
    {
        #region Private Members
        private Models.HashingModel hashing;

        private DelegateCommand computeHashCommand;
        private DelegateCommand clearTextBoxCommand;

        private string hashingTitle;
        private string computedHash;
        #endregion

        #region Constructor
        public HashingViewModel()
        {
            this.hashing = new HashingModel();
            hashingTitle = hashing.AppTitle;
            this.PlainText = string.Empty;
            this.Salt = string.Empty;
            this.computedHash = string.Empty;
        }
        #endregion

        #region Public Properties
        public string HashingTitle
        {
            get { return hashingTitle; }
        }
        public string PlainText
        {
            get { return hashing.PlainText; }
            set { hashing.PlainText = value; }
        }

        public string Salt
        {
            get { return hashing.Salt; }
            set { hashing.Salt = value; }
        }

        public string Result
        {
            get { return hashing.Result; }
        }

        public string ComputedHash
        {
            get { return computedHash; }
            set
            {
                computedHash = value;
                OnPropertyChanged("ComputedHash");
            }
        }
        #endregion

        #region Commands
        public ICommand ComputeHashCommand
        {
            get
            {
                if (computeHashCommand == null)
                {
                    computeHashCommand = new DelegateCommand(ComputeHash, CanComputeHash);
                }
                return computeHashCommand;
            }
        }

        private bool CanComputeHash()
        {
            if (!string.IsNullOrEmpty(this.PlainText) && !string.IsNullOrEmpty(this.Salt))
                return true;
            else
                return false;
        }

        private void ComputeHash()
        {
            hashing.ComputingResult();
            ComputedHash = Result;
        }

        public ICommand ClearTextBoxCommand
        {
            get
            {
                if (clearTextBoxCommand == null)
                {
                    clearTextBoxCommand = new DelegateCommand(ClearTextBox);
                }
                return clearTextBoxCommand;
            }
        }

        private void ClearTextBox()
        {
            this.PlainText = string.Empty;
            OnPropertyChanged("PlainText");

            this.Salt = string.Empty;
            OnPropertyChanged("Salt");

            this.ComputedHash = string.Empty;
        }
        #endregion
    }
}
```

_ViewModelBase.cs_ and _HashingViewModel.cs_ are using the same namespace `SimpleOneWayHashing.ViewModels`. As you can see from the source code above, there is a class called `HashingViewModel` which inherits from `ViewModelBase` class. Since they both are using the same namespace, so I don't need to include it with `using`, but not for the Model and `ICommand` interface helper class. The Model and `ICommand` interface helper class are using different classes and namespaces. So, I need to include them like this in `HashingViewModel.cs`:

```csharp
using SimpleOneWayHashing.Commands;
using SimpleOneWayHashing.Models;
```

Overall, this is the code structure practice inside _HashingViewModel.cs_:

```csharp
using System;
using System.Collections.Generic;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Input;

using SimpleOneWayHashing.Commands;
using SimpleOneWayHashing.Models;

namespace SimpleOneWayHashing.ViewModels
{
    public class HashingViewModel : ViewModelBase
    {
        // Private Members

        // Constructor

        // Public Properties

        // Commands
    }
}
```

#### Next is the View

```
    ├── Views
    |   └── HasherView.xaml
    |       └── HasherView.xaml.cs
```

For this project, I just have one View file only - _HasherView.xaml_. This is usually our MainWindow.xaml. Okay, I don't use `DataTemplate` here. Here's how the XAML code looked like inside _HasherView.xaml_:

```xml
<Window x:Class="SimpleOneWayHashing.Views.HasherView"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="clr-namespace:SimpleOneWayHashing.ViewModels"
        WindowStartupLocation="CenterScreen"
        Title="{Binding HashingTitle}" Height="400" Width="400">
    <Grid>
        <StackPanel Orientation="Vertical" Margin="10">
            <TextBlock Text="Enter your plain password:"/>
            <TextBox x:Name="PlainText" Height="Auto" HorizontalAlignment="Stretch" Text="{Binding Path=PlainText, UpdateSourceTrigger=PropertyChanged}"/>
            <TextBlock Text="Enter your password salt (anything, whatever):" Margin="0,10,0,0"/>
            <TextBox x:Name="Salt" Height="Auto" HorizontalAlignment="Stretch" Text="{Binding Path=Salt, UpdateSourceTrigger=PropertyChanged}"/>
            <Grid Margin="0,10,0,0">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*"/>
                    <ColumnDefinition Width="*"/>
                </Grid.ColumnDefinitions>
                <Button Content="Get Hash" Grid.Column="0" Command="{Binding Path=ComputeHashCommand}"/>
                <Button Content="Clear TextBox" Grid.Column="1" Command="{Binding Path=ClearTextBoxCommand}"/>
            </Grid>
            <TextBlock Text="Hashed String:" Margin="0,10,0,0"/>
            <TextBox x:Name="HashedString" Height="Auto" HorizontalAlignment="Stretch" Text="{Binding Path=ComputedHash, Mode=OneWay}" IsReadOnly="True"/>
            <GroupBox Header="Information" Margin="0,10,0,0">
                <TextBlock TextWrapping="Wrap" Margin="5">
                    This is a sample of MVVM-based WPF application with a simple algorithm to generate a very strong one-way password hashing.
                </TextBlock>
            </GroupBox>
        </StackPanel>
    </Grid>
</Window>
```

As can be seen there I use `Binding` to bind it to ViewModel. Below is how the **untouched** code-behind of _HasherView.xaml.cs_ looked like:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Documents;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.Windows.Shapes;

namespace SimpleOneWayHashing.Views
{
    /// <summary>
    /// Interaction logic for HasherView.xaml
    /// </summary>
    public partial class HasherView : Window
    {
        public HasherView()
        {
            InitializeComponent();
        }
    }
}
```

#### ICommand interface helper class

There is another new class here called `DelegateCommand`, basically inherits from `ICommand` interface. This helper class is essential for action commands and mostly used by button UI. It's a command that is meant to be executed by other classes to run code in this class by invoking delegates. Some people may called it the **RelayCommand**.

```
    ├── Commands
    |   ├── CommandReference.cs
    |   └── DelegateCommand.cs
```

Inside this "Password Hashing Tool" project, there are two files in the **Commands** folder; [CommandReference.cs](https://github.com/heiswayi/SimpleOneWayHashing/blob/master/Commands/CommandReference.cs) and [DeletegateCommand.cs](https://github.com/heiswayi/SimpleOneWayHashing/blob/master/Commands/DelegateCommand.cs). The source code of both helper classes are well-documented, self-explainable and easily reusable for your project if you like.

#### Finally, starting the application

This is usually known as `App.xaml` file. Since my project has **Themes** folder where it contains the XAML theme files, so this is how I created the resources inside `App.xaml`:

```xml
<Application x:Class="SimpleOneWayHashing.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             Startup="Application_Startup">
    <Application.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="Themes/Metro/Metro.MSControls.Core.Implicit.xaml" />
                <ResourceDictionary Source="Themes/Metro/Metro.MSControls.Toolkit.Implicit.xaml" />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

But, to start the application, this is the code snippet I use which can be found in the code-behind of the startup file: `App.xaml.cs`.

```csharp
Views.HasherView view = new Views.HasherView();
view.DataContext = new ViewModels.HashingViewModel();
view.Show();
```

This is how overall code looked like in `App.xaml.cs` file:

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Data;
using System.Linq;
using System.Threading.Tasks;
using System.Windows;

namespace SimpleOneWayHashing
{
    /// <summary>
    /// Interaction logic for App.xaml
    /// </summary>
    public partial class App : Application
    {
        private void Application_Startup(object sender, StartupEventArgs e)
        {
            // Create the ViewModel and expose it using the View's DataContext
            Views.HasherView view = new Views.HasherView();
            view.DataContext = new ViewModels.HashingViewModel();
            view.Show();
        }
    }
}
```

### Conclusion

Well, this is how "Password Hashing Tool" application is created with MVVM design pattern. This is one of the ways implementing MVVM design pattern in a project. Different developer might use different approach in implementing the MVVM design pattern. If you _google_ around, there are a lot of resources about the implementation of MVVM design pattern available online, from the basic or simpler version to the advanced design, large-scale development application.

You may [download the source code](https://github.com/heiswayi/SimpleOneWayHashing/archive/master.zip) of the sample application used in this basic tutorial about developing WPF application using MVVM design pattern. It's much easier for you to understand how it works. I think the source code are well documented. If you are still beginner, you may use this project as a reference or you may reuse some of the code inside this project as the code snippets for your other projects.

You also can start developing your own MVVM application with some [MVVM frameworks](https://catelproject.atlassian.net/wiki/display/CTL/MVVM+framework+comparison+sheet) that are available online and free. Have fun developing!

-----

References:

* [Simple MVVM Pattern in WPF](http://www.c-sharpcorner.com/UploadFile/raj1979/simple-mvvm-pattern-in-wpf/)
* [MVVM in WPF](http://www.c-sharpcorner.com/uploadfile/nipuntomar/mvvm-in-wpf/)
* [A Simple MVVM Example](https://rachel53461.wordpress.com/2011/05/08/simplemvvmexample/)
* [Wikipedia: Model View ViewModel](https://en.wikipedia.org/wiki/Model_View_ViewModel)
* [What is the difference between WPF and WinForms?](http://stackoverflow.com/questions/1337345/what-is-the-difference-between-wpf-and-winforms)
* [Why use MVVM?](http://stackoverflow.com/questions/2653096/why-use-mvvm)
* [Basic MVVM and ICommand Usage Example](http://www.codeproject.com/Tips/813345/Basic-MVVM-and-ICommand-Usage-Example)
* [Most Developers Use MVVM Incorrectly. Are You?](https://dzone.com/articles/most-developers-use-mvvm)
