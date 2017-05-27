---
layout: post
title: C# - Simple SerialPort singleton class
description: My singleton class code snippet called SerialPortManager for handling serial data communication in some of my C# projects.
keywords: c# programming, singleton design pattern, serial port, serial communication
tags: [CSharp, SerialPort, Singleton]
comments: true
---

In my projects, some of them involves serial data communication. So, I think there is a need for me to create a reusable class that I could always reuse whenever I develop application that use serial data communication protocol. Mostly with the projects that I'm using [Arduino](https://www.arduino.cc/) board. Here is my singleton class called `SerialPortManager` which is basically based on [System.IO.Ports.SerialPort](https://msdn.microsoft.com/en-us/library/system.io.ports.serialport(v=vs.110).aspx) class.

This full source code also available on [my gist here](https://gist.github.com/heiswayi/80eda1a6905ba4edee8bd21a45f3a22d).

### SerialPortManager.cs

```csharp
using System;
using System.IO;
using System.IO.Ports;
using System.Threading;

namespace HeiswayiNrird.Singleton
{
    public sealed class SerialPortManager
    {
        private static readonly Lazy<SerialPortManager> lazy = new Lazy<SerialPortManager>(() => new SerialPortManager());
        public static SerialPortManager Instance { get { return lazy.Value; } }

        private SerialPort _serialPort;
        private Thread _readThread;
        private volatile bool _keepReading;

        private SerialPortManager()
        {
            _serialPort = new SerialPort();
            _readThread = null;
            _keepReading = false;
        }

        /// <summary>
        /// Update the serial port status to the event subscriber
        /// </summary>
        public event EventHandler<string> OnStatusChanged;

        /// <summary>
        /// Update received data from the serial port to the event subscriber
        /// </summary>
        public event EventHandler<string> OnDataReceived;

        /// <summary>
        /// Update TRUE/FALSE for the serial port connection to the event subscriber
        /// </summary>
        public event EventHandler<bool> OnSerialPortOpened;

        /// <summary>
        /// Return TRUE if the serial port is currently connected
        /// </summary>
        public bool IsOpen { get { return _serialPort.IsOpen; } }

        /// <summary>
        /// Open the serial port connection using basic serial port settings
        /// </summary>
        /// <param name="portname">COM1 / COM3 / COM4 / etc.</param>
        /// <param name="baudrate">0 / 100 / 300 / 600 / 1200 / 2400 / 4800 / 9600 / 14400 / 19200 / 38400 / 56000 / 57600 / 115200 / 128000 / 256000</param>
        /// <param name="parity">None / Odd / Even / Mark / Space</param>
        /// <param name="databits">5 / 6 / 7 / 8</param>
        /// <param name="stopbits">None / One / Two / OnePointFive</param>
        /// <param name="handshake">None / XOnXOff / RequestToSend / RequestToSendXOnXOff</param>
        public void Open(
            string portname = "COM1",
            int baudrate = 9600,
            Parity parity = Parity.None,
            int databits = 8,
            StopBits stopbits = StopBits.One,
            Handshake handshake = Handshake.None)
        {
            Close();

            try
            {
                _serialPort.PortName = portname;
                _serialPort.BaudRate = baudrate;
                _serialPort.Parity = parity;
                _serialPort.DataBits = databits;
                _serialPort.StopBits = stopbits;
                _serialPort.Handshake = handshake;

                _serialPort.ReadTimeout = 50;
                _serialPort.WriteTimeout = 50;

                _serialPort.Open();
                StartReading();
            }
            catch (IOException)
            {
                if (OnStatusChanged != null)
                    OnStatusChanged(this, string.Format("{0} does not exist.", portname));
            }
            catch (UnauthorizedAccessException)
            {
                if (OnStatusChanged != null)
                    OnStatusChanged(this, string.Format("{0} already in use.", portname));
            }
            catch (Exception ex)
            {
                if (OnStatusChanged != null)
                    OnStatusChanged(this, "Error: " + ex.Message);
            }

            if (_serialPort.IsOpen)
            {
                string sb = StopBits.None.ToString().Substring(0, 1);
                switch (_serialPort.StopBits)
                {
                    case StopBits.One:
                        sb = "1"; break;
                    case StopBits.OnePointFive:
                        sb = "1.5"; break;
                    case StopBits.Two:
                        sb = "2"; break;
                    default:
                        break;
                }

                string p = _serialPort.Parity.ToString().Substring(0, 1);
                string hs = _serialPort.Handshake == Handshake.None ? "No Handshake" : _serialPort.Handshake.ToString();

                if (OnStatusChanged != null)
                    OnStatusChanged(this, string.Format(
                    "Connected to {0}: {1} bps, {2}{3}{4}, {5}.",
                    _serialPort.PortName,
                    _serialPort.BaudRate,
                    _serialPort.DataBits,
                    p,
                    sb,
                    hs));

                if (OnSerialPortOpened != null)
                    OnSerialPortOpened(this, true);
            }
            else
            {
                if (OnStatusChanged != null)
                    OnStatusChanged(this, string.Format(
                    "{0} already in use.",
                    portname));

                if (OnSerialPortOpened != null)
                    OnSerialPortOpened(this, false);
            }
        }

        /// <summary>
        /// Close the serial port connection
        /// </summary>
        public void Close()
        {
            StopReading();
            _serialPort.Close();

            if (OnStatusChanged != null)
                OnStatusChanged(this, "Connection closed.");

            if (OnSerialPortOpened != null)
                OnSerialPortOpened(this, false);
        }

        /// <summary>
        /// Send/write string to the serial port
        /// </summary>
        /// <param name="message"></param>
        public void SendString(string message)
        {
            if (_serialPort.IsOpen)
            {
                try
                {
                    _serialPort.Write(message);

                    if (OnStatusChanged != null)
                        OnStatusChanged(this, string.Format(
                        "Message sent: {0}",
                        message));
                }
                catch (Exception ex)
                {
                    if (OnStatusChanged != null)
                        OnStatusChanged(this, string.Format(
                            "Failed to send string: {0}",
                            ex.Message));
                }
            }
        }

        private void StartReading()
        {
            if (!_keepReading)
            {
                _keepReading = true;
                _readThread = new Thread(ReadPort);
                _readThread.Start();
            }
        }

        private void StopReading()
        {
            if (_keepReading)
            {
                _keepReading = false;
                _readThread.Join();
                _readThread = null;
            }
        }

        private void ReadPort()
        {
            while (_keepReading)
            {
                if (_serialPort.IsOpen)
                {
                    //byte[] readBuffer = new byte[_serialPort.ReadBufferSize + 1];
                    try
                    {
                        //int count = _serialPort.Read(readBuffer, 0, _serialPort.ReadBufferSize);
                        //string data = Encoding.ASCII.GetString(readBuffer, 0, count);
                        string data = _serialPort.ReadLine();

                        if (OnDataReceived != null)
                            OnDataReceived(this, data);
                    }
                    catch (TimeoutException) { }
                }
                else
                {
                    TimeSpan waitTime = new TimeSpan(0, 0, 0, 0, 50);
                    Thread.Sleep(waitTime);
                }
            }
        }
    }
}
```

### Usage examples

To retrieve the data, just subscribe to `OnDataReceived` event.

**Example:**

```csharp
using HeiswayiNrird.Singleton;
using System.Windows;

namespace SerialPortSingleton
{
    /// <summary>
    /// Interaction logic for MainWindow.xaml
    /// </summary>
    public partial class MainWindow : Window
    {
        // Constructor
        public MainWindow()
        {
            InitializeComponent();

            // Subscribe to the event
            SerialPortManager.Instance.OnDataReceived += Handler_OnDataReceived;
        }

        // Event handler
        private void Handler_OnDataReceived(object sender, string incomingData)
        {
            // TODO
            // Process the data from 'incomingData' variable...
        }
    }
}
```

For something simpler, you may use anonymous function and marshall it to the main UI thread.

**Example:**

```csharp
using HeiswayiNrird.Singleton;
using System;
using System.Windows;

namespace SerialPortSingleton
{
    /// <summary>
    /// Interaction logic for MainWindow.xaml
    /// </summary>
    public partial class MainWindow : Window
    {
        // Constructor
        public MainWindow()
        {
            InitializeComponent();

            // Subscribe to the event
            SerialPortManager.Instance.OnDataReceived += (sender, incomingData) =>
            {
                // TODO
                // Process the data from 'incomingData' variable...
                double value = Convert.ToDouble(incomingData); // Assuming incomingData contains "123"

                // Update to UI...
                Application.Current.Dispatcher.BeginInvoke(new Action(() =>
                {
                    BindingTextBoxValue = value;
                }));
            };
        }

        // Binding properties
        public double BindingTextBoxValue { get; set; }
    }
}
```

To handle opening or closing the serial port connection, you may just call `Open()` or `Close()` method.

**Example:**

```csharp
// To open/start serial port on COM4 with 9600 bps
SerialPortManager.Instance.Open("COM4", 9600);

// To close/stop serial port on COM4
SerialPortManager.Instance.Close();
```

`SerialPortManager` also provides a public event to be subscribe for receiving any status update/response. To get the status message, just subscribe to `OnStatusChanged` event or `OnSerialPortOpened` event for getting boolean return either the serial port is opened or closed.

**Example:**

```csharp
using HeiswayiNrird.Singleton;
using System.Windows;

namespace SerialPortSingleton
{
    /// <summary>
    /// Interaction logic for MainWindow.xaml
    /// </summary>
    public partial class MainWindow : Window
    {
        // Constructor
        public MainWindow()
        {
            InitializeComponent();

            // Subscribe to the event
            SerialPortManager.Instance.OnStatusChanged += (sender, status) =>
            {
                // Update connection status message to UI
                ConnectionStatus = status;
            };
        }

        // Binding properties
        public string ConnectionStatus { get; set; }
    }
}
```

### Avoiding application hang during serial port closing

Don't worry, this class doesn't have the issue. Here is the design approach;

As you can see from the class source code, there is no `System.IO.Ports.SerialPort.Close()` is used as this will cause a deadlock issue or hang your application. This is because the serial port base stream is locked while serial port events are handled.

Instead, use `ReadPort()` method to be run on a new thread and use `while` loop statement for acquiring or reading data from the serial port. When `SerialPortManager.Instance.Close()` is called, `_keepReading` will be set to FALSE which will stop UI from receiving and updating data while waiting the thread terminates.
