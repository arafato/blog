---
layout: post
title: "Turning off IE’s Enhanced Security Configuration on Windows Server 2012"
date: 2013-12-22
author: oliver
categories:
- blog
- Azure
---

Since I was reading
[Scott Hanselman’s blog post](http://www.hanselman.com/blog/UsingASurface2RTARMToGetActualWorkDoneRemoteDesktopVisualStudioAzure.aspx)
on how to using a Surface RT in combination with Azure VM I more and
more find myself using this setup. No matter if you’re working under
Linux or Windows, you can get the full power of a full-grown 16 core,
64 GB RAM number crunching monster right in your lightweight and handy
Surface device (or any other device with an RDP app). Welcome back
thin-clients!

Now what’s really annoying when you’re working the first time with a
Windows Server 2012 VM under Azure is IE’s security settings. <!--more--> IE is
really a big big baby when it comes to exploring the internet.

![alt text](/assets/img/blog/2013-12-22-turning-off/ie-enhanced-security-configuration-popup.png "No way to download anything from IE per default")

No matter which site you you want to go to (e.g. bing.com) a security
alert is your constant companion. No matter what file you want to
download (e.g. Git Installer), IE simply says no!

![alt text](/assets/img/blog/2013-12-22-turning-off/ie-enhanced-security-configuration-popup2.png "No matter where you go, a security alert is your constant companion.")

You can thank IE’s enhanced configuration for that which is turned on
by default. Fortunately, it is quite simple to turn it off. Go to
Server Manager and select Local Server (see below screenshot).

![alt text](/assets/img/blog/2013-12-22-turning-off/ie-enhanced-security-configuration-1024x382.png "No way to download anything from IE per default")

Now on the right column, you will see an option **IE Enhanced Security
Configuration**, which is turned on by default. Simply turned it off
(klick on it) to regain full control over your browser and get rid of
all these annoying security alerts.
