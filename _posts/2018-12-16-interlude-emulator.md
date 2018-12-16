---
layout: post
title:  "Tinkering My PS Vita Interlude: Emulator"
date:    2018-12-16 14:00:00 +0700
excerpt: Today I learned PSVita emulator exists. 
categories: log
---
**DISCLAIMER:**
**I write this mainly to help myself learn things more easily so I can't guarantee the accuracy of the informations presented here.**

Today I learned PSVita emulator exists. Well to be honest, I've already heard the news since couple of months ago, but I haven't had chance to download and try the emulator.

Taken from its website, [Vita3K](https://vita3k.org/) is an experimental PSVita emulator. It can already run [some of homebrew apps](https://vita3k.org/compatibility.html?lang=en) available, but commercial games arent supported. Since I'm currently learning on how to program the Vita, I'm kinda interested on using this as Vita substitution, especially in early stages of development. More informations are available at Vita3K [Github](https://github.com/Vita3K/Vita3K) repository.

Let's get started!

# Setup
The setup process is very easy. Just go to the [website](https://vita3k.org/) and scroll to downloads section. There you can download Vita3K binary specific to your OS, either Windows, OSX, or linux. After downloading the file, you are ready to go.  

# Running your program
To run your program, you can put your `.vpk` file as first parameter of the emulator. For example:

```
./Vita3K hello_world.vpk
```

After you run the command, Vita3K will install your program and run it. Then, if you execute the emulator without any parameter it will present you with list of installed programs. You can then press **G** to hide the GUI.

{% include image.html url="/images/vita3k_list.jpg" max-width="100px" description="Vita3K program list" %}

To test the emulator, I used the [hello_world program]({{site.baseurl}}{% link _posts/2018-12-12-hello-world-vita.md %}) I created [earlier], with a `printf` to see whether it actually waited for 3 seconds or not.

```c
#include <psp2/kernel/threadmgr.h>
#include <psp2/kernel/processmgr.h>
#include "debugScreen.h"

#define printf psvDebugScreenPrintf

int main(int argc, char *argv[]) {
    psvDebugScreenInit();
    printf("Hello, world!\n");
    sceKernelDelayThread(3*1000000); // Wait for 3 seconds
    printf("Exiting...\n");
    sceKernelExitProcess(0);
    return 0;
}
```

Below is what it looks in the emulator. You can right click the image > `View Image` to see it more clearly.

{% include image.html url="/images/vita3k_hello_world.jpg" max-width="100px" description="It works!" %}

# Thoughts
The emulator will be very useful as debugging tool, especially for features that don't need fancy Vita features. Also, it's more fun to run your program in the actual device :). I have yet to discover what the limit of this emulator, but we will find out later after I've done some more implementations.

See you again soon!
