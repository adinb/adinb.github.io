---
layout: post
title:  "Tinkering My PS Vita: Hello World"
date:    2018-12-12 08:00:00
excerpt: Following my last post, we are going to write a hello world app and ran it on Vita.
categories: log
---
{% include vita-series-list.html %}

Following my [last post]({{site.baseurl}}{% link _posts/2017-08-25-tinkering-my-vita.md %}), we are going to write a **hello world** app and ran it on Vita. This approach helped me to focus on how to compile and run the application.

To build and run your application on Vita, you will need to have your Vita jailbroken and Vita SDK properly set up. In this post, I will assume you already have your Vita jailbroken. Also, it'll be better if you are already familiar with C programming.

I did all of the steps on Ubuntu 18.04, and the guide will be written as if all the commands are being run in Ubuntu 18.04. Feel free to tweak the steps as needed.

# Vita SDK
[Vita SDK](https://vitasdk.org) is an open source toolchain for PlayStation® Vita application development. To install the toolchain, you can follow the steps from the official website.

After installing Vita SDK, you can do quick confirmation of the installation by running `arm-vita-eabi-g++`. The command will return `arm-vita-eabi-g++: fatal error: no input files` if you have properly setup the SDK.

All good? Fire up your favorite text editor and let's get started.

# Dependencies
Vita SDK [samples](https://github.com/vitasdk/samples) includes a small utility that you can include to print debug messages in your Vita easily. you can see the utility in [common/debugScreen.h](https://github.com/vitasdk/samples/blob/master/common/debugScreen.h). Aside from that, we are going to use API provided by the SDK.

You can put the utility wherever you want. For easier access, I put mine in a directory one level up from my project directory.


# Main program
Our hello world app will display "Hello world!" for 3 seconds, then exit. `debugScreen` utility did the most job here, by converting characters to pixels that are going to be written to Vita's [framebuffer](https://en.wikipedia.org/wiki/Framebuffer).

The flow is quite obvious. After writing the text to the screen, we call [sceKernelDelayThread](https://docs.vitasdk.org/group__SceProcessmgrUser.html#gaf64b2cd0fc96095e3f77dee2dc9a68c3) to suspend the thread from executing anything for 5 seconds, then exit the process using [sceKernelExitProcess](https://docs.vitasdk.org/group__SceProcessmgrUser.html#gaf64b2cd0fc96095e3f77dee2dc9a68c3).

Put this following code in `src/main.c`.

```c
#include <psp2/kernel/threadmgr.h>
#include <psp2/kernel/processmgr.h>
#include "debugScreen.h"

#define printf psvDebugScreenPrintf

int main(int argc, char *argv[]) {
	psvDebugScreenInit();
	printf("Hello, world!\n");
	
	sceKernelDelayThread(5*1000000);
	sceKernelExitProcess(0);
	return 0;
}
```

This hello world program is also included in the Vita SDK sample. Feel free to take a look at it.

# Supporting files
Now that our code is ready, we want to compile and package it so we can install it on the Vita. In order to do that, we need some supporting files are needed. Feel free to grab the files from Vita SDK repository. I took mine from [here](https://github.com/vitasdk/samples/tree/master/hello_world/sce_sys).

Those files will be in `sce_sys` directory. That directory will roughly be like this

```
sce_sys
├── icon0.webp (1)
└── livearea
    └── contents
        ├── bg.webp (2)
        ├── startup.webp (3)
        └── template.xml (4)
```

Notes:
1. Live area bubble icon
2. Live area background **Can be changed in the manifest**
3. Gate background **Can be changed in the manifest**
4. Manifest file. This file is a simple XML file and quite self explanatory.

# Directory structure
After following the steps, your project directory structure will be like this:

```
hello_world
├── sce_sys
│   ├── icon0.webp
│   └── livearea
│       └── contents
│           ├── bg.webp
│           ├── startup.webp
│           └── template.xml
└── src
    └── main.c
```

So far so good. Next, compilation.

# Compile and Bundle
Finally, we will have our binary file to run. To build our app, you will need CMake. Cmake is a tool to generate project build scripts for specific platforms. In this case, we are using  [makefiles](https://en.wikipedia.org/wiki/Makefile). It should be already installed if you followed installation instructions from Vita SDK page. If not, please follow Cmake installation instruction for your platform.

First, we will need a script named `CMakeLists.txt` in your root project directory. To make this process easier, I will grab one from the sample hello world project. I recommend you to do the same, and try to do some modifications to it.

I won't talk about the build process in detail for now. However, there are some parts that you might be interested in.

This command determine directory to search for headers. You can put the directory you used to store the utility from earlier.
```cmake
include_directories(
  ../common 
)

```

Application metadata are also described here.
```cmake
## Configuration options for this app
# Display name (under bubble in LiveArea)
set(VITA_APP_NAME "Hello World")
# Unique ID must be exactly 9 characters. Recommended: XXXXYYYYY where X = 
# unique string of developer and Y = a unique number for this app
set(VITA_TITLEID  "VSDK00006")
# Optional version string to show in LiveArea's more info screen
set(VITA_VERSION  "01.00")
```

Those values are used by this function to bundle the application. You can also configure application bundle file name here.
```cmake
vita_create_vpk(hello_world.vpk ${VITA_TITLEID} hello_world.self
  VERSION ${VITA_VERSION}
  NAME ${VITA_APP_NAME}
  FILE sce_sys/icon0.webp sce_sys/icon0.webp
  FILE sce_sys/livearea/contents/bg.webp sce_sys/livearea/contents/bg.webp
  FILE sce_sys/livearea/contents/startup.webp sce_sys/livearea/contents/startup.webp
  FILE sce_sys/livearea/contents/template.xml sce_sys/livearea/contents/template.xml
)
```

Now, we are ready to build the application. To do that, first create a folder to contain our build artifacts. Let's call it `build`. After that, go inside that directory, then run `cmake ..` if you're using UNIX / Linux system. What that command do is generating build script and other necessary files inside the `build` folder.

After that, just run `make`, and watch the build running. After the build is done, there will be a .vpk file in `build` directory that's ready to install.

# Install and run
To transfer the VPK file that we have built earlier, you can use either USB connection or FTP server. I used FTP as it's easier for me to setup in my Ubuntu. Please refer [here](https://henkaku.xyz/usage/) for the exact steps.

Below are some screenshots on how it will look on the Vita
{% include image.html url="/images/hello_world_livearea.webp" max-width="100px" description="Livearea display" %}
{% include image.html url="/images/hello_world.webp" max-width="100px" description="Pardon the angle, but Hello world!" %}

Building and running Hello World application on your Vita is exciting, no? In the next part I am going to build a third party library and use it in my own application, but for now you can check [this post]({{site.baseurl}}{% link _posts/2018-12-16-interlude-emulator.md %}) about testing your PS Vita application in your PC. 

Stay tuned!
