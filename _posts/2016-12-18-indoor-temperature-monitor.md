---
layout: post
title: Indoor Temperature Monitor
description: Simple Arduino-based project for monitoring and logging indoor temperature of my house. The application is built in .NET C# with live graph using OxyPlot.
keywords: c# programming, serial port, serial communication, indoor temperature monitor, arduino uno, ds18b20 temperature sensor, oxyplot graph
tags: [CSharp, SerialPort, Oxyplot, Arduino]
comments: true
---

Indoor Temperature Monitor is basically a simple Arduino based project for monitoring and logging indoor temperature of my house. Since I have old, unused Arduino Uno board, so I bought [1-wire digital temperature sensor (DS18B20)](https://www.maximintegrated.com/en/products/analog/sensors-and-sensor-interface/DS18B20.html) and make use of my Arduino board for this simple project.

### Arduino board setup with DS18B20

![DS18B20 with Arduino Uno](http://i.imgur.com/9OlPFLG.png)

Here's the Arduino sketch, compiled with [Arduino software](https://www.arduino.cc/en/Main/Software) v1.6.13

```c
#include <OneWire.h>
#include <DallasTemperature.h>

#define ONE_WIRE_BUS 2 /* Connect to Pin 2 */

/* Set up a oneWire instance to communicate with any OneWire device*/
OneWire ourWire(ONE_WIRE_BUS);

/* Tell Dallas Temperature Library to use oneWire Library */
DallasTemperature sensors(&ourWire);

void setup() /* SETUP: RUNS ONCE */
{

delay(1000);
Serial.begin(9600);
//Serial.println("Temperature Sensor: DS18B20");
delay(1000);

/* Start up the DallasTemperature library */
sensors.begin();

}


void loop() /* LOOP: RUNS CONSTANTLY */
{

//Serial.println();
//Serial.print("Requesting temperature...");
sensors.requestTemperatures(); // Send the command to get temperatures
//Serial.println("DONE");

//Serial.print("Device 1 (index 0) = ");
//Serial.print(sensors.getTempCByIndex(0));
Serial.println(sensors.getTempCByIndex(0));
//Serial.println(" Degrees C");
delay(1000);

}
```

### Indoor Temperature Monitor application

Indoor Temperature Monitor is a simple WPF application built in .NET C#. This application uses [OxyPlot](http://www.oxyplot.org/) for the graph plotting and reads temperature value sent by Arduino board using [serial port](https://msdn.microsoft.com/en-us/library/system.io.ports.serialport(v=vs.110).aspx) connection. The background color of current temperature will automatically change based on certain temperature range. The temperature reading will be logged into a CSV file for a certain time interval.

![Indoor Temperature Monitor](http://i.imgur.com/8CPtSVg.png)

#### Source Code and Downloads

The latest source code can be found on [GitHub](http://github.com/heiswayi/IndoorTempMonitor) repository, or you may download the latest compiled binary version from the [release page](https://github.com/heiswayi/IndoorTempMonitor/releases).
