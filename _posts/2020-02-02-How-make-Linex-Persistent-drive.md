---
layout: post
title:  "how to make persistence drive"
description: Linux is my favorite operating system, but I often forget how to set up my Linux drive. This post is a simple guide to help anyone who wants to install Linux and keep their data safe during the process.
tags: miscellaneous
---
I’ll show you how to create a Kali Linux persistent drive, but you can use this method to make a persistent drive for any Linux version. I’ve personally tried it with Parrot OS and Ubuntu.

>  - Requirements

1. any storage devices more then 20gb.
2. computer 🤷🏼‍♂️.

> - Preparation of Drive

1. Download linex live iso file. <a href="https://www.kali.org/get-kali/#kali-live">LIVE iso file is very importent</a> 

   <img src="/assets/blog_image/presistence_drive/1.png" alt="Another image" style="width: 500px; height: auto;">
2. download <a href="https://etcher.balena.io/">etcher balena</a>  to write image on drive.
 <img src="/assets/blog_image/presistence_drive/2.png" alt="Another image" style="width: 500px; height: auto;">

3. open etcher balena to etch iso file to your drive
 <img src="/assets/blog_image/presistence_drive/4.png" alt="Another image" style="width: 500px; height: auto;">

### Your drive is now ready—you can boot and use Linux on any compatible computer.

# ***adding presistence***
1. boot your linex drive with live system mode.
 <img src="/assets/blog_image/presistence_drive/IMG_20241007_193728.jpg" alt="Another image" style="width: 500px; height: auto;">

2. Open GParted on your Linux system.
 <img src="/assets/blog_image/presistence_drive/IMG_20241007_202222.jpg" alt="Another image" style="width: 500px; height: auto;">
.\
3. make new partiton name :- ** persistence **
<img src="/assets/blog_image/presistence_drive/IMG_20241007_202459.jpg" alt="Another image" style="width: 500px; height: auto;">

4. your terminal will look like this
<img src="/assets/blog_image/presistence_drive/IMG_20241007_203046.jpg" alt="Another image" style="width: 500px; height: auto;">



open terminal 

```ruby
 if you have multipal drive then name will change
 first step code
 sudo su
  
 wipefs /dev/sda

 wipefs -o 0x8001 -f /dev/sda

 second step code

 mkdir -p /mnt/usb

 mount /dev/sda3 /mnt/usb

 echo "/ union" > persistence.conf


```

-now boot drive on persistence mode

<img src="/assets/blog_image/presistence_drive/IMG_20241007_203646.jpg" alt="Another image" style="width: 500px; height: auto;">

