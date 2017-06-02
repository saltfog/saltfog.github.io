---
layout: post
title: PowerShell Script - Serial Port Reader
description: My PowerShell script for reading data from a serial port and export the captured data into a file.
keywords: powershell script, serial port, serial communication, ps1
tags: [PowerShell, SerialPort]
comments: true
---

Initial release of my PowerShell script `SerialPortReader.ps1` for reading data from a serial port and export the captured data into a file. This script also available on [my public gist](https://gist.github.com/heiswayi/10b412aa96d3f35d85f5f8c2e0075a33).

```powershell
<#
.SYNOPSIS
   Listens to and read data from a serial port (e.g. COM port)
.DESCRIPTION
   The purpose of this script is to keep listening and read data from a serial port.
   All the data captured will be displayed and log into a file.
.EXAMPLE
   ./SerialPortReader.ps1
.EXAMPLE
   ./SerialPortReader.ps1 -PortName COM3 -BaudRate 9600
.EXAMPLE
   ./SerialPortReader.ps1 -PortName COM3 -BaudRate 9600 -Parity None -DataBits 8 -StopBits One -Handshake None -OutputFile ".\output1.log" -ReadInterval 1000
.LINK
   http://heiswayi.github.io
.AUTHOR
   Heiswayi Nrird, 2016
.LICENSE
   MIT
#>
[CmdletBinding(SupportsShouldProcess=$true, ConfirmImpact='Medium')]
[Alias()]
[OutputType([int])]
Param
(
    [Parameter(Mandatory=$true, Position=0)]
    [ValidateNotNullOrEmpty()]
    [string]$PortName = "COM1",

    [Parameter(Mandatory=$true, Position=1)]
    [ValidateNotNullOrEmpty()]
    [int]$BaudRate = 9600,

    [Parameter(Mandatory=$false, Position=2)]
    [ValidateNotNullOrEmpty()]
    [System.IO.Ports.Parity]$Parity = [System.IO.Ports.Parity]"None",

    [Parameter(Mandatory=$false, Position=3)]
    [ValidateNotNullOrEmpty()]
    [int]$DataBits = 8,

    [Parameter(Mandatory=$false, Position=4)]
    [ValidateNotNullOrEmpty()]
    [System.IO.Ports.StopBits]$StopBits = [System.IO.Ports.StopBits]"One",

    [Parameter(Mandatory=$false, Position=5)]
    [ValidateNotNullOrEmpty()]
    [System.IO.Ports.Handshake]$Handshake = [System.IO.Ports.Handshake]"None",

    [Parameter(Mandatory=$false, Position=6)]
    [ValidateNotNullOrEmpty()]
	  [string]$OutputFile = "notempty",

    [Parameter(Mandatory=$false, Position=7)]
    [ValidateNotNullOrEmpty()]
	  [int]$ReadInterval = 1000
)

<#
.SYNOPSIS
   Listens to and read data from a serial port (e.g. COM port)
.DESCRIPTION
   The purpose of this script is to keep listening and read data from a serial port.
   All the data captured will be displayed and log into a file.
.EXAMPLE
   SerialPortReader
.EXAMPLE
   SerialPortReader -PortName COM3 -BaudRate 9600
.EXAMPLE
   SerialPortReader -PortName COM3 -BaudRate 9600 -Parity None -DataBits 8 -StopBits One -Handshake None -OutputFile ".\output1.log" -ReadInterval 1000
.LINK
   http://heiswayi.github.io
.AUTHOR
   Heiswayi Nrird, 2016
.LICENSE
   MIT
#>
function SerialPortReader
{
    [CmdletBinding(SupportsShouldProcess=$true, ConfirmImpact='Medium')]
    [Alias()]
    [OutputType([int])]
    Param
    (
        [Parameter(Mandatory=$true, Position=0)]
        [ValidateNotNullOrEmpty()]
        [string]$PortName = "COM1",

        [Parameter(Mandatory=$true, Position=1)]
        [ValidateNotNullOrEmpty()]
        [int]$BaudRate = 9600,

        [Parameter(Mandatory=$false, Position=2)]
        [ValidateNotNullOrEmpty()]
        [System.IO.Ports.Parity]$Parity = [System.IO.Ports.Parity]"None",

        [Parameter(Mandatory=$false, Position=3)]
        [ValidateNotNullOrEmpty()]
        [int]$DataBits = 8,

        [Parameter(Mandatory=$false, Position=4)]
        [ValidateNotNullOrEmpty()]
        [System.IO.Ports.StopBits]$StopBits = [System.IO.Ports.StopBits]"One",

        [Parameter(Mandatory=$false, Position=5)]
        [ValidateNotNullOrEmpty()]
        [System.IO.Ports.Handshake]$Handshake = [System.IO.Ports.Handshake]"None",

        [Parameter(Mandatory=$false, Position=6)]
        [ValidateNotNullOrEmpty()]
	      [string]$OutputFile = "notempty",

        [Parameter(Mandatory=$false, Position=7)]
        [ValidateNotNullOrEmpty()]
	      [int]$ReadInterval = 1000
    )

    $proceed = $false
    Write-Output ("Checking PortName...")

    foreach ($item in [System.IO.Ports.SerialPort]::GetPortNames())
    {
        if ($item -eq $PortName)
        {
            $proceed = $true
            Write-Output ("--> PortName " + $PortName + " is available")
            break
        }
    }

    if ($proceed -eq $false)
    {
        Write-Warning ("--> PortName " + $PortName + " not found")
        return
    }

    $filename = ""

    try
    {
        $port = New-Object System.IO.Ports.SerialPort
        $port.PortName = $PortName
        $port.BaudRate = $BaudRate
        $port.Parity = $Parity
        $port.DataBits = $DataBits
        $port.StopBits = $StopBits
        $port.Handshake = $Handshake

        $currentDate = Get-Date -Format yyyyMMdd
        $fileExt = ".log"
        if ($OutputFile -eq "notempty")
        {
            $filename = (Get-Item -Path ".\" -Verbose).FullName + "\SerialPortReader_" + $currentDate + $fileExt
        }
        else
        {
            $filename = $OutputFile
        }

        Write-Output ("Establishing connection to the port...")
        Start-Sleep -Milliseconds 1000
        $port.Open()
        Write-Output $port
        Write-Output ("--> Connection established.")
        Write-Output ("")
    }
    catch [System.Exception]
    {
        Write-Error ("Failed to connect : " + $_)
        $error[0] | Format-List -Force
        if ($port -ne $null) { $port.Close() }
        exit 1
    }

    Start-Sleep -Milliseconds 1000

    do
    {
        $key = if ($host.UI.RawUI.KeyAvailable) { $host.UI.RawUI.ReadKey('NoEcho, IncludeKeyDown') }
        if ($port.IsOpen)
        {
            Start-Sleep -Milliseconds $ReadInterval

            #$data = $port.ReadLine()
            $data = $port.ReadExisting()

            <#
            [byte[]]$readBuffer = New-Object byte[] ($port.ReadBufferSize + 1)
            try
            {
                [int]$count = $port.Read($readBuffer, 0, $port.ReadBufferSize)
                [string]$data = [System.Text.Encoding]::ASCII.GetString($readBuffer, 0, $count)
            }
            catch { }
            #>

            $length = $data.Length

            # remove newline chars
            $data = $data -replace [System.Environment]::NewLine,""

            $getTimestamp = Get-Date -Format yyyy-MM-dd HH:mm:ss.fff
            Write-Output ("[" + $getTimestamp + "] " + $data)
            if ($length -gt 0) { ExportToFile -Filename $filename -Timestamp $getTimestamp -Data $data }
        }
    } until ($key.VirtualKeyCode -eq 81) # until 'q' is pressed

    if ($port -ne $null) { $port.Close() }
}

<#
.SYNOPSIS
   Listens to and read data from a serial port (e.g. COM port)
.DESCRIPTION
   The purpose of this script is to keep listening and read data from a serial port.
   All the data captured will be displayed and log into a file.
.EXAMPLE
   ExportToFile -Filename <fullpath> -Timestamp <time string> -Data <data string>
.AUTHOR
   Heiswayi Nrird, 2016
.LICENSE
   MIT
#>
function ExportToFile
{
    [CmdletBinding(SupportsShouldProcess=$true, ConfirmImpact='Medium')]
    [OutputType([string])]
    Param
    (
        [Parameter(Mandatory=$true, Position=0)]
	      [string]$Filename,

        [Parameter(Mandatory=$true, Position=1)]
        [string]$Timestamp,

        [Parameter(Mandatory=$true, Position=2)]
        [string]$Data
    )

    try
    {
        "[" + $Timestamp + "] " + $Data | Out-File -FilePath $Filename -Encoding ascii -Append
    }
    catch [System.Exception]
    {
        Write-Warning ("Failed to export captured data : " + $_)
        return
    }
}


if (-not ($myinvocation.line.Contains("`$here\`$sut"))) {
    SerialPortReader -PortName $PortName -BaudRate $BaudRate -Parity $Parity -DataBits $DataBits -StopBits $StopBits -Handshake $Handshake -OutputFile $OutputFile -ReadInterval $ReadInterval
}
```
