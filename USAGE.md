# Usage

## Content
* [Events](#events)
* [Structure](#structure)
* [Windows](#windows)
  - [Parent](#parent-window)
  - [Text](#text-window)
  - [Grid](#grid-window)

## Events
This library implements a synchronous event-driven architecture, where events like keypresses and screen resizing trigger functions which executes code and updates the interface. The event handlers can be configurated directly when the object is created.
```c
tui_t* tui = tui_create((tui_config_t)
{
  ...
  .event.init = &tui_init,
  .event.key  = &tui_key,
  ...
});
```

```c
tui_menu_t* menu = tui_menu_create(tui, (tui_menu_config_t)
{
  ...
  .event.init  = &menu_init,
  .event.key   = &menu_key,
  .event.enter = &menu_enter,
  .event.exit  = &menu_exit,
  ...
});
```

```c
tui_window_parent_t* window = tui_window_parent_create(tui, (tui_window_parent_config_t)
{
  ...
  .event.init   = &window_init,
  .event.free   = &window_free,
  .event.key    = &window_key,
  .event.enter  = &window_enter,
  .event.exit   = &window_exit,
  .event.update = &window_update,
  .event.render = &window_render,
  ...
});
```

### Init
The init event is triggered just after the object is created, and is used to initialize the object and create and configure child objects. In the case of windows, this is an opportunity to allocate memory for custom data.

```c
void tui_init(tui_t* tui)
{
  // ex: create menus and windows
}
```

```c
void menu_init(tui_menu_t* menu)
{
  // ex: create windows
}
```

```c
void window_init(tui_window_t* window)
{
  // ex: create child windows
  // ex: allocate custom data
}
```

### Key
The key event is triggered on keypress, which lets the user interact with the application, by processing keys and executing different pieces of code.
```c
void tui_key(tui_t* tui, int key) { ... }

void menu_key(tui_menu_t* menu, int key) { ... }

void window_key(tui_window_t* window, int key) { ... }
```

### Enter & Exit
When the current window / menu is sat, the enter event is triggered for the new window / menu and the exit event is triggered for the previous window / menu.
```c
void menu_enter(tui_menu_t* menu) { ... }

void menu_exit(tui_menu_t* menu) { ... }
```

```c
void window_enter(tui_window_t* window) { ... }

void window_exit(tui_window_t* window) { ... }
```

### Update
The update event is triggered on screen resize, and offers the opportunity to make structural changes for keeping a responsive design.
```c
void window_update(tui_window_t* window)
{
  // ex: edit text
  // ex: add / remove child window
}
```

### Render
The render event is triggered just before the window is rendered to the screen, and offers the opportunity to make **visual changes** to the window. Crucially, no structural changes are allowed to take place that changes the size of the content, because this would break all render alignment.
```c
void window_render(tui_window_t* window)
{
  // ex: change color of text / border
  // ex: change grid of symbols
}
```

### Free
The free event is triggered just before the window object is destroyed, and is used to free any custom data tied to the window.
```c
void window_free(tui_window_t* window)
{
  // free custom data
}
```

## Structure
The terminal user interface is represented by a tui object that contains windows. Windows can either have text, grid of squares or contain windows of it's own. This makes it possible to nest windows to create complex designs. Multiple collections of windows can be stored as menus. What follows are slightly modified structs that represents the relationship between the tui object, the menus and the windows.

The tui object is what represents the terminal user interface. It contains both normal windows and menus with seperate windows in them.
```c
typedef struct tui_t
{
  ...
  tui_window_t** windows;
  tui_menu_t**   menus;
  ...
} tui_t;
```

A menu object is just a collection of windows. These collections can be used as different pages (menus) in an application. A reference to the tui object, which the menu is a part of, is stored for backwards access.
```c
typedef struct tui_menu_t
{
  ...
  tui_t*         tui;
  tui_window_t** windows;
  ...
} tui_menu_t;
```

A window object contains some content and is positioned somewhere on the screen, specifically inside the space of it's parent - either the tui itself, a menu or a parent window. The content of a window can either be text, grid of squares or child windows. References to possible parent window or menu and tui object is stored for backwards access.
```c
typedef struct tui_window_t
{
  ...
  tui_t*               tui;
  tui_menu_t*          menu;
  tui_window_parent_t* parent;
  tui_window_t**       children;
                    // content
  ...
} tui_window_t;
```

## TUI

```c
typedef struct tui_config_t
{
  tui_color_t color;
  tui_event_t event;
} tui_config_t;
```

```c
tui_t* tui_create(tui_config_t config)
```

```c
void tui_start(tui_t* tui)
```

```c
void tui_stop(tui_t* tui)
```

```c
void tui_delete(tui_t** tui)
```

## Menu

```c
typedef struct tui_menu_config_t
{
  char*            name;
  tui_color_t      color;
  tui_menu_event_t event;
} tui_menu_config_t;
```

```c
tui_menu_t* tui_menu_create(tui_t* tui, tui_menu_config_t config)
```

## Set Window
```c
void tui_menu_set(tui_t* tui, tui_menu_t* menu)
```

```c
void tui_window_set(tui_t* tui, tui_window_t* window)
```

```c
int tui_menu_window_search_set(tui_menu_t* menu, char* search)
```

## Grid Window

### Create
```c
typedef struct tui_window_grid_config_t
{
  char*              name;
  tui_window_event_t event;
  tui_rect_t         rect;
  bool               w_grow;
  bool               h_grow;
  tui_color_t        color;
  bool               is_hidden;
  bool               is_atomic;
  bool               is_interact;
  bool               is_contain;
  tui_size_t         size;
  void*              data;
} tui_window_grid_config_t;
```

```c
tui_window_grid_t* tui_window_grid_create(tui_t* tui, tui_window_grid_config_t config)
```

```c
tui_window_grid_t* tui_menu_window_grid_create(tui_menu_t* menu, tui_window_grid_config_t config)
```

```c
tui_window_grid_t* tui_parent_child_grid_create(tui_window_parent_t* parent, tui_window_grid_config_t config)
```

```c
typedef struct tui_window_grid_square_t
{
  tui_color_t color;
  char        symbol;
} tui_window_grid_square_t;
```

```c
void tui_window_grid_square_modify(tui_window_grid_t* window, int x, int y, tui_window_grid_square_t square)
```

```c
void tui_window_grid_square_set(tui_window_grid_t* window, int x, int y, tui_window_grid_square_t square)
```

```c
tui_window_grid_square_t* tui_window_grid_square_get(tui_window_grid_t* window, int x, int y)
```

## Text Window
### Create
```c
typedef struct tui_window_text_config_t
{
  char*              name;
  tui_window_event_t event;
  tui_rect_t         rect;
  bool               w_grow;
  bool               h_grow;
  tui_color_t        color;
  bool               is_hidden;
  bool               is_atomic;
  bool               is_interact;
  bool               is_contain;
  char*              string;
  bool               is_secret;
  tui_pos_t          pos;
  tui_align_t        align;
  void*              data;
} tui_window_text_config_t;
```

```c
tui_window_text_t* tui_window_text_create(tui_t* tui, tui_window_text_config_t config)
```

```c
tui_window_text_t* tui_menu_window_text_create(tui_menu_t* menu, tui_window_text_config_t config)
```

```c
tui_window_text_t* tui_parent_child_text_create(tui_window_parent_t* parent, tui_window_text_config_t config)
```

### Struct
```c
typedef struct tui_window_text_t
{
  ..
  char*       string;
  bool        is_secret;
  tui_pos_t   pos;
  tui_align_t align;
  ...
} tui_window_text_t;
```

```c
void tui_window_text_string_set(tui_window_text_t* window, char* string)
```

### Input Window
```c
typedef struct tui_input_t
{
  char*              buffer;
  size_t             buffer_size;
  size_t             buffer_len;
  size_t             cursor;
  size_t             scroll;
  tui_window_text_t* window;
  tui_t*             tui;
} tui_input_t;
```

```c
tui_input_t* tui_input_create(tui_t* tui, size_t size, tui_window_text_t* window)
```

```c
bool tui_input_event(tui_input_t* input, int key)
```

```c
void tui_input_delete(tui_input_t** input)
```

## Parent Window
### Create

```c
typedef struct tui_window_parent_config_t
{
  char*              name;
  tui_window_event_t event;
  tui_rect_t         rect;
  bool               w_grow;
  bool               h_grow;
  tui_color_t        color;
  bool               is_hidden;
  bool               is_atomic;
  bool               is_interact;
  bool               is_contain;
  tui_border_t       border;
  bool               has_shadow;
  bool               has_padding;
  bool               has_gap;
  tui_pos_t          pos;
  tui_align_t        align;
  bool               is_vertical;
  void*              data;
} tui_window_parent_config_t;
```

```c
tui_window_parent_t* tui_window_parent_create(tui_t* tui, tui_window_parent_config_t config)
```

```c
tui_window_parent_t* tui_menu_window_parent_create(tui_menu_t* menu, tui_window_parent_config_t config)
```

```c
tui_window_parent_t* tui_parent_child_parent_create(tui_window_parent_t* parent, tui_window_parent_config_t config)
```

### Struct
```c
typedef struct tui_window_parent_t
{
  ...
  tui_window_t** children;
  size_t         child_count;
  bool           is_vertical;
  tui_border_t   border;
  bool           has_shadow;
  bool           has_padding;
  bool           has_gap;
  tui_pos_t      pos;
  tui_align_t    align;
  ...
} tui_window_parent_t;
```

### List Window
```c
typedef struct tui_list_t
{
  tui_window_t** items;
  size_t         item_count;
  size_t         item_index;
  bool           is_vertical;
  tui_t*         tui;
} tui_list_t;
```

```c
tui_list_t* tui_list_create(tui_t* tui, bool is_vertical)
```

```c
void tui_list_delete(tui_list_t** list)
```

```c
bool tui_list_event(tui_list_t* list, int key)
```

```c
int tui_list_item_add(tui_list_t* list, tui_window_t* item)
```

## Colors
**BLACK** `TUI_COLOR_BLACK` <br>
**DARKRED** `TUI_COLOR_DARKRED` <br>
**DARKGREEN** `TUI_COLOR_DARKGREEN` <br>
**DARKYELLOW** `TUI_COLOR_DARKYELLOW` <br>
**DARKBLUE** `TUI_COLOR_DARKBLUE` <br>
**PURPLE** `TUI_COLOR_PURPLE` <br>
**AQUA** `TUI_COLOR_AQUA` <br>
**GRAY** `TUI_COLOR_GRAY` <br>
**DARKGRAY** `TUI_COLOR_DARKGRAY` <br>
**RED** `TUI_COLOR_RED` <br>
**GREEN** `TUI_COLOR_GREEN` <br>
**YELLOW** `TUI_COLOR_YELLOW` <br>
**BLUE** `TUI_COLOR_BLUE` <br>
**MAGENTA** `TUI_COLOR_MAGENTA` <br>
**CYAN** `TUI_COLOR_CYAN` <br>
**WHITE** `TUI_COLOR_WHITE`

## Position
`TUI_POS_START` <br>
`TUI_POS_CENTER` <br>
`TUI_POS_END`

## Alignment
`TUI_ALIGN_START` <br>
`TUI_ALIGN_CENTER` <br>
`TUI_ALIGN_END` <br>
`TUI_ALIGN_BETWEEN` <br>
`TUI_ALIGN_AROUND` <br>
`TUI_ALIGN_EVENLY`
