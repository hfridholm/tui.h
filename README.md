# tui.h

A library written in C using ncurses for creating Terminal User Interfaces (TUI) with menus and windows. It is built with the infrastructure necessary for more complex designs and provides tools that lets the user create the application of their dreams.

## Features
* No installation [needed\*](#install)
* Multiple pages of windows ([menus](USAGE.md#menus))
* Built-in responsive layout manager
* Transparent background support
* [Event](USAGE.md#events) driven architecture
* Parent-child window [relationship](USAGE.md#structure)
* z-depth illusions (indentation and shadows)
* 16 available [colors](USAGE.md#colors) (256 fg-bg combinations)

## Projects
### [stocks](https://github.com/hfridholm/stocks) by hfridholm

![stocks image](https://github.com/hfridholm/stocks/raw/master/screenshot1.png)

## Install
This is a Header Library, also known as Header-Only library, which means that the whole library is contained in a single header file (tui.h). Therefor, all\* you need to install the library is to download the [header file](tui.h), either by clicking the download button or running the following command.

```bash
wget https://raw.githubusercontent.com/hfridholm/tui.h/refs/heads/master/tui.h
```

### SHA256
```
4d9bcefe5a00401d6a72a517a9c414cdbaaddae46a8fbedb712314f31dd8d34f  tui.h
```

This library depends on the `ncurses` library. Make sure it is installed when compiling your program. The following are commands for installing ncurses with pacman on Arch and apt on Debian based distros.

### Arch
```bash
sudo pacman -S ncurses
```

### Debian (Ubuntu, Linux Mint)
```bash
sudo apt install ncurses
```

## Setup
To use the tui.h library in your project, simply include it in every source file that should have access to it. **It is recommended** to put the tui.h file directly in the source directory.
```c
#include "tui.h"
```

**One** of the `.c` files (preferably the one with the `main` function) should define the implementation. It is crucial that TUI_IMPLEMENTATION is defined just before the **last** include of tui.h in the whole compilation unit.
```c
#define TUI_IMPLEMENTATION
```

If you are unsure where to place the #include and the #define, just put them in the following order in your `main.c` file, after the includes and before your code.
```c
// includes

#define TUI_IMPLEMENTATION
#include "tui.h"

// your code
```

## Usage
The basis is that you create a tui object, then start it, which starts your application. When you want to stop your application, stop the tui. Lastly, delete the tui object.
```c
tui_t* tui = tui_create((tui_config_t)
{
  ...
});

tui_start(tui);

tui_stop(tui);

tui_delete(&tui);
```

This library deserves a more in-depth explaination of the architecture and the usage. Therefore, check out [USAGE.md](USAGE.md) for more details.
