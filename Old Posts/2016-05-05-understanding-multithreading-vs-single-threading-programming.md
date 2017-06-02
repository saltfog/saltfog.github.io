---
layout: post
title: Understanding multithreading vs single threading programming
description: Full source code written in .NET C# demonstrating multithreading programming using Windows Console application.
keywords: multithreading, single threading, sample source code, demonstration, threadpool, task, backgroundworker, windows console application
tags: [CSharp, Multithreading, Console Application]
comments: true
---



Multithreading is a widespread programming and execution model that allows multiple threads to exist within the context of one process. They share the process's resources, but they are able to execute independently while the single threading is the processing of one command at a time.

The purpose of threading is to allow computer to do more than one thing at a time. In a single core computer, multithreading won't give much advantages for overall speed. But for computer with multiple processor cores (which is so common these days), multithreading can take advantage of additional cores to perform separate instructions at the same time or by splitting the tasks between the cores.

Below is full source of Windows Console application written in C# that will demonstrate multithreading programming vs single threading programming. You may copy and paste the source code into your Visual Studio to have a try. The demonstration covers four multithreading options; `Thread()`, `ThreadPool.QueueUserWorkItem()`, `Task()` and `BackgroundWorker()`.

```csharp
using System;
using System.ComponentModel;
using System.Diagnostics;
using System.Threading;
using System.Threading.Tasks;

namespace MultithreadingVsSingleThreading
{
    internal class Program
    {
        #region Fields

        private static int selectedMode;

        // The number of threads to be spawned.
        private const int threadCount = 1000;

        // The total number of spins the actual work is carried out repeatedly.
        private const int totalCount = 100000;

        #endregion Fields

        #region Methods

        private static void Main(string[] args)
        {
            Console.Title = "Multithreading vs Single Threading Example";
            start:
            Console.WriteLine("Select which mode to run:-");
            Console.WriteLine("  (1) Multithreading");
            Console.WriteLine("  (2) Single Threading");
            Console.WriteLine("  (3) Threadpool");
            Console.WriteLine("  (4) Task");
            Console.WriteLine("  (5) BackgroundWorker");
            var input = Console.ReadLine();
            if (input.Length == 0)
            {
                Console.Clear();
                goto start;
            }
            else if (Convert.ToInt32(input) == 0 || Convert.ToInt32(input) >= 6)
            {
                Console.Clear();
                goto start;
            }
            else
            {
                selectedMode = Convert.ToInt32(input);
            }

            Thread.CurrentThread.Priority = ThreadPriority.Highest;
            Stopwatch watch = new Stopwatch();
            Console.Clear();
            watch.Start();

            try
            {
                HandleMode(selectedMode);

                watch.Stop();
                Console.WriteLine("Work complete!");
                Console.WriteLine("Time elapsed: {0}", watch.Elapsed);
                Console.WriteLine(Environment.NewLine);
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Press Enter key to test again or Esc to quit");
                Console.ResetColor();
                var key = Console.ReadKey();
                if (key.Key == ConsoleKey.Enter)
                {
                    Console.Clear();
                    goto start;
                }
                if (key.Key == ConsoleKey.Escape) Environment.Exit(0);
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
            Console.ReadLine();
        }

        /// <summary>
        /// Performs the task based on user defined option.
        /// </summary>
        /// <param name="mode"></param>
        private static void HandleMode(int mode)
        {
            switch (mode)
            {
                case 1:
                    Console.WriteLine("Using Thread() | No. of threads = {0}", threadCount);
                    Console.WriteLine("Initialize work...");
                    RunThreadMode();
                    break;
                case 2:
                    Console.WriteLine("Using this Main() thread, directly call ComplexWork({0})", totalCount);
                    Console.WriteLine("Initialize work...");
                    ComplexWork(totalCount);
                    break;
                case 3:
                    Console.WriteLine("Using ThreadPool.QueueUserWorkItem() | No. of threads = {0}", threadCount);
                    Console.WriteLine("Initialize work...");
                    RunInThreadPool();
                    break;
                case 4:
                    Console.WriteLine("Using Task() | No. of threads = {0}", threadCount);
                    Console.WriteLine("Initialize work...");
                    RunTaskMode();
                    break;
                case 5:
                    Console.WriteLine("Using BackgroundWorker() | No. of threads = {0}", threadCount);
                    Console.WriteLine("Initialize work...");
                    RunInBackgroundWorker();
                    break;
                default:
                    break;
            }
        }

        #region Work Task
        /// <summary>
        /// Performs CPU intensive task.
        /// </summary>
        /// <param name="n"></param>
        private static void ComplexWork(int n)
        {
            for (int j = 0; j < n; j++)
            {
                for (int i = 1; i < 100; i++)
                {
                    Fac(i);
                }
            }
        }

        /// <summary>
        /// Calculates the factorial of a number.
        /// </summary>
        /// <param name="n"></param>
        /// <returns></returns>
        private static double Fac(double n)
        {
            if (n > 1)
            {
                return n * Fac(n - 1);
            }
            else
            {
                return 1;
            }
        }

        #endregion Work Task

        /// <summary>
        /// Spawns new threads based on the thread count and starts the activity.
        /// </summary>
        private static void RunThreadMode()
        {
            Thread[] t = new Thread[threadCount];

            for (int i = 0; i < threadCount; i++)
            {
                t[i] = new Thread(() =>
                {
                    ComplexWork(totalCount / threadCount);
                });
                t[i].Priority = ThreadPriority.Highest;
                t[i].Start();
            }

            // Waits for all the threads to finish.
            foreach (var ct in t)
            {
                ct.Join();
            }
        }

        /// <summary>
        /// Executes the task in a thread pooling context.
        /// </summary>
        private static void RunInThreadPool()
        {
            using (CountdownEvent signaler = new CountdownEvent(threadCount))
            {
                for (int i = 0; i < threadCount; i++)
                {
                    ThreadPool.QueueUserWorkItem((x) =>
                    {
                        ComplexWork(totalCount / threadCount);
                        signaler.Signal();
                    });
                }
                signaler.Wait();
            }
        }

        /// <summary>
        /// Creates a new task based on the TPL library.
        /// </summary>
        private static void RunTaskMode()
        {
            Task[] taskList = new Task[threadCount];
            for (int i = 0; i < threadCount; i++)
            {
                taskList[i] = new Task(new Action(() =>
                {
                    ComplexWork(totalCount / threadCount);
                }));
                taskList[i].Start();
            }
            Task.WaitAll(taskList);
        }

        /// <summary>
        /// Starts BackgroundWorker to perform the same action.
        /// </summary>
        private static void RunInBackgroundWorker()
        {
            BackgroundWorker[] backgroundWorkerList = new BackgroundWorker[threadCount];
            using (CountdownEvent signaler = new CountdownEvent(threadCount))
            {
                for (int i = 0; i < threadCount; i++)
                {
                    backgroundWorkerList[i] = new BackgroundWorker();
                    backgroundWorkerList[i].DoWork += delegate (object sender, DoWorkEventArgs e)
                    {
                        ComplexWork(totalCount / threadCount);
                        signaler.Signal();
                    };
                    backgroundWorkerList[i].RunWorkerAsync();
                }
                signaler.Wait();
            }
        }

        #endregion Methods
    }
}
```

