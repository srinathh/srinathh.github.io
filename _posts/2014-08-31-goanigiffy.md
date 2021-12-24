---
layout: post
title: "GoAniGiffy: A versatile animated GIF creator"
tags:
- Programming
- Go
- Video
permalink: /opensource/goanigiffy/
author: srinath
featured: false
image: assets/images/ardgrafix6100/ardgrafix6100-video.gif
---
GoAniGiffy is a small utility written in the Go Programming language 
to create animated GIFs from a set of alphabetically sorted image
files (eg. video frames extracted as JPEGs with MPlayer or VLC)
with support for basic image editing operations like 
cropping, scaling, rotation & flip. Scaling & Downsampling 
the images for creating the GIF are performed with high
quality algorithms.

Motivation
----------
Animated GIFs have made a comeback as very convenient ways to post short video clips or slideshows on the web 
without needing Flash, YouTube or Flickr embedding. Their convenience often outweights image quality limitations
(only 256 colors palette) and poor compression for basic uses. 

While a number of free web services have sprung up to convert videos to animated GIFs, they do not typically
allow editing beyond basic trimming. You _could_ use a traditional video editor of course but the free ones
are not exactly intuitive and have limited utility in creating slideshows. 

GoAniGiffy
----------
GoAniGiffy is a small utility written in [Go Programming language](http://www.golang.org) that was born out 
of the need to convert my [demo video](https://www.youtube.com/watch?v=esBDMSmbg3E) of 
[ArdGrafix6100](/opensource/ardgrafix6100/) into an [animated GIF](/assets/images/ardgrafix6100/ardgrafix6100-video.gif) for 
hosting on GitHub Pages. This video was originally shot on a phone camera and I wanted to crop it 
to show only the LCD Display with some scaling and rotation. I tried MPlayer first but found it does 
not use good dithering in downsampling the video to 256 colors for GIFs resulting in poor splotchy images.

GoAniGiffy converts a set of alphabetically sorted image files (eg. video frames extracted as JPEGs)
into an animated GIF with the options to crop, scale, rotate & flip source images. Downsampling
and Scaling are done with high quality algorithms from the `github.com/disintegration/imaging` library by 
[Grigory Dryapak](https://www.github.com/disintegration/imaging) and the [Go Standard Library](http://golang.org/pkg/image/draw/#Drawer)

Download
------
[Zipped executable binaries]( https://github.com/srinathh/goanigiffy/tree/master/binaries) are available for Linux 
& Windows in both 32 bit & 64 bit flavors. Download the zip appropriate to your OS and extract somewhere in your
system PATH.  If you want to [build from source](https://github.com/srinathh/goanigiffy) `go get` should fetch the imported libraries & build the utility
in your $GOPATH/bin.  
    
    go get github.com/srinathh/goanigiffy

Usage
-----
GoAniGiffy is a command line utility to be used from the terminal - no GUIs for the present. Use GoAniGiffy with
a command line of the form below. All parameters are optional and have sane default values.

    goanigiffy -src="<file name pattern>" -<option>=<value> -<option>=<value>... -dest="<gif file name>"

### Image Options
- `-src = "<file name pattern>"`  
  This specifies the input images using a file name pattern. You can use 
  standard wildcards or a [more complex pattern](http://golang.org/pkg/path/filepath/#Match) supported by 
  the `path/filepath` package in Go Language. GoAniGiffy will find matching files and sort them
  alphabetically before processing which makes it easy to use GoAniGiffy with numbered image frames extracted
  from videos.  If you do not specfiy `-src`, GoAniGiffy will use `"*.jpg"` as the file pattern.

- `-dest="<gif file name>"`  
  This specifies the file name and path for the output gif file. If you do not specifiy an
  output file, GoAniGiffy will by default create "movie.gif" in the current folder.

- `-delay=<integer value>`  
  This specifies the delay between frames of the image in multiples of hundredths of a second.
  A value of 3 which is the default should theoritically give you 33 frames per second close to the default video speed.

### Cropping Options
- `-cropleft=<integer value>`  
  If you want to crop the images, specify the left co-ordinate of the top-left corner of the
  rectangle you would like to crop here. The default value is 0

- `-croptop=<integer value>`  
  If you want to crop the images, specify the top co-ordinate of the top-left corner of the
  rectangle you would like to crop here. The default value is 0
 
- `-cropwidth=<integer value>`  
  If you want to crop the images, specify the width of the rectangle you would like to crop
  here. The default value is -1 which specifies the full image width.

- `-cropheigh=<integer value>`  
  If you want to crop the images, specify the height of the rectangle you would like to crop
  here. The default value is -1 which specifies the full image height.

### Other Image Editing Options
- `-scale=<decimal value>`  
  If you want to scale the images, specify a scaling factor here in terms of decimals. For instance
  to make the animated GIF half the size as source images use -scale=0.5. The default value is 1.0 
  which specifies no scaling

- `-rotate=<0 or 90 or 180 or 270>`  
  If you want the images to be rotated, specify the rotation angle in degrees here. Arbitrary
  rotations are not supported and you can only use 90, 180 or 270. The default value is 0 which specifies no rotation

- `-flip=<"none" or "horizontal" or "vertical">`  
  If you want the images to be flipped, specify "horizontal" or 
  "vertical" here. The default value is "none" which specifies no flipping

### Other Options
- `-verbose`  
  By default GoAniGiffy will process the files and exit without any messages unless there is an error. 
  Specifying -verbose will show in-process messages as the individual frames get processed

Extracting Frames from Video
----------------------------
If you want to convert a video to an animated GIF, you first need to extract frames. I use [MPlayer](http://www.mplayerhq.hu/)
with a command line like below to extract JPEG frames. The `-ss 6 -endpos 8` options specify extraction between the 6th and 8th seconds

    mplayer -vo jpeg:quality=80 -nosound -ss 6 -endpos 8 vid.mp4

Example
-------
This command line creates a movie.gif from the images in the [sample folder]( https://github.com/srinathh/goanigiffy/tree/master/sample)
of the source repository.

    goanigiffy -src="sample/*.jpg" -dest="sample/movie.gif" -cropleft=100 -croptop=280 -cropwidth=550 -cropheight=351 -scale=0.5 -rotate=270 -verbose


License
-------
GoAniGiffy code is licensed under the [Apache v2.0](https://github.com/srinathh/goanigiffy/blob/master/LICENSE) license.
All other media files & documentation in the repository is licened under <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

