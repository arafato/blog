---
layout: post
title: "Weinre on Azure in Combination with Browserstack"
date: 2013-11-16
author: oliver
categories:
- blog
- Testing
- Debugging
- Mobile
- Azure
---

####tl;dr
*Quickly setting up a service in only a few minutes on Azure that
allows us to debug both our locally and remotely deployed apps and web
applications on almost every device and platform you can imagine via
Browserstack through one common debugging tool.*

####Debugging Mobile
Debugging HTML5 mobile apps or web applications on mobile devices isn’t big fun nowadays unless you are enjoying the land of alert-debugging due to mostly absent debugging tools for DOM inspection or even a JavaScript console. <!--more--> Yes, there is Android Debugging Bridge (ADB) and since iOS 6 it is fairly easy debugging your app or web application on your iPhone or iPad. But not every one of us owns a Mac (needed for debugging), nor does every one of us own 20 different Android devices to thoroughly test it on version 2.0, 2.1, 2.2… 4.4… you get the idea. And what about Windows Phone, Firefox OS, Tizen, Sailfish OS? I don’t want to set up and learn a new debugging tool for every platform that’s out there. Most of these emulators like Windows Phone emulator don’t even provide debugging support for HTML5 apps or web applications.

So what if we could quickly set up a service in only a few minutes on Azure that allows us to debug both our locally and remotely deployed applications on almost every device and platform you can imagine via Browserstack through one common debugging tool and UI respectively that provides support for DOM inspection and a JavaScript console (among others)? What if we could basically clone debug.phonegap.com and let it talk to Browserstack? Sounds cool? Read on!

