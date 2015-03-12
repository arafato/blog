---
layout: post
title: Weinre on AWS in Combination with Browserstack
categories: [Testing, AWS, Mobile]
tags: [AWS, Browserstack, Testing, Weinre ]
---

####tl;dr

Quickly setting up a service in only a few minutes on AWS that allows
us to debug both our locally and remotely deployed apps and web
applications on almost every device and platform you can imagine via
Browserstack through one common debugging tool.

*This post was published before on my
 [old domain](http://kodedistiller.net/2013/11/16/weinre-on-azure-in-combination-with-browserstack/)
 using Azure as public cloud provider.*

####Debugging Mobile

Debugging HTML5 mobile apps or web applications on mobile devices
isn't big fun nowadays unless you are enjoying the land of
alert-debugging due to mostly absent debugging tools for DOM
inspection or even a JavaScript console. Yes, there is Android
Debugging Bridge (ADB) and since iOS 6 it is fairly easy debugging
your app or web application on your iPhone or iPad. But not every one
of us owns a Mac (needed for debugging), nor does every one of us own
20 different Android devices to thoroughly test it on version 2.0,
2.1, 2.2… 4.4… you get the idea. And what about Windows Phone, Firefox
OS, Tizen, Sailfish OS? I don’t want to set up and learn a new
debugging tool for every platform that’s out there. Most of these
emulators like Windows Phone emulator don’t even provide debugging
support for HTML5 apps or web applications.

So what if we could quickly set up a service in only a few minutes on
AWS that allows us to debug both our locally and remotely deployed
applications on almost every device and platform you can imagine via
Browserstack through one common debugging tool and UI respectively
that provides support for DOM inspection and a JavaScript console
(among others)? What if we could basically clone debug.phonegap.com
and let it talk to Browserstack? Sounds cool? Read on!
