---
layout: post
title:  "XP-Pen Deco Pro Graphic Tablet on Ubuntu 20.04"
date:    2021-01-16 15:00:00
excerpt: Yeah, I bought a new graphic tablet.
categories: log
---

*Note: This post is not a review nor intended to be one. I'm not giving recommendations to or not to purchase the tablets mentioned in this post. Enjoy the read!*

## But Why?
I've been using my [Wacom Intuos Draw CTL-490](https://web.archive.org/web/20170419201159/http://wacom.com/en-us/products/intuos-draw) for almost five years. The work area is heavily scratched, and the bundled pen nibs are all spent. Otherwise, it's still working perfectly fine. I rarely draw these days, so I was not interested in finding a replacement for it. (Un)fortunately, that was the case until my friend asked me whether he should learn digital painting or not. I immediately browsed the internet for good beginner graphic tablets just because I want him to buy things. 

It backfired.

My friend is a Linux guy, so I started searching for graphic tablets with official Linux support. XP-Pen was the first one I found with Linux support. Their products are also cheaper compared to Wacom tablets in the same class. When I was browsing for XP-Pen tablets, I found [XP-Pen Deco Pro Medium](https://www.xp-pen.com/product/432.html). It has an 11" x 6" work area, much bigger than my 6.0"x 3.7" Intuos work area. It also has a rotatable ring and a circle touchpad that we can remap to custom shortcuts. I got tempted immediately to get one for myself. My main workstation was running Ubuntu, so the Linux support increased the temptation even further.

The next morning, I ordered the Deco Pro tablet. The tablet arrived in less than 90 minutes, sparing me no time for remorse. I then tested it using Krita on my Windows machine, and all features worked fine after installing the driver. Usually, with tablets like this, we have to uninstall other existing graphic tablet drivers. I had my Wacom tablet driver installed, but I didn't have to uninstall that driver for the Deco Pro to work. Things were looking good, so I went to test the Deco Pro on my Linux machine.

{% include image.html url="/images/deco-pro-wacom-size.webp" description="Look at the size difference!"%}

## Default Configuration (Generic HID + Libinput)
I am using Ubuntu 20.04 with Linux kernel 5.4. Ubuntu was able to detect the tablet without needing any additional configurations. To test the tablet features, I used Krita.

Things that work:
- Stylus tracking
- Pen pressure
- Shortcut buttons (but I couldn't remap the buttons)
- Ring control (I also couldn't remap this. By default, it's used to zoom in/out)

{% include image.html url="/images/deco-scribbles.webp" description="Some quick scribble tests, the pressure sensitivity is excellent."%}

I got two issues with the default configuration. Both my laptop screen and an external display are enabled, so the work area got mapped to both screens combined. Running `xinput map-to-output 30 <display_name>`  immediately fixed the issue. The trackpad is also not working properly. The whole tablet even stopped working when I touched the trackpad. When that happened, I had to reconnect the USB plug to get the tablet to work again.

I think the Linux generic HID driver and libinput did a great job handling the tablet. The tablet's USB HID implementation likely doesn't stray far from the standard as well, which is good. There's an open source driver project to support generic graphic tablets called [DIGImend](https://digimend.github.io/). However,
DIGImend doesn't support this tablet yet. Maybe I should take a look at it?

## Xorg Wacom Driver
I also tried using Xorg Wacom Driver to see if it could pick up the buttons. Unfortunately, it couldn't. I could set the screen mapping using the `xsetwacom` utility, but the Wacom utility from GNOME couldn't detect the tablet. The rest of the functionalities seem to be the same as the libinput ones.

## XP-Pen Linux Driver
XP-Pen provides [a beta Linux driver for Deco Pro](https://www.xp-pen.com/download-421.html). It's a shame the driver is closed-source, [apparently because they're concerned about their competitors](https://twitter.com/XPPEN/status/1019870192464269312). It's still better than nothing though. Running the driver is very easy. I only had to run the given executable file. It needed to be run with superuser privilege though, so I had to enter my password every time I want to use the tablet. There's a way to avoid that, described by [this blog post comment](https://thehackerdiary.wordpress.com/2019/12/21/linux-tips-configuring-xp-pen-on-linux/#comment-706).

With the driver, I could:
- Remap the pen buttons and tablet buttons
- Reassign the trackpad and the ring to keyboard shortcuts
- Adjust the light brightness.
- Map the tablet work area to the display

One annoyance I found is that I could only remap the ring and the trackpad to a set of predefined options. Considering that the driver is still in beta, I think the functionality is good enough. You should be able to use the tablet comfortably enough if you don't care about the ring or the trackpad.

{% include image.html url="/images/ring-settings.webp" description="The ring configuration page. There's no option for custom key mappings."%}


## Conclusion
I'm happy with how the tablet performs on Linux. The ring settings are a bit limiting, so I tried to disassemble the input driver from XP-Pen. I managed to overwrite the hardcoded options to the shortcuts of my liking, giving me a bit more options. In the next post, I'll show the way I've done it. 

Until next time!
