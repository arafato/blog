---
layout: post
title: "Setting IP-Ranges in Microsoft Azure"
date: 2013-11-01
author: oliver
categories:
- blog
- IIS
- Code-Snippets
- Azure
---

Often you need to restrict or exclusively grant access to your web
page based on certain IP ranges. For example you might want to make
sure that only certain IP ranges are allowed to access your test
environment in Microsoft Azure. Remember, websites and cloud services
are all public per default.  It’s straightforward to configure IP
ranges in IIS through a UI, but of course things should work
automatically when a new instance of a web/worker role is deployed.
Fortunately, all you need is a little powershell script that will do
the job for you:

{% highlight powershell linenos %}
import-module servermanager
import-module webadministration
Add-WindowsFeature Web-IP-Security
# Disabling access for anyone per default
Set-WebConfigurationProperty -Filter /system.webserver/security/ipsecurity -Name allowUnlisted -Value $false -PSPath 'IIS:'
# Explicitly setting allowed ip ranges for...
# Some IP Range (XXX.XXX.XXX.*)
Add-WebConfiguration -Filter /system.webserver/security/ipsecurity -PSPath 'IIS:' -Value @{ipAddress='XXX.XXX.XXX.XXX';subnetMask='255.255.255.0';allowed=$true
# Some other IP Range (XXX.XXX.XXX.*)
Add-WebConfiguration -Filter /system.webserver/security/ipsecurity -PSPath 'IIS:' -Value @{ipAddress='XXX.XXX.XXX.XXX';subnetMask='255.255.255.0';allowed=$true
{% endhighlight  %}

Key is to add the Windows feature Web-IP-Security which allows you to
configure allowed/disallowed IP ranges.In line 5 of this script we
then disable access for any IP address per default. In line 8 and 10
we then explicitly grant access to some IP ranges. All that is left to
do is to call this script from a startup task to ensure that every
time a new instance is deployed IIS is automatically configured
accordingly.

Have fun!
