---
layout: post
title: Creating super simple logger class in C#
description: SimpleLogger.cs is simply my super simple logger class I wrote for my own use in mini C# project development.
keywords: c# programming, simple logger class
tags: [CSharp]
comments: true
---

Nothing much to say here, nah I just want to share my super simple logger class that I wrote for my own use in my mini C# projects. If you like it, you may use it. It's free for commercial use. This code snippet also available on my [gist](https://gist.github.com/heiswayi/69ef5413c0f28b3a58d964447c275058).

_SimpleLogger.cs_

```csharp
using System;
using System.IO;
using System.Reflection;
using System.Text;

namespace HeiswayiNrird.SimpleLogger
{
    public class SimpleLogger
    {
        private string DatetimeFormat;
        private string Filename;

        /// <summary>
        /// Initialize a new instance of SimpleLogger class.
        /// Log file will be created automatically if not yet exists, else it can be either a fresh new file or append to the existing file.
        /// Default is create a fresh new log file.
        /// </summary>
        /// <param name="append">True to append to existing log file, False to overwrite and create new log file</param>
        public SimpleLogger(bool append = false)
        {
            DatetimeFormat = "yyyy-MM-dd HH:mm:ss.fff";
            Filename = Assembly.GetExecutingAssembly().GetName().Name + ".log";

            // Log file header line
            string logHeader = Filename + " is created.";
            if (!File.Exists(Filename))
            {
                WriteLine(DateTime.Now.ToString(DatetimeFormat) + " " + logHeader, false);
            }
            else
            {
                if (append == false)
                    WriteLine(DateTime.Now.ToString(DatetimeFormat) + " " + logHeader, false);
            }
        }

        /// <summary>
        /// Log a debug message
        /// </summary>
        /// <param name="text">Message</param>
        public void Debug(string text)
        {
            WriteFormattedLog(LogLevel.DEBUG, text);
        }

        /// <summary>
        /// Log an error message
        /// </summary>
        /// <param name="text">Message</param>
        public void Error(string text)
        {
            WriteFormattedLog(LogLevel.ERROR, text);
        }

        /// <summary>
        /// Log a fatal error message
        /// </summary>
        /// <param name="text">Message</param>
        public void Fatal(string text)
        {
            WriteFormattedLog(LogLevel.FATAL, text);
        }

        /// <summary>
        /// Log an info message
        /// </summary>
        /// <param name="text">Message</param>
        public void Info(string text)
        {
            WriteFormattedLog(LogLevel.INFO, text);
        }

        /// <summary>
        /// Log a trace message
        /// </summary>
        /// <param name="text">Message</param>
        public void Trace(string text)
        {
            WriteFormattedLog(LogLevel.TRACE, text);
        }

        /// <summary>
        /// Log a waning message
        /// </summary>
        /// <param name="text">Message</param>
        public void Warning(string text)
        {
            WriteFormattedLog(LogLevel.WARNING, text);
        }

        /// <summary>
        /// Format a log message based on log level
        /// </summary>
        /// <param name="level">Log level</param>
        /// <param name="text">Log message</param>
        private void WriteFormattedLog(LogLevel level, string text)
        {
            string pretext;
            switch (level)
            {
                case LogLevel.TRACE: pretext = DateTime.Now.ToString(DatetimeFormat) + " [TRACE]   "; break;
                case LogLevel.INFO: pretext = DateTime.Now.ToString(DatetimeFormat) + " [INFO]    "; break;
                case LogLevel.DEBUG: pretext = DateTime.Now.ToString(DatetimeFormat) + " [DEBUG]   "; break;
                case LogLevel.WARNING: pretext = DateTime.Now.ToString(DatetimeFormat) + " [WARNING] "; break;
                case LogLevel.ERROR: pretext = DateTime.Now.ToString(DatetimeFormat) + " [ERROR]   "; break;
                case LogLevel.FATAL: pretext = DateTime.Now.ToString(DatetimeFormat) + " [FATAL]   "; break;
                default: pretext = ""; break;
            }

            WriteLine(pretext + text);
        }

        /// <summary>
        /// Write a line of formatted log message into a log file
        /// </summary>
        /// <param name="text">Formatted log message</param>
        /// <param name="append">True to append, False to overwrite the file</param>
        /// <exception cref="System.IO.IOException"></exception>
        private void WriteLine(string text, bool append = true)
        {
            try
            {
                using (StreamWriter Writer = new StreamWriter(Filename, append, Encoding.UTF8))
                {
                    if (text != "") Writer.WriteLine(text);
                }
            }
            catch
            {
                throw;
            }
        }

        /// <summary>
        /// Supported log level
        /// </summary>
        [Flags]
        private enum LogLevel
        {
            TRACE,
            INFO,
            DEBUG,
            WARNING,
            ERROR,
            FATAL
        }
    }
}
```

### Usage example

To get started, just initialize the `SimpleLogger` class. Initializing the constructor without parameter will create a fresh new log file or overwrite the file if already exists by default. **Log file will be created in the same folder with the application assembly file and the log filename will follow the executing assembly name.** For example, let say the application filename is `SimpleLoggerDemo.exe`, so the log filename would be `SimpleLoggerDemo.log`.

Here is the "how-to-use" example:

```csharp
using HeiswayiNrird.SimpleLogger;

namespace SimpleLoggerDemo
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            // Instantiate the class
            var logger = new SimpleLogger(); // Will create a fresh new log file

            // To log Trace message
            logger.Trace("--> Trace in message here...");

            // To log Info message
            logger.Info("Anything to info here...");

            // To log Debug message
            logger.Debug("Something to debug...");

            // To log Warning message
            logger.Warning("Anything to put as a warning log...");

            // To log Error message
            logger.Error("Error message...");

            // To log Fatal error message
            logger.Fatal("Fatal error message...");
        }
    }
}
```

```csharp
var logger = new SimpleLogger(true); // Will not overwrite the existing log file
```

Initializing the constructor with parameter like above will not overwrite or clear the existing log file. Next log message added will be appended to the existing log file. Unless if the log file is not exist, then it will create a fresh new one. This is useful when I instantiate this class in different class within the same project, my log messages continue to append to my single log file without overwriting the existing log file.
