---
layout: post
title: "FullScreenWrapper2: Python GUI framework for Android"
categories: [Programming, Android, Python]
permalink: /opensource/fullscreenwrapper/
author: srinath
image: assets/images/fullscreenwrapper2/fullscreenwrapper2_demo_screenshots.png

---
**Note: No longer under development** FullScreenWapper2 is a GUI Framework for developing full screen apps using the FullScreenUI API functions provided by 
[Scripting Layer for Android (SL4A)](https://code.google.com/p/android-scripting/) using [Python-for-Android](Shttps://code.google.com/p/python-for-android/)
It enables Python SL4A scripts to get a look & feel similar to Android Java apps using the same Views used by native
Java apps XML Layouts and provides pythonic access to events.

Features
--------
- Managed eventloop & a layout stack enabling easy parent->child->parent transitions
- EventHandler classes with event-matching functions pre-built for standard View events like click,itemclick(ListView) and key
- Device Sensors & other similar SL4A/Custom events can also be caught & managed eventloop + EventHandler class
- Pythonic access to the views and properties of the layout (ie. MainLayout.views.txt_label.background = "#FFAA00AA")

Get it
------
Full documentation & library download is available at the [Python-for-Android Wiki](https://code.google.com/p/python-for-android/wiki/fullscreenwrapper2)

Framework Overview
------------------
![FullScreenWrapper2 framework design](/assets/images/fullscreenwrapper2/fullscreenwrapper2.png)

Screenshots
-----------
![FullScreenWrapper2 Demo Screenshots](/assets/images/fullscreenwrapper2/fullscreenwrapper2_demo_screenshots.png)