####Meet the Three: Weinre, Browserstack and Azure
So what ingredients do we need to cook our ultimate mobile debugging
soup? Not too many: [Azure](http://www.windowsazure.com/), [Browserstack](http://www.browserstack.com) and [Weinre](http://people.apache.org/~pmuellr/weinre/docs/latest/) (WEb INspector Remote).

Weinre is part of the Apache Cordova project and [thanks to MS Open
Technologies it is no longer restricted to Webkit browsers but now
also works with IE 10+ and Firefox](http://msopentech.com/blog/2013/05/31/now-on-ie-and-firefox-debug-your-mobile-html5-page-remotely-with-weinre-web-inspector-remote/). If you are familiar with the F12
tools on IE, Firebug on Firefox or Web Inspector on Chrome, then you
will feel right at home to debug your HTML5 pages on mobiles
devices. Browserstack is a cool service that lets you fire up almost
every browser on almost every platform you can imagine in the sky,
including a few dozen mobile devices running Android, iOS and WP8. You
can then let these browsers point to your externally hosted web
applications or even to your locally deployed app or web app by
setting up a tunnel to Browserstack. And last but not least,
Azure. Microsoft’s cloud platform which we are going to use set up a
VM that will host our service.

####Setting it all up in 5 minutes
So why would you want to host Weinre on Azure? Well, there a couple of
good reasons. First, Weinre is a server (running on Node.js) that
communicates with your app or web application that you want to
debug. This is realized by the apps’s inclusion of a special script
(via HTML script tag) that is hosted on the Weinre server.

Running it on the public internet allows your app to communicate with
Weinre no matter where on the Intra/Internet it is deployed. Second,
you only need to set up this service once and then everyone on your
team may just use it. No configuration errors, no endless fiddling
with IP-settings, no waste of precious time. Simple.

Alright, let’s go through the steps needed to set this service up and
then I’ll show you some really amazing things you can do with it.

First of all, go to
[http://www.windowsazure.com](http://www.windowsazure.com) and get a
free 1-month trial (if you don’t already have an account) worth
$200. This is enough to let your VM run for thousands of hours… more
hours than your free trial period.

Then create a new Windows Server 2012 VM. For our purpose I would
suggest to go with Medium which is 2 cores and 3.5 GB RAM (but as
always, it depends on your requirements). Give it a username and
password and choose a region which is close to you.

When it has finished to provision your VM, you need to configure its
endpoints. Weinre will be listending on port 8080 per default. So we
need to make sure that it can receive messages on that port. Per
default, Azure only configures endpoints for Remote Desktop Protocol
(RDP) and Powershell. So in your VM’s dashboard go to Endpoints and
add a new entry for Weinre. As you can see below, I configured it to
forward any messages send to its public port 8080 to its private
port 8080. The private port is used internally by the virtual machine
to listen for traffic on that endpoint. The public port is used by the
Windows Azure load balancer to communicate with the virtual machine
from external resources.

![alt text](/assets/img/blog/2013-11-16-weinre-browserstack/endpoints.png "Configuring endpoints for Weinre on Azure Management Portal")


Then connect per RDP to your VM. This is death simple. Just click on
the Connect button you will find in the lower left of the dashboard
view. When you are connected, first thing we need to install is
Node.js (Weinre runs on top of it). Fire up IE, go to
[http://nodejs.org/](http://nodejs.org/) and install it from
there. Once it has been set up open up the command-line and type npm
–g install weinre. This will install Weinre through the Node.js
package management system. Then start weinre with weinre –boundHost
<IPv4 address of your VM>. If you want to change the port it will
listen to, use –boundPort but don’t forget to adjust your endpoint in
the Azure management portal accordingly! Believe it or not, now you’re
all set! Now to the fun part!

####Let the Magic happen!
For the rest of this post it is assumed that you include the following
script-tag in your app. This let’s your app talk to the weinre
server:

{% highlight html %}
<script src="http://[Weinre-Server's IP]:[port]/target/target-script-min.js#anonymous" />
{% endhighlight %}

Keep in mind that only one device at a time can be connected under the
same URL. The device that connects last is the active one. If you need
to support more multiple devices simultaneously just provide different
names after the hashtag.

Let’s start easy. Ever tried to debug your HTML5 Windows Phone 8 app?
No matter being run in the emulator or on a real device? No? Well,
comes as no surprise because it is not supported (unless you’re a fan
of alert()-debugging). As you can see in the below picture, I’m
debugging an HTML5 app on my Lumia 920. In particular, I have just
modified the DOM. I can select every element I want, change its
contents, styles and get immediate visual feedback. I can also switch
to the JavaScript console and evaluate and run any code in the context
of my app that I like.

![alt text](/assets/img/blog/2013-11-16-weinre-browserstack/lumia.jpg "DOM manipulation with Weinre on Azure interacting with my Lumia 920")

Just as an example, I’ve started the same application in the WP8
emulator and ran some alert-statements as can be seen in below
picture. The console is even more useful, by providing error logs,
warnings, etc. Great!

![alt text](/assets/img/blog/2013-11-16-weinre-browserstack/working-console.jpg "JavaScript console on Azure interacting with my local WP8 emulator")

But we can even turn this up one notch! This time, we will go with an
ASP.NET MVC application. We will host it on an Azure Website, but want
to see how it looks and behaves on an Android Nexus 7 tablet. At the
same time, I want to debug it with Weinre just like I did with my WP8
app. Of course I don’t have a Nexus at my disposal so I just fire one
up on Browserstack. (We could also host it locally on IIS Express. For
that matter, we would need to create a tunnel between Browserstack and
our local system. This is dead easy (unless they aren’t using
SSL-Breakers at your company’s intranet) and
[well documented](http://www.browserstack.com/local-testing) on
Browserstack.com.)

![alt text](/assets/img/blog/2013-11-16-weinre-browserstack/browserstack.jpg "Debugging an ASP.NET app hosted on Azure running on Browserstack on a Nexus 7")

Now look at above screenshot! Isn’t that amazing? What you’re seeing
is our ASP.NET MVC app hosted on an Azure Website running on
Browserstack on a Nexus 7 tablet on the right side. On the left, I’m
debugging it with Weinre on Azure. I’m manipulating its DOM and get
instant visual feedback. I can also play around with the JavaScript
console and do all sorts of funny things (such as starting an alert
message).

Appreciate any comments or feedback!  Have fun!
