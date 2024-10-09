---
layout: post
author: KING
title: How to make live linex usb drive
date: 2024-10-09T14:45:49.762Z
thumbnail-img: /assets/img/posts/034ds0hehpxygjpu93ceu5h-4.webp
category: hacking
summary: making usb for any live linex
keywords: usb, hacking, linex
thumbnail: /assets/img/posts/code.jpg
permalink: /blog/live-linex_in_usb_drive/
usemathjax: true
---


![](/assets/img/posts/img_20241007_193728.jpg)

l﻿ive linex command

1. o﻿pen GParted

   ![](/assets/img/posts/img_20241007_201838.jpg)
2. o﻿pen terminal and enter this commands

```shellsession
sudo su
wipefs /dev/sdb

DEVICE OFFSET TYPE           UUID             LABEL
sda 0x8001 iso9660 2024-08-18-14-51-59-00 Kali Live
sda 0x1fe dos

wipefs -o 0x8001 -f /dev/sdb
```

1.