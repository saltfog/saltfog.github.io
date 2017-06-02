---
layout: post
title: MVVM common classes in a single file
description: MVVM.cs is a collection of Model-View-ViewModel (MVVM) design pattern common classes that can be implemented by any ViewModel (VM).
keywords: mvvm common classes, viewmodelbase, onpropertychanged, raisepropertychanged, icommand, delegatecommand, relaycommand, lambda expression
tags: [MVVM, CSharp]
comments: true
---

**Download the source code: [MVVM.cs](https://git.io/v1TAI)**

_MVVM.cs_ is a collection of Model-View-ViewModel (MVVM) design pattern common classes that can be implemented by any ViewModel (VM). I created this collection into a single file, so whenever I want to develop MVVM-based application (without using MVVM frameworks) in .NET C#, I just need to reference it and inherit the `ViewModelBase` class into any of my VM classes.

### Usage examples

To raise any property change, you may use these following methods:

- `OnPropertyChanged("YourPropertyName");`
- Lambda function: `RaisePropertyChanged(() => YourPropertyName);`

**Example:**

```csharp
using HeiswayiNrird.MVVM.Common; // MVVM.cs
using System;

namespace MVVMExampleApp
{
    public class ViewModelExample : ViewModelBase
    {
        private object _propertyName1;
        public object PropertyName1
        {
            get { return _propertyName1; }
            set
            {
                _propertyName1 = value;
                OnPropertyChanged("PropertyName1");
            }
        }
        private object _propertyName2;
        public object PropertyName2
        {
            get { return _propertyName2; }
            set
            {
                _propertyName2 = value;
                RaisePropertyChanged(() => PropertyName2); // Use of lambda expression
            }
        }
    }
}
```

For `ICommand` property, you may use any of these following classes:

- `DelegateCommand()`
- `RelayCommand()`

**Example:**

```csharp
using HeiswayiNrird.MVVM.Common; // MVVM.cs
using System.Windows.Input;
using System;

namespace MVVMExampleApp
{
    public class ViewModelExample : ViewModelBase
    {
        // Example 1 : DelegateCommand
        private ICommand _commandProperty1;
        public ICommand CommandProperty1
        {
            get
            {
                if (_commandProperty1 == null)
                {
                    //_commandProperty1 = new DelegateCommand(methodToExecute);
                    _commandProperty1 = new DelegateCommand(methodToExecute, canExecuteMethodToExecute);
                }
                return _commandProperty1;
            }
        }

        // Example 2 : DelegateCommand with type
        private ICommand _commandProperty2;
        public ICommand CommandProperty2
        {
            get
            {
                if (_commandProperty2 == null)
                {
                    _commandProperty2 = new DelegateCommand<int>(methodToExecute2);
                }
                return _commandProperty2;
            }
        }

        // Example 3 : RelayCommand
        private ICommand _commandProperty3;
        public ICommand CommandProperty3
        {
            get
            {
                if (_commandProperty3 == null)
                {
                    _commandProperty3 = new RelayCommand(
                        param => methodToExecute(),
                        param => canExecuteMethodToExecute()
                        );
                }
                return _commandProperty3;
            }
        }

        // Example 4 : RelayCommand with parameter
        private ICommand _commandProperty4;
        public ICommand CommandProperty4
        {
            get
            {
                if (_commandProperty4 == null)
                {
                    _commandProperty4 = new RelayCommand(
                        param => methodToExecute2((int)param), // Use with UI control CommandParameter property
                        param => canExecuteMethodToExecute()
                        );
                }
                return _commandProperty4;
            }
        }

        private void methodToExecute2(int param)
        {
            throw new NotImplementedException();
        }
        private void methodToExecute()
        {
            // Logic to execute...
        }
        private bool canExecuteMethodToExecute()
        {
            // Put your expression/condition here, return true/false...
            return true;
        }
    }
}
```
