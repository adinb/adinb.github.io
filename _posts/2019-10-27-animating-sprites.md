---
layout: post
title:  "Tinkering With Vita: Animating Sprites"
date:    2019-10-27 05:00:00
excerpt: It moves!
categories: log
---
{% include vita-series-list.html %}


## Design
Since I was doing this bit by bit, I didn't think far ahead in terms of application design. I wanted to have working examples for each module and stitch them together later with some modifications if needed. That doesn't mean I will ignore the interface of the modules, but I won't try to get it right the first time.

The sprite animation system should be able to:
1. Load both image texture and sprite properties from files.
2. Keep track of its frame state.
3. Play the animation at a different frame rate than the game's frame rate.
4. Be rendered correctly anytime.

I also have some limitation to make it simpler:
1. Each frame have same dimensions.
2. The sprite's frame rate must be lower than the game's frame rate. In this case, I hardcoded the game's frame rate to 60 frames per second.

Below is the final result:
{% include video.html url="/images/knight.mp4" description="Look, it moves!"%}

## Implementation
### Loading files
Initially, I didn't know where all the files are stored. But it turned out that PS Vita will mount the game data to `app0` during runtime. I then prepended `app0://` in front of the file path, and then everything is working fine. [Vita dev wiki](https://playstationdev.wiki/psvitadevwiki/index.php?title=Main_Page) is a good source for these kinds of details. It's a bit messy, but with correct keywords, you should be able to find what you want.

### Rendering
I'm using SDL2 to handle the rendering and stuff. Fortunately, there is a port of it available in the vitasdk Github so you can use vdpm to install the library. By using sdl2 and sdl2_image, you will also need to install vita2d, libpng, libjpeg, and zlib. You can also get those using vdpm.

To render graphics using SDL2, we first need to create an `SDL_Window` and `SDL_Renderer`. Then, we load the image, convert it to `SDL_Texture`, and call the render function inside a loop. In my case, I wrapped the render function in the sprite module so I can render the sprite more easily.

```c
 SDL_Surface *image = IMG_Load(image_path);
    assert(image != NULL);
    sprite.texture = SDL_CreateTextureFromSurface(renderer, image);
```

### Sprite Animation
To have the sprite animation system up and running, I needed to create the main game loop. For simplicity, the main loop is a simple `while(1)` infinite loop. To close the application, you have to go to the LiveArea menu.

Inside the loop, I did three things.
1. Get elapsed time since the last render
2. Update the sprite
3. Render everything to the screen

The application keeps track of the last render with a counter. SDL2 has a high-resolution counter out of the box. To get the value from the counter, they provide a function called `SDL_GetPerformanceCounter()`. On every iteration, the application will calculate elapsed time since the last render by subtracting the current counter value with the stored last render counter value. The result is then compared to a predetermined value to make sure we wait for a certain time before each render. I also made a helper function called `get_delta_time` to convert the counter ticks into seconds.

```c
while (1) {
        if (get_delta_time(last_frame_counter, SDL_GetPerformanceCounter()) >= TARGET_FRAME_TIME) {
            SDL_RenderClear(renderer);
            next_frame(&knight_idle, TARGET_FRAME_TIME);
            render_sprite(renderer,&knight_idle,960/2-32,544/2-56); // I hardcoded the values for simplicity
            SDL_RenderPresent(renderer);
            last_frame_counter = SDL_GetPerformanceCounter();
        }
    }
```

I stored the sprite frames in a PNG sprite sheet file. The application loads the PNG image using `IMG_Load()` from `sdl_image` as an `SDL_Surface`. Since the renderer only accepts `SDL_Texture`, the loader will convert it to an `SDL_Texture` first before storing it. I'm using assets from [https://0x72.itch.io/dungeontileset-ii](https://0x72.itch.io/dungeontileset-ii) in this project.

{% include image.html url="/images/spritesheet.png" description="The sprite sheet"%}

Besides the sprite sheet file, I also made a plain text file containing metadata of the sprite. The file stores:
1. The width and height of the sprite
2. Total frame count of the animation
3. The frame rate of the animation

Those values are stored in one line, separated by spaces.

```
// The format is:
// Width Height FrameCount FrameRate
64 28 4 8
```

The sprite animation system plays the animation using an independent frame rate. The sprite will only get updated after the specified wait time has passed. To achieve this, we need to somehow sync the global frame rate with the sprite's frame rate. 

To sync the frame, I stored the available time before the next frame occurrence. Then, I subtracted the stored time with the global frame time. For example, if we set the global frame rate to 60 fps and the sprite frame rate to 12 fps, it will first substracts 1/12 with 1/60. Then 1/15 with 1/60, and so on. After the stored interval reaches 0, we update the sprite to use the next frame. 

```c
void next_frame(Sprite *sprite, double delta_t) {
    sprite->waitTime -= delta_t;
    if (sprite->waitTime <= 0) {
        sprite->currentFrame = (sprite->currentFrame + 1) % sprite->frameCount;
        sprite->waitTime = 1.0f/sprite->frameRate;
    }
}
```

Currently, my system only supports playing infinite linear animation, meaning the frame counter will only move forward and will move back to the beginning after it reaches the end.

You can get the full source code [here](https://github.com/adinb/vita-playground/tree/master/sprite_sheet). 

I hope you enjoyed this post. Next, I'm going to talk about controls, stay tuned!



