![Shampoo logo](https://raw.githubusercontent.com/zbanack/shampoo/main/logo.png?token=AHDW5FK6VH24H7VJ6WR7CXS7WQZHA)

### This documentation is still a WIP, so please excuse incomplete information and typos!
#### The highly in-depth demo included with the framework should be more than enough to show you a majority of features.

📚 **Documentation** for the GameMaker GUI framework, Shampoo

✏️ Framework v0.9.1; last modified 11/19/20 by [Zack Banack](https://zackbanack.com)
___
Shampoo is a [GameMaker Studio 2.3](https://www.yoyogames.com/) (GMS 2.3) framework. It lets you build graphical user interfaces (GUIs) using a [markup-like language](https://en.wikipedia.org/wiki/markup). Shampoo supports live-coding, so you can create and debug interfaces in real-time. Shampoo was created for rapid, lightweight interface creation and user input handling. Its development began in early 2019 and went through several major pivots and rewrites since.

⚠️ Shampoo has only been tested and verified to work on GMS 2.3 Windows export (including YoYo Compiler) and MacOSX export.​

## Table of Contents
- [Introduction](#introduction)
- [Features](#features)
- [Roadmap](#roadmap)
- [Installation and Setup](#installation-and-setup)
- [Framework Breakdown](#framework-breakdown)
- [Types](#types)
- [Tags](#tags)
- [Attributes](#attributes)
- [Meta](#meta)
- [User Input and Capturing](#user-input-and-capturing)
- [Variables and Dynamic Content](#variables-and-dynamic-content)
- [Symbols](#symbols)
- [Classes](#classes)
- [Character Escaping](#character-escaping)
- [Spaces and Linebreaks](#spaces-and-linebreaks)
- [SFX and Sound Toggling](#sfx-and-sound-toggling)
- [Contact](#contact)
- [FAQ/Reminders](#faq-and-reminders)

### Introduction
In Shampoo, a "menu" or particular interface is called a **canvas**.

Canvases are constructed using **[tags](#tags)**. Tags accept **[attributes](#attributes)**.

Canvases are written in text `.txt` files. One canvas per file. You can change the `.txt` extension to whatever you prefer in macro `SH_FILE_EXT`.

The unique identifier (UID) of a canvas is derived from its text file name. For example, a canvas file named `hello_world.txt` would have a UID of `hello_world`.

The following functions are developer facing:
- `shampoo_enabled()` returns a bool: whether or not the framework is enabled
- `shampoo_init(bool:release_mode, string:live_updating_directory, bool:live_updating)` initializes the framework. See the section [Installation and Setup](#installation-and-setup) to learn more about how to setup Shampoo.
- `shampoo_free()` attempts to free the framework from memory. You *probably* won't need to call this if you plan on using Shampoo throughout your entire project. Do **not** call this function inside of a canvas. Try to call it in the Begin Step or Step Event to avoid callstack errors.
- `shampoo_update()` called each gamestep; main loop for the framework
- `shampoo_render()` renders all canvases to the screen
- `shampoo_show_canvas(string:UID)` shows the canvas associated with the given UID, assuming no such canvas already exists
- `shampoo_hide_canvas(string:UID)` hides the canvas associated with the given UID.
- `shampoo_close_all_canvases()` hides all active canvases
- `shampoo_get_canvas(string:UID)` returns the struct of the canvas with the given UID (or undefined if not found)
- `shampoo_set_canvas_x(string:UID, real:x)` sets the x-coord of the canvas associated with the given UID
- `shampoo_set_canvas_y(string:UID, real:y)` sets the y-coord of the canvas associated with the given UID
- `shampoo_set_canvas_pos(string:UID, real:x, real:y)` combination of the above two functions; sets x-coord and y-coord of the canvas associated with the given UID
- `shampoo_refresh_canvas(string:UID)` refreshes a canvas of a given UID
- `shampoo_sanitize(string:str)` escapes characters and sanitizes a string; done automatically to text entered via textareas

### Features
- Live-coding development environment so you can create and debug interfaces in real-time
- Lightweight setup: it only takes a single function call to get the framework initialized
- A unique markup-like language to build interfaces quick and easy
- Buttons with ability to enable/disable them
- Draggable interfaces
- Checkbox and toggles
- Radio button groups
- Text input with maxlength and numbersOnly attributes
- Sliders with customizable min/max ranges and intervals
- Content overflow/linebreaks
- Custom callback functions and in-line hyperlinks
- Sound effect (and toggling) support
- Tooltips
- Hex, blue-red-green, and string color support, with a built-in palette
- onLoad, onStep, onClose, onDraw, onDrag listeners
- Out-of-the-box window resizing support

### Roadmap
- Out-of-the-box HTML5 support​
- Dropdown menus
- More advanced textareas and input interception
- Paginations
- More efficient text parsing and character escaping
- Better number support (e.g. decimals operators)
- Element padding, margins
- Scrollviews
- Lists, expansion of indentation logic

### Installation and Setup
Shampoo is compatible only with the GMS 2.3 IDE.

The framework can be purchased on [itch.io](https://zackbanack.itch.io/shampoo) and the [YoYo Games Marketplace](https://marketplace.yoyogames.com/assets/9536/shampoo-markup-gui-framework). Please see the marketplace listing for more information on purchasing, distribution, and warranty.

1. Install GMS2.3
2. Purchase Shampoo using the link above
3. Extract the contents of the Shampoo framework .zip file.
4. In GMS 2.3, open the framework project file `shampoo.yyp`
5. Press F5 to run the project. You will see the "Hello, World!" interactive demo.
6. If you wish to import the framework into an existing project, use the option "Create Local Package" in the IDE. But before that, consider reading the following section, [Framework Breakdown](#framework-breakdown), to familiarize yourself with structure. `Tools > Create Local Package`. Fill out the text fields and click the "Add All" button to add all resources from Shampoo to the package. Press OK and the local package will be created. To import the local package into your existing project, open the project in the IDE and navigate to `Tools > Import Local Package`. You can cherry-pick which bundled assets (sprites, fonts, sounds, etc.) should be imported. However, note that if Shampoo tries to reference an asset you've opted out of including, you'll get an error. All scripts and Objects are necessary!
7. After import resources, make sure object `SH_OBJ` is placed in the first room of the game.

#### Live-Coding and Release Mode
If you do not wish to enable live-coding in your project, you can skip the rest of this section. Otherwise, continue following the steps.
1. First, ensure that the file system sandbox is disabled. This allows the game to read and write to files outside of the default `working_directory`.
```
Resource Tree hamburger button -> Game Options -> Platform Settings -> [your platform] -> General -> check "Disable file system sandbox"
```
2. Next, navigate to the project's Included Files
```
Resource Tree hamburger button -> Included Files -> Open in Explorer
```
3. You'll see a folder called "shampoo". **Copy** the folder.
4. On your desktop, or in a *non-GMS project directory* of your choice, **paste** the folder. This folder is what you'll work out of when you want to live-code. GameMaker doesn't allow for project-directory folders to be edited while the game is running. This means two things: 1) the game needs to be made aware of this new directory, and 2) when it comes time to release the game, the contents of this new folder need to be brought back in to the Included Files.
5. In the Create Event of object SH_OBJ, find the `shampoo_init` function and modify it to the following:
```
shampoo_init(
	false,
	true, // built-in window resize handling
	"shampoo/directory/goes/here/",
	true
);
```
For example, I created a shampoo folder on my desktop, so this is my function:
```
shampoo_init(
	false,
	true, // built-in window resize handling
	"C:/Users/Zack/Desktop/shampoo/",
	true
);
```
The first parameter of `shampoo_init` is whether the game is in "release mode" or not. **When it comes time for you to release your game to world, make sure you set this flag to `true`!** When release mode is true, Shampoo will read files in Included Files, not from your live-coding directory. If you wish to temporary disable live-coding, but stay in non-release mode, set the last parameter to `false`.

While live-coding, the game will check the Shampoo directory every `SH_LIVE_REFRESH_RATE` game steps for file changes. If a file change happens, the game will read `TESTLOAD.txt` and load the canvas described in it.

While live-coding, you can press "R" to force-reload `TESTLOAD.txt`.

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
open:hello_world.txt&third.txt
```

If I make changes in any live-code folder, all canvases will be removed and `hello_world.txt` and `third.txt` will be opened per my TESTLOAD.txt instructions.

### Framework Breakdown
Inside the template project, you'll find a resource tree structured similar to the following:
* Fonts
	* shampoo
* Objects
	* SH_OBJ
	* SH_DEMO
* Rooms
	* Room1
* Scripts
	* shampoo
		* ShampooConfig
		* ShampooDefaults
		* ShampooInserts
		* ShampooMain
* Sounds
	* shampoo
* Sprites
	* shampoo
		* Buttons
		* Checkbox
		* Demo
		* Radio
		* Shapes
		* Slider
		* Symbols
		* Textarea
		* Toggles
* Includes Files
	* shampoo

Notice that all resource types have a sub-folder called `shampoo`. Further, notice that all resources (sprites, scripts, sounds, etc.), global variables, function names, and enumerators are prefixed with either `sh_` or `shampoo_`. The reasoning for this is two-fold: to prevent naming conflicts and keep all shampoo-related assets under umbrella naming conventions.

#### Fonts
There are 12 fonts bundled with the framework. There are six header fonts of various sizes. There is an input-specific font for buttons, textboxes, checkboxes, toggles, and radio buttons. Four fonts are body fonts (regular, bold, italic, and bold-italic). Finally, included is a small font for things like tooltips or unimportant body text. The fonts are Segoe UI typeface, but can be changed to whatever your project requires.

#### Objects
The framework only consists of two objects.

SH_DEMO is a dummy object meant for the demo. It can be removed from the project, assuming all code references to it in script `ShampooInserts` and SH_OBJ Create Event are removed as well. 

SH_OBJ is the primary Shampoo object. It's marked as persistent, so only one instance of it needs to be created at the start of the game. It handles framework initialize, general updating and input handling, as well as rendering.

#### Rooms
Room1 is an arbitrary test room which houses the SH_OBJ Shampoo controller object.

#### Scripts
There are four primary scripts.

`ShampooConfig` is user-facing and allows you to customize dozens of default and fall-back values.

`ShampooInserts` is also user-facing and allows you to dynamically insert variables to, and get input values from, canvases.

`ShampooMain` is the bulk of the framework logic. **Do not** modify this file unless you know what you're doing.

`ShampooOverhead` is similar to `ShampooConfig`, though not user-facing. It houses constants and important framework-wide values. Modify with caution.

#### Sounds

Some default sound effects are provided for the sake of demo purposes. Sound effects are courtesy of [Kenney.nl](https://kenney.nl).

#### Sprites

Many default sprites are provided for the sake of demo purposes. Sprites are courtesy of [Kenney.nl](https://kenney.nl).

#### Included Files

##### TESTLOAD

If live-editing is enabled, this file will instruct the game which canvas to load.

example(s):
```
open:main.txt
```

In the above example, the `main.txt` canvas file will be reloaded any time a change is detected in any canvas file. See Installation section above for more information.

##### CLASSES

A file that houses only class declaration. This is a way to organize classes across various canvas files in your project. See the Classes section for more information.

### Types

Because Shampoo canvases are created from giant chunks of plaintext, the framework needs to define certain "types" so it knows what category a piece of text falls into. For example, "123" as a string is very different from 123 as a number. The following contains a list of types that Shampoo uses and can sanitize. During the string sanitization process, pieces of text are tried against the following.

Note that tag attributes will be split at colons (`:`), so you can pass in multiple values for certain attributes.

e.g. `color=red:green` in a link tag will set the contents to red by default and green when hovering. Certain tags handle multiple colon-split attributes differently, so read up on specific tags.

#### Number
A number will attempt to be parsed from a string. If no valid, non-exponential, non-decimal number can be resolved, `undefined` is returned.

Note that numbers may be whole number only, positive or negative. Decimals and exponentials are not supported at the moment.

Examples of valid numbers: 123, -123, 1, -1.
Examples of invalid numbers: 100.1, -100.0, 100e, 100^3, 10*10, 100+0

Further, there are hard-coded special strings that resolve to values:
- `1/16w`, `1/12w`, `1/10w`, `1/8w`, `1/4w`, `1/3w`, `1/2w`, `1w`, `w` returns 1/nth the width of the game's GUI width
- `1/16h`, `1/12h`, `1/10h`, `1/8h`, `1/4h`, `1/3h`, `1/2h`, `1h`, `h` returns 1/nth the height of the game's GUI height
- `room_speed`, `fps`, `fps_real`, `current_time`, and `get_timer` return their respective GM value

#### Function
A string that must resolve to a valid function name. If no such function can be resolved, `undefined` is returned.

#### Sprite
A string that must resolve to a valid sprite name. If no such sprite can be resolved, `undefined` is returned.

#### String
An value that is wrapped with matching single or double quotes. **Note** if you wish to insert a quote or comma character into a string, be sure to escape it! See the section on Character Escaping for more information.

#### Color
Tries to parse a string to return a color. If no valid color can be parsed, `undefined` is returned. Strings can be hexidecimal format (with a leading hash `#`), GameMaker blue-red-green (with leading `$`), or a pre-defined color in the `SH_COLORS` color lookup table.

Hex colors can be defined in tuples which are repeated as many times as necessary. For example: `#f0` will resolve to `#f0f0f0` and `#f00` will resolve to `#f00f00`.

#### Align
`fa_left`, `fa_right`, `fa_center`, `fa_top`, `fa_middle`, `fa_bottom` return GameMaker-equivalent values. Used for aligning the canvas respectively.

#### Bool
If a string is "0" or begins with case-insensitive "F", `false` is returned. If a string is "1" or begins with case-insensitive "T", `true` is returned. Fallback value is `undefined`.

#### Ease
A string that must resolve to be a valid ShEase enum value. If no such ease can be resolved, `undefined` is returned.

#### ID
An alphanumeric value that is wrapped with matching single or double quotes. For use with inputs.

#### Sound
A string that must resolve to a valid sound name. If no such sound can be resolved, `undefined` is returned.

#### Canvas
A string that must resolve to a valid canvas file name. If no such sound can be resolved, `undefined` is returned.

### Tags

Tags are the building blocks of canvases. This section outlines all tags currently available in the Shampoo framework.

Tags can be nested and Shampoo will try its best to resolve formatting. If no tags are provided, the parser defaults to body text of default colors and attributes as defined in the script `ShampooConfig`.

##### br
Inserts a linebreak, forcing content here on out to start beneath this break. Note that you must provide a closing slash if you manually insert a break as this tag is a singleton (`[br/]`).

accepts: `height`

example(s):
```
[span]This text is on line 1[br/]and this text is on line 2[/span].
```
```
[span]This text is on line 1[br, h=100/]and this text is 100 pixels beneath[/span]
```
___
#### hr
Similar to a linebreak, a horizontal rule will force new content to start beneath. Horizontal rules have visible lines of varying thickness, however. Note that you must provide a closing slash if you manually insert a horizontal rule as this tag is a singleton (`[hr/]`).

A shorthand way to insert a horizontal rule into a canvas is three or more consecutive equal signs (`=`) or dashes (`-`) at the very start of a line.

accepts: `height`, `thickness`, `color`
example(s):
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
#### sp
Inserts a spacer, where following contents will be pushed `width` number of pixels. Spacers of default width can also be inserted via `&nbsp;`. Note that you must provide a closing slash if you manually insert a spacer as this tag is a singleton (`[sp/]`).

accepts: `width`
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
#### img
Inserts an image into a canvas, in-line with content. If an image is inserted beside text or other images, then *all content in that row will be centered vertically*. You can specify which sprite and sprite frame to draw, however note that **animations are currently not supported**. If you wish to draw an animated sprite, then consider using an `onDraw` meta attribute.

An image can "float" if either `float-x` or `float-y` are set. While floating, an image will be positioned at its float coordinates relative to the canvas top-left (x, y) coordinate. Floating images will not affect the positioning of other contents whatsoever and may overlap and overflow. Note that floating images are rendered in the order they're declared, so be cautious of where you define floating images in your canvas file.

The size of the image in the canvas will default to the sprite's width and height. However, you can optionally provide `width` and `height` attributes to override these dimensions. If two inline image heights differ, a linebreak may be inserted automatically if deemed necessary to preserve canvas row overflow integrity.

An image can be blended with a color using the `blend` attribute.

accepts: `image`, `frame`, `alpha`, `color`, `width`, `height`, `float-x`, `float-y`, `blend`
___
#### p
Define a block of body text with various formatting attributes acceptable. These tags will terminate lines, acting as linebreaks.

accepts: `color`, `bg`, `radius`, `border`, `border-r`, `border-d`, `border-l`, `border-u`

example(s):
```
[p]this is a paragraph[/p]
[p, c=blue]this is a second paragraph with blue text[/p]
```
___
#### span
This tag is identical to the aforementioned `p` tag, except it will not terminate lines. You can use this tag to customize text within a paragraph, e.g. highlight certain words or change text colors.

accepts: `color`, `bg`, `radius`, `border`, `border-r`, `border-d`, `border-l`, `border-u`

example(s):
```
[p]this is a paragraph [span, c=blue]and this text is blue[/span][/p]
```
```
[span, bg=white]this text has [span, bg=yellow]highlighted[/span] words in it![/span]
```
___
#### function/f
All content wrapped inside function tags will, when clicked, execute the callback function provided. These are essentially HTML hyperlinks. You can mix and match body font types, colors, and more non-line terminating attributes inside a singular link.

The `open` attribute will open a canvas of a given UID, passed as string. Similarly, the `close` attribute will close a canvas of a given UID. If you'd like to close <this> canvas, simple pass `self`; to close all canvases, pass `all`. You can pass multiple UIDs by separating them with the `&` character.

You can specify a tooltip string to appear upon link hover.

accepts: `scr`, `thickness`, `open`, `close`, `tooltip`, `underline`, `color`, `bg`, `sound`

exampes(s):
```
[f, scr=test_callback_function]Click me![/f]
```
```
[f, open="popup_menu", tooltip="This is hovertext"]Create a popup menu![/f]
```
```
[f, close=self]Close this canvas[/f]
```
___
#### button
Buttons are similar to function links, except they allow for more customization. In particular, you can set images, text offsets on hover, and enable/disable statuses. If an `id` attribute is provided

accepts: `scr`, `hover-x`, `hover-y`, `open`, `close`, `id`, `disabled`, `tooltip`, `color`, `image`, `bg`, `radius`, `width`, `height`, `sound`, `disable-shake`

exampes(s):
```

```
```

```
___
#### sm
Text wrapped in these header tags will be the smallest in size, smaller than the body fonts.

accepts: `undefined`

exampes(s):
```
[sm]This text is tiny![/sm] This text is standard size!
```

___
#### h1
Text wrapped in these h1 header tags will be the largest in size, as supported by the framework. You can shorthand-declare these tags by starting a new line followed by a hash symbol followed by a space followed by text.

accepts: `undefined`

examples:
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
#### h2
Text wrapped in these h2 header tags are second-largest in size, as supported by the framework. You can shorthand-declare these tags by starting a new line with two consecutive hash symbols followed by a space followed by text.

accepts: `undefined`

examples:
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
#### h3
Text wrapped in these h3 header tags are third-largest in size, as supported by the framework. You can shorthand-declare these tags by starting a new line with three consecutive hash symbols followed by a space followed by text.

accepts: `undefined`

examples:
```
[h3]Hello, World[/h3]
```
```
### Hello, World!
```
#### h4
Text wrapped in these h4 header tags are fourth-largest in size, as supported by the framework. You can shorthand-declare these tags by starting a new line with four consecutive hash symbols followed by a space followed by text.

accepts: `undefined`

examples:
```
[h4]Hello, World[/h4]
```
```
#### Hello, World!
```
___
#### h5
Text wrapped in these h5 header tags are fifth-largest in size, as supported by the framework. You can shorthand-declare these tags by starting a new line with five consecutive hash symbols followed by a space followed by text.

accepts: `undefined`

examples:
```
[h5]Hello, World[/h5]
```
```
##### Hello, World!
```
___
#### h6
Text wrapped in these h6 header tags are sixth-largest in size, right above the body fonts, as supported by the framework. You can shorthand-declare these tags by starting a new line with six consecutive hash symbols followed by a space followed by text.

accepts: `undefined`

examples:
```
[h6]Hello, World[/h6]
```
```
###### Hello, World!
```
___
#### textarea

Note that you must provide a closing slash when working with textareas as they are singletons (`[textarea ... /]`). These tags will terminate lines, acting as linebreaks.

accepts: `id`, `width`, `height`, `value`, `img`, `maxlength`, `numbersOnly`, `placeholder`, `color`, `bg`, `radius`, `sound`

exampes(s):
```
[textarea, id="favorite_number", indent=64, placeholder="My fave # is ___"", value="{$var}", maxlength=2, numbersOnly=true, img=SH_IMG_TEXTAREA_BLUE /]
```
```

```
___
#### checkbox
Note that you must provide a closing slash when working with checkboxes as they are singletons (`[checkbox ... /]`). These tags will terminate lines, acting as linebreaks.

If attribute `value` is provided, the contents of `value` will be displayed to the right of the checkbox. By default, such text stems from `id`.

accepts: `id`, `checked`, `value`, `image`, color`, `sound`

exampes(s):
```

```
```

```
___
#### toggle
Note that you must provide a closing slash when working with toggles as they are singletons (`[toggle ... /]`). These tags will terminate lines, acting as linebreaks.

If attribute `value` is provided, the contents of `value` will be displayed to the right of the toggle. By default, such text stems from `id`.

accepts: `id`, `checked`, `image`, `value`, color`, `sound`

exampes(s):
```

```
```

```
___
#### radio
Note that you must provide a closing slash when working with radio buttons as they are singletons (`[radio ... /]`). These tags will terminate lines, acting as linebreaks.

If attribute `value` is provided, the contents of `value` will be displayed to the right of the radio button. By default, such text stems from `id`.

accepts: `id`, `group`, `checked`, `img`, `value`, `color`, `sound`

exampes(s):
```

```
```

```
___
#### slider
Note that you must provide a closing slash when working with sliders as they are singletons (`[slider ... /]`). These tags will terminate lines, acting as linebreaks.

Below the slider, the value of the slider will be displayed, wrapped around attribute values `prefix` and `suffix`, respectively.

accepts: `id`, `min`, `max`, `interval`, `value`, `color`, `bg`, `img`, `sound`, `prefix`, `suffix`

exampes(s):
```

```
```

```
___
#### italic
Body text wrapped in this tag will be italic. These tags will **not** terminate lines by themselves. Italic and bold tags can be nested.

Wrap text around single asterices to emulate this tag.

accepts: `undefined`

example(s):
```
Hello! [i]this text is italic[/i] and this is not
```
```
Hello! *this text is italic* and this is not
```
___
#### bold
Body text wrapped in this tag will be bold. These tags will **not** terminate lines by themselves. Italic and bold tags can be nested.

Wrap text around double asterices to emulate this tag. Wrap text around triple asterices to emulate bold *and* italic tags.

accepts: `undefined`

example(s):
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
#### left
Contents wrapped in this tag will be aligned to the left. These tags will terminate lines, acting as linebreaks.

accepts: `undefined`

exampes(s):
```

```
___
#### right
Contents wrapped in this tag will be aligned to the right. These tags will terminate lines, acting as linebreaks.

accepts: `undefined`

exampes(s):
```

```
___
#### center
Contents wrapped in this tag will be horizontally centered. These tags will terminate lines, acting as linebreaks.

accepts: `undefined`

exampes(s):
```

```
___
#### meta
The meta tag should be declared once at the top of the canvas file. It accepts all meta attributes. These attributes are special in that they affect the entire canvas rather than individual nodes within. Note that you must provide a closing slash when working with meta tags as they are singletons (`[meta ... /]`).

accepts: *see Meta section*

### Attributes

#### width
The horizontal size, in pixels, of a given element.

type: `number`
___
#### height
The vertical size, in pixels, of a given element.

type: `number`
___
#### color
The color of an element. Some elements allow for "hover" colors to be defined. To define such a hover color, provide two colors separated by a colon.

type: `color`
___
#### bg-color
The background color of an element. Some elements allow for "hover" background colors to be defined. To define such a hover background color, provide two colors separated by a colon.

type: `color`
___
#### image
Specifies which sprite to draw.

type: `sprite`
___
#### frame
Specifies which frame of a given sprite to draw.

type: `number`
___
#### alpha
Transparency of an element (0-100), where 0 is invisible and 100 is fully-visible. Note that not all elements support this attribute.

type: `number`
___
#### float-x
See the `image` tag for more information. Horizontal position of element relative to the canvas left x-coord.

type: `number`
___
#### float-y
See the `image` tag for more information. Vertical position of element relative to the canvas left x-coord.

type: `number`
___
#### label
Text that should be rendered ontop of a button

type: `string`
___
#### tooltip
When the cursor is ontop of an element with this attribute defined, such text will appear on the screen relative to the cursor.

type: `string`
___
#### function
A callback function.

type: `function`
___
#### open
A canvas UID that should be opened. Note that UID is derived from the canvas filename.

type: `string`
___
#### close
A canvas UID that should be closed. Note that UID is derived from the canvas filename.

type: `string`
___
#### hover-x
For use with buttons, the x-offset of the label when the cursor is hovered over the element.

type: `number`
___
#### hover-y
For use with buttons, the y-offset of the label when the cursor is hovered over the element.

type: `number`
___
#### radius
How rounded the corners of the element's background color are, in pixels (where radius of corner "circles" = value)

type: `number`
___
#### underline
For use with links, this is the color of the underline that appears when the cursor hovers over a link.

type: `color`
___
#### border/border-r/border-d/border-l/border-u
Border around the **background** of an element. Note this functionality is quite limited and only meant to add breathing room to background colors. It doesn't affect position of the element itself.

type: `number`
___
#### disabled
For use with buttons. If true, the element will be disabled and not clickable (thus not triggering the callback).

type: `bool`
___
#### id
A unique identifier for an input type or button.

type: `id`
___
#### value
For use with textareas, this is the textarea content string that the player can manipulate with the keyboard when the textarea is in focus.

type: `string`
___
#### group
For use with radio buttons, define which group a given radio button belongs to. Only one radio button per group can be set at a given time.

type: `id`
___
#### checked
For use with radio buttons, checkboxes, and toggles. If true, this input element will be "checked". If false, this input element will not be checked.

type: `bool`
___
#### maxlength
Restrict the maximum number of characters a textarea string can be.

type: `number`
___
#### numbersOnly
Bool; if true, the textarea will only accept 0-9, while an optional leading negative sign. If false, any text can be entered into the textarea.

type: `bool`
___
#### placeholder
If the textarea string is empty, this placeholder string will be drawn. The color of the placeholder string defaults to `SH_TEXTBOX_PLACEHOLDER_COLOR`.

type: `string`
___
#### min
For use with sliders, this is the minimum value a slider can be.

type: `number`
___
#### max
For use with sliders, this is the maximum value a slider can be.

type: `number`
___
#### interval
For use with sliders, the slider will "snap" to the nearest interval between the `min` and `max` range defined.

type: `number`

example(s):
```
[slider, id="slider", min=0, max=50, interval=10 /]
```

In the above example, the slider can only ever be set to 0, 10, 20, 30, 40, or 50 because of the interval being 10 and the range being [0, 50].

___
#### sound
Which sound effect should play when an interaction occurs.

You can specify two sounds, separated by a colon. The first sound will play when clicked and enabled, and the second sound will play when clicked and disabled.

type: `sound`

example(s):
```
[button, id="btn", label="Click Me!", sound=snd_enabled:snd_disabled, disabled=false /]
```
```
[button, id="name_button", close=self, open=shopping_list&toggles, img=SH_IMG_BUTTON_BLUE, c=white, label="Continue", hover-y=2, disable-shake=true, sound=SH_SND_BUTTON:SH_SND_CLICK_DISABLED /]

```

In the above example, `snd_enabled` will play when the user clicks the button when it's not disabled. If the button *is* disabled, `snd_disabled` will play.

___
#### disable-shake
Should the canvas shake horizontally when a disabled button is pressed? Not enabled by default. Shake values are defined in `ShampooConfig` under the section "SHAKING".

type: `bool`
___
#### thickness
Certain tags, like horizontal rules and function links, accept this attribute. It affects the thickness of lines. For horizontal rules, thickness affects the colored line thickness and for links, thickness affects the underline thickness on hover.

type: `number`

example(s):
```
[hr, h=12, thickness=4, c=#2980B9 /]
```
```
[function, src=test_callback_function, underline=blue, th=2, c=blue /]
```

### Meta
Meta attributes are attributes that work exclusively with the `meta` tag. Meta attributes affect the entire canvas.

#### version
For compatibility purposes and future-proofing, you can define which version a particular menu is.

type: `string`, default: `SH_VERSION_NUMBER`
___
#### x,xpos
Canvas left-x coordinate on GUI layer

type: `number`, default: `16`
___
#### y,ypos
Canvas left-y coordinate on GUI layer

type: `number`, default: `16`
___
#### w,width
Width of canvas, in pixels

type: `number`, default: `320`
___
#### h,height
Height of canvas, in-pixels

type: `number`, default: `480`
___
#### halign
Horizontal alignment of canvas at left-x coord (e.g. fa_center shifts canvas x by -50%canvas width; fa_right, -100%)

type: `align`, default: `fa_left`
___
#### valign
Vertical alignment of canvas at top-y coord (e.g. fa_middle shifts canvas y by -50% canvas height; fa_bottom, -100%)

type: `align`, default: `fa_top`
___
#### depth
Depth of canvas (*not* to be confused with GM's `depth`), used for rendering and click priority; highest->lowest, where lowest numbers are rendered at the "front"

type: `number`, default: `0`
___
#### onLoad
Function to call the first step the canvas is created

type: `function`, default: `undefined`
___
#### onUpdate
Function to call whenever an input (checkbox, radio button, slider, textarea, etc.) is changed

type: `function`, default: `undefined`
___
#### onClose
Function to call the last step of the canvas existing

type: `function`, default: `undefined`
___
#### onStep
Function to call each game step the canvas exists

type: `function`, default: `undefined`
___
#### onDraw
*Draw* function to call each game step the canvas exists; `x1`, `y1`, `x2`, `y2` reference bounding coords of canvas, respectively

type: `function`, default: `undefined`
___
___
#### onDrag
Function called while element is being dragged

type: `function`, default: `undefined`
___
#### shadow-x
Relative x-coord of shadow prior to shadow blur radius being applied

type: `number`, default: `0`
___
#### shadow-y
Relative y-coord of shadow prior to shadow blur radius being applied

type: `number`, default: `24`
___
#### shadow-color
Color of shadow

type: `color`, default: `SH_BLACK`
___
#### shadow-alpha
Transparency of shadow (0-100), where 0 is invisible and 100 is fully-visible

type: `number`, default: `50`
___
#### shadow-rad
Spread of shadow; how many pixels the shadow extends in all directions outside of the canvas bounds

type: `number`, default: `64`
___
#### pad,padding
Padding, in pixels, between canvas content and border of canvas

type: `number`, default: `16`
___
#### rad,radius
How rounded the corners of the canvas are, in pixels (where radius of corner "circles" = value)

type: `number`, default: `16`
___
#### coverup
If true, a full-screen rectangle is drawn beneath the canvas "blocking" input to canvases beneath

type: `bool`, default: `false`
___
#### coverup-color
Color of the full-screen rectangle

type: `color`, default: `SH_BLACK`
___
#### coverup-alpha
Transparency (0-100) of the full-screen rectangle, where 0 is invisible and 100 is fully-visible

type: `number`, default: `60`
___
#### fragile
Should the canvas be closed automatically when it 1) has priority 2) a click occurs outside of its bounds

type: `bool`, default: `false`
___
#### c,color,,colour
Canvas text color

type: `color`, default: `SH_BLACK`
___
#### bg
canvas color background, only visible should no background image be set -- otherwise, the background is blended this color

type: `color`, default: `c_white`
___
#### img,image
Canvas background image

type: `sprite`, default: `undefined`
___
#### tiled
Whether to tile the background image; if false, the image will be stretched

type: `bool`, default: `true`
___
#### 9slice
Whether to 9-slice and repeat the background image; overrides background tile bool

type: `bool`, default: `false`
___
#### delay
Number of steps before canvas is visible and interactive after being created

type: `number`, default: `0`
___
#### fadeIn
Number of steps before canvas becomes fully visible upon creation (0->1 alpha over this number of steps)

type: `number`, default: `0`
___
#### fadeOut
Number of steps before canvas becomes fully invisible upon destruction (1->0 alpha over this number of steps)

type: `number`, default: `0`
___
#### sndOpen
Sound that plays upon a canvas being opened

type: `sound`, default: `undefined`
___
#### sndClose
Sound that plays upon a canvas being closed

type: `sound`, default: `undefined`
___
#### openEaseX
Horizontal ease function when canvas is created

You can pass three arguments to this attribute as a shorthand way to combine this attribute, `openEaseXDuration`, and `openEaseXDuration` together.

type: `ease`, default: `ShEase.easeLinear`

example(s):
```
[meta, openEaseX=linear:50,room_speed /]
```
___
#### openEaseY
Vertical ease function when canvas is created

You can pass three arguments to this attribute as a shorthand way to combine this attribute, `openEaseYDuration`, and `openEaseYDuration` together.

type: `ease`, default: `ShEase.easeLinear`

example(s):
```
[meta, openEaseY=linear:50,room_speed /]
```
___
#### closeEaseX
Horizontal ease function when canvas is destroyed

You can pass three arguments to this attribute as a shorthand way to combine this attribute, `closeEaseXDuration`, and `closeEaseXDuration` together.


type: `ease`, default: `ShEase.easeLinear`

example(s):
```
[meta, closeEaseX=linear:50,room_speed /]
```
___
#### closeEaseY
Vertical ease function when canvas is destroyed

You can pass three arguments to this attribute as a shorthand way to combine this attribute, `closeEaseYDuration`, and `closeEaseYDuration` together.

type: `ease`, default: `ShEase.easeLinear`

example(s):
```
[meta, closeEaseY=linear:50,room_speed /]
```
___
#### openEaseXDuration
Horizontal ease duration (in steps) when canvas is created

type: `number`, default: `room_speed`
___
#### openEaseYDuration
Vertical ease duration (in steps) when canvas is created

type: `number`, default: `room_speed`
___
#### closeEaseXDuration
Horizontal ease duration (in steps) when canvas is destroyed

type: `number`, default: `room_speed`
___
#### closeEaseYDuration
Vertical ease duration (in steps) when canvas is destroyed

type: `number`, default: `room_speed`
___
#### openEaseXOffset
x-coord relative to canvas position; where canvas horizontally eases from when created

type: `number`, default: `0`
___
#### openEaseYOffset
y-coord relative to canvas position; where canvas vertically eases from when created 

type: `number`, default: `0`
___
#### closeEaseXOffset
x-coord relative to canvas position; where canvas horizontally eases from when destroyed

type: `number`, default: `0`
___
#### closeEaseYOffset
y-coord relative to canvas position; where canvas vertically eases from when destroyed

type: `number`, default: `0`

___
#### draggable
is the canvas draggable? if yes, it can be moved around the screen; will set meta attribute `rad` to 0

type: `bool`, default: `SH_DEFAULT_DRAGGABLE`
___
#### drag-height
height, in px, of draggable caption area

type: `number`, default: `SH_DEFAULT_CAPTION_HEIGHT`
___
#### caption
string in caption area

type: `number`, default: `SH_DEFAULT_CAPTION`
___
#### caption-color
color of caption string

type: `number`, default: `SH_DEFAULT_CAPTION_COLOR`
___
#### caption-color-hover
color of caption string while being dragged

type: `number`, default: `SH_DEFAULT_CAPTION_COLOR_HOVER`
___
#### caption-halign
horizontal alignment of caption string

type: `ALIGN`, default: `SH_DEFAULT_CAPTION_HALIGN`
___
#### caption-bg
 background color of caption

type: `color`, default: `SH_DEFAULT_CAPTION_BKG_COLOR`
___
#### caption-bg-hover
background color of caption while being dragged

type: `color`, default: `SH_DEFAULT_CAPTION_BKG_COLOR_HOVER`
___
#### ticker-color
ticker color on text input

type: `color`, default: `SH_DEFAULT_CAPTION_BKG_COLOR_HOVER`
___
#### placeholder-color
placeholder text color

type: `color`, default: `SH_TEXTBOX_PLACEHOLDER_COLOR`
___
#### placeholder-color-focus
placeholder text color while in focus

type: `color`, default: `SH_TEXTBOX_PLACEHOLDER_COLOR_FOCUS`

### User Input and Capturing
This section builds off the concepts of textareas, checkboxes, toggles, radio buttons, and sliders already established in the Tags section.

Whenever an input type is updated (its value is now different from its value last frame), the function `sh_var_catch` (inside `ShampooInserts` script) is called alongside the optional meta `onUpdate` callback function. Inside `sh_var_catch`, you'll be able to communicate directly with the canvas and get any necessary input values.

Notice the script has `canvas` as its argument. This variable is the canvas that was just updated. Using the `canvas.uid` in a switch statement, we can handle different canvases.

We can use a combination of the `sh_var_catch` function and the `sh_var_replacement` function discussed in the Variables and Dynamic Content section to store and set canvas input throughout the duration of the game.

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

example:
```
function sh_var_catch(canvas) {
	
	switch(canvas.uid) {
		case("main"):
			global.player_name = canvas.textarea_get_value("player_name");
			score = canvas.slider_get_value("player_score");
		break;
	}
	
}
```

### Variables and Dynamic Content
Sometimes you need more control over what content is rendered to a canvas. For example, suppose we want to display the player's name and score.

```
# Player: {$var}
## Score: {$var}
```

By writing `{$var}` we signal to the canvas processor that a variable should be inserted in place of that text. Unfortunately, this cannot be done via live-editing and requires manipulation of the `ShampooInserts` script, `sh_var_replacement`.

The `sh_var_replacement` script is a switch statement that is called a single time during the canvas creation process. Each case in the switch statement is a canvas UID. Each case should return an array of values. Each value in the array will replace the nth `{$var}`, where n is the index.

Going back to the player and score example. Suppose the canvas uid is `gameover`. Then, your `sh_var_replacement` function may look like the following:
```
function sh_var_replacement(uid) {
	
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
function sh_var_replacement(uid) {
	
	switch(uid) {
		case("list"):
			return [global.store_name, global.banana, global.apple, global.orange];
	}
	
	return undefined;
}
```

```
function sh_var_catch(canvas) {
	
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

### Symbols
You can insert symbols in-line with text. Graphics are courtesy of [BoxIcons](https://boxicons.com). Symbols will inherit the current text color and text. Symbols stem from the included framework sprite, `SH_IMG_SYMBOLS`. To insert a symbol in text, simply write `&symbX;`, where X is the frame number of sprite `SH_IMG_SYMBOLS` you wish to insert. For example, if you want to insert the first image between two words, you'd write the following:


```
[p]Hello&symb0;world![/p]
```

To further elaborate:
```
&nbsp0; first frame
&nbsp1; second frame
and lastly, &nbsp2; third frame
```

### Classes
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

### Character Escaping
Rather than use the forward slash to escape characters, Shampoo uses the `~` tilde to escape characters. The reasoning for this is more control over tokenization in-engine. The escape character can be changed from the tilde to almost any other **single character** you desire in the `ShampooOverhead` script. However, cross-reference the "CHARACTER ESCAPING" section to ensure that your escaped character does **not** appear in any escaped placeholder strings in the section. Otherwise, things will certainly break.

You can escape a newline from being created by ending a given line with the escape character. You can escape the escape character by writing two escape characters in a row.

Perhaps most importantly, you will need to escape from commas in quotes within a tag. Otherwise, the tokenizer will think you're ending the current attribute value. While this is not ideal, it drastical speeds up the tokenization process.

See [User Input and Capturing](#user-input-and-capturing) for important information regarding textarea input and which characters are invalid.

```
[textbox, value="This value~, right here~, has commas" /]
[button, label="Hello~, World!", bg=blue, radius=5, c=white /]
```

### Spaces and Linebreaks
This section builds upon the concepts of `br` break tags, `hr` horizontal rule tags, and `sp` spacer tags discussed earlier in the Attributes section.

The tokenizer will strip all double spaces from text content and replace them with a single space. If this is not desired, consider using `&nbsp;' in-string. This allows for unlimited spaces.

The tokenizer will split words at spaces ` ` and dashes `-`. Any spaces that appear at the start or end of a line (post-format during the canvas generation process) will be hidden. The horizontal space these hidden tokens occupy will not be taken into account when it comes to left, right, or center alignments of contents on a row-by-row basis.

### SFX and Sound Toggling
Use function `shampoo_sound_enable(bool)` to enable or disable sound effects being played via the Shampoo `sh_play_sound` function. You may want to tie this function call to your existing sound toggle logic. Or, simple bypass using this function call and directly set globalvar `SH_SOUND_ENABLED` though that is not recommended for Shampoo future-proofing.

### Contact
If you wish to report a bug, contact, or support the Shampoo developer, send an email to zbanack (at) gmail (dot) com or reach out on Twitter @zackbanack.

### FAQ and Reminders
##### My `{$var}` isn't being parsed out of the document
	- Try adding a space after `{$var}` for singleton tags, like `slider`, or value may not be tokenized correctly
	- Make sure you're wrapping quotes, where necessary
	- Make sure you're escaping certain characters, like commas and nested quotes

#### Misc
- If you want two buttons to be side by side, make sure you set the "height" attribute to the exact same value otherwise a line break is forced for mixed content heights
- Make sure commas, colons, brackets, and quotes are escaped correctly in strings
- Make sure to disable file sandbox is checked in Global Game Settings
- Don't forget quotes around textarea `value`, even if using `numbersOnly` attribute
- Callback functions need to be in global scope
- If TESTLOAD.txt isn't loading, make sure everything is written on one line with no linebreaks
- Use `SH_THIS_CANVAS` to reference the canvas at *this* scope
- Ensure TESTLOAD.txt and CLASSES.txt are placed in your `shampoo` live-code directory and Included Files Directory
- Do not name .txt files `self` or `all`
