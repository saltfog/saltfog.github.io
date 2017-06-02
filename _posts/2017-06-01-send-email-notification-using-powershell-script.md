---
layout: post
title: Send email notification using PowerShell script
description: Automate your task for sending email notification with PowerShell script and Windows SMTP Client.
keywords: software build automation, send email notification, using smtp client in powershell, software build notification
tags: [PowerShell]
comments: true
---

Today, I just want to share a snippet of PowerShell script that can make your task for sending email becomes easier if you're using Windows. However, it still depends on your requirements and the script I share not necessarily can provide you a complete solution or solve your whole problem. Some may need you to modify or derive the script to suit your case.

At work, I have been implementing software build automation a lot. I also have been creating some internal tools or writing some scripts to automate tasks. One of the common tasks in my automation is sending email notification. If the build machine is using Windows, batch and PowerScript are my best friends to the automation process. For this case, I would like to use PowerShell script because it is lightweight and flexible to any further change, no IDE required. I can just use [Notepad2](https://xhmikosr.github.io/notepad2-mod/) to edit it.

> Please note that the snippet below is very straightforward and simply send email notification using SMTP client. If you want to use it, you may go through the code so you can get the idea how it works and where you can get started.

**EmailNotification.ps1**

```powershell
# SETTING UP NECESSARY PARAMETERS
$emailTo = "user1@email.com,user2@example.com" # Separate by comma for multiple email addresses
$emailCC = "user3@email.com,user4@example.com" # Separate by comma for multiple email addresses
$emailFrom = "sender@email.com"
$emailFromDisplayName = "Auto Email Notification"
$smtpServer = "smtp.yourserver.com"
$smtpPort = 0 # 0 = not in use
$enableSSL = $false
$useDefaultCredentials = $true
$username = "" # Provide this when $useDefaultCredentials = $false
$password = "" # Provide this when $useDefaultCredentials = $false
$useHTML = $true
$priority = [System.Net.Mail.MailPriority]::Normal # Low|Normal|High

# ACQUIRING DATA OR INFO, INTERPRET IT AND PROCESS THE RESULT
# Many things you can do here...

# CREATING MESSAGE BODY
# Generally I use HTML for email message body.
$html_var1 = "..."
$html_var2 = "..."
$html_var3 = "..."
$emailBody = $html_var1 + $html_var2 + $html_var3

# CREATING EMAIL SUBJECT
# Sometimes I decide the email subject at last, or auto-generate it.
$emailSubject = "..."

# SENDING EMAIL...
# You might want to do checking/validation beforehand on necessary variables
$mailMessage = New-Object System.Net.Mail.MailMessage
if ($emailFromDisplayName -eq "" -or $emailFromDisplayName -eq $null) {
    $mailMessage.From = New-Object System.Net.Mail.MailAddress($emailFrom)
} else {
    $mailMessage.From = New-Object System.Net.Mail.MailAddress($emailFrom, $emailFromDisplayName)
}
$mailMessage.To.Add($emailTo)
if ($emailCC -ne "") {
    $mailMessage.CC.Add($emailCC)
}
$mailMessage.Subject = $emailSubject
$mailMessage.Body = $emailBody
$mailMessage.IsBodyHtml = $useHTML
$mailMessage.Priority = $priority

$smtp = $null
if ($smtpPort -ne 0) {
    $smtp = New-Object System.Net.Mail.SmtpClient($smtpServer, $smtpPort)
} else {
    $smtp = New-Object System.Net.Mail.SmtpClient($smtpServer)
}
$smtp.EnableSsl = $enableSSL
$smtp.DeliveryMethod = [System.Net.Mail.SmtpDeliveryMethod]::Network

if ($useDefaultCredentials -eq $true) {
    $smtp.UseDefaultCredentials = $true
} else {
    $smtp.UseDefaultCredentials = $false
    $smtp.Credentials = New-Object System.Net.NetworkCredential($username, $password)
}

try {
    Write-Output ("Sending email notification...")
    $smtp.Send($mailMessage)
    Write-Output ("Sending email notification --> Complete")
} catch {
    Write-Output ("Sending email notification --> Error")
    Write-Warning ('Failed to send email: "{0}"', $_.Exception.Message)
}
```

### Executing the script with passing arguments

To execute the script from other program with argument(s) to pass, basically you just need to include `[CmdletBinding()]` and `Param()` on top of your main script code.

```powershell
[CmdletBinding()]
Param
(
    [string]$EmailTo = "",
    [string]$EmailCC = "",
    [string]$EmailFrom = "sender@example.com"
    
    # ...more parameters that suit your needs
    # continue here...
)
```

Then, you can execute your PowerShell script in something looked like this way depending on what kind of program you use. Some program may need you to provide the path of script and the arguments explicitly.

```
./EmailNotification.ps1 -EmailTo "qa-engineer@email.com" -EmailCC "manager@email.com" ...
```

### Handling complex huge script

Sometimes, your PowerShell script may become complex and contains thousand lines of code. In this case, you might want to organize and separate them into multiple functions where each function might have its own parameters too. The example snippet below shows you how it should be done or looked like.

> However, I do not recommend for you to write huge and complex script into a single file. It's best for you to keep your script as simple as possible. If you really need complex operations, it's best for you to create something more like command-line program which you can execute with a set of arguments or configuration file.

```powershell
[CmdletBinding(SupportsShouldProcess=$true, ConfirmImpact='Medium')]
[Alias()]
[OutputType([int])]
Param
(
    [Parameter(Mandatory=$true, Position=0)]
    [ValidateNotNullOrEmpty()]
    [string]$EmailTo = "",

    [Parameter(Position=1)]
    [ValidateNotNullOrEmpty()]
    [string]$EmailFrom = "sender@example.com",
    
    [switch]$EnableLog

    # ...more parameters that suit your needs
    # continue here...
)

function EmailNotification
{
    [CmdletBinding(SupportsShouldProcess=$true, ConfirmImpact='Medium')]
    [Alias()]
    [OutputType([int])]
    Param
    (
        [Parameter(Mandatory=$true, Position=0)]
        [ValidateNotNullOrEmpty()]
        [string]$EmailTo = "",

        [Parameter(Position=1)]
        [ValidateNotNullOrEmpty()]
        [string]$EmailFrom = "sender@example.com",

        [Parameter(Position=2)]
        [AllowEmptyString()]
        [string]$EmailFromDisplayName = "Auto-build Notification System",

        [Parameter(Position=3)]
        [ValidateNotNullOrEmpty()]
        [string]$SmtpServer = "smtp.example.com"

        # ...more parameters that suit your needs
        # continue here...
    )

    # YOUR MAIN SCRIPT GOES HERE WITH OTHER FUNCTIONS...
    
    # Example:
    $otherVar1 = "..."
    $otherVar2 = "..."
    $template = GenerateEmailTemplate -Var1 "..." -Var2 1 -Var3
    
    # Process variable $template...
    # Do other things...
    
    # YOUR MAIN SCRIPT ENDS HERE...
}

function GenerateEmailTemplate
{
    [CmdletBinding(SupportsShouldProcess=$true, ConfirmImpact='Medium')]
    [Alias()]
    [OutputType([string])]
    Param
    (
        [string]$Var1 = "",
        [int]$Var2 = 0,
        [switch]$Var3
    )

    # CODE TO GENERATE EMAIL TEMPLATE HTML HERE... THEN RETURN AS STRING.
}

function CreateLogFile
{
    # CODE TO GENERATE LOG FILE...
}

if (-not ($myinvocation.line.Contains("`$here\`$sut"))) {
    if ($EnableLog -eq $true) {
        CreateLogFile
    }
    EmailNotification -EmailTo $EmailTo -EmailFrom $EmailFrom # ...and more parameters here...
}
```

Happy scripting!
