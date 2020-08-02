---
layout: post
title: "Using FuseCompress to compress folders in Ubuntu"
tags: 
- Linux
permalink: /blog/fusecompress-on-ubuntu/
author: "Hariharan Srinath"
image: assets/images/generic/linux.svg
---

Fusecompress is a great little legacy utility that uses the FUSE (File System 
in User Space) library to provide an on the fly compressed mountable filesystem 
that in effect lets you compress specific directories in manner completely 
transparent to all other programs.

Why consider using Fusecompress
-------------------------------
Many light-weight Notebooks and all Chromebooks today come with balzingly fast but 
limited capacity Solid State Drives (SSDs) on the theory that people store much of 
their data in the cloud. However, to use these as Android development machines, 
you typically do need to store space hogs like sources and library documentation, 
emulator images etc locally.


I use my HP Chromebook 14 loaded with Ubuntu 14.04 via the ChrUbuntu on a lean 
9GB partition as my primary development machine. I use fusecompress to compress
offline documentation for Android and Python and the huge Android Emulator
system images. This reduces the SSD footprint considerably with barely any impact
on load times. The Emulator startup does become significantly slower but I need to 
to do this only once each session.


Installing fusecompress on Ubuntu 14.04
---------------------------------------

Fusecompress however is not available in the standard Ubuntu repositories and 
must be compiled from source. Given the legacy nature of the codebase, it's 
a bit tricky to compile for novices. This blog post outlines the process step-by-step.


1. **Install Aptitude**  

   When I was using apt-get to install the dependency library sources, I found in 
   a couple of cases the latest development releases of the libraries had 
   conflicts and I had to regress to the previous version to install them. 
   Aptitude does a much better job of solving for conflicts

   ```shell
   sudo apt-get install aptitude
   ```

2. **Install the auto build tools**  

   If they're not installed on your system already, you'll need to install 
   autoconf and automake to be able to build from source.
   
   ```shell
   sudo aptitude install autoconf automake libtool
   ```

3. **Install compression algorithms development packages**   

   Next install the development packages for compression algorithms supported 
   by fusecompress - LZO, LZMA, ZLib & BZ2. I ran into trouble in the LZO package 
   where I had to revert from the 2.06-1.2ubuntu1.1 version to 2.06-1.2ubuntu1 
   version. Aptitude gives you a series of menu choices to handle this smoothly

   ```shell
   sudo aptitude install zlib1g-dev libbz2-dev liblzma-dev liblzo2-dev
   ```

4. **Install the FUSE development package**  

   ```shell
   sudo aptitude install libfuse-dev
   ```

5. **Download the fusecompress sources**  

   You can checkout the fusecompress source from [Github repository](https://github.com/hexxellor/fusecompress)

   ```shell
   git clone https://github.com/hexxellor/fusecompress.git
   ```

6. **Run the fusecompress build process**

   Change to the folder where you've downloaded the fusecompress sources and
   run the following commands. Put the generated fusecompress binary somewhere
   in your system path.

   ```shell
   ./autogen.sh
   ./configure
   make
   ```

Using fusecompress
------------------

The least disruptive way I found to use fusecompress is to simply mount the compressed 
folders exactly where they currently are. The key thing to remember while using 
fusecompress is that it compress files when they get written to disk. So files that 
are already in the folder won't automatically get compressed. The way I typically 
manage this is:

1. **Rename the directory you want to compress**

   ```shell
   mv directoryname temporaryname
   ```

2. **Create an empty directory with the old name**

   ```shell
   mkdir directoryname
   ```

3. **Fusecompress the directory**

   The default compression algorithm used is LZO which is very fast. You can also 
   use LZMA as indicated below to get a better compression ratio. You maw want to 
   add this line to your ~/.profile to automount on boot.

   ```shell
   fusecompress directoryname
   fusecompress -c lzma directoryname
   ```

4. **Move back all the contents**

   Since Fusecompress will now compress each of the files as it is moved back, this
   step will take quite a bit of time depending on the amount of data

   ```shell
   cd temporaryname
   mv *.* ../directoryname
   ```
