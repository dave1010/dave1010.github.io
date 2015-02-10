---
layout: post
title:  "Batch converting images with ImageMagick"
date:   2012-08-22 21:24:07
categories: bash shell linux
---


Every now and then I need to batch resize or change the quality in some images. Photoshop and the GIMP let you do this, which is fine if you’re happy using your mouse or lots of RAM. The most efficient way of processing images is with ImageMagick. ImageMagick is the canonical way to do image manipulation and many GUI tools are based on it. ImageMagick is a useful tool to know and learning the basics of it will hopefully save you lots of time.

First up, you may need to install ImageMagick. A `sudo apt-get install imagemagick` for Ubuntu/Debian or a `yum install ImageMagick` for Fedora/CentOS should do the trick if you’re on Linux. Head to the [ImageMagick download page](http://www.imagemagick.org/script/binary-releases.php) if you’re using a different OS.

ImageMagick’s main commands are `convert` (create a new file) and `mogrify` (overwrite a file). These commands take many options and can do lots of interesting stuff. Here’s a couple of snippets that should come in handy.

## Reducing image quality / size

If you need to reduce the file size of some jpgs (eg so their more suitable for the web), you can mogrify all images in the current directory like this:

    # give all jpg images a quality of 70
    mogrify -quality 70 *.jpg

The 70 here is the quality, which can be anything from 0 to 100. Unfortunately increasing the quality of a low quality image has no effect, [unless you’re CSI](http://www.lolwtfcomics.com/upload/uploads/1317571091.jpg).

*Note: ImageMagick supports multiple input files, so you can easily use them with your shell’s file expansion (e.g. * and ?). If you want more control, you can always use [find and xargs](http://www.linuxplanet.com/linuxplanet/tutorials/6522/1) or a simple [for loop](http://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO-7.html).*

To shrink large images (8MP images are common on today’s smartphones), use the `-resize` option instead of (or as well as) `-quality`:

    # shrink to 1/4 original size
    mogrify -resize 25% photo.jpg 
    # resize to fit in a box 1024x768
    mogrify -resize 1024x768 photo.jpg 
    # resize to max width 800px, keeping aspect ratio, reducing quality to 60
    mogrify -resize 800 -quality 60 photo.jpg

## Convert between JPG / PNG / GIF

Unlike `mogrify`, `convert` takes input and output file parameters. This means converting between different formats is as simple as choosing a different output format:

    convert image.png image.jpg

If you’re dealing with PNG images, [Pngcrush](http://pmt.sourceforge.net/pngcrush/) is useful too.

## Further reading

I’ve only touched on a small fragment of what ImageMagick is capable of. It also allows you to add filters like Instagram and layers like text and watermarks.

If you thirst for more image processing knowledge, read through the documentation for [convert](http://www.imagemagick.org/www/convert.html) and [mogrify](http://www.imagemagick.org/www/mogrify.html) on the ImageMagick site, then have a look at some of the [other commands](http://www.imagemagick.org/www/command-line-tools.html) ImageMagick provides.
