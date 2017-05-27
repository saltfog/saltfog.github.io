---
layout: post
title: Experiment with Project NotMe
description: Basically NotMe is an application that will verify either the person who is using my computer is me or not after I left it unlocked or idle.
keywords: c# programming, usb drive insertion, usb drive removal, hardware as password, command prompt, owner verification, deviceid
tags: [CSharp]
comments: true
---

Basically, Project NotMe is an application that will verify either the person who is using my computer is me or someone else. For example, I went for a coffee and left my computer unlocked (maybe I forgot) on my desk, then someone came in and try to use my computer without my knowledge, hence this application will be triggered to verify if the user is me within a specific duration of time.

### Screenshot

![NotMe in action](http://i.imgur.com/4FZAt8H.png)

Figure 1: The application will prevent user by going fullscreen and started showing a countdown timer.

If the verification failed, this application will shutdown or lock my computer automatically. If the user try to close this application by pressing [Alt]+[F4] or any other method, it will either shutdown or lock my computer too based on my preferred configuration.

### Application flowchart

![NotMe Flowchart](http://i.imgur.com/fp4y8U3.png)

Figure 2: Project NotMe application flowchart

### Purpose of application service

The purpose of the application service is just to monitor the main application process. If the main application process gets terminated, the service will takeover the task to either shutdown or lock my computer.

### Verification method

This is the interesting part for this application. The verification method I use here is hardware ID verification. It means to prove that I am me, during the countdown timer I need to insert my personal USB drive. If the device ID is matched with the one in the configuration file, it means the verification is success.

Here the C# code for monitoring the insertion or removal event of USB drive:

```csharp
private void StartHardwareVerification()
{
    BackgroundWorker _worker = new BackgroundWorker();
    _worker.DoWork += (obj, e) =>
    {
        try
        {
            // Insertion
            WqlEventQuery insertQuery = new WqlEventQuery("SELECT * FROM __InstanceCreationEvent WITHIN 2 WHERE TargetInstance ISA 'Win32_USBHub'");
            ManagementEventWatcher insertWatcher = new ManagementEventWatcher(insertQuery);
            insertWatcher.EventArrived += (objj, ee) =>
            {
                ManagementBaseObject instance = (ManagementBaseObject)ee.NewEvent["TargetInstance"];
                foreach (var property in instance.Properties)
                {
                    if (property.Name == "DeviceID")
                    {
                        if (property.Value.ToString() == MYUSBID)
                        {
                            // Device ID is matched with the inserted USB drive
                            // Verification status is success!
                            // Do something...
                        }
                    }
                }
            };
            insertWatcher.Start();

            // Removal
            WqlEventQuery removeQuery = new WqlEventQuery("SELECT * FROM __InstanceDeletionEvent WITHIN 2 WHERE TargetInstance ISA 'Win32_USBHub'");
            ManagementEventWatcher removeWatcher = new ManagementEventWatcher(removeQuery);
            removeWatcher.EventArrived += (objj, ee) =>
            {
                ManagementBaseObject instance = (ManagementBaseObject)ee.NewEvent["TargetInstance"];
                foreach (var property in instance.Properties)
                {
                    if (property.Name == "DeviceID")
                    {
                        if (property.Value.ToString() == MYUSBID)
                        {
                            // If the inserted USB drive is removed while
                            // verification process still not complete,
                            // flag verification status as a failure.
                        }
                    }
                }
            };
            removeWatcher.Start();
        }
        catch (Exception ex)
        {
            // Something goes wrong.. Flag the verification status as a failure.
            // Log the exception message and stack...
        }
    };
    _worker.RunWorkerAsync();
}
```

### Executing Commands

As for now, only these two commands relevant for this application; shutting down the computer in hibernate mode or lock it. For executing these commands, the method I am using now is make the application silently execute any command using Command Prompt. Currently I preferred this kind of method because it's easy for me in future later I might want to add any extra fancy thing that will be easily executed using Command Prompt.

Here the C# function to silently execute the command using Command Prompt:

```csharp
public const string ShutdownCommand = "ping -n 3 127.0.0.1 > NUL 2>&1 && shutdown /h /f";
public const string LockCommand = "ping -n 3 127.0.0.1 > NUL 2>&1 && rundll32.exe user32.dll,LockWorkStation";

public void ExecuteCommand(string command)
{
    ProcessStartInfo processInfo;
    Process process;
    processInfo = new ProcessStartInfo("cmd.exe", "/c " + command);
    processInfo.WindowStyle = ProcessWindowStyle.Hidden;
    processInfo.CreateNoWindow = true;
    processInfo.UseShellExecute = false;
    processInfo.RedirectStandardError = true;
    processInfo.RedirectStandardOutput = true;
    process = Process.Start(processInfo);
    process.WaitForExit();
    process.Close();
}
```

### Other technical info

The startup mode of this application will sit in the tray system which is using `System.Windows.Forms.NotifyIcon()`. I use .INI file for saving or updating the configurations as it's easy for me to manually modify the configuration directly from the .INI file. [The .INI file helper class source code is available on my gist.](https://gist.github.com/heiswayi/56f4707a6cf45161807989db24dc3cea) For logging, I use my own [SimpleLogger class](https://heiswayi.github.io/2016/creating-super-simple-logger-class-in-csharp). Other than that, I use [MouseKeyHook library](https://github.com/gmamaladze/globalmousekeyhook) by George Mamaladze for globally capture any keyboard inputs, mouse actions or movement.

_Note: This article expected to revised/updated again once the project is totally complete._
