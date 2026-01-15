# Todos

## Quick Fixes
* why do tui_list_t have is_vertical? use parent.is_vertical?
* add bold and italic attribute to structs, beside tui_color_t
* in tui_children_rect_calc: don't make is_atomic windows invisable first
  (go through children again if align_size is larger than max_size and make !is_atomic windows invisable in first hand, then is_atomic windows if necessary)