### Screenshots

![Multithreading vs Single Threading Example #1](http://i.imgur.com/q19Eigj.png)

_Figure 1 (above): Showing few options of multithreadings._

![Multithreading vs Single Threading Example #2](http://i.imgur.com/3YQTqCA.png)

_Figure 2 (above): Example of multithreading using `ThreadPool.QueueUserWorkItem()`._

### Notes

- The `Thread` class is used for creating and manipulating a [thread](http://msdn.microsoft.com/en-us/library/windows/desktop/ms684841%28v=vs.85%29.aspx) in Windows.
- A `Task` represents asynchronous operation and is part of the [Task Parallel Library](http://msdn.microsoft.com/en-us/library/dd460717%28v=vs.110%29.aspx), a set of APIs for running tasks asynchronously and in parallel.
- The `ThreadPool` class manages a group of threads in which tasks are added to a queue and automatically started when threads are created.
- The `BackgroundWorker` class executes an operation on a separate thread.

### Demonstration results based on my PC

Please note that, while I'm running this demonstration, my PC has several stuffs that are running such as multiple Google Chrome browser tabs left opened, 2 instances of Visual Studio 2015 software, Atom editor, etc.

**Computer info:**

```
Processor: Intel(R) Core(TM) i3-4130 CPU @ 3.40GHz (2 cores, 4 threads)
RAM: 12 GB DDR3
GPU: AMD Radeon R7 200 Series
```

**Demonstration results:**

```
// Multithreading
Using Thread() | No. of threads = 1000
Initialize work...
Work complete!
Time elapsed: 00:00:01.6529574

// Single Threading
Using this Main() thread, directly call ComplexWork(100000)
Initialize work...
Work complete!
Time elapsed: 00:00:04.6129301

// ThreadPool
Using ThreadPool.QueueUserWorkItem() | No. of threads = 1000
Initialize work...
Work complete!
Time elapsed: 00:00:01.6173233

// Task
Using Task() | No. of threads = 1000
Initialize work...
Work complete!
Time elapsed: 00:00:01.6230018

// BackgroundWorker
Using BackgroundWorker() | No. of threads = 1000
Initialize work...
Work complete!
Time elapsed: 00:00:01.6416939
```

Based on the results above (using my PC), multithreading programming provides about 4x faster compared to single threading.
