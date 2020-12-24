![Shampoo logo](https://raw.githubusercontent.com/zbanack/shampoo/main/logo.png?token=AHDW5FK6VH24H7VJ6WR7CXS7WQZHA)

### This documentation is still a WIP, so please excuse incomplete information and typos!
#### The highly in-depth demo included with the framework should be more than enough to show you a majority of features.

#### Included Demo showcase video:

[![Shampoo Demo Showcase](http://img.youtube.com/vi/vLIDNX8VmfY/0.jpg)](http://www.youtube.com/watch?v=vLIDNX8VmfY "Shampoo Demo Showcase")

📚 **Documentation** for the GameMaker GUI framework, Shampoo

✏ **Framework v0.9.3**; last modified 12/24/20 by [Zack Banack](https://zackbanack.com)

🔵 [**itch storefront**](https://zackbanack.itch.io/shampoo)

🟢 [**YoYo Games Marketplace**](https://marketplace.yoyogames.com/assets/9536/shampoo-markup-gui-framework)
___
Shampoo is a [GameMaker Studio 2.3](https://www.yoyogames.com/) (GMS 2.3) framework. It lets you build graphical user interfaces (GUIs) using a [markup-like language](https://en.wikipedia.org/wiki/markup). Shampoo supports live-coding, so you can create and debug interfaces in real-time. Shampoo was created for rapid, lightweight interface creation and user input handling. Its development began in early 2019 and went through several major pivots and rewrites since.

⚠ Shampoo has only been tested and verified to work on GMS 2.3 Windows export (including YoYo Compiler) and MacOSX export. HTML5 is also working, but performance is poor at the moment.
## Table of Contents
- [Introduction](#introduction)
- [Changelog](#changelog)
- [Features](#features)
- [Roadmap](#roadmap)
- [Installation and Setup](#installation-and-setup)
- [Types](#types)
- [Tags](#tags)
- [Attributes](#attributes)
- [Meta Attributes](#meta-attributes)
- [User Input and Capturing](#user-input-and-capturing)
- [Variables and Dynamic Content](#variables-and-dynamic-content)
- [Symbols](#symbols)
- [Classes](#classes)
- [Screen Sizes](#screen-sizes)
- [Character Escaping](#character-escaping)
- [Spaces and Linebreaks](#spaces-and-linebreaks)
- [SFX and Sound Toggling](#sfx-and-sound-toggling)
- [Contact](#contact)
- [FAQ/Reminders](#faq-and-reminders)
## Introduction

This section highlights import vocabulary, logic, and functions for the framework.

### Vocabulary
In Shampoo, any menu or interface is referred to as a **canvas**.

Canvases are composed of **elements**. There are many different types of elements, like buttons, images, text, checkboxes, and more.

Elements are constructed using **[tags](#tags)**.

Tags are customized with **[attributes](#attributes)**. Different tags accept different attributes, and these docs will tell you which are tags and attributes work together.

A canvas itself is customized using [**meta attributes**](#meta). Meta attributes are similar to tag attributes. The main different is meta attributes style the canvas rather than the individual elements *within* the canvas.

### Canvas files
You can create canvases by writing their tags and meta attributes in `.txt` files. Each canvas gets its own file. (You can change the `.txt` extension to whatever you prefer in macro `SH_FILE_EXT`).

The unique identifier (UID) of a given canvas is derived from its file name. For example, a canvas file named `hello_world.txt` would have a UID of `hello_world`.

Canvas files exist in `Included Files -> datafiles -> shampoo`.

Certain canvas names are **restricted**, like `CLASSES`, `TESTLOAD`, `CHECKSUM`, `self`, and `all` as they assist the framework.

You can comment out code in files using double-slash comments at the *start* of lines only. Multi-line `/* ... */` comments are not supported at the moment.

### Canvas drawing and refreshing
Canvases are drawn to surfaces. If content on a canvas is updated, the canvas needs to be **refreshed** for it to be visible. You can use function `shampoo_canvas_refresh(string:uid)` to refresh a canvas. Shampoo handles most user-input related refreshes automatically.

A canvas must be **regenerated** if you wish for any [Meta Attributes](#meta-attributes) to change, or any elements to be added or removed. You can use function `shampoo_canvas_regenerate(string:uid)` to regenerate a canvas.

The framework also understands that [surfaces in GameMaker are volatile](https://docs2.yoyogames.com/source/_build/3_scripting/4_gml_reference/drawing/surfaces/index.html). Shampoo does all the behind-the-scenes work to ensure surfaces exist and are re-created when necessary.

### Security
As of version 0.8.2, Shampoo supports file checksums to deter tampering. When a live update file change is registered (or `control + G` is pressed in non-release mode), a checksum is generated of all files in the `/shampoo/` directory. When in release mode, if the checksums do not match, stub script `shampoo_checksum_failed` is called which you can fill with your own code. You can enable checksum hashing by setting the fifth parameter (bool `compare_checksums`) in initialization function, `shampoo_init`.

### Useful functions
The following functions are developer facing. Most of these functions are located within the framework's `__ShampooUserFacing` script.
- `shampoo_enabled()` returns a bool: whether or not the framework is enabled
- `shampoo_init(bool:release_mode, bool:handle_window_resizing, string:project_directory, bool:live_updating, bool:compare_checksums)` initializes the framework. See the section [Installation and Setup](#installation-and-setup) to learn more about how to setup Shampoo.
- `shampoo_free()` attempts to free the framework from memory. You *probably* won't need to call this if you plan on using Shampoo throughout your entire project. Do **not** call this function inside of a canvas. Try to call it in the Begin Step or Step Event to avoid callstack errors.
- `shampoo_update()` called each gamestep; main loop for the framework
- `shampoo_render()` renders all canvases to the screen
- `shampoo_canvas_exists(string:uid)` returns truthy of a given canvas existing
- `shampoo_canvas_show(string:uid)` compatible with shampoo_generate_canvas; creates a canvas from a file
- `shampoo_canvas_regenerate(string:uid)` regerenates a canvas of a given UID
- `shampoo_canvas_refresh(string:uid)` refreshes a canvas of a given UID
- `shampoo_canvas_close(string:uid, ?bool:instant)` closes a canvas, if it exists
- `shampoo_canvas_close_self(?bool:instant)` closes *this* canvas
- `shampoo_canvas_get(string:uid)` returns undefined, or id, of canvas
- `shampoo_canvas_set_x(string:uid, real:xpos)` sets x-position of canvas
- `shampoo_canvas_set_y(string:uid, real:ypos)` sets y-position of canvas
- `shampoo_canvas_set_pos(string:uid, real:xpos, real:ypos)` sets x-position and y-position of canvas
- `shampoo_canvases_close_all(?bool:instant)` closes all canvasesshampoo_sanitize(string:input)
- `shampoo_checksum_mismatch()` this function is called if in release + checksum modes and Shampoo file checksums don't match (i.e. they have been tampered with)

#### Dynamic Content
See the [Variables and Dynamic Content](#variables-and-dynamic-content) section for more information.
- `shampoo_var_replacement(string:canvas_name)` return an array based on the canvas name (arg0), where every argument will replace the corresponding nth `{$var}`
- `shampoo_var_catch(struct:canvas)` script is called each time a canvas receives changed user input

#### Sounds
See the [sound](#sound) section for more information.
- `shampoo_play_sound(sound:snd)` plays a given sound
- `shampoo_sound_enable(bool:enabled)` enabled or disabled sounds
- `shampoo_sound_enabled()` returns truthy; whether sound is enabled
- `shampoo_sound_toggle()` toggles framework sounds between on and off
## Changelog
You can view the [Shampoo changelogs on the itch devlog](https://zackbanack.itch.io/shampoo/devlog).
## Features
- Live-coding development environment so you can create and debug interfaces in real-time
- Lightweight setup: it only takes a single function call to get the framework initialized
- File checksum verification to hinder tampering
- A unique markup-like language to build interfaces quickly 
- Customization of colors, fonts, sprites, sizes, and more
- Built-in easing/tween engine to give your menus juice
- Buttons with ability to enable/disable them
- Draggable interfaces
- Checkboxes and toggles
- Radio button groups
- Text input with maxlength and numbersOnly attributes
- Sliders with customizable min/max ranges and intervals
- Automatic content overflow and linebreaks
- Custom callback functions and in-line hyperlinks
- Sound effect (and toggling) support
- Tooltips and hover effects
- Hex, blue-red-green, and string color support, with a built-in palette
- onLoad, onStep, onClose, onDraw, onDrag, onResize event listeners
- Out-of-the-box window resizing support
- Arrow key/tab navigation
## Roadmap
- Framework isolated from the included demo
- Improved HTML5 support​
- iOS, Android, and console support
- Dropdown menus
- More advanced textareas and input interception
- Paginations
- More efficient text parsing and character escaping
- Element padding, margins
- Scrollviews
- Lists, expansion of indentation logic
## Installation and Setup
Shampoo is compatible only with the GMS 2.3 IDE.

The framework can be purchased on [**itch.io**](https://zackbanack.itch.io/shampoo) and the [**YoYo Games Marketplace**](https://marketplace.yoyogames.com/assets/9536/shampoo-markup-gui-framework). Please see the marketplace listing for more information on purchasing, distribution, and warranty.

1. Install GMS2.3
2. Purchase Shampoo using the link above
3. Extract the contents of the Shampoo framework .zip file.
4. In GMS 2.3, open the framework project file `shampoo*.yyp`
5. Press F5 to run the project. You will see the 'Hello, World!' interactive demo. 
5. Any assets in the `demo` folder are only necessary for the demo to run, not the framework. In a later release, the demo will be isolated from the framework entirely.
	- If you wish to import the framework into an existing project, use the option 'Create Local Package' in the IDE. Then, go to `Tools > Create Local Package`. Fill out the text fields and click the 'Add All' button to add all resources from Shampoo to the package. Press OK and the local package will be created. To import the local package into your existing project, open the project in the IDE and navigate to `Tools > Import Local Package`. You can cherry-pick which bundled assets (sprites, fonts, sounds, etc.) should be imported. However, note that if Shampoo tries to reference an asset you've opted out of including, you may get an error (Shampoo will try its best to use fallback assets).
7. After import resources, make sure object `obj_shampoo` is placed in the first room of the game.

### Live-Coding
If you do not wish to enable live-coding in your project, you can skip the rest of this section. Otherwise, continue following the steps.
First, ensure that the file system sandbox is disabled. This allows the game to read and write to files outside of the default `working_directory`.
```
Resource Tree hamburger button -> Game Options -> Platform Settings -> [your platform] -> General -> check 'Disable file system sandbox'
```

Next, in the Create Event of object `obj_shampoo`, find the function `shampoo_init` and modify it:
- Set the first parameter, release mode, to `false`. Live-editing will only work in non-release mode.
- Change the third parameter string to point to the location of the project .yyp file. In other words, where exactly on your computer is your source code?

```
// initialize the framework
shampoo_init(

	// release mode (if true, live-editing & debug controls are disabled)
	false,
	
	// out-of-the-box window resize handling
	true, 
	
	// location of *this* project (.yyp)
	"C:\Users\Zack\Desktop\Coding\shampoo_v0_9_2", // <-- your directory here
	
	// enable live-editing?
	true,
	
	// in release mode, should canvas files be checksum-verified?
	// if CHECKSUM.txt does not match the checksum generated via this function,
	// stub function `shampoo_checksum_mismatch` will be called
	true
);
```

The first parameter of `shampoo_init` is whether the game is in 'release mode' or not.

**When it comes time for you to release your game to world, make sure you set this flag to `true`!**

When release mode is `true`, Shampoo will read files in Included Files. If you wish to temporary disable live-coding, but stay in non-release mode, set the last parameter of the initialization function to `false`.

While live-coding, the game will check the Shampoo directory every `SH_LIVE_REFRESH_RATE` game steps for file changes. If a file change happens, the game will read `TESTLOAD.txt` and load the canvas(es) described in it.

While live-coding, you can press `R` to force-reload `TESTLOAD.txt`.

When a reload occurs, all classes are cleared.

For example, suppose the following is my live-code folder:
- shampoo
	- CLASSES.txt
	- TESTLOAD.txt
	- hello_world.txt
	- second.txt
	- third.txt

The contents of TESTLOAD.txt are:
```
open:hello_world&third
```

If I make changes in any live-code folder, all canvases will be removed and `hello_world.txt` and `third.txt` will be opened per my `TESTLOAD.txt` instructions.

Note that you can open multiple canvases by separating their file names with the ampersand (`&`).

### Legacy Live-Coding Option
If you still cannot get live-coding to work, and are sure that you are pointing the init function at your project file, you may use the legacy live-coding approach.

1. Navigate to the project's Included Files

```
Resource Tree hamburger button -> Included Files -> Open in Explorer
```

2. You'll see a folder called `shampoo`. **Copy** the folder.
3. On your desktop, or in a *non-GMS project directory* of your choice, **paste** the folder. This folder is what you'll work out of when you want to live-code.
4. Change the third parameter string of the init function to point to this new folder location

Please note the following if you use this live-code approach: when it comes time to release the game, the contents of this new folder need to be brought back in to the Included Files (by copy-pasting).
## Types

Because Shampoo canvases are created from giant chunks of plaintext, the framework needs to define certain 'types' so it knows what category a piece of text falls into. For example, `123` as a string is very different from 123 as a number. The following contains a list of types that Shampoo uses and can sanitize. During the string sanitization process, pieces of text are tried against the following.

Note that tag attributes will be split at colons (`:`), so you can pass in multiple values for certain attributes.

e.g. `color=red:green` in a link tag will set the contents to red by default and green when hovering. Certain tags handle multiple colon-split attributes differently, so read up on specific tags.

### Number
A number will attempt to be parsed from a string. If no valid, non-exponential number can be resolved, `undefined` is returned.

Examples of valid numbers: 123, -123, 1, -1., 100.1, -100.0,
Examples of invalid numbers: 100e, 100^3, 10*10, 100+0

Further, there are hard-coded special strings that resolve to values:
- `1/16w`, `1/12w`, `1/10w`, `1/8w`, `1/4w`, `1/3w`, `1/2w`, `1w`, `w` returns 1/nth the width of the game's GUI width
- `1/16h`, `1/12h`, `1/10h`, `1/8h`, `1/4h`, `1/3h`, `1/2h`, `1h`, `h` returns 1/nth the height of the game's GUI height
- `room_speed`, `fps`, `fps_real`, `current_time`, and `get_timer` return their respective GM value

### Function
A string that must resolve to a valid function name. If no such function can be resolved, `undefined` is returned.

You may pass arguments into this function using the following syntax: `my_function?args=first_arg&second_arg&third_arg`. Please note that arguments are passed as strings, so if you are expecting a number, you must cast using `real(argument)`.

### Sprite
A string that must resolve to a valid sprite name. If no such sprite can be resolved, `undefined` is returned.

### String
An value that is wrapped with matching single or double quotes. **Note** if you wish to insert a quote or comma character into a string, be sure to escape it! See the section on [Character Escaping](#character-escaping) for more information.

### Color
Tries to parse a string to return a color. If no valid color can be parsed, `undefined` is returned. Strings can be hexidecimal format (with a leading hash `#`), GameMaker blue-red-green (with leading `$`), a built-in GameMaker `c_*` color constant, or a pre-defined color in the `SH_COLORS` color lookup table.

[Tailwind.css](#https://tailwindcss.com/docs/customizing-colors) colors are included as well and can be constructed using `colorName` + `number 0-9`, e.g. `red-4`, `green-7`, `grey-5`, `gray-5`, `purple-0`.

Hex colors can be defined in tuples which are repeated as many times as necessary. For example: `#f0` will resolve to `#f0f0f0` and `#f00` will resolve to `#f00f00`.

### Align
`fa_left`, `fa_right`, `fa_center`, `fa_top`, `fa_middle`, `fa_bottom` return GameMaker-equivalent values. Used for aligning the canvas respectively.

### Bool
If a string is '0' or begins with case-insensitive 'F', `false` is returned. If a string is '1' or begins with case-insensitive 'T', `true` is returned. The fallback value is `undefined`.

### Ease
A string that must resolve to be a valid ShEase enum value. If no such ease can be resolved, `undefined` is returned.

The following eases are valid:

`Linear`, `InOutSine`, `Instant`, `InExpo`,`InQuad`, `OutExpo`,	`OutQuad`, `InOutExpo`, `InOutQuad`, `InCirc`, `InCubic`, `OutCirc`, `OutCubic`, `InOutCirc`, `InOutCubic`, `InElastic`, `InQuart`, `OutElastic`, `OutQuart`, `InOutElastic`, `InOutQuart`, `InBack`, `InQuint`, `OutBack`, `OutQuint`, `InOutBack`, `InOutQuint`, `InBounce`, `InSine`, `OutBounce`, `OutSine`,`InOutBounce`,	

### ID
An alphanumeric value that is wrapped with matching single or double quotes. For use with inputs.

### Sound
A string that must resolve to a valid sound name. If no such sound can be resolved, `undefined` is returned.

### Canvas
A string that must resolve to a valid canvas file name. If no such sound can be resolved, `undefined` is returned.

## Tags

Tags are the building blocks of canvases. Tags form elements, like text, buttons, images, checkboxes, radio buttons, textfields, and more. This section outlines all tags currently available in the Shampoo framework. Tags can be nested and Shampoo will try its best to resolve formatting. If no tags are provided, the parser defaults to attribute values as defined in the framework configuration files.
___
### break (tag)
Inserts a linebreak, forcing further content to start beneath this break. Note that you must provide a closing slash if you manually insert a break as this tag is self-closing (`[br /]`).


**Associated Tags**: [hrule](#hrule-tag), [spacer](#spacer-tag)


| property | value
--- | ---
self-closing | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`br`

#### Compatible Attributes

[height](#height-attribute)

#### Examples

```
[span]This text is on line 1[br/]and this text is on line 2[/span].
```

```
[span]This text is on line 1[br, h=100/]and this text is 100 pixels beneath[/span]
```


___
### hrule (tag)
Similar to a linebreak, a horizontal rule will force new content to start beneath. Horizontal rules have visible lines of varying thickness, however. Note that you must provide a closing slash if you manually insert a horizontal rule as this tag is self-closing (`[hr /]`).

A shorthand way to insert a horizontal rule into a canvas is three or more consecutive equal signs (`=`) or dashes (`-`) at the very start of a line.


**Associated Tags**: [break](#break-tag), [spacer](#spacer-tag)


| property | value
--- | ---
self-closing | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`hr`

#### Compatible Attributes

[color](#color-attribute), [height](#height-attribute), [thickness](#thickness-attribute)

#### Examples

```
[span]This text is on line 1[hr/]and this text is on line 2[/span]
```

```
[span]This text is on line 1[hr, h=20, thickness=8, color=red/]and this text is on line 2 separated by 20 pixels with a red line 8 pixels thick inside[/span]
```

```
This text is on line 1

===

This text is on line 2

-----

This text is on line 3
```


___
### span (tag)
Define a block of body text with various formatting attributes acceptable. You can use this tag to customize text within a paragraph, e.g. highlight certain words or change text colors. These tags, if declared as `p` will terminate lines, acting as linebreaks.

| property | value
--- | ---
self-closing | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`p`, `span`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [border](#border-attribute), [border_bottom](#border_bottom-attribute), [border_left](#border_left-attribute), [border_right](#border_right-attribute), [border_top](#border_top-attribute), [color](#color-attribute), [radius](#radius-attribute)

#### Examples

```
[p]this is a paragraph[/p]

[p, c=blue]this is a second paragraph with blue text[/p]
```

```
[p]this is a paragraph [span, c=blue]and this text is blue[/span][/p]
```

```
[span, bg=white]this text has [span, bg=yellow]highlighted[/span] words in it![/span]
```


___
### image (tag)
Inserts an image into a canvas, in-line with content. If an image is inserted beside text or other images, then all content in that row will be centered vertically. You can specify which sprite and sprite frame to draw, however note that animations are currently not supported. If you wish to draw an animated sprite, then consider using an [**ondraw**](#ondraw-meta-attribute) attribute.

An image can "float" if either [**float_x**](#float_x-meta-attribute) or [**float_y**](#float_y-meta-attribute) are set. While floating, an image will be positioned at its float coordinates relative to the canvas top-left (x, y) coordinate. Floating images will not affect the positioning of other contents whatsoever and may overlap and overflow. Note that floating images are rendered in the order they're declared, so be cautious of where you define floating images in your canvas file.

The size of the image in the canvas will default to the sprite's width and height. However, you can optionally provide [**width**](#width-meta-attribute) and [**height**](#height-meta-attribute) attributes to override these dimensions. If two inline image heights differ, a linebreak may be inserted automatically if deemed necessary to preserve canvas row overflow integrity.

An image can be blended with a color using the [**bkg_color**](#bkg_color-meta-attribute) attribute.

| property | value
--- | ---
self-closing | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`img`

#### Compatible Attributes

[alpha](#alpha-attribute), [bkg_color](#bkg_color-attribute), [float_x](#float_x-attribute), [float_y](#float_y-attribute), [frame](#frame-attribute), [height](#height-attribute), [image](#image-attribute), [width](#width-attribute)

#### Examples

```
[img src=my_image, blend=red, frame=1, width=200, height=200, float-x=50, float-y=50, alpha=50 /]
```


___
### function (tag)
All content wrapped inside function tags will, when clicked, execute the callback function provided (so long as the element is not [**disabled**](#disabled-attribute). These are essentially HTML hyperlinks. You can mix and match body font types, colors, and more non-line terminating attributes inside a singular link.

The [**open**](#open-attribute) attribute will open a canvas of a given name, passed as string. Note that the canvas name is derived from the canvas filename (the file name minus paths and file extension). You may open multiple canvases by separating canvas names with the ampersand (`&`).

Similarly, the [**close**](#close-attribute) attribute will close a canvas of a given name. If you'd like to close *this* canvas, pass `self`; to close *all* canvases, pass `all`.

You can specify a [**tooltip**](#tooltip-attribute) string to appear upon link hover.


**Associated Tags**: [button](#button-tag)


| property | value
--- | ---
self-closing | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`f`, `func`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [border_bottom](#border_bottom-attribute), [close](#close-attribute), [color](#color-attribute), [function](#function-attribute), [open](#open-attribute), [sound](#sound-attribute), [thickness](#thickness-attribute), [tooltip](#tooltip-attribute), [underline](#underline-attribute), 

#### Examples

```
[f, scr=test_callback_function]Click me![/f]
```

```
[f, open=popup_menu", tooltip="This is hovertext"]Create a popup menu![/f]
```

```
[f, close=self]Close this canvas[/f]
```

```
[f, close=self&banana]Close this canvas and a canvas named "banana"[/f]
```

```
[f, close=all]Close ALL canvases[/f]
```


___
### small (tag)
Text wrapped in these header tags will be the smallest in size, smaller than the body fonts.


**Associated Tags**: [h1](#h1-tag), [h2](#h2-tag), [h3](#h3-tag), [h4](#h4-tag), [h5](#h5-tag), [h6](#h6-tag)


| property | value
--- | ---
self-closing | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`sm`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [border](#border-attribute), [border_bottom](#border_bottom-attribute), [border_left](#border_left-attribute), [border_right](#border_right-attribute), [border_top](#border_top-attribute), [color](#color-attribute), [radius](#radius-attribute)

#### Examples

```
[sm]This text is tiny![/sm] This text is standard size!
```


___
### h1 (tag)
Text wrapped in these `h1` header tags will be the largest in size, as supported by the framework. You can shorthand-declare these tags by starting a new line followed by a octothorpe (`#`) symbol followed by a space followed by text.


**Associated Tags**: [h2](#h2-tag), [h3](#h3-tag), [h4](#h4-tag), [h5](#h5-tag), [h6](#h6-tag), [small](#small-tag)


| property | value
--- | ---
self-closing | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`h1`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [border](#border-attribute), [border_bottom](#border_bottom-attribute), [border_left](#border_left-attribute), [border_right](#border_right-attribute), [border_top](#border_top-attribute), [color](#color-attribute), [radius](#radius-attribute)

#### Examples

```
[h1]Hello, World[/h1]
```

```
# Hello, World!
```

```
[h1, c=red]This text is red[/h1]
```


___
### h2 (tag)
Text wrapped in these `h2` header tags will be the second-largest in size, as supported by the framework. You can shorthand-declare these tags by starting a new line followed by two consecutive octothorpe (`#`) symbols followed by a space followed by text.


**Associated Tags**: [h1](#h1-tag), [h3](#h3-tag), [h4](#h4-tag), [h5](#h5-tag), [h6](#h6-tag), [small](#small-tag)


| property | value
--- | ---
self-closing | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`h2`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [border](#border-attribute), [border_bottom](#border_bottom-attribute), [border_left](#border_left-attribute), [border_right](#border_right-attribute), [border_top](#border_top-attribute), [color](#color-attribute), [radius](#radius-attribute)

#### Examples

```
[h2]Hello, World[/h2]
```

```
## Hello, World!
```

```
[center][h2]This text is centered![/h2][/center]
```


___
### h3 (tag)
Text wrapped in these `h3` header tags will be the third-largest in size, as supported by the framework. You can shorthand-declare these tags by starting a new line followed by three consecutive octothorpe (`#`) symbols followed by a space followed by text.


**Associated Tags**: [h1](#h1-tag), [h2](#h2-tag), [h4](#h4-tag), [h5](#h5-tag), [h6](#h6-tag), [small](#small-tag)


| property | value
--- | ---
self-closing | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`h3`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [border](#border-attribute), [border_bottom](#border_bottom-attribute), [border_left](#border_left-attribute), [border_right](#border_right-attribute), [border_top](#border_top-attribute), [color](#color-attribute), [radius](#radius-attribute)

#### Examples

```
[h3]Hello, World[/h3]
```

```
### Hello, World!
```


___
### h4 (tag)
Text wrapped in these `h4` header tags will be the fourth-largest in size, as supported by the framework. You can shorthand-declare these tags by starting a new line followed by four consecutive octothorpe (`#`) symbols followed by a space followed by text.


**Associated Tags**: [h1](#h1-tag), [h2](#h2-tag), [h3](#h3-tag), [h5](#h5-tag), [h6](#h6-tag), [small](#small-tag)


| property | value
--- | ---
self-closing | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`h4`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [border](#border-attribute), [border_bottom](#border_bottom-attribute), [border_left](#border_left-attribute), [border_right](#border_right-attribute), [border_top](#border_top-attribute), [color](#color-attribute), [radius](#radius-attribute)

#### Examples

```
[h4]Hello, World[/h4]
```

```
##### Hello, World!
```


___
### h5 (tag)
Text wrapped in these `h5` header tags will be the fifth-largest in size, as supported by the framework. You can shorthand-declare these tags by starting a new line followed by five consecutive octothorpe (`#`) symbols followed by a space followed by text.


**Associated Tags**: [h1](#h1-tag), [h2](#h2-tag), [h3](#h3-tag), [h4](#h4-tag), [h6](#h6-tag), [small](#small-tag)


| property | value
--- | ---
self-closing | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`h5`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [border](#border-attribute), [border_bottom](#border_bottom-attribute), [border_left](#border_left-attribute), [border_right](#border_right-attribute), [border_top](#border_top-attribute), [color](#color-attribute), [radius](#radius-attribute)

#### Examples

```
[h5]Hello, World[/h5]
```

```
##### Hello, World!
```


___
### h6 (tag)
Text wrapped in these `h6` header tags will be the sixth-largest in size, as supported by the framework. You can shorthand-declare these tags by starting a new line followed by six consecutive octothorpe (`#`) symbols followed by a space followed by text.


**Associated Tags**: [h1](#h1-tag), [h2](#h2-tag), [h3](#h3-tag), [h4](#h4-tag), [h5](#h5-tag), [small](#small-tag)


| property | value
--- | ---
self-closing | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`h6`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [border](#border-attribute), [border_bottom](#border_bottom-attribute), [border_left](#border_left-attribute), [border_right](#border_right-attribute), [border_top](#border_top-attribute), [color](#color-attribute), [radius](#radius-attribute)

#### Examples

```
[h6]Hello, World[/h6]
```

```
###### Hello, World!
```


___
### italic (tag)
Body text wrapped in this tag will be italic. These tags will not terminate lines by themselves. Italic and bold tags can be nested. Wrap text around single asterices (`*`) to emulate this tag.


**Associated Tags**: [bold](#bold-tag)


| property | value
--- | ---
self-closing | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`i`, `italic`

#### Compatible Attributes



#### Examples

```
Hello! [i]this text is italic[/i] and this is not
```

```
Hello! *this text is italic* and this is not
```


___
### bold (tag)
Body text wrapped in this tag will be bold. These tags will not terminate lines by themselves. Italic and bold tags can be nested. Wrap text around double asterices (`*`) to emulate this tag. Wrap text around triple asterices to emulate bold and italic tags.


**Associated Tags**: [italic](#italic-tag)


| property | value
--- | ---
self-closing | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`b`, `bold`

#### Compatible Attributes



#### Examples

```
Hello! [b]this text is bold[/b] and this is not
```

```
Hello! *this text is bold* and this is not
```

```
[b][i]This text is bold and italic![/i][/b]
```

```
***This text is bold and italic!***
```


___
### button (tag)
Buttons are similar to function links, except they allow for more customization. In particular, you can set images, text offsets on hover, and [**disable**](#disable-attribute) status If an [**id**](#id-attribute) attribute is provided.


**Associated Tags**: [function](#function-tag)


| property | value
--- | ---
self-closing | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`button`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [close](#close-attribute), [color](#color-attribute), [disabled](#disabled-attribute), [function](#function-attribute), [height](#height-attribute), [hover_x_offset](#hover_x_offset-attribute), [hover_y_offset](#hover_y_offset-attribute), [image](#image-attribute), [input_id](#input_id-attribute), [label](#label-attribute), [open](#open-attribute), [order](#order-attribute), [radius](#radius-attribute), [shake_not_enabled](#shake_not_enabled-attribute), [sound](#sound-attribute), [tooltip](#tooltip-attribute), [width](#width-attribute), 

#### Examples

```
[button, bg=c_ltgray:c_gray, content="Click me!", width=320, height=128, scr=my_function /]
```

```
[button, bg=blue:green, scr=my_function, src=my_sprite /]
```

```
[button, w=320, h=32, label="I'm a button!" bg=blue, c=white /]
```

```
[button, label="I'm a button", img=button_img /]
```

```
[button, content="Hover me!", tooltip="You hovered me!", scr=my_function /]
```

```
[button, content="Click me!", w=200, h=50, open=second_canvas&third_canvas /]
```

```
[button, content="Click me!", w=200, h=50, close=second_canvas&third_canvas /]
```

```
[button color=white, bg=blue, width=400, height=64, value="I move right 32px when you hover me!", hover-x=32 /]
```

```
[button color=black, img=button_sprite, value="Hover me", hover-y=8 /]
```

```
[button, content="I'm a rounded button", rad=32, bg=blue, c=white, scr=my_function /]
```

```
[button, radius=2, content="I'm ever so slightly rounded", width=300, height=64, bg=purple:c_lime, c=white:black, scr=my_function /]
```

```
[button, id="btn1", bg=blue, c=white, content="Cannot click me", src=my_function, disabled=true /]
```

```
[button, id="btn1", bg=blue, c=white, content="I'm a button", src=my_function /]
```

```
[button, scr=my_function, bg=blue, c=white, content="Hello!", snd=my_sound_enabled:my_sound_disabled, disabled=true /]
```

```
[button, scr=my_function_winner, bg=green-5, c=white, content="You Won!!", snd=fun_sound_effect, disabled=false /]
```


___
### left (tag)
Contents wrapped in this tag will be aligned to the left.


**Associated Tags**: [center](#center-tag), [right](#right-tag)


| property | value
--- | ---
self-closing | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`left`, `l`

#### Compatible Attributes



#### Examples

```
[left]left-aligned text[/left]
```


___
### right (tag)
Contents wrapped in this tag will be aligned to the right.


**Associated Tags**: [center](#center-tag), [left](#left-tag)


| property | value
--- | ---
self-closing | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`right`, `r`

#### Compatible Attributes



#### Examples

```
[right]right-aligned text[/right]
```


___
### center (tag)
Contents wrapped in this tag will be horizontally centered.


**Associated Tags**: [left](#left-tag), [right](#right-tag)


| property | value
--- | ---
self-closing | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`center`, `cen`, `mid`

#### Compatible Attributes



#### Examples

```
[center]center-aligned text[/center]
```


___
### meta (tag)
The meta tag is special. It affects the canvas itself rather than individual tags/elements. See the [**meta**](#meta) section for more a plethora of information.

| property | value
--- | ---
self-closing | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`meta`

#### Compatible Attributes

[attach](#attach-meta-attribute), [attach_halign](#attach_halign-meta-attribute), [attach_valign](#attach_valign-meta-attribute), [attach_x_offset](#attach_x_offset-meta-attribute), [attach_y_offset](#attach_y_offset-meta-attribute), [bkg_color](#bkg_color-meta-attribute), [bkg_image](#bkg_image-meta-attribute), [bkg_nineslice](#bkg_nineslice-meta-attribute), [bkg_tiled](#bkg_tiled-meta-attribute), [blocking](#blocking-meta-attribute), [blocking_alpha](#blocking_alpha-meta-attribute), [blocking_color](#blocking_color-meta-attribute), [caption](#caption-meta-attribute), [caption_bkg_color](#caption_bkg_color-meta-attribute), [caption_bkg_color_hover](#caption_bkg_color_hover-meta-attribute), [caption_color](#caption_color-meta-attribute), [caption_color_hover](#caption_color_hover-meta-attribute), [caption_halign](#caption_halign-meta-attribute), [caption_height](#caption_height-meta-attribute), [click_out_close](#click_out_close-meta-attribute), [close_button](#close_button-meta-attribute), [close_ease_x](#close_ease_x-meta-attribute), [close_ease_x_dur](#close_ease_x_dur-meta-attribute), [close_ease_x_offset](#close_ease_x_offset-meta-attribute), [close_ease_y](#close_ease_y-meta-attribute), [close_ease_y_dur](#close_ease_y_dur-meta-attribute), [close_ease_y_offset](#close_ease_y_offset-meta-attribute), [color](#color-meta-attribute), [delay](#delay-meta-attribute), [depth](#depth-meta-attribute), [draggable](#draggable-meta-attribute), [escape_to_close](#escape_to_close-meta-attribute), [fadein](#fadein-meta-attribute), [fadeout](#fadeout-meta-attribute), [fast_refresh](#fast_refresh-meta-attribute), [func_on_close](#func_on_close-meta-attribute), [func_on_drag](#func_on_drag-meta-attribute), [func_on_draw](#func_on_draw-meta-attribute), [func_on_open](#func_on_open-meta-attribute), [func_on_resize](#func_on_resize-meta-attribute), [func_on_step](#func_on_step-meta-attribute), [func_on_update](#func_on_update-meta-attribute), [halign](#halign-meta-attribute), [height](#height-meta-attribute), [icon](#icon-meta-attribute), [on_close_despawn](#on_close_despawn-meta-attribute), [on_close_spawn](#on_close_spawn-meta-attribute), [on_open_despawn](#on_open_despawn-meta-attribute), [on_open_spawn](#on_open_spawn-meta-attribute), [open_ease_x](#open_ease_x-meta-attribute), [open_ease_x_dur](#open_ease_x_dur-meta-attribute), [open_ease_x_offset](#open_ease_x_offset-meta-attribute), [open_ease_y](#open_ease_y-meta-attribute), [open_ease_y_dur](#open_ease_y_dur-meta-attribute), [open_ease_y_offset](#open_ease_y_offset-meta-attribute), [pad](#pad-meta-attribute), [placeholder_color](#placeholder_color-meta-attribute), [placeholder_color_focus](#placeholder_color_focus-meta-attribute), [radius](#radius-meta-attribute), [resize_recreate](#resize_recreate-meta-attribute), [shadow_alpha](#shadow_alpha-meta-attribute), [shadow_color](#shadow_color-meta-attribute), [shadow_radius](#shadow_radius-meta-attribute), [shadow_xoffset](#shadow_xoffset-meta-attribute), [shadow_yoffset](#shadow_yoffset-meta-attribute), [sound_close](#sound_close-meta-attribute), [sound_open](#sound_open-meta-attribute), [steal_focus](#steal_focus-meta-attribute), [ticker_color](#ticker_color-meta-attribute), [valign](#valign-meta-attribute), [version](#version-meta-attribute), [width](#width-meta-attribute), [xpos](#xpos-meta-attribute), [x_offset](#x_offset-meta-attribute), [ypos](#ypos-meta-attribute), [y_offset](#y_offset-meta-attribute)

#### Examples

```
[meta, attach=second_canvas, attach-y=-500 /]
```

```
[meta, y-offset=500 /]
```

```
[meta version="1.0" /]
```

```
[meta version="5th Anniversary Re-release" /]
```

```
[meta width=200, height=200, x=1/2w, y=1/2h, halign=fa_center, valign=fa_middle /]
```

```
[meta width=200, height=200, x=1/2w, y=1/2h, halign=fa_center, valign=fa_middle /]
```

```
[meta halign=fa_left, valign=fa_top /]
```

```
[meta halign=fa_right, valign=fa_bottom /]
```

```
[meta depth=-50 /]
```

```
[meta onUpdate=my_input_update_function /]
```

```
[meta onStep=my_gamestep_function /]
```

```
[meta shadow-color=orange /]
```

```
[meta coverup-color=black /]
```

```
[meta tiled=true, img=my_background_sprite, blend=yellow /]
```

```
[meta fadeIn=room_speed /]
```

```
[meta placeholder-color=purple-3, placeholder-color-focus=purple-5 /]
```

```
[meta, openEaseY=InCubic, openEaseYOffset=25, openEaseYDuration=room_speed /]
```

```
[meta, closeEaseY=InOutQuint, closeEaseYOffset=25, closeEaseYDuration=room_speed /]
```

```
[meta, closeSpawn=second_canvas$third_canvas /]
```

```
[meta, yOffset=333, halign=fa_left, valign=fa_top /]
```

```
[meta, attach=right_canvas, attach-halign=fa_right /]
```

```
[meta, attach-yoffset=125, attach=second_canvas /]
```

```
[meta, caption-colour=$71CC2E, caption-color-hover=$60AE27, caption="Green->Emerald Caption Text", caption-height=32 /]
```

```
[meta, caption-bg=red-5, caption-bg-hover=red-7, caption="Red->Maroon Background", caption-height=48 /]
```

```
[meta, icon=my_sprite_123, caption="I have an icon to my left!", caption-bg=blue, caption-height=32 /]
```


___
### spacer (tag)
Inserts a spacer, where following contents will be pushed a given number of pixels. Spacers of default width can also be inserted via `&nbsp;`. Note that you must provide a closing slash if you manually insert a spacer as this tag is self-closing (`[sp /]`).


**Associated Tags**: [break](#break-tag), [hrule](#hrule-tag)


| property | value
--- | ---
self-closing | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`sp`

#### Compatible Attributes

[width](#width-attribute)

#### Examples

```
[span]text on the left[sp, w=100/]text 100px to the right[/span]
```

```
[span]this$nbsp;text&nbsp;is&nbsp;spaced&nbsp;apart[/span]
```

```
[p]&nbsp;This text is indented.[/p]
```


___
### textarea (tag)
A textfield is an element that, when in focus, accepts keyboard input. A unique [**id**](#id-attribute) is required for each textfield. Note that you must provide a closing slash when working with textfields as they are self-closing (`[textfields ... /]`). These tags will terminate lines, acting as linebreaks.


**Associated Tags**: [checkbox](#checkbox-tag), [radio](#radio-tag), [slider](#slider-tag), [toggle](#toggle-tag)


| property | value
--- | ---
self-closing | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`textarea`, `textbox`, `textfield`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [color](#color-attribute), [disabled](#disabled-attribute), [height](#height-attribute), [image](#image-attribute), [indent](#indent-attribute), [input_id](#input_id-attribute), [input_maxlength](#input_maxlength-attribute), [input_numbers_only](#input_numbers_only-attribute), [input_value](#input_value-attribute), [order](#order-attribute), [placeholder](#placeholder-attribute), [radius](#radius-attribute), [shake_not_enabled](#shake_not_enabled-attribute), [sound](#sound-attribute), [value_y_offset](#value_y_offset-attribute), [width](#width-attribute), 

#### Examples

```
[textfield, id="textfield1", value="Hello, World!"/]
```

```
[textfield, id="textfield1", value="Cannot edit me", disabled=true /]
```

```
[textfield, id="textfield1", value="Hello, World!"/]
```

```
[textfield, maxlength=10, id="t1", value="This text will get cut off because it exceeds 10 characters" /]
```

```
[textfield, numbersOnly=true, id="age" /]
```

```
[textfield, numbers-only=true, id="favorite-number" /]
```

```
[textfield, placeholder="Your text here" id="textbox1" /]
```


___
### checkbox (tag)
A checkbox is an element that, when clicked or pressed, will toggle between being "on" an "off". If attribute [**input_value**](#input_value-attribute) is provided, the contents of the value will be displayed to the right of the checkbox. By default, such text stems from the [**id**](#id-attribute). A unique id is required for each checkbox. Note that you must provide a closing slash when working with textareas as they are self-closing (`[checkbox ... /]`). These tags will terminate lines, acting as linebreaks.


**Associated Tags**: [radio](#radio-tag), [slider](#slider-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)


| property | value
--- | ---
self-closing | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`ch`, `checkbox`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [color](#color-attribute), [disabled](#disabled-attribute), [function](#function-attribute), [hitbox_full](#hitbox_full-attribute), [image](#image-attribute), [indent](#indent-attribute), [input_checked](#input_checked-attribute), [input_id](#input_id-attribute), [input_value](#input_value-attribute), [order](#order-attribute), [shake_not_enabled](#shake_not_enabled-attribute), [sound](#sound-attribute), [value_x_offset](#value_x_offset-attribute), [value_y_offset](#value_y_offset-attribute), , 

#### Examples

```
[checkbox, id="checkbox1", value="Checkbox", checked=true /]
```

```
[checkbox, id="checkbox1", value="Cannot uncheck me", disabled=true, checked=true /]
```

```
[checkbox, id="checkbox1", value="Not Checked":"Checked!", checked=true /]
```


___
### radio (tag)
Radio buttons belongs to a "group". Only one radio button per group can be enabled at a given time. When a radio button is clicked or pressed, it will turn "on", and any previously-on radio button turns off the *next gamestep*. If attribute [**input_value**](#input_value-attribute) is provided, the contents of the value will be displayed to the right of the radio button. By default, such text stems from the [**id**](#id-attribute). A unique id and [**group_id**](#group_id-attribute) iare required for each radio button. Note that you must provide a closing slash when working with textareas as they are self-closing (`[radio ... /]`). These tags will terminate lines, acting as linebreaks.


**Associated Tags**: [checkbox](#checkbox-tag), [slider](#slider-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)


| property | value
--- | ---
self-closing | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`rd`, `radio`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [color](#color-attribute), [disabled](#disabled-attribute), [hitbox_full](#hitbox_full-attribute), [image](#image-attribute), [indent](#indent-attribute), [input_checked](#input_checked-attribute), [input_group](#input_group-attribute), [input_id](#input_id-attribute), [input_value](#input_value-attribute), [order](#order-attribute), [shake_not_enabled](#shake_not_enabled-attribute), [sound](#sound-attribute), [value_x_offset](#value_x_offset-attribute), [value_y_offset](#value_y_offset-attribute), 

#### Examples

```
[radio, group="group1", id="radio1", value="Cannot select me", disabled=true /]
```

```
[radio, group="group1", id="radio1", value="Apples" /]
[radio, group="group1", id="radio2", value="Bananas" /]
```

```
[radio, group="group1", id="radio1", value="Apples" /]
[radio, group="group1", id="radio2", value="Bananas" /]
[radio, group="group1", id="radio3", value="Cherries" /]
```

```
[radio, group="group2", id="r1", value="Separate Apples" /]
[radio, group="group2", id="r2", value="Separate Bananas" /]
[radio, group="group2", id="r3", value="Separate Cherries" /]
```


___
### slider (tag)
A slider allows for dragging a value in the range of [[**input_min_value**](#input_min_value-attribute), [**input_max_value]**](#input_max_value]-attribute), with interval rounding via [**input_interval**](#input_interval-attribute). A unique [**id**](#id-attribute) is required for each slider. Note that you must provide a closing slash when working with textareas as they are self-closing (`[slider ... /]`). These tags will terminate lines, acting as linebreaks.


**Associated Tags**: [checkbox](#checkbox-tag), [radio](#radio-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)


| property | value
--- | ---
self-closing | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`slider`, `range`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [bkg_color_alt](#bkg_color_alt-attribute), [color](#color-attribute), [disabled](#disabled-attribute), [height](#height-attribute), [image](#image-attribute), [image2](#image2-attribute), [indent](#indent-attribute), [input_id](#input_id-attribute), [input_interval](#input_interval-attribute), [input_max_value](#input_max_value-attribute), [input_min_value](#input_min_value-attribute), [order](#order-attribute), [shake_not_enabled](#shake_not_enabled-attribute), [sound](#sound-attribute), [value_prefix](#value_prefix-attribute), [value_suffix](#value_suffix-attribute), [value_x_offset](#value_x_offset-attribute), [value_y_offset](#value_y_offset-attribute), , 

#### Examples

```
[slider, bkg=green, bkg2=blue, id="slider0", min=0, max=100, interval=5, value=50 /]
```

```
[slider, id="slider0", min=0, max=100, interval=5, value=50, bkg=green, bg-col2=#8E44AD /]
```

```
[slider, img=my_slider_image, img-alt=my_slider_knob_img, id="slider0", min=0, max=100, interval=5, value=50, /]
```

```
[slider, src2=my_slider_knob_img, id="slider1", min=10, max=110, interval=10, value=70, /]
```

```
[slider, indent=200, value-y=-400, id="slider1", min=0, max=100, value=50, interval=5, prefix="sound volume: ", suffix="%" /]
```


___
### toggle (tag)
Similar to a checkbox, a toggle will toggle between being "on" and "off" when clicked or pressed. If attribute [**input_value**](#input_value-attribute) is provided, the contents of the value will be displayed to the right of the toggle. By default, such text stems from the [**id**](#id-attribute). A unique id is required for each toggle. Note that you must provide a closing slash when working with textareas as they are self-closing (`[toggle ... /]`). These tags will terminate lines, acting as linebreaks.


**Associated Tags**: [checkbox](#checkbox-tag), [radio](#radio-tag), [slider](#slider-tag), [textarea](#textarea-tag)


| property | value
--- | ---
self-closing | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`tog`, `toggle`

#### Compatible Attributes

[bkg_color](#bkg_color-attribute), [color](#color-attribute), [disabled](#disabled-attribute), [function](#function-attribute), [hitbox_full](#hitbox_full-attribute), [image](#image-attribute), [indent](#indent-attribute), [input_checked](#input_checked-attribute), [input_id](#input_id-attribute), [input_value](#input_value-attribute), [order](#order-attribute), [shake_not_enabled](#shake_not_enabled-attribute), [sound](#sound-attribute), [value_x_offset](#value_x_offset-attribute), [value_y_offset](#value_y_offset-attribute), , 

#### Examples

```
[toggle, id="toggle1", value="Cannot toggle me", disabled=true /]
```

```
[toggle, id="toggle1", value="Toggle" /]
```

```
[toggle, id="toggle1", value="Not Toggled":"Toggled" /]
```

```
[toggle, id="toggle1", indent=50, checked=true /]
```

```
[toggle, id="toggle2", indent=-50, checked=false /]
```

```
[toggle, id="toggle2", checked=false, value="Milk", disabled=false, disable-shake=true /]
```

```
[toggle, id="toggle1", value="Click anywhere horizontally here", hbox-full=true /]
```

```
[toggle, id="toggle2", value="<-- Need to click on graphic", hbox-full=false /]
```

```
[toggle, indent=100, valueX=300, id="toggle2", checked=false, value="Milk" /]
```



## Attributes

Attributes are passed into [tags](#tags) to customize canvas [elements](#introduction). Conversely, [Meta Attributes](#meta) customize the canvas as a whole.

___
### alpha (attribute)
The transparency of certain aspects of the element. This attribute accepts a number of 0-100, where 0 is invisible and 100 is fully-visible.


**Associated Attributes**: [float_x](#float_x-attribute), [float_y](#float_y-attribute), [frame](#frame-attribute), [image](#image-attribute), [image2](#image2-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`alpha`

#### Compatible Tags

[image](#image-tag)

#### Examples

```
[img, src=my_sprite, width=200, height=200, alpha=50 /]
```

```
[img, src=my_sprite, alpha=90, frame=1 /]
```


___
### bkg_color (attribute)
The secondary, background color of an element. Some elements allow for this background color to be changed when hovered over by the cursor. To define such hover color, provide two colors separated by a colon (`:`).


**Associated Attributes**: [bkg_color_alt](#bkg_color_alt-attribute), [color](#color-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`bg`, `bkg`, `bg-col`, `blend`

#### Compatible Tags

[button](#button-tag), [checkbox](#checkbox-tag), [function](#function-tag), [h1](#h1-tag), [h2](#h2-tag), [h3](#h3-tag), [h4](#h4-tag), [h5](#h5-tag), [h6](#h6-tag), [image](#image-tag), [radio](#radio-tag), [slider](#slider-tag), [small](#small-tag), [span](#span-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[img, src=my_sprite, blend=blue /]
```

```
[button, bg=blue:green, scr=my_function, src=my_sprite /]
```


___
### bkg_color_alt (attribute)
This is a secondary background accent color that is used rarely for certain input types, like sliders. See [**bkg_color**](#bkg_color-attribute) for more information.


**Associated Attributes**: [bkg_color](#bkg_color-attribute), [color](#color-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`bg2`, `bkg2`, `bg-col2`, `blend2`, `bg-alt`

#### Compatible Tags

[slider](#slider-tag)

#### Examples

```
[slider, bkg=green, bkg2=blue, id="slider0", min=0, max=100, interval=5, value=50 /]
```

```
[slider, id="slider0", min=0, max=100, interval=5, value=50, bkg=green, bg-col2=#8E44AD /]
```


___
### border (attribute)
The border thickness, in pixels, around the background of an element. Note this functionality is quite limited and only meant to add breathing room to background colors. It doesn't affect position of the element itself.

This attribute accepts shorthand declaration. You may pass multiple values separated by colons (`:`).

- If one value is passed, all four border paddings are set to that value.

- If two values are passed, the first value sets the top and bottom border padding while the second value sets the left and right padding.

- If three values are passed, the first value sets the top, the second value sets the left and right, and the third value sets the bottom border padding.

- Finally, if four values are passed, the top, right, bottom, and left border paddings are set clockwise, respectively.




**Associated Attributes**: [border_bottom](#border_bottom-attribute), [border_left](#border_left-attribute), [border_right](#border_right-attribute), [border_top](#border_top-attribute), [thickness](#thickness-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`border`

#### Compatible Tags

[h1](#h1-tag), [h2](#h2-tag), [h3](#h3-tag), [h4](#h4-tag), [h5](#h5-tag), [h6](#h6-tag), [small](#small-tag), [span](#span-tag)

#### Examples

```
[span, c=white, bg=blue, border=5, rad=5]5px padding all around[/span]
```

```
[span, c=white, bg=blue, border=0:5 ]5px padding horizontally only[/span]
```

```
[span, c=white, bg=blue, border=5:5:0 ]5px padding everywhere except the bottom[/span]
```

```
[span, c=white, bg=blue, border=1:2:3:4 ]1px top, 2px right, 3px bottom, and 4px left[/span]
```


___
### border_bottom (attribute)
The bottom border thickness of the element's background, in pixels. See [**border**](#border-attribute) for more information.


**Associated Attributes**: [border](#border-attribute), [border_left](#border_left-attribute), [border_right](#border_right-attribute), [border_top](#border_top-attribute), [thickness](#thickness-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`border-b`, `bb`

#### Compatible Tags

[function](#function-tag), [h1](#h1-tag), [h2](#h2-tag), [h3](#h3-tag), [h4](#h4-tag), [h5](#h5-tag), [h6](#h6-tag), [small](#small-tag), [span](#span-tag)

#### Examples

```
[span, color=c_white, bg=c_blue, border-t=10]Bottom padding[/span]
```

```
[span, c=white, bg=blue-5, bt=10]Bottom padding[/span]
```


___
### border_left (attribute)
The left border thickness of the element's background, in pixels. See [**border**](#border-attribute) for more information.


**Associated Attributes**: [border](#border-attribute), [border_bottom](#border_bottom-attribute), [border_right](#border_right-attribute), [border_top](#border_top-attribute), [thickness](#thickness-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`border-l`, `bl`

#### Compatible Tags

[h1](#h1-tag), [h2](#h2-tag), [h3](#h3-tag), [h4](#h4-tag), [h5](#h5-tag), [h6](#h6-tag), [small](#small-tag), [span](#span-tag)

#### Examples

```
[span, color=c_white, bg=c_blue, border-l=10]Left padding[/span]
```

```
[span, c=white, bg=blue-5, bl=10]Left padding[/span]
```


___
### border_right (attribute)
The right border thickness of the element's background, in pixels. See [**border**](#border-attribute) for more information.


**Associated Attributes**: [border](#border-attribute), [border_bottom](#border_bottom-attribute), [border_left](#border_left-attribute), [border_top](#border_top-attribute), [thickness](#thickness-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`border-r`, `br`

#### Compatible Tags

[h1](#h1-tag), [h2](#h2-tag), [h3](#h3-tag), [h4](#h4-tag), [h5](#h5-tag), [h6](#h6-tag), [small](#small-tag), [span](#span-tag)

#### Examples

```
[span, color=c_white, bg=c_blue, border-r=10]Right padding[/span]
```

```
[span, c=white, bg=blue-5, br=10]Right padding[/span]
```


___
### border_top (attribute)
The top border thickness of the element's background, in pixels. See [**border**](#border-attribute) for more information.


**Associated Attributes**: [border](#border-attribute), [border_bottom](#border_bottom-attribute), [border_left](#border_left-attribute), [border_right](#border_right-attribute), [thickness](#thickness-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`border-t`, `bt`

#### Compatible Tags

[h1](#h1-tag), [h2](#h2-tag), [h3](#h3-tag), [h4](#h4-tag), [h5](#h5-tag), [h6](#h6-tag), [small](#small-tag), [span](#span-tag)

#### Examples

```
[span, color=c_white, bg=c_blue, border-t=10]Top padding[/span]
```

```
[span, c=white, bg=blue-5, bt=10]Top padding[/span]
```


___
### close (attribute)
The canvas name(s) that should be closed when clicking/pressing on a button or link. It works identical to [**open**](#open-attribute), so see that for more information. Note that you can use `self` to close *this* canvas, and `all` to close *all* canvases.


**Associated Attributes**: [function](#function-attribute), [hover_x_offset](#hover_x_offset-attribute), [hover_y_offset](#hover_y_offset-attribute), [label](#label-attribute), [open](#open-attribute), [radius](#radius-attribute), [tooltip](#tooltip-attribute), [underline](#underline-attribute)


| property | value
--- | ---
type | `CANVAS`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`close`

#### Compatible Tags

[button](#button-tag), [function](#function-tag)

#### Examples

```
[button, content="Click me!", w=200, h=50, close=second_canvas&third_canvas /]
```

```
[f, close=second_canvas]I'm a link that will close the second canvas[/f]
```

```
[f, close=self]Click to close canvas[/f]
```

```
[f, close=all]Close EVERYTHING![/f]
```


___
### color (attribute)
The primary blend color of the element. Some elements allow for this color to be changed when hovered over by the cursor. To define such hover color, provide two colors separated by a colon (`:`).


**Associated Attributes**: [bkg_color](#bkg_color-attribute), [bkg_color_alt](#bkg_color_alt-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`c`, `color`, `colour`

#### Compatible Tags

[button](#button-tag), [checkbox](#checkbox-tag), [function](#function-tag), [h1](#h1-tag), [h2](#h2-tag), [h3](#h3-tag), [h4](#h4-tag), [h5](#h5-tag), [h6](#h6-tag), [hrule](#hrule-tag), [radio](#radio-tag), [slider](#slider-tag), [small](#small-tag), [span](#span-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[span, c=red]I'm red text![/span]
```

```
[f, c=blue:green, scr=my_function]I'm a colorful link[/f]
```

```
[button, colour=red:white, width=96, height=32, scr=my_function, content="Hover me!" /]
```


___
### disabled (attribute)
For use with buttons and input elements. If `true`, the element will be disabled and not clickable (thus not triggering the callback). Call `set_disability(bool)` on such element in GML to dynamically set the disability status of eligible elements.


**Associated Attributes**: [hitbox_full](#hitbox_full-attribute), [input_checked](#input_checked-attribute), [input_group](#input_group-attribute), [input_id](#input_id-attribute), [input_value](#input_value-attribute), [order](#order-attribute), [shake_not_enabled](#shake_not_enabled-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`disabled`

#### Compatible Tags

[button](#button-tag), [checkbox](#checkbox-tag), [radio](#radio-tag), [slider](#slider-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[button, id="btn1", bg=blue, c=white, content="Cannot click me", src=my_function, disabled=true /]
```

```
[checkbox, id="checkbox1", value="Cannot uncheck me", disabled=true, checked=true /]
```

```
[slider, id="slider1", value=50, min=0, max=100, interval=10, disabled=true, suffix="%" /]
```

```
[radio, group="group1", id="radio1", value="Cannot select me", disabled=true /]
```

```
[toggle, id="toggle1", value="Cannot toggle me", disabled=true /]
```

```
[textfield, id="textfield1", value="Cannot edit me", disabled=true /]
```


___
### float_x (attribute)
If this attribute is set, the element will become a floating element. A floating element will not flow with other elements. It will be positioned independently, allowing for overlap. The value of this attribute will be the horizontal position of the element relative to the top-left x-coordinate of the canvas. See also, [**float_y**](#float_y-attribute).


**Associated Attributes**: [alpha](#alpha-attribute), [float_y](#float_y-attribute), [frame](#frame-attribute), [image](#image-attribute), [image2](#image2-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`fx`, `float-x`

#### Compatible Tags

[image](#image-tag)

#### Examples

```
[img, src=my_sprite, float-x=200, float-y=200 /]
```

```
[img, src=my_sprite, fx=200, frame=1, blend=red /]
```


___
### float_y (attribute)
See [**float_x**](#float_x-attribute) for more information. The value of this attribute will be the vertical position of the element relative to the top-left y-coordinate of the canvas.


**Associated Attributes**: [alpha](#alpha-attribute), [float_x](#float_x-attribute), [frame](#frame-attribute), [image](#image-attribute), [image2](#image2-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`fy`, `float-y`

#### Compatible Tags

[image](#image-tag)

#### Examples

```
[img, src=my_sprite, float-y=200, float-x=200 /]
```

```
[img, src=my_sprite, fy=200, frame=1, blend=pink /]
```


___
### frame (attribute)
Specifies which frame of a given sprite to draw. Used in conjunction with [**image**](#image-attribute).


**Associated Attributes**: [alpha](#alpha-attribute), [float_x](#float_x-attribute), [float_y](#float_y-attribute), [image](#image-attribute), [image2](#image2-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`frame`, `index`

#### Compatible Tags

[image](#image-tag)

#### Examples

```
[img, src=my_sprite, frame=0 /]
```

```
[img, src=my_sprite, index=1, blend=orange /]
```


___
### function (attribute)
The callback function that is executed when clicking/pressing on a button or link. You may pass arguments into this function using the following syntax: `my_function?args=first_arg&second_arg&third_arg`. Please note that arguments are passed as strings, so if you are expecting a number, you must cast using `real(argument)`.


**Associated Attributes**: [close](#close-attribute), [hover_x_offset](#hover_x_offset-attribute), [hover_y_offset](#hover_y_offset-attribute), [label](#label-attribute), [open](#open-attribute), [radius](#radius-attribute), [tooltip](#tooltip-attribute), [underline](#underline-attribute)


| property | value
--- | ---
type | `FUNCTION`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`scr`

#### Compatible Tags

[button](#button-tag), [checkbox](#checkbox-tag), [function](#function-tag), [toggle](#toggle-tag)

#### Examples

```
[f, scr=my_function]Click me![/f]
```

```
[f, scr=my_function, c=blue:purple]**I am a bold, blue->purple link**[/f]
```


___
### height (attribute)
The vertical size, in pixels, of the element. If the element is an image, the element height will default to the height of the image. See also, [**width**](#width-attribute).


**Associated Attributes**: [indent](#indent-attribute), [value_x_offset](#value_x_offset-attribute), [value_y_offset](#value_y_offset-attribute), [width](#width-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`h`, `height`

#### Compatible Tags

[break](#break-tag), [button](#button-tag), [hrule](#hrule-tag), [image](#image-tag), [slider](#slider-tag), [textarea](#textarea-tag)

#### Examples

```
[button, height=128, scr=my_function, bg=c_red:c_maroon, content="Click me!" /]
```

```
[img, h=500, src=my_sprite /]
```


___
### hitbox_full (attribute)
If `true`, certain input elements receive a canvas-wide hitmask to make clicking on elements (like radio buttons, checkboxes, and toggles) easier. If `false`, you must click directly on such input element graphics to toggle them.


**Associated Attributes**: [disabled](#disabled-attribute), [input_checked](#input_checked-attribute), [input_group](#input_group-attribute), [input_id](#input_id-attribute), [input_value](#input_value-attribute), [order](#order-attribute), [shake_not_enabled](#shake_not_enabled-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.2`
deprecated | `false`

#### Syntax

`hitboxFull`, `hitboxLarge`, `hitbox-full`, `hitbox-large`, `hbox-full`, `hbox-large`, `hbox-lg`

#### Compatible Tags

[checkbox](#checkbox-tag), [radio](#radio-tag), [toggle](#toggle-tag)

#### Examples

```
[toggle, id="toggle1", value="Click anywhere horizontally here", hbox-full=true /]
```

```
[toggle, id="toggle2", value="<-- Need to click on graphic", hbox-full=false /]
```

```
[radio, group="group1", id="radio1", value="click anywhere <-->", hbox-full=true /]
```


___
### hover_x_offset (attribute)
For use with buttons, this attribute is the horizontal offset, in pixels, of the [**label**](#label-attribute) text while being hovered over with the cursor.


**Associated Attributes**: [close](#close-attribute), [function](#function-attribute), [hover_y_offset](#hover_y_offset-attribute), [label](#label-attribute), [open](#open-attribute), [radius](#radius-attribute), [tooltip](#tooltip-attribute), [underline](#underline-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`hx`, `hover-x`

#### Compatible Tags

[button](#button-tag)

#### Examples

```
[button color=white, bg=blue, width=400, height=64, value="I move right 32px when you hover me!", hover-x=32 /]
```


___
### hover_y_offset (attribute)
This attribute is the vertical offset, in pixels, of the [**label**](#label-attribute) text while being hovered over with the cursor. Similar to [**hover_x_offset**](#hover_x_offset-attribute).


**Associated Attributes**: [close](#close-attribute), [function](#function-attribute), [hover_x_offset](#hover_x_offset-attribute), [label](#label-attribute), [open](#open-attribute), [radius](#radius-attribute), [tooltip](#tooltip-attribute), [underline](#underline-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`hy`, `hover-y`

#### Compatible Tags

[button](#button-tag)

#### Examples

```
[button color=black, img=button_sprite, value="Hover me", hover-y=8 /]
```


___
### image (attribute)
Specifies which sprite to draw. Use [**frame**](#frame-attribute) to draw a specific index of the sprite.


**Associated Attributes**: [alpha](#alpha-attribute), [float_x](#float_x-attribute), [float_y](#float_y-attribute), [frame](#frame-attribute), [image2](#image2-attribute)


| property | value
--- | ---
type | `SPRITE`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`src`, `img`

#### Compatible Tags

[button](#button-tag), [checkbox](#checkbox-tag), [image](#image-tag), [radio](#radio-tag), [slider](#slider-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[img, src=my_sprite, width=200, height=200 /]
```

```
[img, src=my_sprite, frame=2, blend=red /]
```


___
### image2 (attribute)
This is a secondary sprite to draw, used rarely for certain input types, like sliders (where it assumes the "knob" graphic). See [**image**](#image-attribute) for more information.


**Associated Attributes**: [alpha](#alpha-attribute), [float_x](#float_x-attribute), [float_y](#float_y-attribute), [frame](#frame-attribute), [image](#image-attribute)


| property | value
--- | ---
type | `SPRITE`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`img-alt`, `src2`, `img2`

#### Compatible Tags

[slider](#slider-tag)

#### Examples

```
[slider, img=my_slider_image, img-alt=my_slider_knob_img, id="slider0", min=0, max=100, interval=5, value=50, /]
```

```
[slider, src2=my_slider_knob_img, id="slider1", min=10, max=110, interval=10, value=70, /]
```


___
### indent (attribute)
The horizontal span, in pixels, an element should be shifted. It's similar to [**float_x**](#float_x-attribute), except using this attribute does not make the element a floating element.


**Associated Attributes**: [height](#height-attribute), [value_x_offset](#value_x_offset-attribute), [value_y_offset](#value_y_offset-attribute), [width](#width-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`indent`

#### Compatible Tags

[checkbox](#checkbox-tag), [radio](#radio-tag), [slider](#slider-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[toggle, id="toggle1", indent=50, checked=true /]
```

```
[toggle, id="toggle2", indent=-50, checked=false /]
```


___
### input_checked (attribute)
For use with radio buttons, checkboxes, and toggles. If `true`, this input element will be "checked" (on). If `false`, this input element will not be checked.


**Associated Attributes**: [disabled](#disabled-attribute), [hitbox_full](#hitbox_full-attribute), [input_group](#input_group-attribute), [input_id](#input_id-attribute), [input_value](#input_value-attribute), [order](#order-attribute), [shake_not_enabled](#shake_not_enabled-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`checked`

#### Compatible Tags

[checkbox](#checkbox-tag), [radio](#radio-tag), [toggle](#toggle-tag)

#### Examples

```
[radio, group="group1", id="radio1", value="Apples" /]
[radio, group="group1", id="radio2", value="Bananas" /]
[radio, group="group1", id="radio3", value="Cherries" /]
```

```
[radio, group="group2", id="r1", value="Separate Apples" /]
[radio, group="group2", id="r2", value="Separate Bananas" /]
[radio, group="group2", id="r3", value="Separate Cherries" /]
```


___
### input_group (attribute)
For use with radio buttons, define which group a given radio button belongs to. Only one radio button per group can be "checked" (on) at a given time. Note that it takes one gamestep for the group to process a change in children's on/off status.


**Associated Attributes**: [disabled](#disabled-attribute), [hitbox_full](#hitbox_full-attribute), [input_checked](#input_checked-attribute), [input_id](#input_id-attribute), [input_value](#input_value-attribute), [order](#order-attribute), [shake_not_enabled](#shake_not_enabled-attribute)


| property | value
--- | ---
type | `ID`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`group`

#### Compatible Tags

[radio](#radio-tag)

#### Examples

```
[radio, group="group1", id="radio1", value="Apples" /]
[radio, group="group1", id="radio2", value="Bananas" /]
[radio, group="group1", id="radio3", value="Cherries" /]
```

```
[radio, group="group2", id="r1", value="Separate Apples" /]
[radio, group="group2", id="r2", value="Separate Bananas" /]
[radio, group="group2", id="r3", value="Separate Cherries" /]
```


___
### input_id (attribute)
A unique identifier for an input type or button.


**Associated Attributes**: [disabled](#disabled-attribute), [hitbox_full](#hitbox_full-attribute), [input_checked](#input_checked-attribute), [input_group](#input_group-attribute), [input_value](#input_value-attribute), [order](#order-attribute), [shake_not_enabled](#shake_not_enabled-attribute)


| property | value
--- | ---
type | `ID`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`id`

#### Compatible Tags

[button](#button-tag), [checkbox](#checkbox-tag), [radio](#radio-tag), [slider](#slider-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[button, id="btn1", bg=blue, c=white, content="I'm a button", src=my_function /]
```

```
[radio, group="group1", id="radio1", value="Apples" /]
[radio, group="group1", id="radio2", value="Bananas" /]
```

```
[checkbox, id="checkbox1", value="Checkbox", checked=true /]
```

```
[slider, id="slider1", value=50, min=0, max=100, interval=10, suffix="%" /]
```

```
[toggle, id="toggle1", value="Toggle" /]
```

```
[textfield, id="textfield1", value="Hello, World!"/]
```


___
### input_interval (attribute)
For use with sliders, the slider will "snap" to the nearest interval between the [**input_min_value**](#input_min_value-attribute) and [**input_max_value**](#input_max_value-attribute) range defined.


**Associated Attributes**: [input_max_value](#input_max_value-attribute), [input_min_value](#input_min_value-attribute), [value_prefix](#value_prefix-attribute), [value_suffix](#value_suffix-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`interval`

#### Compatible Tags

[slider](#slider-tag)

#### Examples

```
[slider, id="slider2", min=500, max=1000, interval=2 ]
```

```
[slider, id="slider1", min=0, max=100, interval=10, value=50 ]
```


___
### input_maxlength (attribute)
For use with textfields, this is the maximum number of characters the input string can be. See [**input_value**](#input_value-attribute) for more information.


**Associated Attributes**: [input_numbers_only](#input_numbers_only-attribute), [placeholder](#placeholder-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`maxlength`

#### Compatible Tags

[textarea](#textarea-tag)

#### Examples

```
[textfield, maxlength=10, id="t1", value="This text will get cut off because it exceeds 10 characters" /]
```


___
### input_max_value (attribute)
For use with sliders, this is the maximum [**input_value**](#input_value-attribute) a slider can be. See also, [**input_min_value**](#input_min_value-attribute).


**Associated Attributes**: [input_interval](#input_interval-attribute), [input_min_value](#input_min_value-attribute), [value_prefix](#value_prefix-attribute), [value_suffix](#value_suffix-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`max`

#### Compatible Tags

[slider](#slider-tag)

#### Examples

```
[slider, id="slider1", min=0, max=100, interval=10, value=50 ]
```

```
[slider, id="slider2", min=500, max=1000 ]
```


___
### input_min_value (attribute)
For use with sliders, this is the minimum [**input_value**](#input_value-attribute) a slider can be. See also, [**input_max_value**](#input_max_value-attribute).


**Associated Attributes**: [input_interval](#input_interval-attribute), [input_max_value](#input_max_value-attribute), [value_prefix](#value_prefix-attribute), [value_suffix](#value_suffix-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`min`

#### Compatible Tags

[slider](#slider-tag)

#### Examples

```
[slider, id="slider1", min=0, max=100, interval=10, value=50 ]
```

```
[slider, id="slider2", min=500, max=1000 ]
```


___
### input_numbers_only (attribute)
For use with textfields; if `true`, the textfield will only accept numbers (real and ints). If `false`, any text can be entered into the textfield.


**Associated Attributes**: [input_maxlength](#input_maxlength-attribute), [placeholder](#placeholder-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`numbersOnly`, `numbers-only`

#### Compatible Tags

[textarea](#textarea-tag)

#### Examples

```
[textfield, numbersOnly=true, id="age" /]
```

```
[textfield, numbers-only=true, id="favorite-number" /]
```


___
### input_value (attribute)
For use with input elements (e.g. checkboxes, toggles, radio buttons, sliders, and textfields), this is the `value` the element will return. For textfields, this is the text the user can edit. For sliders, this is the value of the slider. For checkboxes, toggles, and radio buttons, this is simply the value of the element. Note that for checkboxes and toggles, you can also specify two values--one for being "off" and one for being "on"-- by separating two values with a colon (`:`).


**Associated Attributes**: [disabled](#disabled-attribute), [hitbox_full](#hitbox_full-attribute), [input_checked](#input_checked-attribute), [input_group](#input_group-attribute), [input_id](#input_id-attribute), [order](#order-attribute), [shake_not_enabled](#shake_not_enabled-attribute)


| property | value
--- | ---
type | `STRING`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`value`

#### Compatible Tags

[checkbox](#checkbox-tag), [radio](#radio-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[checkbox, id="checkbox1", value="Not Checked":"Checked!", checked=true /]
```

```
[toggle, id="toggle1", value="Not Toggled":"Toggled" /]
```

```
[textfield, id="textfield1", value="Hello, World!"/]
```


___
### label (attribute)
Plaintext string that is rendered ontop of certain elements, like buttons.


**Associated Attributes**: [close](#close-attribute), [function](#function-attribute), [hover_x_offset](#hover_x_offset-attribute), [hover_y_offset](#hover_y_offset-attribute), [open](#open-attribute), [radius](#radius-attribute), [tooltip](#tooltip-attribute), [underline](#underline-attribute)


| property | value
--- | ---
type | `STRING`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`label`, `content`

#### Compatible Tags

[button](#button-tag)

#### Examples

```
[button, w=320, h=32, label="I'm a button!" bg=blue, c=white /]
```

```
[button, label="I'm a button", img=button_img /]
```


___
### open (attribute)
The canvas name(s) that should be opened when clicking/pressing on a button or link. Note that the canvas name is derived from the canvas filename (the file name minus paths and file extension). You may open multiple canvases by separating canvas names with the ampersand (`&`).


**Associated Attributes**: [close](#close-attribute), [function](#function-attribute), [hover_x_offset](#hover_x_offset-attribute), [hover_y_offset](#hover_y_offset-attribute), [label](#label-attribute), [radius](#radius-attribute), [tooltip](#tooltip-attribute), [underline](#underline-attribute)


| property | value
--- | ---
type | `CANVAS`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`open`

#### Compatible Tags

[button](#button-tag), [function](#function-tag)

#### Examples

```
[button, content="Click me!", w=200, h=50, open=second_canvas&third_canvas /]
```

```
[f, open=second_canvas]I'm a link that will open a second canvas[/f]
```


___
### order (attribute)
For use with input elements and buttons. You may cycle between elements by pressing tab/shift+tab or the arrow keys. This attribute specifies the order that navigation between elements within a canvas happens. The higher the number, the further down the stack the element is in the cycling process.


**Associated Attributes**: [disabled](#disabled-attribute), [hitbox_full](#hitbox_full-attribute), [input_checked](#input_checked-attribute), [input_group](#input_group-attribute), [input_id](#input_id-attribute), [input_value](#input_value-attribute), [shake_not_enabled](#shake_not_enabled-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.1`
deprecated | `false`

#### Syntax

`order`, `ord`

#### Compatible Tags

[button](#button-tag), [checkbox](#checkbox-tag), [radio](#radio-tag), [slider](#slider-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[radio, group="radio-group1", id="rd-3", value="Third", order=2 /]
[radio, group="radio-group1", id="rd-1", value="Second", order=1 /]
[radio, group="radio-group1", id="rd-button", value="Fourth", order=3 /]
[radio, group="radio-group1", id="rd-999", value="First", order=0 /]
[button, scr=my_submit_function, order=4, content="Submit" id="submit", c=white, bg=green, w=128, h=32 /]
```


___
### placeholder (attribute)
If the textfield string is empty, this placeholder string will be drawn. The color of the placeholder string defaults to constant `SH_TEXTBOX_PLACEHOLDER_COLOR`.


**Associated Attributes**: [input_maxlength](#input_maxlength-attribute), [input_numbers_only](#input_numbers_only-attribute)


| property | value
--- | ---
type | `STRING`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`placeholder`

#### Compatible Tags

[textarea](#textarea-tag)

#### Examples

```
[textfield, placeholder="Your text here" id="textbox1" /]
```


___
### radius (attribute)
How rounded the corners of the element's background are, in pixels. The higher the number, the more curved the canvas corners are.


**Associated Attributes**: [close](#close-attribute), [function](#function-attribute), [hover_x_offset](#hover_x_offset-attribute), [hover_y_offset](#hover_y_offset-attribute), [label](#label-attribute), [open](#open-attribute), [tooltip](#tooltip-attribute), [underline](#underline-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`rad`, `radius`

#### Compatible Tags

[button](#button-tag), [h1](#h1-tag), [h2](#h2-tag), [h3](#h3-tag), [h4](#h4-tag), [h5](#h5-tag), [h6](#h6-tag), [small](#small-tag), [span](#span-tag), [textarea](#textarea-tag)

#### Examples

```
[button, content="I'm a rounded button", rad=32, bg=blue, c=white, scr=my_function /]
```

```
[button, radius=2, content="I'm ever so slightly rounded", width=300, height=64, bg=purple:c_lime, c=white:black, scr=my_function /]
```


___
### shake_not_enabled (attribute)
If `true`, the canvas will shake horizontally when a disabled button, link, or input element is pressed. This is not enabled by default.


**Associated Attributes**: [disabled](#disabled-attribute), [hitbox_full](#hitbox_full-attribute), [input_checked](#input_checked-attribute), [input_group](#input_group-attribute), [input_id](#input_id-attribute), [input_value](#input_value-attribute), [order](#order-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`disable-shake`

#### Compatible Tags

[button](#button-tag), [checkbox](#checkbox-tag), [radio](#radio-tag), [slider](#slider-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[checkbox, id="checkbox1", checked=true, value="off":"on", disabled=true, disable-shake=true /]
```

```
[toggle, id="toggle2", checked=false, value="Milk", disabled=false, disable-shake=true /]
```


___
### sound (attribute)
If an element is interacted with (e.g. clicked/pressed), a sound may play assuming there is a valid callback function. You can specify a seconary sound that should play if the element is interacted with while disabled. To provide a secondary sound, pass two sounds separated by a colon (`:`).

| property | value
--- | ---
type | `SOUND`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`snd`, `sound`

#### Compatible Tags

[button](#button-tag), [checkbox](#checkbox-tag), [function](#function-tag), [radio](#radio-tag), [slider](#slider-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[button, scr=my_function, bg=blue, c=white, content="Hello!", snd=my_sound_enabled:my_sound_disabled, disabled=true /]
```

```
[button, scr=my_function_winner, bg=green-5, c=white, content="You Won!!", snd=fun_sound_effect, disabled=false /]
```


___
### thickness (attribute)
Certain tags, like horizontal rules and function links, accept this attribute. It affects the thickness of lines. For horizontal rules, thickness affects the colored line thickness and for links, thickness affects the underline thickness on hover.


**Associated Attributes**: [border](#border-attribute), [border_bottom](#border_bottom-attribute), [border_left](#border_left-attribute), [border_right](#border_right-attribute), [border_top](#border_top-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`th`, `thickness`

#### Compatible Tags

[function](#function-tag), [hrule](#hrule-tag)

#### Examples

```
[hr, thickness=4, height=16, color=red /]
```

```
[hr, th=4, h=16, c=gray /]
```


___
### tooltip (attribute)
When the cursor is ontop of an element that has this attribute defined, such text will appear relative to the cursor. This tooltip will be drawn over all canvases.


**Associated Attributes**: [close](#close-attribute), [function](#function-attribute), [hover_x_offset](#hover_x_offset-attribute), [hover_y_offset](#hover_y_offset-attribute), [label](#label-attribute), [open](#open-attribute), [radius](#radius-attribute), [underline](#underline-attribute)


| property | value
--- | ---
type | `STRING`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`tooltip`

#### Compatible Tags

[button](#button-tag), [function](#function-tag)

#### Examples

```
[f, tooltip="I'm a tooltip!", scr=my_function /]
```

```
[button, content="Hover me!", tooltip="You hovered me!", scr=my_function /]
```


___
### underline (attribute)
For use with links, this is the color of the underline that appears when the cursor hovers over the element.


**Associated Attributes**: [close](#close-attribute), [function](#function-attribute), [hover_x_offset](#hover_x_offset-attribute), [hover_y_offset](#hover_y_offset-attribute), [label](#label-attribute), [open](#open-attribute), [radius](#radius-attribute), [tooltip](#tooltip-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`ul`, `underline`

#### Compatible Tags

[function](#function-tag)

#### Examples

```
[f, open=second_canvas, underline=red, thickness=4, color=red /]I'm a red link with a red underline when you hover me[/f]
```


___
### value_prefix (attribute)
The string of text that is to the left of the [**input_value**](#input_value-attribute) of a slider. See also, [**value_suffix**](#value_suffix-attribute).


**Associated Attributes**: [input_interval](#input_interval-attribute), [input_max_value](#input_max_value-attribute), [input_min_value](#input_min_value-attribute), [value_suffix](#value_suffix-attribute)


| property | value
--- | ---
type | `STRING`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`prefix`

#### Compatible Tags

[slider](#slider-tag)

#### Examples

```
[slider, id="slider1", min=0, max=100, value=50, interval=5, prefix="sound volume: ", suffix="%" /]
```


___
### value_suffix (attribute)
The string of text that is to the right of the [**input_value**](#input_value-attribute) of a slider. See also, [**value_prefix**](#value_prefix-attribute).


**Associated Attributes**: [input_interval](#input_interval-attribute), [input_max_value](#input_max_value-attribute), [input_min_value](#input_min_value-attribute), [value_prefix](#value_prefix-attribute)


| property | value
--- | ---
type | `STRING`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`suffix`

#### Compatible Tags

[slider](#slider-tag)

#### Examples

```
[slider, id="slider2", min=1, max=5, value=3, interval=1, prefix="multiplier: ", suffix=" times" /]
```


___
### value_x_offset (attribute)
The horizontal offset, in pixels, of the [**input_value**](#input_value-attribute) text label on the input element. This is applied after [**indent**](#indent-attribute). See also, [**value_y_offset**](#value_y_offset-attribute).


**Associated Attributes**: [height](#height-attribute), [indent](#indent-attribute), [value_y_offset](#value_y_offset-attribute), [width](#width-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.2`
deprecated | `false`

#### Syntax

`value-x`, `valueX`

#### Compatible Tags

[checkbox](#checkbox-tag), [radio](#radio-tag), [slider](#slider-tag), [toggle](#toggle-tag)

#### Examples

```
[checkbox, indent=-200, value-x=500, id="checkbox1", checked=true, value="off":"on" /]
```

```
[toggle, indent=100, valueX=300, id="toggle2", checked=false, value="Milk" /]
```

```
[slider, indent=200, value-x=-400, id="slider1", min=0, max=100, value=50, interval=5, prefix="sound volume: ", suffix="%" /]
```


___
### value_y_offset (attribute)
The vertical offset, in pixels, of the [**input_value**](#input_value-attribute) text label on the input element. This is applied after [**indent**](#indent-attribute). See also, [**value_x_offset**](#value_x_offset-attribute).


**Associated Attributes**: [height](#height-attribute), [indent](#indent-attribute), [value_x_offset](#value_x_offset-attribute), [width](#width-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.2`
deprecated | `false`

#### Syntax

`value-y`, `valueY`

#### Compatible Tags

[checkbox](#checkbox-tag), [radio](#radio-tag), [slider](#slider-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[checkbox, indent=-200, value-y=500, id="checkbox1", checked=true, value="off":"on" /]
```

```
[toggle, indent=100, valueY=300, id="toggle2", checked=false, value="Milk" /]
```

```
[slider, indent=200, value-y=-400, id="slider1", min=0, max=100, value=50, interval=5, prefix="sound volume: ", suffix="%" /]
```


___
### width (attribute)
The horizontal size, in pixels, of the element. If the element is an image, the element width will default to the width of the image. See also, [**height**](#height-attribute).


**Associated Attributes**: [height](#height-attribute), [indent](#indent-attribute), [value_x_offset](#value_x_offset-attribute), [value_y_offset](#value_y_offset-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`w`, `width`

#### Compatible Tags

[button](#button-tag), [image](#image-tag), [spacer](#spacer-tag), [textarea](#textarea-tag)

#### Examples

```
[img, src=my_sprite, w=400, h=200 /]
```

```
[button, bg=c_ltgray:c_gray, content="Click me!", width=320, height=128, scr=my_function /]
```


___
### value_suffix (attribute)
The string of text that is to the right of the [**input_value**](#input_value-attribute) of a slider. See also, [**value_prefix**](#value_prefix-attribute).


**Associated Attributes**: [input_interval](#input_interval-attribute), [input_max_value](#input_max_value-attribute), [input_min_value](#input_min_value-attribute), [value_prefix](#value_prefix-attribute)


| property | value
--- | ---
type | `STRING`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`suffix`

#### Compatible Tags

[slider](#slider-tag)

#### Examples

```
[slider, id="slider2", min=1, max=5, value=3, interval=1, prefix="multiplier: ", suffix=" times" /]
```


___
### indent (attribute)
The horizontal span, in pixels, an element should be shifted. It's similar to [**float_x**](#float_x-attribute), except using this attribute does not make the element a floating element.


**Associated Attributes**: [height](#height-attribute), [value_x_offset](#value_x_offset-attribute), [value_y_offset](#value_y_offset-attribute), [width](#width-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`indent`

#### Compatible Tags

[checkbox](#checkbox-tag), [radio](#radio-tag), [slider](#slider-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[toggle, id="toggle1", indent=50, checked=true /]
```

```
[toggle, id="toggle2", indent=-50, checked=false /]
```


___
### order (attribute)
For use with input elements and buttons. You may cycle between elements by pressing tab/shift+tab or the arrow keys. This attribute specifies the order that navigation between elements within a canvas happens. The higher the number, the further down the stack the element is in the cycling process.


**Associated Attributes**: [disabled](#disabled-attribute), [hitbox_full](#hitbox_full-attribute), [input_checked](#input_checked-attribute), [input_group](#input_group-attribute), [input_id](#input_id-attribute), [input_value](#input_value-attribute), [shake_not_enabled](#shake_not_enabled-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.1`
deprecated | `false`

#### Syntax

`order`, `ord`

#### Compatible Tags

[button](#button-tag), [checkbox](#checkbox-tag), [radio](#radio-tag), [slider](#slider-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[radio, group="radio-group1", id="rd-3", value="Third", order=2 /]
[radio, group="radio-group1", id="rd-1", value="Second", order=1 /]
[radio, group="radio-group1", id="rd-button", value="Fourth", order=3 /]
[radio, group="radio-group1", id="rd-999", value="First", order=0 /]
[button, scr=my_submit_function, order=4, content="Submit" id="submit", c=white, bg=green, w=128, h=32 /]
```


___
### placeholder (attribute)
If the textfield string is empty, this placeholder string will be drawn. The color of the placeholder string defaults to constant `SH_TEXTBOX_PLACEHOLDER_COLOR`.


**Associated Attributes**: [input_maxlength](#input_maxlength-attribute), [input_numbers_only](#input_numbers_only-attribute)


| property | value
--- | ---
type | `STRING`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`placeholder`

#### Compatible Tags

[textarea](#textarea-tag)

#### Examples

```
[textfield, placeholder="Your text here" id="textbox1" /]
```


___
### disabled (attribute)
For use with buttons and input elements. If `true`, the element will be disabled and not clickable (thus not triggering the callback). Call `set_disability(bool)` on such element in GML to dynamically set the disability status of eligible elements.


**Associated Attributes**: [hitbox_full](#hitbox_full-attribute), [input_checked](#input_checked-attribute), [input_group](#input_group-attribute), [input_id](#input_id-attribute), [input_value](#input_value-attribute), [order](#order-attribute), [shake_not_enabled](#shake_not_enabled-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`disabled`

#### Compatible Tags

[button](#button-tag), [checkbox](#checkbox-tag), [radio](#radio-tag), [slider](#slider-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[button, id="btn1", bg=blue, c=white, content="Cannot click me", src=my_function, disabled=true /]
```

```
[checkbox, id="checkbox1", value="Cannot uncheck me", disabled=true, checked=true /]
```

```
[slider, id="slider1", value=50, min=0, max=100, interval=10, disabled=true, suffix="%" /]
```

```
[radio, group="group1", id="radio1", value="Cannot select me", disabled=true /]
```

```
[toggle, id="toggle1", value="Cannot toggle me", disabled=true /]
```

```
[textfield, id="textfield1", value="Cannot edit me", disabled=true /]
```


___
### open (attribute)
The canvas name(s) that should be opened when clicking/pressing on a button or link. Note that the canvas name is derived from the canvas filename (the file name minus paths and file extension). You may open multiple canvases by separating canvas names with the ampersand (`&`).


**Associated Attributes**: [close](#close-attribute), [function](#function-attribute), [hover_x_offset](#hover_x_offset-attribute), [hover_y_offset](#hover_y_offset-attribute), [label](#label-attribute), [radius](#radius-attribute), [tooltip](#tooltip-attribute), [underline](#underline-attribute)


| property | value
--- | ---
type | `CANVAS`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`open`

#### Compatible Tags

[button](#button-tag), [function](#function-tag)

#### Examples

```
[button, content="Click me!", w=200, h=50, open=second_canvas&third_canvas /]
```

```
[f, open=second_canvas]I'm a link that will open a second canvas[/f]
```


___
### close (attribute)
The canvas name(s) that should be closed when clicking/pressing on a button or link. It works identical to [**open**](#open-attribute), so see that for more information. Note that you can use `self` to close *this* canvas, and `all` to close *all* canvases.


**Associated Attributes**: [function](#function-attribute), [hover_x_offset](#hover_x_offset-attribute), [hover_y_offset](#hover_y_offset-attribute), [label](#label-attribute), [open](#open-attribute), [radius](#radius-attribute), [tooltip](#tooltip-attribute), [underline](#underline-attribute)


| property | value
--- | ---
type | `CANVAS`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`close`

#### Compatible Tags

[button](#button-tag), [function](#function-tag)

#### Examples

```
[button, content="Click me!", w=200, h=50, close=second_canvas&third_canvas /]
```

```
[f, close=second_canvas]I'm a link that will close the second canvas[/f]
```

```
[f, close=self]Click to close canvas[/f]
```

```
[f, close=all]Close EVERYTHING![/f]
```


___
### hover_x_offset (attribute)
For use with buttons, this attribute is the horizontal offset, in pixels, of the [**label**](#label-attribute) text while being hovered over with the cursor.


**Associated Attributes**: [close](#close-attribute), [function](#function-attribute), [hover_y_offset](#hover_y_offset-attribute), [label](#label-attribute), [open](#open-attribute), [radius](#radius-attribute), [tooltip](#tooltip-attribute), [underline](#underline-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`hx`, `hover-x`

#### Compatible Tags

[button](#button-tag)

#### Examples

```
[button color=white, bg=blue, width=400, height=64, value="I move right 32px when you hover me!", hover-x=32 /]
```


___
### hover_y_offset (attribute)
This attribute is the vertical offset, in pixels, of the [**label**](#label-attribute) text while being hovered over with the cursor. Similar to [**hover_x_offset**](#hover_x_offset-attribute).


**Associated Attributes**: [close](#close-attribute), [function](#function-attribute), [hover_x_offset](#hover_x_offset-attribute), [label](#label-attribute), [open](#open-attribute), [radius](#radius-attribute), [tooltip](#tooltip-attribute), [underline](#underline-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`hy`, `hover-y`

#### Compatible Tags

[button](#button-tag)

#### Examples

```
[button color=black, img=button_sprite, value="Hover me", hover-y=8 /]
```


___
### float_x (attribute)
If this attribute is set, the element will become a floating element. A floating element will not flow with other elements. It will be positioned independently, allowing for overlap. The value of this attribute will be the horizontal position of the element relative to the top-left x-coordinate of the canvas. See also, [**float_y**](#float_y-attribute).


**Associated Attributes**: [alpha](#alpha-attribute), [float_y](#float_y-attribute), [frame](#frame-attribute), [image](#image-attribute), [image2](#image2-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`fx`, `float-x`

#### Compatible Tags

[image](#image-tag)

#### Examples

```
[img, src=my_sprite, float-x=200, float-y=200 /]
```

```
[img, src=my_sprite, fx=200, frame=1, blend=red /]
```


___
### float_y (attribute)
See [**float_x**](#float_x-attribute) for more information. The value of this attribute will be the vertical position of the element relative to the top-left y-coordinate of the canvas.


**Associated Attributes**: [alpha](#alpha-attribute), [float_x](#float_x-attribute), [frame](#frame-attribute), [image](#image-attribute), [image2](#image2-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.0`
deprecated | `false`

#### Syntax

`fy`, `float-y`

#### Compatible Tags

[image](#image-tag)

#### Examples

```
[img, src=my_sprite, float-y=200, float-x=200 /]
```

```
[img, src=my_sprite, fy=200, frame=1, blend=pink /]
```


___
### hitbox_full (attribute)
If `true`, certain input elements receive a canvas-wide hitmask to make clicking on elements (like radio buttons, checkboxes, and toggles) easier. If `false`, you must click directly on such input element graphics to toggle them.


**Associated Attributes**: [disabled](#disabled-attribute), [input_checked](#input_checked-attribute), [input_group](#input_group-attribute), [input_id](#input_id-attribute), [input_value](#input_value-attribute), [order](#order-attribute), [shake_not_enabled](#shake_not_enabled-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.2`
deprecated | `false`

#### Syntax

`hitboxFull`, `hitboxLarge`, `hitbox-full`, `hitbox-large`, `hbox-full`, `hbox-large`, `hbox-lg`

#### Compatible Tags

[checkbox](#checkbox-tag), [radio](#radio-tag), [toggle](#toggle-tag)

#### Examples

```
[toggle, id="toggle1", value="Click anywhere horizontally here", hbox-full=true /]
```

```
[toggle, id="toggle2", value="<-- Need to click on graphic", hbox-full=false /]
```

```
[radio, group="group1", id="radio1", value="click anywhere <-->", hbox-full=true /]
```


___
### value_x_offset (attribute)
The horizontal offset, in pixels, of the [**input_value**](#input_value-attribute) text label on the input element. This is applied after [**indent**](#indent-attribute). See also, [**value_y_offset**](#value_y_offset-attribute).


**Associated Attributes**: [height](#height-attribute), [indent](#indent-attribute), [value_y_offset](#value_y_offset-attribute), [width](#width-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.2`
deprecated | `false`

#### Syntax

`value-x`, `valueX`

#### Compatible Tags

[checkbox](#checkbox-tag), [radio](#radio-tag), [slider](#slider-tag), [toggle](#toggle-tag)

#### Examples

```
[checkbox, indent=-200, value-x=500, id="checkbox1", checked=true, value="off":"on" /]
```

```
[toggle, indent=100, valueX=300, id="toggle2", checked=false, value="Milk" /]
```

```
[slider, indent=200, value-x=-400, id="slider1", min=0, max=100, value=50, interval=5, prefix="sound volume: ", suffix="%" /]
```


___
### value_y_offset (attribute)
The vertical offset, in pixels, of the [**input_value**](#input_value-attribute) text label on the input element. This is applied after [**indent**](#indent-attribute). See also, [**value_x_offset**](#value_x_offset-attribute).


**Associated Attributes**: [height](#height-attribute), [indent](#indent-attribute), [value_x_offset](#value_x_offset-attribute), [width](#width-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `undefined`
isMeta | `false`
since | `0.9.2`
deprecated | `false`

#### Syntax

`value-y`, `valueY`

#### Compatible Tags

[checkbox](#checkbox-tag), [radio](#radio-tag), [slider](#slider-tag), [textarea](#textarea-tag), [toggle](#toggle-tag)

#### Examples

```
[checkbox, indent=-200, value-y=500, id="checkbox1", checked=true, value="off":"on" /]
```

```
[toggle, indent=100, valueY=300, id="toggle2", checked=false, value="Milk" /]
```

```
[slider, indent=200, value-y=-400, id="slider1", min=0, max=100, value=50, interval=5, prefix="sound volume: ", suffix="%" /]
```



## Meta Attributes

The **meta tag** should be declared once at the top of the canvas file. The meta tag accepts all meta attributes. These attributes are special in that they affect the entire canvas rather than individual elements within. Note that you must provide a closing slash when working with meta tags e.g. (`[meta ... /]`).
___
### attach (meta attribute)
You may attach one canvas to another under limited criteria. If a canvas has an attachment, it cannot be attached to another canvas. The canvas with the lower [**depth**](#depth-meta-attribute) gets updated first, so attach canvases with that have higher-depths to canvases with lower-depths or the coordinate updating will lag behind by one gamestep. Canvases that are attached may not have a [**caption**](#caption-meta-attribute) bar and cannot be marked as [**draggable**](#draggable-meta-attribute), even if the canvas being attached to no longer exists.


**Associated Meta Attributes**: [attach_halign](#attach_halign-meta-attribute), [attach_valign](#attach_valign-meta-attribute), [attach_x_offset](#attach_x_offset-meta-attribute), [attach_y_offset](#attach_y_offset-meta-attribute)


| property | value
--- | ---
type | `CANVAS`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`attach`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, attach=second_canvas /]
```

```
[meta, attach=my_other_canvas /]
```


___
### attach_halign (meta attribute)
The horizontal alignment of the canvas, relative to the left-x coordinate of the canvas being attached to (parent). `fa_center` shifts the canvas left by 50% the width of the parent's canvas width. `fa_right` shifts the canvas left by 100% the width of the parent canvas.


**Associated Meta Attributes**: [attach](#attach-meta-attribute), [attach_valign](#attach_valign-meta-attribute), [attach_x_offset](#attach_x_offset-meta-attribute), [attach_y_offset](#attach_y_offset-meta-attribute)


| property | value
--- | ---
type | `ALIGN`
defaultValue | `0`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`attachHalign`, `attachhalign`, `attach-halign`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, attach=left_canvas, attach-halign=fa_left /]
```

```
[meta, attach=other_canvas, attachHalign=fa_center /]
```

```
[meta, attach=right_canvas, attach-halign=fa_right /]
```


___
### attach_valign (meta attribute)
The vertical alignment of the canvas, relative to the top-y coordinate of the canvas being attached to (parent). `fa_middle` shifts the canvas up by 50% the height of the parent's canvas height. `fa_bottom` shifts the canvas up by 100% the height of the parent canvas.


**Associated Meta Attributes**: [attach](#attach-meta-attribute), [attach_halign](#attach_halign-meta-attribute), [attach_x_offset](#attach_x_offset-meta-attribute), [attach_y_offset](#attach_y_offset-meta-attribute)


| property | value
--- | ---
type | `ALIGN`
defaultValue | `0`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`attachValign`, `attachvalign`, `attach-valign`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, attach=top_canvas, attach-valign=fa_top /]
```

```
[meta, attach=other_canvas, attachValign=fa_middle /]
```

```
[meta, attach=bottom_canvas, attach-valign=fa_bottom /]
```


___
### attach_x_offset (meta attribute)
Applied after [**attach_halign**](#attach_halign-meta-attribute) and [**attach_valign**](#attach_valign-meta-attribute), this is the horizontal offset, in pixels, of the canvas relative to its parent.


**Associated Meta Attributes**: [attach](#attach-meta-attribute), [attach_halign](#attach_halign-meta-attribute), [attach_valign](#attach_valign-meta-attribute), [attach_y_offset](#attach_y_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `0`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`attachX`, `attachx`, `attach-x`, `attach-xoffset`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, attach=second_canvas, attach-x=200 /]
```

```
[meta, attach=second_canvas, attach-x=-500 /]
```

```
[meta, attach-xoffset=125, attach=second_canvas /]
```


___
### attach_y_offset (meta attribute)
Applied after [**attach_halign**](#attach_halign-meta-attribute) and [**attach_valign**](#attach_valign-meta-attribute), this is the vertical offset, in pixels, of the canvas relative to its parent.


**Associated Meta Attributes**: [attach](#attach-meta-attribute), [attach_halign](#attach_halign-meta-attribute), [attach_valign](#attach_valign-meta-attribute), [attach_x_offset](#attach_x_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `0`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`attachY`, `attachy`, `attach-y`, `attach-yoffset`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, attach=second_canvas, attach-y=200 /]
```

```
[meta, attach=second_canvas, attach-y=-500 /]
```

```
[meta, attach-yoffset=125, attach=second_canvas /]
```


___
### bkg_color (meta attribute)
The background color of the canvas. If a [**bkg_img**](#bkg_img-meta-attribute) is set, the image will be blended with this color.


**Associated Meta Attributes**: [bkg_image](#bkg_image-meta-attribute), [bkg_nineslice](#bkg_nineslice-meta-attribute), [bkg_tiled](#bkg_tiled-meta-attribute), [color](#color-meta-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `16777215`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`bg`, `bkg`, `blend`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta blend=red /]
```

```
[meta bkg=#D73C2C /]
```

```
[meta img=my_background_sprite, blend=$884CEA /]
```


___
### bkg_image (meta attribute)
The background image of the canvas. The image will be stretched to fit the canvas by default (see [**bkg_tiled**](#bkg_tiled-meta-attribute) and [**bkg_nineslice**](#bkg_nineslice-meta-attribute) for alternatives).


**Associated Meta Attributes**: [bkg_color](#bkg_color-meta-attribute), [bkg_nineslice](#bkg_nineslice-meta-attribute), [bkg_tiled](#bkg_tiled-meta-attribute), [color](#color-meta-attribute)


| property | value
--- | ---
type | `SPRITE`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`img`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta img=my_background_sprite /]
```

```
[meta img=my_background_sprite, blend=pink /]
```


___
### bkg_nineslice (meta attribute)
This attribute has priority over the [**bkg_tiled**](#bkg_tiled-meta-attribute) attribute. If `true,` a 9-slice of the image will be generated and drawn over the canvas. The corner 1/3s of the image will be drawn in the respective corners of the canvas. The remaining sides (and center) of the image will be tiled.


**Associated Meta Attributes**: [bkg_color](#bkg_color-meta-attribute), [bkg_image](#bkg_image-meta-attribute), [bkg_tiled](#bkg_tiled-meta-attribute), [color](#color-meta-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`9slice`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta 9slice=my_nineslice_graphic, blend=red /]
```


___
### bkg_tiled (meta attribute)
If `false`, the background image will be stretched to fit the size of the canvas. If true, the image will be tiled horizontally and vertically across the canvas.


**Associated Meta Attributes**: [bkg_color](#bkg_color-meta-attribute), [bkg_image](#bkg_image-meta-attribute), [bkg_nineslice](#bkg_nineslice-meta-attribute), [color](#color-meta-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `1`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`tiled`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta tiled=true, img=my_background_sprite, blend=yellow /]
```


___
### blocking (meta attribute)
If true, a full-screen rectangle is drawn beneath the canvas. This "blocks" user input and interaction with all canvases beneath.


**Associated Meta Attributes**: [blocking_alpha](#blocking_alpha-meta-attribute), [blocking_color](#blocking_color-meta-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`coverup`, `blocking`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta blocking=true /]
```

```
[meta coverup=true /]
```

```
[meta coverup=false /]
```


___
### blocking_alpha (meta attribute)
If [**blocking**](#blocking-meta-attribute) is enabled, this attribute is the transparency of the full-screen rectangle. This attribute accepts a number of 0-100, where 0 is invisible and 100 is fully-visible.


**Associated Meta Attributes**: [blocking](#blocking-meta-attribute), [blocking_color](#blocking_color-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `60`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`coverup-alpha`, `coverupAlpha`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta coverup-alpha=50 /]
```

```
[meta coverup-alpha=100 /]
```

```
[meta coverupAlpha=10 /]
```


___
### blocking_color (meta attribute)
If [**blocking**](#blocking-meta-attribute) is enabled, this attribute is the color of the full-screen rectangle.


**Associated Meta Attributes**: [blocking](#blocking-meta-attribute), [blocking_alpha](#blocking_alpha-meta-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `2696481`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`coverup-color`, `coverup-colour`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta coverup-color=black /]
```

```
[meta coverup-colour=c_black /]
```


___
### caption (meta attribute)
The string of text in the caption bar of the canvas.


**Associated Meta Attributes**: [caption_bkg_color](#caption_bkg_color-meta-attribute), [caption_bkg_color_hover](#caption_bkg_color_hover-meta-attribute), [caption_color](#caption_color-meta-attribute), [caption_color_hover](#caption_color_hover-meta-attribute), [caption_halign](#caption_halign-meta-attribute), [caption_height](#caption_height-meta-attribute), [close_button](#close_button-meta-attribute), [draggable](#draggable-meta-attribute), [icon](#icon-meta-attribute)


| property | value
--- | ---
type | `STRING`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`caption`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, caption="I'm a caption!" /]
```


___
### caption_bkg_color (meta attribute)
The background color of the canvas's caption bar.


**Associated Meta Attributes**: [caption](#caption-meta-attribute), [caption_bkg_color_hover](#caption_bkg_color_hover-meta-attribute), [caption_color](#caption_color-meta-attribute), [caption_color_hover](#caption_color_hover-meta-attribute), [caption_halign](#caption_halign-meta-attribute), [caption_height](#caption_height-meta-attribute), [close_button](#close_button-meta-attribute), [draggable](#draggable-meta-attribute), [icon](#icon-meta-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `6179124`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`caption-bg`, `caption-bkg`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, caption-bg=red-5, caption="Red Background", caption-height=48 /]
```

```
[meta, caption-bg=#E74C3C, caption="Red Background", caption-height=48 /]
```

```
[meta, caption-bg=$3C4CE7, caption="Red Background", caption-height=48 /]
```


___
### caption_bkg_color_hover (meta attribute)
The background color of the canvas's caption bar when the canvas caption bar is being hovered over.


**Associated Meta Attributes**: [caption](#caption-meta-attribute), [caption_bkg_color](#caption_bkg_color-meta-attribute), [caption_color](#caption_color-meta-attribute), [caption_color_hover](#caption_color_hover-meta-attribute), [caption_halign](#caption_halign-meta-attribute), [caption_height](#caption_height-meta-attribute), [close_button](#close_button-meta-attribute), [draggable](#draggable-meta-attribute), [icon](#icon-meta-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `5258796`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`caption-bg-hover`, `caption-bkg-hover`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, caption-bg=red-5, caption-bg-hover=red-7, caption="Red->Maroon Background", caption-height=48 /]
```

```
[meta, caption-bg=#E74C3C, caption-bg-hover=#C0392B, caption="Red->Maroon Background", caption-height=48 /]
```

```
[meta, caption-bg=$3C4CE7, caption-bg-hover=$2B39C0, caption="Red->Maroon Background", caption-height=48 /]
```


___
### caption_color (meta attribute)
The [**caption**](#caption-meta-attribute) text color in the canvas's caption bar.


**Associated Meta Attributes**: [caption](#caption-meta-attribute), [caption_bkg_color](#caption_bkg_color-meta-attribute), [caption_bkg_color_hover](#caption_bkg_color_hover-meta-attribute), [caption_color_hover](#caption_color_hover-meta-attribute), [caption_halign](#caption_halign-meta-attribute), [caption_height](#caption_height-meta-attribute), [close_button](#close_button-meta-attribute), [draggable](#draggable-meta-attribute), [icon](#icon-meta-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `15855852`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`caption-color`, `caption-colour`, `caption-c`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, caption-color=green-4, caption="Green Caption Text", caption-height=32 /]
```

```
[meta, caption-colour=#2ECC71, caption="Green Caption Text", caption-height=32 /]
```

```
[meta, caption-color=$71CC2E, caption="Green Caption Text", caption-height=32 /]
```


___
### caption_color_hover (meta attribute)
The [**caption**](#caption-meta-attribute) text color in the canvas's caption bar when the canvas caption bar is being hovered over.


**Associated Meta Attributes**: [caption](#caption-meta-attribute), [caption_bkg_color](#caption_bkg_color-meta-attribute), [caption_bkg_color_hover](#caption_bkg_color_hover-meta-attribute), [caption_color](#caption_color-meta-attribute), [caption_halign](#caption_halign-meta-attribute), [caption_height](#caption_height-meta-attribute), [close_button](#close_button-meta-attribute), [draggable](#draggable-meta-attribute), [icon](#icon-meta-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `16777215`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`caption-color-hover`, `caption-colour-hover`, `caption-c-hover`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, caption-color=green-4, caption-color-hover=green-6, caption="Green->Emerald Caption Text", caption-height=32 /]
```

```
[meta, caption-color=#2ECC71, caption-colour-hover=#27AE60, caption="Green->Emerald Caption Text", caption-height=32 /]
```

```
[meta, caption-colour=$71CC2E, caption-color-hover=$60AE27, caption="Green->Emerald Caption Text", caption-height=32 /]
```


___
### caption_halign (meta attribute)
The horizontal alignment of the [**caption**](#caption-meta-attribute) text within the canvas's caption bar, accounting for a present [**close_button**](#close_button-meta-attribute) and/or [**icon**](#icon-meta-attribute).


**Associated Meta Attributes**: [caption](#caption-meta-attribute), [caption_bkg_color](#caption_bkg_color-meta-attribute), [caption_bkg_color_hover](#caption_bkg_color_hover-meta-attribute), [caption_color](#caption_color-meta-attribute), [caption_color_hover](#caption_color_hover-meta-attribute), [caption_height](#caption_height-meta-attribute), [close_button](#close_button-meta-attribute), [draggable](#draggable-meta-attribute), [icon](#icon-meta-attribute)


| property | value
--- | ---
type | `ALIGN`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`caption-halign`, `caption-ha`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, caption-halign=fa_left, caption="left-aligned caption" /]
```

```
[meta, caption-halign=fa_center, caption="center-aligned caption" /]
```

```
[meta, caption-halign=fa_right, caption="right-aligned caption" /]
```


___
### caption_height (meta attribute)
The height, in pixels, of the caption bar of the canvas.


**Associated Meta Attributes**: [caption](#caption-meta-attribute), [caption_bkg_color](#caption_bkg_color-meta-attribute), [caption_bkg_color_hover](#caption_bkg_color_hover-meta-attribute), [caption_color](#caption_color-meta-attribute), [caption_color_hover](#caption_color_hover-meta-attribute), [caption_halign](#caption_halign-meta-attribute), [close_button](#close_button-meta-attribute), [draggable](#draggable-meta-attribute), [icon](#icon-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `20`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`drag-h`, `drag-height`, `caption-h`, `caption-height`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, caption-h=64, caption="Tall caption area!", draggable=true /]
```

```
[meta, drag-height=24, caption="Short caption area!", draggable=true /]
```


___
### click_out_close (meta attribute)
If `true`, the canvas will be closed automatically when a click occurs outside of the canvas bounds. Note that such a click will not close a canvas blocked by another canvas with [**blocking**](#blocking-meta-attribute) enabled.


**Associated Meta Attributes**: [escape_to_close](#escape_to_close-meta-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`click-out-close`, `fragile`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta fragile=true /]
```

```
[meta click-out-close=true /]
```


___
### close_button (meta attribute)
If `true`, a close button will appear in the right of the caption bar. Clicking this button will allow a canvas to be closed.


**Associated Meta Attributes**: [caption](#caption-meta-attribute), [caption_bkg_color](#caption_bkg_color-meta-attribute), [caption_bkg_color_hover](#caption_bkg_color_hover-meta-attribute), [caption_color](#caption_color-meta-attribute), [caption_color_hover](#caption_color_hover-meta-attribute), [caption_halign](#caption_halign-meta-attribute), [caption_height](#caption_height-meta-attribute), [draggable](#draggable-meta-attribute), [icon](#icon-meta-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `0`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`closeButton`, `close-btn`, `close-button`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, close-button=true, caption="I have a close button to my right!", caption-bg=red, caption-height=32 /]
```

```
[meta, close-btn=true, caption="Close Me -->", caption-height=48 /]
```


___
### close_ease_x (meta attribute)
When a canvas is being closed, it will ease out horizontally using this easing function.

You can pass three arguments to this attribute (separated by colons `:`) as a way to merge this attribute with [**close_ease_x_dur**](#close_ease_x_dur-meta-attribute) and [**close_ease_x_offset**](#close_ease_x_offset-meta-attribute).


**Associated Meta Attributes**: [close_ease_x_dur](#close_ease_x_dur-meta-attribute), [close_ease_x_offset](#close_ease_x_offset-meta-attribute), [close_ease_y](#close_ease_y-meta-attribute), [close_ease_y_dur](#close_ease_y_dur-meta-attribute), [close_ease_y_offset](#close_ease_y_offset-meta-attribute), [open_ease_x](#open_ease_x-meta-attribute), [open_ease_x_dur](#open_ease_x_dur-meta-attribute), [open_ease_x_offset](#open_ease_x_offset-meta-attribute), [open_ease_y](#open_ease_y-meta-attribute), [open_ease_y_dur](#open_ease_y_dur-meta-attribute), [open_ease_y_offset](#open_ease_y_offset-meta-attribute)


| property | value
--- | ---
type | `EASE`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`closeEaseX`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, closeEaseX=InQuart:60:225 /]
```

```
[meta, closeEaseX=OutQuart, closeEaseXOffset=25, closeEaseXDuration=room_speed /]
```

```
[meta, closeEaseX=InOutQuart:200:100 /]
```

```
[meta, closeEaseX=InQuint, closeEaseXOffset=64, closeEaseXDuration=150 /]
```


___
### close_ease_x_dur (meta attribute)
When a canvas is being closed, it will ease out horizontally over this many number of gamesteps. Use this attribute in conjunction with [**close_ease_x**](#close_ease_x-meta-attribute) and [**close_ease_x_offset**](#close_ease_x_offset-meta-attribute).


**Associated Meta Attributes**: [close_ease_x](#close_ease_x-meta-attribute), [close_ease_x_offset](#close_ease_x_offset-meta-attribute), [close_ease_y](#close_ease_y-meta-attribute), [close_ease_y_dur](#close_ease_y_dur-meta-attribute), [close_ease_y_offset](#close_ease_y_offset-meta-attribute), [open_ease_x](#open_ease_x-meta-attribute), [open_ease_x_dur](#open_ease_x_dur-meta-attribute), [open_ease_x_offset](#open_ease_x_offset-meta-attribute), [open_ease_y](#open_ease_y-meta-attribute), [open_ease_y_dur](#open_ease_y_dur-meta-attribute), [open_ease_y_offset](#open_ease_y_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `60`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`closeEaseXDuration`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, closeEaseX=OutElastic:60:225 /]
```

```
[meta, closeEaseX=InOutElastic, closeEaseXOffset=25, closeEaseXDuration=room_speed /]
```

```
[meta, closeEaseX=InBack:200:100 /]
```

```
[meta, closeEaseX=OutBack, closeEaseXOffset=64, closeEaseXDuration=150 /]
```


___
### close_ease_x_offset (meta attribute)
When a canvas is being closed, it will ease out horizontally over this many number of pixels. Use this attribute in conjunction with [**close_ease_x**](#close_ease_x-meta-attribute) and [**close_ease_x_dur**](#close_ease_x_dur-meta-attribute).


**Associated Meta Attributes**: [close_ease_x](#close_ease_x-meta-attribute), [close_ease_x_dur](#close_ease_x_dur-meta-attribute), [close_ease_y](#close_ease_y-meta-attribute), [close_ease_y_dur](#close_ease_y_dur-meta-attribute), [close_ease_y_offset](#close_ease_y_offset-meta-attribute), [open_ease_x](#open_ease_x-meta-attribute), [open_ease_x_dur](#open_ease_x_dur-meta-attribute), [open_ease_x_offset](#open_ease_x_offset-meta-attribute), [open_ease_y](#open_ease_y-meta-attribute), [open_ease_y_dur](#open_ease_y_dur-meta-attribute), [open_ease_y_offset](#open_ease_y_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`closeEaseXOffset`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, closeEaseX=OutCubic:50:100 /]
```

```
[meta, closeEaseX=linear, closeEaseXOffset=50, closeEaseXDuration=25 /]
```

```
[meta, closeEaseX=InOutBack:200:room_speed /]
```

```
[meta, closeEaseX=linear, closeEaseXOffset=32, closeEaseXDuration=room_speed /]
```


___
### close_ease_y (meta attribute)
When a canvas is being closed, it will ease out vertically using this easing function.

You can pass three arguments to this attribute (separated by colons `:`) as a way to merge this attribute with [**close_ease_y_dur**](#close_ease_y_dur-meta-attribute) and [**close_ease_y_offset**](#close_ease_y_offset-meta-attribute).

| property | value
--- | ---
type | `EASE`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`closeEaseY`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, closeEaseY=OutQuint:60:225 /]
```

```
[meta, closeEaseY=InOutQuint, closeEaseYOffset=25, closeEaseYDuration=room_speed /]
```

```
[meta, closeEaseY=InSine:200:100 /]
```

```
[meta, closeEaseY=OutSine, closeEaseYOffset=64, closeEaseYDuration=150 /]
```


___
### close_ease_y_dur (meta attribute)
When a canvas is being closed, it will ease out vertically over this many number of gamesteps. Use this attribute in conjunction with [**close_ease_y**](#close_ease_y-meta-attribute) and [**close_ease_y_offset**](#close_ease_y_offset-meta-attribute).


**Associated Meta Attributes**: [close_ease_x](#close_ease_x-meta-attribute), [close_ease_x_dur](#close_ease_x_dur-meta-attribute), [close_ease_x_offset](#close_ease_x_offset-meta-attribute), [close_ease_y](#close_ease_y-meta-attribute), [close_ease_y_offset](#close_ease_y_offset-meta-attribute), [open_ease_x](#open_ease_x-meta-attribute), [open_ease_x_dur](#open_ease_x_dur-meta-attribute), [open_ease_x_offset](#open_ease_x_offset-meta-attribute), [open_ease_y](#open_ease_y-meta-attribute), [open_ease_y_dur](#open_ease_y_dur-meta-attribute), [open_ease_y_offset](#open_ease_y_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `60`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`closeEaseYDuration`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, closeEaseY=InOutBack:60:225 /]
```

```
[meta, closeEaseY=InBounce, closeEaseYOffset=25, closeEaseYDuration=room_speed /]
```

```
[meta, closeEaseY=OutBounce:200:100 /]
```

```
[meta, closeEaseY=InOutBounce, closeEaseYOffset=64, closeEaseYDuration=150 /]
```


___
### close_ease_y_offset (meta attribute)
When a canvas is being closed, it will ease out vertically over this many number of pixels. Use this attribute in conjunction with [**close_ease_y**](#close_ease_y-meta-attribute) and [**close_ease_y_dur**](#close_ease_y_dur-meta-attribute).


**Associated Meta Attributes**: [close_ease_x](#close_ease_x-meta-attribute), [close_ease_x_dur](#close_ease_x_dur-meta-attribute), [close_ease_x_offset](#close_ease_x_offset-meta-attribute), [close_ease_y](#close_ease_y-meta-attribute), [close_ease_y_dur](#close_ease_y_dur-meta-attribute), [open_ease_x](#open_ease_x-meta-attribute), [open_ease_x_dur](#open_ease_x_dur-meta-attribute), [open_ease_x_offset](#open_ease_x_offset-meta-attribute), [open_ease_y](#open_ease_y-meta-attribute), [open_ease_y_dur](#open_ease_y_dur-meta-attribute), [open_ease_y_offset](#open_ease_y_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`closeEaseYOffset`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, closeEaseY=linear:50:room_speed /]
```

```
[meta, closeEaseY=linear, closeEaseYOffset=50, closeEaseYDuration=25 /]
```

```
[meta, closeEaseY=OutCubic:200:100 /]
```

```
[meta, closeEaseY=InOutElastic, closeEaseYOffset=32, closeEaseYDuration=room_speed /]
```


___
### color (meta attribute)
The default color of the text on the canvas.


**Associated Meta Attributes**: [bkg_color](#bkg_color-meta-attribute), [bkg_image](#bkg_image-meta-attribute), [bkg_nineslice](#bkg_nineslice-meta-attribute), [bkg_tiled](#bkg_tiled-meta-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `2696481`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`color`, `col`, `c`, `colour`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta color=white /]
```

```
[meta colour=#ECF0F1 /]
```

```
[meta color=$F1F0EC /]
```


___
### delay (meta attribute)
The number of gamesteps before the canvas is visible and interactive after being created.


**Associated Meta Attributes**: [fadein](#fadein-meta-attribute), [fadeout](#fadeout-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`delay`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta delay=30 /]
```

```
[meta delay=0 /]
```

```
[meta delay=room_speed /]
```


___
### depth (meta attribute)
The depth of the canvas (not to be confused with GameMaker's built-in `depth`), is used for rendering order and click priority. The draw order goes from highest number to lowest number, so canvases with lower depths will be drawn on top of canvaes with higher depths.


**Associated Meta Attributes**: [halign](#halign-meta-attribute), [height](#height-meta-attribute), [valign](#valign-meta-attribute), [width](#width-meta-attribute), [xpos](#xpos-meta-attribute), [x_offset](#x_offset-meta-attribute), [ypos](#ypos-meta-attribute), [y_offset](#y_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`depth`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta depth=-50 /]
```

```
[meta depth=50 /]
```


___
### draggable (meta attribute)
If `true`, the canvas will be marked as draggable. This allows the canvas to be dragged around the screen via clicking and holding within the caption bar. Note that each gamestep the canvas is dragged, an optional [**func_on_drag**](#func_on_drag-meta-attribute) function will be called. A [**caption**](#caption-meta-attribute) must be provided.


**Associated Meta Attributes**: [caption](#caption-meta-attribute), [caption_bkg_color](#caption_bkg_color-meta-attribute), [caption_bkg_color_hover](#caption_bkg_color_hover-meta-attribute), [caption_color](#caption_color-meta-attribute), [caption_color_hover](#caption_color_hover-meta-attribute), [caption_halign](#caption_halign-meta-attribute), [caption_height](#caption_height-meta-attribute), [close_button](#close_button-meta-attribute), [icon](#icon-meta-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`draggable`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, caption="Drag me!", draggable=true /]
```


___
### escape_to_close (meta attribute)
If `true`, this canvas can be closed by pressing the escape key on the keyboard. Note that such a button press will not close a canvas blocked by another canvas with [**blocking**](#blocking-meta-attribute) enabled.


**Associated Meta Attributes**: [click_out_close](#click_out_close-meta-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `0`
isMeta | `true`
since | `0.9.1`
deprecated | `false`

#### Syntax

`escape-close`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, escape-close=true /]
```


___
### fadein (meta attribute)
The number of gamesteps before the canvas becomes fully visible after being created. The alpha will fade from 0% to 100% after the course of this many number of gamesteps.


**Associated Meta Attributes**: [delay](#delay-meta-attribute), [fadeout](#fadeout-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`fade-in`, `fadein`, `fadeIn`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta fadein=30 /]
```

```
[meta fade-in=0 /]
```

```
[meta fadeIn=room_speed /]
```


___
### fadeout (meta attribute)
The number of gamesteps before the canvas becomes fully invisible after being marked as "closed". The alpha will fade from 100% to 0% after the course of this many number of gamesteps. When the alpha reaches 0%, the canvas will enter its destruction state and be freed from memory.


**Associated Meta Attributes**: [delay](#delay-meta-attribute), [fadein](#fadein-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`fade-out`, `fadeout`, `fadeOut`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta fadeout=30 /]
```

```
[meta fade-out=0 /]
```

```
[meta fadeOut=room_speed /]
```


___
### fast_refresh (meta attribute)
If `true`, when a window resize occurs, the opening eases and fade-in will be skipped over. Note that this will only work if [**resize_recreate**](#resize_recreate-meta-attribute) is set to `true` because such canvases are reconstructed when a resize is registered.


**Associated Meta Attributes**: [resize_recreate](#resize_recreate-meta-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `1`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`fast-refresh`, `fast-resize`, `fastrefresh`, `fastRefresh`, `fastResize`, `fastresize`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, fast-refresh=true /]
```

```
[meta, fastRefresh=false /]
```

```
[meta, fastresize=true /]
```


___
### func_on_close (meta attribute)
The function to call the last gamestep the canvas exists. The scope of the function call is canvas-level.


**Associated Meta Attributes**: [func_on_drag](#func_on_drag-meta-attribute), [func_on_draw](#func_on_draw-meta-attribute), [func_on_open](#func_on_open-meta-attribute), [func_on_resize](#func_on_resize-meta-attribute), [func_on_step](#func_on_step-meta-attribute), [func_on_update](#func_on_update-meta-attribute)


| property | value
--- | ---
type | `FUNCTION`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`onclose`, `onClose`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta onClose=my_close_function /]
```


___
### func_on_drag (meta attribute)
If the canvas is marked as [**draggable**](#draggable-meta-attribute), this function will be called each gamestep the canvas is dragged around the room. The scope of the function call is canvas-level.


**Associated Meta Attributes**: [func_on_close](#func_on_close-meta-attribute), [func_on_draw](#func_on_draw-meta-attribute), [func_on_open](#func_on_open-meta-attribute), [func_on_resize](#func_on_resize-meta-attribute), [func_on_step](#func_on_step-meta-attribute), [func_on_update](#func_on_update-meta-attribute)


| property | value
--- | ---
type | `FUNCTION`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`ondrag`, `onDrag`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta onDrag=my_drag_function /]
```


___
### func_on_draw (meta attribute)
This function is called at a canvas-level scope each gamestep the canvas exists. It's called immediately after the canvas is rendered, so it can be used to draw directly *ontop* of the canvas. You can use `x1`, `y1`, `x2`, and `y2` to reference the bounding coordinates, respectively, of the canvas.


**Associated Meta Attributes**: [func_on_close](#func_on_close-meta-attribute), [func_on_drag](#func_on_drag-meta-attribute), [func_on_open](#func_on_open-meta-attribute), [func_on_resize](#func_on_resize-meta-attribute), [func_on_step](#func_on_step-meta-attribute), [func_on_update](#func_on_update-meta-attribute)


| property | value
--- | ---
type | `FUNCTION`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`ondraw`, `onDraw`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta onDraw=my_draw_function /]
```


___
### func_on_open (meta attribute)
The function to call the first gamestep the canvas is created. The scope of the function call is canvas-level.


**Associated Meta Attributes**: [func_on_close](#func_on_close-meta-attribute), [func_on_drag](#func_on_drag-meta-attribute), [func_on_draw](#func_on_draw-meta-attribute), [func_on_resize](#func_on_resize-meta-attribute), [func_on_step](#func_on_step-meta-attribute), [func_on_update](#func_on_update-meta-attribute)


| property | value
--- | ---
type | `FUNCTION`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`onload`, `onLoad`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta onLoad=my_create_function /]
```


___
### func_on_resize (meta attribute)
The function to call each gamestep the game window is resized. The scope of the function call is canvas-level. Note that Shampoo *must* be initialized with resize-handling enabled.


**Associated Meta Attributes**: [func_on_close](#func_on_close-meta-attribute), [func_on_drag](#func_on_drag-meta-attribute), [func_on_draw](#func_on_draw-meta-attribute), [func_on_open](#func_on_open-meta-attribute), [func_on_step](#func_on_step-meta-attribute), [func_on_update](#func_on_update-meta-attribute)


| property | value
--- | ---
type | `FUNCTION`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`onresize`, `onResize`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta onResize=my_resize_function /]
```


___
### func_on_step (meta attribute)
The function to call each gamestep the canvas exists. The scope of the function call is canvas-level.


**Associated Meta Attributes**: [func_on_close](#func_on_close-meta-attribute), [func_on_drag](#func_on_drag-meta-attribute), [func_on_draw](#func_on_draw-meta-attribute), [func_on_open](#func_on_open-meta-attribute), [func_on_resize](#func_on_resize-meta-attribute), [func_on_update](#func_on_update-meta-attribute)


| property | value
--- | ---
type | `FUNCTION`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`onstep`, `onStep`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta onStep=my_gamestep_function /]
```


___
### func_on_update (meta attribute)
The function to call whenever an input (checkbox, toggle, radio button, textfield, and slider) is changed. This function will also be called immediately upon a canvas being created. The scope of the function call is canvas-level.


**Associated Meta Attributes**: [func_on_close](#func_on_close-meta-attribute), [func_on_drag](#func_on_drag-meta-attribute), [func_on_draw](#func_on_draw-meta-attribute), [func_on_open](#func_on_open-meta-attribute), [func_on_resize](#func_on_resize-meta-attribute), [func_on_step](#func_on_step-meta-attribute)


| property | value
--- | ---
type | `FUNCTION`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`onupdate`, `onUpdate`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta onUpdate=my_input_update_function /]
```


___
### halign (meta attribute)
The horizontal alignment of the canvas, relative to the left-x coordinate. `fa_center` shifts the canvas left by 50% the width of the canvas width. `fa_right` shifts the canvas left by 100% the width of the canvas.


**Associated Meta Attributes**: [depth](#depth-meta-attribute), [height](#height-meta-attribute), [valign](#valign-meta-attribute), [width](#width-meta-attribute), [xpos](#xpos-meta-attribute), [x_offset](#x_offset-meta-attribute), [ypos](#ypos-meta-attribute), [y_offset](#y_offset-meta-attribute)


| property | value
--- | ---
type | `ALIGN`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`ha`, `halign`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta halign=fa_left, valign=fa_top /]
```

```
[meta halign=fa_right, valign=fa_bottom /]
```


___
### height (meta attribute)
The height of the canvas, in pixels.


**Associated Meta Attributes**: [depth](#depth-meta-attribute), [halign](#halign-meta-attribute), [valign](#valign-meta-attribute), [width](#width-meta-attribute), [xpos](#xpos-meta-attribute), [x_offset](#x_offset-meta-attribute), [ypos](#ypos-meta-attribute), [y_offset](#y_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `480`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`h`, `height`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta width=200, height=200, x=1/2w, y=1/2h, halign=fa_center, valign=fa_middle /]
```


___
### icon (meta attribute)
If set to a valid sprite, such sprite will be drawn in the left of the caption bar.


**Associated Meta Attributes**: [caption](#caption-meta-attribute), [caption_bkg_color](#caption_bkg_color-meta-attribute), [caption_bkg_color_hover](#caption_bkg_color_hover-meta-attribute), [caption_color](#caption_color-meta-attribute), [caption_color_hover](#caption_color_hover-meta-attribute), [caption_halign](#caption_halign-meta-attribute), [caption_height](#caption_height-meta-attribute), [close_button](#close_button-meta-attribute), [draggable](#draggable-meta-attribute)


| property | value
--- | ---
type | `SPRITE`
defaultValue | `-1`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`icon`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, icon=my_sprite_123, caption="I have an icon to my left!", caption-bg=blue, caption-height=32 /]
```


___
### on_close_despawn (meta attribute)
Use this attribute to close other canvases the gamestep *this* canvas is closed. You can close multiple canvases, separating their names by the ampersand (`&`).


**Associated Meta Attributes**: [on_close_spawn](#on_close_spawn-meta-attribute), [on_open_despawn](#on_open_despawn-meta-attribute), [on_open_spawn](#on_open_spawn-meta-attribute)


| property | value
--- | ---
type | `CANVAS`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`closeDespawn`, `closedespawn`, `closeDespawns`, `closedespawns`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, closeDespawn=second_canvas$third_canvas /]
```

```
[meta, closeDespawns=other_canvas /]
```


___
### on_close_spawn (meta attribute)
Use this attribute to create other canvases the gamestep *this* canvas is closed. You can create multiple canvases, separating their names by the ampersand (`&`).


**Associated Meta Attributes**: [on_close_despawn](#on_close_despawn-meta-attribute), [on_open_despawn](#on_open_despawn-meta-attribute), [on_open_spawn](#on_open_spawn-meta-attribute)


| property | value
--- | ---
type | `CANVAS`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`closeSpawn`, `closespawn`, `closeSpawns`, `closespawns`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, closeSpawn=second_canvas$third_canvas /]
```

```
[meta, closespawns=other_canvas /]
```


___
### on_open_despawn (meta attribute)
Use this attribute to close other canvases the gamestep *this* canvas is created. You can close multiple canvases, separating their names by the ampersand (`&`).


**Associated Meta Attributes**: [on_close_despawn](#on_close_despawn-meta-attribute), [on_close_spawn](#on_close_spawn-meta-attribute), [on_open_spawn](#on_open_spawn-meta-attribute)


| property | value
--- | ---
type | `CANVAS`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`openDespawn`, `opendespawn`, `openDespawns`, `opendespawns`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, openDespawns=second_canvas$third_canvas /]
```

```
[meta, opendespawns=other_canvas /]
```


___
### on_open_spawn (meta attribute)
Use this attribute to create other canvases the gamestep *this* canvas is created. You can create multiple canvases, separating their names by the ampersand (`&`).


**Associated Meta Attributes**: [on_close_despawn](#on_close_despawn-meta-attribute), [on_close_spawn](#on_close_spawn-meta-attribute), [on_open_despawn](#on_open_despawn-meta-attribute)


| property | value
--- | ---
type | `CANVAS`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`openSpawn`, `openspawn`, `openSpawns`, `openspawns`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, openSpawn=second_canvas$third_canvas /]
```

```
[meta, openspawn=other_canvas /]
```


___
### open_ease_x (meta attribute)
When a canvas is created, it will ease in horizontally using this easing function.

You can pass three arguments to this attribute (separated by colons `:`) as a way to merge this attribute with [**open_ease_x_dur**](#open_ease_x_dur-meta-attribute) and [**open_ease_x_offset**](#open_ease_x_offset-meta-attribute).


**Associated Meta Attributes**: [close_ease_x](#close_ease_x-meta-attribute), [close_ease_x_dur](#close_ease_x_dur-meta-attribute), [close_ease_x_offset](#close_ease_x_offset-meta-attribute), [close_ease_y](#close_ease_y-meta-attribute), [close_ease_y_dur](#close_ease_y_dur-meta-attribute), [close_ease_y_offset](#close_ease_y_offset-meta-attribute), [open_ease_x_dur](#open_ease_x_dur-meta-attribute), [open_ease_x_offset](#open_ease_x_offset-meta-attribute), [open_ease_y](#open_ease_y-meta-attribute), [open_ease_y_dur](#open_ease_y_dur-meta-attribute), [open_ease_y_offset](#open_ease_y_offset-meta-attribute)


| property | value
--- | ---
type | `EASE`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`openEaseX`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, openEaseX=Linear:60:room_speed /]
```

```
[meta, openEaseX=Instant, openEaseXOffset=25, openEaseXDuration=50 /]
```

```
[meta, openEaseX=InQuad:200:100 /]
```

```
[meta, openEaseX=OutQuad, openEaseXOffset=64, openEaseXDuration=150 /]
```


___
### open_ease_x_dur (meta attribute)
When a canvas is created, it will ease in horizontally over this many number of gamesteps. Use this attribute in conjunction with [**open_ease_x**](#open_ease_x-meta-attribute) and [**open_ease_x_offset**](#open_ease_x_offset-meta-attribute).


**Associated Meta Attributes**: [close_ease_x](#close_ease_x-meta-attribute), [close_ease_x_dur](#close_ease_x_dur-meta-attribute), [close_ease_x_offset](#close_ease_x_offset-meta-attribute), [close_ease_y](#close_ease_y-meta-attribute), [close_ease_y_dur](#close_ease_y_dur-meta-attribute), [close_ease_y_offset](#close_ease_y_offset-meta-attribute), [open_ease_x](#open_ease_x-meta-attribute), [open_ease_x_offset](#open_ease_x_offset-meta-attribute), [open_ease_y](#open_ease_y-meta-attribute), [open_ease_y_dur](#open_ease_y_dur-meta-attribute), [open_ease_y_offset](#open_ease_y_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `60`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`openEaseXDuration`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, openEaseX=InOutSine:60:225 /]
```

```
[meta, openEaseX=InExpo, openEaseXOffset=25, openEaseXDuration=room_speed /]
```

```
[meta, openEaseX=OutExpo:200:100 /]
```

```
[meta, openEaseX=InOutExpo, openEaseXOffset=64, openEaseXDuration=150 /]
```


___
### open_ease_x_offset (meta attribute)
When a canvas is created, it will ease in horizontally over this many number of pixels. Use this attribute in conjunction with [**open_ease_x**](#open_ease_x-meta-attribute) and [**open_ease_x_dur**](#open_ease_x_dur-meta-attribute).


**Associated Meta Attributes**: [close_ease_x](#close_ease_x-meta-attribute), [close_ease_x_dur](#close_ease_x_dur-meta-attribute), [close_ease_x_offset](#close_ease_x_offset-meta-attribute), [close_ease_y](#close_ease_y-meta-attribute), [close_ease_y_dur](#close_ease_y_dur-meta-attribute), [close_ease_y_offset](#close_ease_y_offset-meta-attribute), [open_ease_x](#open_ease_x-meta-attribute), [open_ease_x_dur](#open_ease_x_dur-meta-attribute), [open_ease_y](#open_ease_y-meta-attribute), [open_ease_y_dur](#open_ease_y_dur-meta-attribute), [open_ease_y_offset](#open_ease_y_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`openEaseXOffset`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, openEaseX=Linear:50:225 /]
```

```
[meta, openEaseX=InSine, openEaseXOffset=25, openEaseXDuration=room_speed /]
```

```
[meta, openEaseX=OutBounce:100:100 /]
```

```
[meta, openEaseX=OutExpo, openEaseXOffset=32, openEaseXDuration=50 /]
```


___
### open_ease_y (meta attribute)
When a canvas is created, it will ease in vertically using this easing function.

You can pass three arguments to this attribute (separated by colons `:`) as a way to merge this attribute with [**open_ease_y_dur**](#open_ease_y_dur-meta-attribute) and [**open_ease_y_offset**](#open_ease_y_offset-meta-attribute).


**Associated Meta Attributes**: [close_ease_x](#close_ease_x-meta-attribute), [close_ease_x_dur](#close_ease_x_dur-meta-attribute), [close_ease_x_offset](#close_ease_x_offset-meta-attribute), [close_ease_y](#close_ease_y-meta-attribute), [close_ease_y_dur](#close_ease_y_dur-meta-attribute), [close_ease_y_offset](#close_ease_y_offset-meta-attribute), [open_ease_x](#open_ease_x-meta-attribute), [open_ease_x_dur](#open_ease_x_dur-meta-attribute), [open_ease_x_offset](#open_ease_x_offset-meta-attribute), [open_ease_y_dur](#open_ease_y_dur-meta-attribute), [open_ease_y_offset](#open_ease_y_offset-meta-attribute)


| property | value
--- | ---
type | `EASE`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`openEaseY`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, openEaseY=InOutQuad:60:225 /]
```

```
[meta, openEaseY=InCubic, openEaseYOffset=25, openEaseYDuration=room_speed /]
```

```
[meta, openEaseY=OutCubic:200:100 /]
```

```
[meta, openEaseY=InOutCubic, openEaseYOffset=64, openEaseYDuration=150 /]
```


___
### open_ease_y_dur (meta attribute)
When a canvas is created, it will ease in vertically over this many number of gamesteps. Use this attribute in conjunction with [**open_ease_y**](#open_ease_y-meta-attribute) and [**open_ease_y_offset**](#open_ease_y_offset-meta-attribute).


**Associated Meta Attributes**: [close_ease_x](#close_ease_x-meta-attribute), [close_ease_x_dur](#close_ease_x_dur-meta-attribute), [close_ease_x_offset](#close_ease_x_offset-meta-attribute), [close_ease_y](#close_ease_y-meta-attribute), [close_ease_y_dur](#close_ease_y_dur-meta-attribute), [close_ease_y_offset](#close_ease_y_offset-meta-attribute), [open_ease_x](#open_ease_x-meta-attribute), [open_ease_x_dur](#open_ease_x_dur-meta-attribute), [open_ease_x_offset](#open_ease_x_offset-meta-attribute), [open_ease_y](#open_ease_y-meta-attribute), [open_ease_y_offset](#open_ease_y_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `60`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`openEaseYDuration`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, openEaseX=InCirc:60:225 /]
```

```
[meta, openEaseX=OutCirc, openEaseXOffset=25, openEaseXDuration=room_speed /]
```

```
[meta, openEaseX=InOutCirc:200:100 /]
```

```
[meta, openEaseX=InElastic, openEaseXOffset=64, openEaseXDuration=150 /]
```


___
### open_ease_y_offset (meta attribute)
When a canvas is created, it will ease in vertically over this many number of pixels. Use this attribute in conjunction with [**open_ease_y**](#open_ease_y-meta-attribute) and [**open_ease_y_dur**](#open_ease_y_dur-meta-attribute).


**Associated Meta Attributes**: [close_ease_x](#close_ease_x-meta-attribute), [close_ease_x_dur](#close_ease_x_dur-meta-attribute), [close_ease_x_offset](#close_ease_x_offset-meta-attribute), [close_ease_y](#close_ease_y-meta-attribute), [close_ease_y_dur](#close_ease_y_dur-meta-attribute), [close_ease_y_offset](#close_ease_y_offset-meta-attribute), [open_ease_x](#open_ease_x-meta-attribute), [open_ease_x_dur](#open_ease_x_dur-meta-attribute), [open_ease_x_offset](#open_ease_x_offset-meta-attribute), [open_ease_y](#open_ease_y-meta-attribute), [open_ease_y_dur](#open_ease_y_dur-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`openEaseYOffset`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, openEaseY=OutQuint:50:225 /]
```

```
[meta, openEaseY=InSine, openEaseYOffset=25, openEaseYDuration=room_speed /]
```

```
[meta, openEaseY=InOutBack:100:100 /]
```

```
[meta, openEaseY=InOutBounce, openEaseYOffset=32, openEaseYDuration=50 /]
```


___
### pad (meta attribute)
The padding, in pixels, between canvas content and border of canvas. The higher the number, the thicker the canvas "outline", so to speak.


**Associated Meta Attributes**: [radius](#radius-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `16`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`pad`, `padding`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta pad=10 /]
```

```
[meta padding=25 /]
```


___
### placeholder_color (meta attribute)
The color of placeholder text in textfields.


**Associated Meta Attributes**: [placeholder_color_focus](#placeholder_color_focus-meta-attribute), [ticker_color](#ticker_color-meta-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `8222060`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`placeholder-color`, `placeholder-colour`, `placeholder-c`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta placeholder-color=pink-6 /]
```

```
[meta placeholder-colour=#EA4C88 /]
```

```
[meta placeholder-color=$884CEA /]
```


___
### placeholder_color_focus (meta attribute)
The color of placeholder text in textfields, while the textfield is in focus.


**Associated Meta Attributes**: [placeholder_color](#placeholder_color-meta-attribute), [ticker_color](#ticker_color-meta-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `12167836`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`placeholder-color-focus`, `placeholder-colour-focus`, `placeholder-c-focus`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta placeholder-color=purple-3, placeholder-color-focus=purple-5 /]
```

```
[meta placeholder-colour-focus=#8E44AD /]
```

```
[meta placeholder-color-focus=$AD448E /]
```


___
### radius (meta attribute)
How rounded the corners of the canvas are, in pixels. The higher the number, the more curved the canvas corners are. If a [**bkg_img**](#bkg_img-meta-attribute) if provided, there will be no radius.


**Associated Meta Attributes**: [pad](#pad-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `32`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`rad`, `radius`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta radius=20 /]
```

```
[meta rad=32 /]
```


___
### resize_recreate (meta attribute)
If `true`, the canvas will be reconstructed when a window resize is detected. This is enabled by default, making canvases **volatile**. Note that Shampoo *must* be initialized with resize-handling enabled.


**Associated Meta Attributes**: [fast_refresh](#fast_refresh-meta-attribute)


| property | value
--- | ---
type | `BOOL`
defaultValue | `1`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`recreateOnResize`, `resizeRecreate`, `resize-refresh`, `recreate-resize`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, recreateOnResize=true /]
```

```
[meta, resize-refresh=true /]
```

```
[meta, recreate-resize=false /]
```


___
### shadow_alpha (meta attribute)
The transparency of the canvas shadow. This attribute accepts a number of 0-100, where 0 is invisible and 100 is fully-visible.


**Associated Meta Attributes**: [shadow_color](#shadow_color-meta-attribute), [shadow_radius](#shadow_radius-meta-attribute), [shadow_xoffset](#shadow_xoffset-meta-attribute), [shadow_yoffset](#shadow_yoffset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `50`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`shadow-alpha`, `shadowAlpha`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta shadow-alpha=75, shadow-color=black /]
```

```
[meta shadowAlpha=100 /]
```


___
### shadow_color (meta attribute)
The color of the canvas shadow.


**Associated Meta Attributes**: [shadow_alpha](#shadow_alpha-meta-attribute), [shadow_radius](#shadow_radius-meta-attribute), [shadow_xoffset](#shadow_xoffset-meta-attribute), [shadow_yoffset](#shadow_yoffset-meta-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `2696481`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`shadow-c`, `shadow-color`, `shadow-colour`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta shadow-color=orange /]
```

```
[meta shadow-colour=#FF8CC8 /]
```

```
[meta shadow-c=c_yellow /]
```


___
### shadow_radius (meta attribute)
The spread of the shadow, in pixels. The shadow radius is how many pixels the shadow extends in all directions outside of the canvas bounds.


**Associated Meta Attributes**: [shadow_alpha](#shadow_alpha-meta-attribute), [shadow_color](#shadow_color-meta-attribute), [shadow_xoffset](#shadow_xoffset-meta-attribute), [shadow_yoffset](#shadow_yoffset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `64`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`shadow-rad`, `shadow-radius`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta shadow-rad=50 /]
```

```
[meta shadow-radius=100 /]
```


___
### shadow_xoffset (meta attribute)
The horizontal offset, in pixels, of the canvas shadow.


**Associated Meta Attributes**: [shadow_alpha](#shadow_alpha-meta-attribute), [shadow_color](#shadow_color-meta-attribute), [shadow_radius](#shadow_radius-meta-attribute), [shadow_yoffset](#shadow_yoffset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`shadowX`, `shadow-x`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta shadowX=50, shadowY=50, shadow-color=green /]
```

```
[meta shadowX=-25, shadow-y=-50, shadow-color=yellow /]
```


___
### shadow_yoffset (meta attribute)
The vertical offset, in pixels, of the canvas shadow.


**Associated Meta Attributes**: [shadow_alpha](#shadow_alpha-meta-attribute), [shadow_color](#shadow_color-meta-attribute), [shadow_radius](#shadow_radius-meta-attribute), [shadow_xoffset](#shadow_xoffset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `24`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`shadowY`, `shadow-y`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta shadowY=50, shadowX=50, shadow-color=black /]
```

```
[meta shadowY=-25, shadow-x=-50, shadow-color=blue /]
```


___
### sound_close (meta attribute)
The sound that plays the final gamestep the canvas exists.


**Associated Meta Attributes**: [sound_open](#sound_open-meta-attribute)


| property | value
--- | ---
type | `SOUND`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`sound-close`, `sndClose`, `soundClose`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta sndClose=my_close_sfx /]
```

```
[meta sound-close=my_close_sfx /]
```


___
### sound_open (meta attribute)
The sound that plays the first gamestep the canvas is created.


**Associated Meta Attributes**: [sound_close](#sound_close-meta-attribute)


| property | value
--- | ---
type | `SOUND`
defaultValue | `undefined`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`sound-open`, `sndOpen`, `soundOpen`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta sndOpen=my_open_sfx /]
```

```
[meta sound-open=my_open_sfx /]
```


___
### steal_focus (meta attribute)
If `true`, this canvas, when created, will steal input priority over existing canvases. This would be useful if you want to design sequential menus that can be navigated with only keyboard key/controller input.

| property | value
--- | ---
type | `BOOL`
defaultValue | `0`
isMeta | `true`
since | `0.9.1`
deprecated | `false`

#### Syntax

`steal-focus`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, steal-focus=true /]
```


___
### ticker_color (meta attribute)
The color of the text input flashing ticker.


**Associated Meta Attributes**: [placeholder_color](#placeholder_color-meta-attribute), [placeholder_color_focus](#placeholder_color_focus-meta-attribute)


| property | value
--- | ---
type | `COLOR`
defaultValue | `4209204`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`ticker-color`, `ticker-c`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta ticker-color=blue-8 /]
```

```
[meta ticker-color=blue /]
```

```
[meta ticker-c=$DB9834 /]
```


___
### valign (meta attribute)
The vertical alignment of the canvas, relative to the top-y coordinate. `fa_middle` shifts the canvas up by 50% the height of the canvas width. `fa_bottom` shifts the canvas up by 100% the height of the canvas.


**Associated Meta Attributes**: [depth](#depth-meta-attribute), [halign](#halign-meta-attribute), [height](#height-meta-attribute), [width](#width-meta-attribute), [xpos](#xpos-meta-attribute), [x_offset](#x_offset-meta-attribute), [ypos](#ypos-meta-attribute), [y_offset](#y_offset-meta-attribute)


| property | value
--- | ---
type | `ALIGN`
defaultValue | `0`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`va`, `valign`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta halign=fa_left, valign=fa_top /]
```

```
[meta halign=fa_right, valign=fa_bottom /]
```


___
### version (meta attribute)
For compatibility purposes and/or future-proofing, you can define which version the canvas is.

| property | value
--- | ---
type | `STRING`
defaultValue | `0.9.3`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`v`, `version`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta version="1.0" /]
```

```
[meta version="5th Anniversary Re-release" /]
```


___
### width (meta attribute)
The width of the canvas, in pixels.


**Associated Meta Attributes**: [depth](#depth-meta-attribute), [halign](#halign-meta-attribute), [height](#height-meta-attribute), [valign](#valign-meta-attribute), [xpos](#xpos-meta-attribute), [x_offset](#x_offset-meta-attribute), [ypos](#ypos-meta-attribute), [y_offset](#y_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `320`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`w`, `width`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta width=200, height=200, x=1/2w, y=1/2h, halign=fa_center, valign=fa_middle /]
```


___
### xpos (meta attribute)
The left-x coordinate of the canvas on the GUI layer.


**Associated Meta Attributes**: [depth](#depth-meta-attribute), [halign](#halign-meta-attribute), [height](#height-meta-attribute), [valign](#valign-meta-attribute), [width](#width-meta-attribute), [x_offset](#x_offset-meta-attribute), [ypos](#ypos-meta-attribute), [y_offset](#y_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `16`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`x`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta x=200 /]
```

```
[meta x=1/2w /]
```

```
[meta x=1w /]
```

```
[meta x=1/8w /]
```


___
### x_offset (meta attribute)
This is the horizontal offset, in pixels, of the canvas applied after [**halign**](#halign-meta-attribute). Note that this is only used when the canvas is first created.


**Associated Meta Attributes**: [depth](#depth-meta-attribute), [halign](#halign-meta-attribute), [height](#height-meta-attribute), [valign](#valign-meta-attribute), [width](#width-meta-attribute), [xpos](#xpos-meta-attribute), [ypos](#ypos-meta-attribute), [y_offset](#y_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `0`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`xo`, `x-offset`, `xOffset`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, x=200, y=200, xo=-100 /]
```

```
[meta, x-offset=500 /]
```

```
[meta, xOffset=333, halign=fa_right, valign=fa_bottom /]
```


___
### ypos (meta attribute)
The top-y coordinate of the canvas on the GUI layer.


**Associated Meta Attributes**: [depth](#depth-meta-attribute), [halign](#halign-meta-attribute), [height](#height-meta-attribute), [valign](#valign-meta-attribute), [width](#width-meta-attribute), [xpos](#xpos-meta-attribute), [x_offset](#x_offset-meta-attribute), [y_offset](#y_offset-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `16`
isMeta | `true`
since | `0.9.0`
deprecated | `false`

#### Syntax

`y`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta y=200 /]
```

```
[meta y=1/2h /]
```

```
[meta y=1h /]
```

```
[meta y=1/8h /]
```


___
### y_offset (meta attribute)
This is the vertical offset, in pixels, of the canvas applied after [**valign**](#valign-meta-attribute). Note that this is only used when the canvas is first created.


**Associated Meta Attributes**: [depth](#depth-meta-attribute), [halign](#halign-meta-attribute), [height](#height-meta-attribute), [valign](#valign-meta-attribute), [width](#width-meta-attribute), [xpos](#xpos-meta-attribute), [x_offset](#x_offset-meta-attribute), [ypos](#ypos-meta-attribute)


| property | value
--- | ---
type | `NUMBER`
defaultValue | `0`
isMeta | `true`
since | `0.9.2`
deprecated | `false`

#### Syntax

`yo`, `y-offset`, `yOffset`

#### Compatible Tags

[meta](#meta-tag)

#### Examples

```
[meta, x=200, y=200, yo=100 /]
```

```
[meta, y-offset=500 /]
```

```
[meta, yOffset=333, halign=fa_left, valign=fa_top /]
```


## User Input and Capturing
This section builds off the concepts of textareas, checkboxes, toggles, radio buttons, and sliders already established in the Tags section.

Whenever an input type is updated (its value is now different from its value last frame), the function `shampoo_var_catch` (inside `__ShampooInsertVariables` script) is called alongside the optional meta `onUpdate` callback function. Inside `shampoo_var_catch`, you'll be able to communicate directly with the canvas and get any necessary input values.

Notice the script has `canvas` as its argument. This variable is the canvas that was just updated. Using the `canvas.uid` in a switch statement, we can handle different canvases.

We can use a combination of the `shampoo_var_catch` function and the `shampoo_var_replacement` function discussed in the Variables and Dynamic Content section to store and set canvas input throughout the duration of the game.

The following functions will return values for repsective input types:
- **textarea_get_value(string:id)**: returns a textarea input value
- **slider_get_value(string:id)**: returns a given slider's value
- **checkbox_get_checked(string:id)**: return checked status of a given checkbox (or toggle)
- **radio_group_get_value(string:group_id)**: returns radio group selection value, or undefined
- **radio_get_checked(string:groupd_id, string:id)**: return checked status of a given radio button in a group

You can also use the following functions to disable/enable input elements:
- **textarea_set_enable(string:id, bool:enabled)**: either enables or disables a textarea
- **slider_set_enabled(string:id, bool:enabled)**: either enables or disables a slider
- **checkbox_set_enabled(string:id, bool:enabled)**: either enables or disables a checkbox
- **toggle_set_enabled(string:id, bool:enabled)**: see checkbox_set_enabled
- **toggle_set_enabled(string:group_id, string:id, bool:enabled)**: either enables or disables a radio button

#### Example
```
function shampoo_var_catch(canvas) {
	
	switch(canvas.uid) {
		case("main"):
			global.player_name = canvas.textarea_get_value("player_name");
			score = canvas.slider_get_value("player_score");
		break;
	}
	
}
```

## Variables and Dynamic Content
Sometimes you need more control over what content is rendered to a canvas. For example, suppose we want to display the player's name and score.

```
# Player: {$var}
## Score: {$var}
```

By writing `{$var}` we signal to the canvas processor that a variable should be inserted in place of that text. Unfortunately, this cannot be done via live-editing and requires manipulation of the `__ShampooInsertVariables` script, `shampoo_var_replacement`.

The `shampoo_var_replacement` script is a switch statement that is called a single time during the canvas creation process. Each case in the switch statement is a canvas UID. Each case should return an array of values. Each value in the array will replace the nth `{$var}`, where n is the index.

Going back to the player and score example. Suppose the canvas uid is `gameover`. Then, your `shampoo_var_replacement` function may look like the following:
```
function shampoo_var_replacement(uid) {
	
	switch(uid) {
		case("gameover"):
		return [global.player_name, score];
	}
	
	return undefined;
}
```

See the section User Input and Capturing on how to grab user input from textareas, checkboxes, toggles, radio buttons, and sliders. Below is an example that will store checkbox values in global variables and re-set checkboxes each time a canvas is opened.

**list.txt**
```
# Shopping list
[textarea, id="store_name", value="{$var}" /]
[checkbox, id="banana", checked={$var} /]
[checkbox, id="apple", checked={$var} /]
[checkbox, id="orange", checked={$var} /]
```

```
function shampoo_var_replacement(uid) {
	
	switch(uid) {
		case("list"):
		return [global.store_name, global.banana, global.apple, global.orange];
	}
	
	return undefined;
}
```

```
function shampoo_var_catch(canvas) {
	
	switch(canvas.uid) {
		case("main"):
			global.banana = canvas.textarea_get_value("store_name");
			global.banana = canvas.checkbox_get_checked("banana");
			global.apple = canvas.checkbox_get_checked("apple");
			global.orange = canvas.checkbox_get_checked("orange");

		break;
	}
	
}
```



## Variables and Dynamic Content
Sometimes you need more control over what content is rendered to a canvas. For example, suppose we want to display the player's name and score.

```
# Player: {$var}
## Score: {$var}
```

By writing `{$var}` we signal to the canvas processor that a variable should be inserted in place of that text. Unfortunately, this cannot be done via live-editing and requires manipulation of the `__ShampooInsertVariables` script, `shampoo_var_replacement`.

The `shampoo_var_replacement` script is a switch statement that is called a single time during the canvas creation process. Each case in the switch statement is a canvas UID. Each case should return an array of values. Each value in the array will replace the nth `{$var}`, where n is the index.

Going back to the player and score example. Suppose the canvas uid is `gameover`. Then, your `shampoo_var_replacement` function may look like the following:
```
function shampoo_var_replacement(uid) {
	
	switch(uid) {
		case("gameover"):
		return [global.player_name, score];
	}
	
	return undefined;
}
```

See the section User Input and Capturing on how to grab user input from textareas, checkboxes, toggles, radio buttons, and sliders. Below is an example that will store checkbox values in global variables and re-set checkboxes each time a canvas is opened.

**list.txt**
```
# Shopping list
[textarea, id="store_name", value="{$var}" /]
[checkbox, id="banana", checked={$var} /]
[checkbox, id="apple", checked={$var} /]
[checkbox, id="orange", checked={$var} /]
```

```
function shampoo_var_replacement(uid) {
	
	switch(uid) {
		case("list"):
		return [global.store_name, global.banana, global.apple, global.orange];
	}
	
	return undefined;
}
```

```
function shampoo_var_catch(canvas) {
	
	switch(canvas.uid) {
		case("main"):
			global.banana = canvas.textarea_get_value("store_name");
			global.banana = canvas.checkbox_get_checked("banana");
			global.apple = canvas.checkbox_get_checked("apple");
			global.orange = canvas.checkbox_get_checked("orange");

		break;
	}
	
}
```



## Classes
Classes can eliminate redundancy in code. For example, if you consistently style buttons a particular way, consider writing a button class and apply the class to all buttons.

If you peek under the hood, you'll see that all classes really do are copy+paste attributes and values into tags.

Classes can either be defined in canvas files, or in the the `CLASSES` file. A class defined in the CLASSES file will be accessible to all canvases project-wide. A class defined in a canvas file may be used in any canvases project-wide only **after** the canvas in which it is declared has been created in-game. The syntax to declare a class is slightly different from a tag itself. One tag per line, where the line starts with a period, followed by the classname, followed by attributes->values inside curly braces.

```
.className{bg=yellow,c=black}
.class2{scr="test_callback_function"}
```

The above code declared two classes: `className` and `class2`.

```
[span.className]This text is black with a yellow background[/span]
[function.class2]click me![/function]
```

The above code takes the classes and applies them to tags using **dot-reference**. The end result would be identical to the following:
```
[span, bg=yellow, c=black]This text is black with a yellow background[/span]
[function, scr="test_callback_function"]click me![/function]
```



## Screen Size

You may use C language processor-like syntax for designing canvases differently based on screen size.

Note that you **must** be using out-of-the-box Shampoo resize handling (see [Installation and Setup](#installation-and-setup)).

Further, the canvas meta attribute [**resize_recreate**](#resize_recreate-meta-attribute) must be set to `true` as the canvas needs to be re-generated when the window size is changed.

The following sizes are available: `XL`, `L`, `M`, `S`, `XS`. Their pixel sizes are defined in macros `SH_SIZE_*`, where `*` is the size name.

Canvas code between lines **starting with** `#ifdef` and `#endif` will only be parsed if the *horizontal window size* is greater than, or equal to, the conditional requirements.

Use the following syntax:
- `#ifdef` followed by sizes, separated by `or`
- `#endif` to end the switch
- `#elif` followed by sizes, separated by `or`
- `#else` covers anything not already accounted for

### Examples
```
// standard if, elif
#ifdef XL~
extra large window detected
#elif L
large screen window detected
#elif M
medium screen window detected
#elif S or XS
extra small, or small, screen window detected
#endif
```

```
// only XL or L
#ifdef XL or L
# Big screen!
#endif

```

```
// only XL or L with else
#ifdef XL or L
# Big screen!
#else
### Not a big screen!
#endif

```

```
// change class based on screen size

.resizeClass{}

#if XL
.resizeClass{bkg=purple-4, width=480, shadow-color=purple-4, h=240, c=white}
#elif L
.resizeClass{bkg=red-4, width=640, shadow-color=red-4, h=320, c=white}
#elif M
.resizeClass{bkg=yellow-4, width=480, rad=64, shadow-color=yellow-4, h=512, c=white}
#elif S
.resizeClass{bkg=green-4, width=350,shadow-color=green-4, h=400, c=white}
#elif XS
.resizeClass{bkg=blue-4, width=320, shadow-color=blue-4, h=320, c=purple-500}
#endif

```

```
// display different things based on screen size
#if XL
## the window is **extra large**
#elif L
## the window is **large**
#elif M
### the window is **medium** 
[right]right[/right]
[center]center[/center]
[br, h=96 /]
[button, scr=sh_log?args=test, id="btn-m", w=200, h=64, c=white, img=SH_IMG_BUTTON_GREEN, content="Green button", order=1, tooltip="green", hover-y=4 /]
#elif S
### the window is **small**
[br, h=32 /]
[button, scr=sh_log?args=test, id="btn-m", w=400, h=64, c=white, img=SH_IMG_BUTTON_ORANGE, content="Orange button", tooltip="orange", hover-y=4 /]
#else
#### the window is **extra small**
#endif
[/center]
```




## Character Escaping
Rather than use the forward slash to escape characters, Shampoo uses the `~` tilde to escape characters. The reasoning for this is more control over tokenization in-engine.

You can escape a newline from being created by ending a given line with the escape character. You can escape the escape character by writing two escape characters in a row.

Perhaps most importantly, you will need to escape from commas in quotes within a tag. Otherwise, the tokenizer will think you're ending the current attribute value. While this is not ideal, it drastical speeds up the tokenization process.

See [User Input and Capturing](#user-input-and-capturing) for important information regarding textarea input and which characters are invalid.

```[textbox, value="This value~, right here~, has commas" /]
[button, label="Hello~, World!", bg=blue, radius=5, c=white /]
```



## Spaces and Linebreaks
This section builds upon the concepts of `br` break tags, `hr` horizontal rule tags, and `sp` spacer tags discussed earlier in the [tags](#tags) section.

The tokenizer will strip all double spaces from text content and replace them with a single space. If this is not desired, consider using `&nbsp;' in-string. This allows for unlimited spaces in a row.

The tokenizer will split words at spaces ` ` and dashes `-`. Any spaces that appear at the start or end of a line (post-format during the canvas generation process) will be hidden. The horizontal space these hidden tokens occupy will not be taken into account when it comes to left, right, or center alignments of contents on a row-by-row basis.


## SFX and Sound Toggling
Use function `shampoo_sound_enable(bool:enable)` to enable or disable sound effects being played via the Shampoo `shampoo_play_sound(sound:snd)` function. You may want to tie this function call to your existing sound toggle logic. Or, simple bypass using this function call and directly set globalvar `SH_SOUND_ENABLED` though that is not recommended for Shampoo future-proofing.

Check out the [Introduction section](#introduction) for more user-facing sound functions.


## CONTACT
If you wish to report a bug, contact, or support the Shampoo developer, send an email to zbanack (at) gmail (dot) com or reach out on Twitter [@zackbanack](https://twitter.com/zackbanack).


## FAQ and Reminders
- `{$var}` isn't being parsed out of the document
	- Try adding a space after `{$var}` for self-closing tags, like `slider`, or value may not be tokenized correctly
	- Make sure you're wrapping quotes, where necessary
	- Make sure you're escaping certain characters, like commas and nested quotes
- If you want two buttons to be side by side, make sure you set the `height` attribute to the exact same value otherwise a line break is forced for mixed content heights
- Make sure commas, colons, brackets, and quotes are escaped correctly in strings. If you wish to insert a quote or comma character into a string, be sure to escape it! See the section on [Character Escaping](#character-escaping) for more information.
- Make sure to disable file sandbox is checked in Global Game Settings
- Don't forget quotes around textfield `value`, even if using `numbersOnly` attribute
- Callback functions need to be in the global scope
- If TESTLOAD.txt isn't loading, make sure everything is written on one line with no linebreaks
- Use `SH_THIS_CANVAS` to reference the canvas at *this* scope
- Ensure TESTLOAD.txt and CLASSES.txt are placed in your `shampoo` live-code directory and Included Files Directory
- Do not name .txt files `self` or `all`
- Only `border-bottom` will work on `function` tags as they control the underline thickness. Consider using a `button` if you wish for more stylizing over clickable areas of text.
- Please note that arguments are passed as strings, so if you are expecting a number, you must cast using `real(argument)`.
- [**hitbox_full**](#hitbox_full-attribute) will control whether certain input elements receive a canvas-wide hitmask to make clicking on elements (like radio buttons, checkboxes, and toggles) easier. If `false`, you must click directly on such input element graphics to toggle them.
- If you do not want a canvas to be regenerated each time the window is resized, use attribute [**resize_recreate**](#resize_recreate-attribute).
- You can comment out code in files using double-slash comments at the *start* of lines only. Multi-line `/* ... */` comments are not supported at the moment.
- Note that you can open multiple canvases by separating their file names with the ampersand (`&`).
- Note that tag attributes will be split at colons (`:`), so you can pass in multiple values for certain attributes.
- Note that you must provide a closing slash when working with meta tags or input tags as they are self-closing tags. e.g. (`[meta ... /]`).
