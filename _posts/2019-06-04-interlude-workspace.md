---
layout: post
title:  "Tinkering My PS Vita Interlude: My Workspace"
date:    2019-06-04 09:00:00
excerpt: We won't write a game just yet.
categories: log
---
{% include vita-series-list.html %}

People usually are quite fussy with their development environment, and so do I. I use a text editor called Neovim as my main editor. For you who haven’t heard it, Neovim is a Vim-based text editor with extra features and a nice community. I will talk about my current workspace setup for writing Vita programs.

# VitaSDK toolchain
I use the recommended setup on the official webpage.

# Linting
I rely on [ALE plugin](https://github.com/w0rp/ale) for linting. ALE is compatible with gcc, which we use to compile our codes. However, it’s not fully working out of the box because it's using default installation of gcc.

{% include image.html url="/images/vita-linter.webp" max-width="100px" description="Nicely detailed error message" %}

Below is my ALE configuration. It’s fairly self-explanatory, but the idea is to use the VitaSDK toolchain instead of installed gcc. I also add a directory containing shared headers that don’t belong to the toolchain.

```
let g:ale_linters = {'c': ['gcc']}
let b:ale_c_gcc_executable = 'arm-vita-eabi-gcc'
let g:ale_c_gcc_options = '-std=c11 -Wall -I/home/adinb/projects/vita-playground/common'
```

I have a hardcoded path there, but I prefer this rather than having a global variable floating in the system because it’s easier to check and change if necessary. This piece of the parameter is system-specific as well, so I didn’t bother to make it more general.

# Code completion
I’ve been spoiled by IDE code completion, so I want that in my tools as well. Just look at this screenshot below. You can open the image in a new tab or window to see it more clearly.

{% include image.html url="/images/vita-completion.webp" max-width="100px" description="This will be very helpful" %}

For autocompletion, I use [YCM](https://github.com/Valloric/YouCompleteMe). It has excellent C completion and diagnostic support. I also have to supply custom compile flags so it can provide completion for the headers. YCM provides an easy way to do this using `.ycm_extra_conf.py` inside the project root directory.

```python
import os

def Settings( **kwargs ):
    return {'flags': [ '-x', 'c++', '-Wall', f'-I{os.environ["VITASDK"]}/arm-vita-eabi/include', '-I../common'],}
```

Similar to the ALE configuration, I added extra include path. However, YCM only supports Clang for completer. That means I can’t use the gcc executable from VitaSDK. Fortunately, I can use Clang to provide the completion as long as I include the headers properly.

# Neovim config file separation
I want to keep my Neovim configuration as portable as possible because I sync my config across multiple PCs. The way this it’s done is by making Vita related configuration complementary since the configuration is system-specific.

This is how I have done it:

```
set runtimepath+=~/.vim

" Main vim configuration file
source ~/.vim/vimrc

" Vita related configuration file
source ~/.vim/vita-vimrc
```

This way, I can keep the main configuration clean and commit it separately. If you're interested in my main Neovim configuration, you can check it [here](https://github.com/adinb/dotfiles/blob/master/vim/vimrc).

# Project specific config loader
Because I also use Neovim to work on other projects, I want my Vita toolchain custom config to be applied just for Vita projects. To achieve that, I use the autocmd feature in Neovim. Simply put, the command will be executed every time I open a file inside my Vita workspace directory.

```
" Set Ale to use Vita SDK toolchain for linter
autocmd BufRead */projects/vita-playground/*/*.c call SetALEVitaSDKToolchain()

function SetALEVitaSDKToolchain()
    let b:ale_c_gcc_executable = 'arm-vita-eabi-gcc'
    let g:ale_linters = {'c': ['gcc']}
    let g:ale_c_gcc_options = '-std=c11 -Wall -I/home/adinb/projects/vita-playground/common'
endfunction
```

Again, I have a hardcoded path in the config for the same reason as the ALE parameters one.

# Emulator
As previously explained [here]({{site.baseurl}}{% link _posts/2018-12-16-interlude-emulator.md %}), I use Vita3K PSVita emulator. There's no specific setup for the emulator. I just put the emulator somewhere easy to reach.
