---
layout: post
title: "Making Android Studio (or Eclipse ADT) work on 64-bit Ubuntu Saucy 13.10 or Trusty 14.04"
tags: programming coding android
permalink: /blog/android-studio-on-trusty/
author: "Hariharan Srinath"
---

**Note: This is of historical interest only.** Getting Android Studio (or Eclipse Android Development Tools)
to run successfully on 64-bit (ie. amd64)
installations of the newer Ubuntu (or underlying Debian) distributions like Trusty Thar (14.04)
is not a very straightforward process. While the IDE you download itself will work assuming you have JDK
installed, it will throw a bunch of build errors when you try to build any app. This article explains
how to install the correct dependencies.

Why does this happen
--------------------
The build errors happen because the Android `build-tools` are actually 32-bit (ie. i386) binaries dynamically linked 
to several 32-bit libraries. For instance, this is what the the `file` outputs on my installation 
of the `aapt` tool in the `build-tools` folder within the sdk
    file aapt
    aapt: ELF 32-bit LSB  shared object, Intel 80386, version 1 (SYSV), dynamically linked 
    (uses shared libs), for GNU/Linux 2.6.8, not stripped

While the 64-bit installations of Linux can run 32-bit binaries, they do not come pre-installed with 
the necessary 32-bit libraries pre-installed. Googling throws up dozens of posts advising installation
of the `ia32-libs` but this advise is unfortunately outdated. Newer versions of Ubuntu from 13.10 Saucy
onwards have [removed](http://askubuntu.com/questions/359156/how-do-you-run-a-32-bit-program-on-a-64-bit-version-of-ubuntu/359184#359184)
the `ia32-libs` package and rely fully on [Multiarch](https://wiki.debian.org/Multiarch) which is a
much more comprehensive solution.

The Solution
-------------
To get Android Studio (or Eclipse ADT) build properly on the newer 64 bit Ubuntu releases like 13.10 
Saucy or 14.04 Trusty you need to add the 32-bit i386 library dependencies as follows:

```shell

sudo dpkg --add-architecture i386
sudo apt-get install libc6:i386 libncurses5:i386 libstdc++6:i386 zlib1g:i386

```

That's it! You shouldn't get the build errors any more! Happy app development!
