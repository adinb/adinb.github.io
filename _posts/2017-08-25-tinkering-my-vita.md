---
layout: post
title:  "Tinkering My PS Vita"
date:    2017-08-25 00:30:00 +0700
---
I bought a 2nd hand Vita on May last year. 2 games were included in the package: FIFA 2013 and ~~Anime Tiddies: The Game~~ Senran Kagura Shinovi Versus. The following week, I sold Senran Kagura. That game is... just not my thing. I didn't find interesting game with low price in my local Vita marketplace, so I was stuck with that FIFA. However, it gets boring after few dozens of consecutive plays. In the end I borrowed Disgaea 3 from my friend and played it for around two month until I got bored again.

When I was browsing for more game info, I stumbled upon a forum thread (or a blog post, I forget the details) about a release of homebrew enabler called [HENkaku](http://henkaku.xyz) for PS Vita. It had been known that Vita is very hard to hack, so I immediately interested. I tried to install HENkaku, and it works. I was really excited seeing a proper file manager inside my Vita. You can browse files, including system files inside your PS Vita using that file manager. Even more , you can even transfer any files via FTP.

I also found an open source toolchain for Vita development. I'm a nerd, so I tried writing simple hello world code and run it on my Vita. And it worked. I also tried writing a program using [Box2D](https://github.com/erincatto/Box2D). When that program ran on the Vita, I was very thrilled. However, I got busy with uni stuffs so I stopped writing program for my Vita.

Inevitably, piracy also started showing at that time, as the access to game data is easier. Being a cheap person, I searched for pirated games and I found Ar Nosurge, a game I want to play since a long time ago. I played that until around last month (I even got the platinum trophy). Driven by guilt for pirating a game, I decided to tinker my Vita again and make homebrew after finishing that game. PS Vita hacking scene has improved quite a bit since last time. And so, I start my journey again from the beginning.

{% include image.html url="/images/platinum.jpg" description="Beautiful." %}

# Installing HENkaku
Proceed with your own risk. HENkaku installation is safe, but anything can happen. Also, this whole process only works with firmware 3.60. if your firmware is newer than that, currently no way for installing HENkaku. However if it's lower than that, you can follow the instruction [here](http://henkaku.xyz/usage/). Note that I only have one Vita, so I haven't try the guide. This post is more like my log for installing HENkaku rather than tutorial. Proper guide for installing HENkaku can be found at <http://henkaku.xyz/usage>.

The installation process hasn't changed since I started tinkering my vita last year. Just open <http://henkaku.xyz> in the Vita browser and follow the instruction carefully. If an error message from the browser pops up, just press Ok and try again. In my case, three error dialog showed 3 times before the installation begin. You can find the troubleshooting page [here](http://henkaku.xyz/usage) so consult that page if anything weird happen. There'll be a black screen with installation information when the installation starts. Just wait until the installation is done.

After the installation is completed, your vita will automatically return to Live Area. There, you can find a new app called molecularShell. You can use that to transfer file with your PC via FTP.

{% include image.html url="/images/live_area.jpg" description="molecularShell bubble." %}

If you check the Vita settings, there'll also a new menu called `HENkaku Settings`. You can configure various settings such as version spoofing and unsafe homebrew support.

{% include image.html url="/images/vita_settings.jpg" description="New settings menu" %}

Congratulations! You have enabled homebrew in your PS Vita.

# Now What?
You can start browse files inside your Vita using FTP client like [FileZilla](https://filezilla-project.org/). To start FTP connection, both of your PSVita and your PC must be connected to a same local connection. Open molecularShell and press `SELECT` to start the FTP server. Then a dialog will pop out with server information. Connect to that server using FTP client and you are good to go. Go find some ~~game dumps~~ homebrews and install it by putting the .vpk file inside `ux0:` then open it inside molecularShell. As for me, I ended up installing a cool homebrew called [VITAident](https://github.com/joel16/VITAident) this time. It shows you various information about your machine. Very cool homebrew indeed.

I'm planning to make this post as part of a series. In the next part, I'm going to write simple program and run it on my Vita.
