# Lua-Widgets
**Widgets for embedding user input fields**

Need real-time input for your lua script?  Look no further!  This is an easy to use module for grabbing user input.  Simply download the repository, open it, and move `Widgets.lua` into the same folder as your lua script.  Write `require "Widgets"` at the top of your script, and write `Widgets:draw()` in the main loop.

To create widgets, set a variable equal to the type of widget you want, followed by parentheses.  
Example: `inputfield = InputBox()` This creates an input box named inputfield that you can click on and write in.  Creation only needs to happen once.  Any widgets created in this way will automatically be drawn to the screen every frame.  You can delete them by calling their `del` method: `inputfield:del()`.  

Example Usage:
```
require "Widgets"

b1 = Button(20, 20, "button 1")          -- creates a button widget
b1.func = function() print("wakawaka") end   -- assigns a function to b1 that runs when clicked

while true do 
    Widgets:draw()  -- draws all widgets to the screen
    emu.frameadvance()
end
```

**Any attributes may be modified or read using dot notation:**  `name.attrib = example`  
**Methods may be called using colon notation:** `name:method(args)`  

**Hold `ctrl` + `shift`, and click on widgets to drag them around on screen.**
- you can move multiple widgets at once by clicking on more than one
- you can deselect moving widgets by right-clicking on them  

**Hold `delete` and click on widgets to delete them.**


# Supported Widgets:
- [InputBox](#inputbox)
- [TextBox](#textbox)
- [CheckBox](#checkbox)
- [Button](#button)
- [Radio](#radio)
- [Slider](#slider)
- [Window](#window)
- [Canvas](#canvas)



## InputBox
Creates a box where the user can type text

 **Parameters**  
 `InputBox (x, y, charlimit, text, flags)`
  - `x, y` - coordinates
  - `charlimit`  - max number of characters; set to -1 to make it unlimited
  - `text` - the text in the box
  - `flags` - 4 bits that determine the following settings:
    - 0x1 : the text will clear itself when you press enter
    - 0x2 : the box will be deselected when you press enter
    - 0x4 : the box will be hidden, but the text will be there
    - 0x8 : Active/Inactive will be the same color
    
**Colors**
- `colors_active = {"#FFFFFFFF", "#000000FF", "#C0C0C0C0", "#000000FF"}`; text, text border, box, box border
- `colors_inactive = {"#C0C0C080", "#808080FF", "#C0C0C080", "#00000040"}`; text, text border, box, box border

**Info**  
- You can click on the box to begin typing.  Press `ctrl`+`backspace` to delete everything left of your cursor, or `ctrl`+`delete` to delete everything to the right.  `Home` and `End` work as you'd expect them to.  
- You can bind functions to it by modifying its **`func`** attribute.  Pressing `enter` will call that function, with the box's text as the first argument.
- Press up to view the history of the input box

- The file includes a global function called **`exec`**, that is designed for this widget.  It will execute the commands in the input field like a lua interpreter. Example: `inputfield = InputBox(10, 10); inputfield.func = exec`

- If you want to limit the accepted inputs, then you can modify its **`charlist`** attribute.  You can set it equal to any of the following built-in tables:
  - TotalCharList -- default, accepts all characters
  - LetterList -- letters only
  - NumberList -- numbers only (not numpad numbers)
  - HexNumberList -- hex numbers only
  - DirectionList -- "up","down","left","right"
  - NumPadList -- all the numpad keys
  - FunctionKeyList -- the function buttons

- You can create custom charlists by adding and subtracting these lists from each other.  
`inputfield.charlist = LetterList + NumberList` lets the box accept only letters and numbers.  

**Example**
```
inputfield = InputBox(10, 10)
inputfield.func = exec

while true do
  Widgets:draw()
  emu.frameadvance()
end
```

## TextBox  
**Parameters**  
`TextBox(x, y, text, anchor, color1, color2)`
- `x, y` - coordinates
- `text` - text
- `anchor` - may be be `0`, `1`, or `2`, for left, center, and right-alignment
- `color1, color2` - color and border color

**Info**  
- This is basically gui.text with some extra features.  Since it's also a widget, you can move it around like any others, and also embed it in other widgets.  

  
## CheckBox

**Parameters**  
`CheckBox(x,y)`
- `x, y` - coordinates

**Colors**
- `colors = {"#40FF40FF", "#C0C0C0FF", "#000000FF"}`; marking color, box, box border

**Info**  
- Creates a checkbox that can be toggled on or off.  The **func** attribute may be set to cause it to call its function whenever its state is changed.  It sends its current state as the first argument to that function.

- The **style** attribute may be `"x"` or `"check"`, and determines whether to make it an `x` mark or a `check` mark.


## Button  

**Parameters**  
`Button(x, y, text, func)`
- `x, y` - coordinates
- `text` - the text on the button
- `func` - the function called when this button is clicked

**Colors**
- `colors_unclicked = {"#FFFFFFE0", "#000000E0", "#606060FF", "#B0B0B0FF"}`; text, text border, box, box border
- `colors_clicked = {"#FFFFFFFF", "#000000FF", "#C0C0C0FF", "#A0A0A0FF"}`; text, text border, box, box border


## Radio  

**Parameters**  
`Radio(x, y, options)`
- `x, y` - coordinates
- `options` - a table of strings that comprise the options of this widget  

**Colors**
- `colors_txt = {"#FFFFFFFF", "#000000FF"}`; text, text border
- `colors_rad = {"#404040FF", "#000000FF", "#C0C0C0FF", "#303030FF"}`; button, button border, active button, active button border

**Info**  
- Creates a list of options, of which only one may be chosen at a time
- Setting its **`func`** attribute causes it to call that function each time the option is changed.  It sends the text of that option as the first argument for the function.  


## Slider  
  
**Parameters**  
`Slider(x, y, left, right, step, orientation, length)`
- `x, y` - coordinates
- `left, right` - the value limits of the slider
- `step` - the increment size of the slider; must be positive; may be fractional
- `orientation` - may be "h" or "v" for "horizontal" or "vertical"
- `length` - the length of the slider in pixels

**Colors**  
- `color_line = "#000000FF"` 
- `color_slide = {"#C0C0C0FF", "#000000FF"}`

**Info**  
- Creates a slider that can be clicked and dragged to modify its value.  To acquire that value, you can check its `value` attribute.  You can also set its `func` attribute, which calls a function each time the slider is moved, and sends its value as the first argument.  


## Window  

**Parameters**  
`Window(x, y, data, charwidth, charheight)`
- `x, y` - coordinates
- `data` - the data contained in the window; may be a string, a table, or a function
- `charwidth` - how many characters wide the window should be
- `charheight` - how many characters tall the window should be

**Colors**  
- `colors_window = {"#C0C0C0C0", "#00000FF"}`; background, border
- `colors_scroll = {"#808080FF", "#E0E0E0FF", "#00000FF"}`; handle, background, border
- `colors_txt = {"#FFFFFFFF", "#000000FF"}`; text, text border

**Info**  
- Creates a window that displays data.  It has scroll bars, and a search feature
- Set its **`data`** attribute to display info on screen.  The data may be any table, string, or function.  If it's a table, it will format it for you.  Strings are displayed normally.  Functions should return either strings or tables, which it will display.
- You can select a line by clicking on it, which automatically sets the window's **`line`** attribute equal to the text of that line
- You can embed other widgets into this window, causing them to move around with it, and always be displayed in front of it
- If you don't need real time updates, you can set its **`auto_update`** attribute to `false`, which speeds it up significantly
- The file includes a global function called **`table_string`**, which this widget uses to convert table data to a string.
  - You can call it yourself with the parameters `data, sort, depth`.  `Data` is the table you're using, `Sort` is whether to sort it alphabetically or not, and `Depth` is the recursion depth to iterate through.  You can then set the `data` attribute of the widget equal
to the return of `table_string`

**Methods**  
- `embed(widget)` - embeds a widget into the window
- `detach(widget)` - detaches an embedded widget
- `search(text)` - search for text in the window; if found, the text will be highlighted and jumped to  

## Canvas  

**Parameters**  
`Canvas(x, y, width, height)`  
- `x, y` - coordinates of top left of drawing region
- `width, height` - the width and height of the drawing region

**Default Palette**
```
palette = {
        0x000000FF, 0xFFFFFFFF, 0x00000000, 0x808080FF, 
        0xC3C3C3FF, 0xB97A57FF, 0x880015FF, 0xED1C24FF, 
        0xFFAEC9FF, 0xFF7F27FF, 0xFFC90EFF, 0xFFF200FF, 
        0x22B14CFF, 0xB5E61DFF, 0x00A2E8FF, 0x99D9EAFF, 
        0x3F48CCFF, 0x7092BEFF, 0xA349A4FF, 0xC8BFE7FF, 
}
```

**Info**  
- Creates a canvas that's like a mini MS Paint
- Has 10 buttons:
  - pencil mode
  - brush mode
  - line mode
  - box mode
  - circle mode
  - triangle mode
  - fill
  - dropper
  - undo
  - clear
- Has a max undo history of 10 actions
- Transparent colors may be used on the palette, allowing you to draw on things in the background

**Attributes**
- `show` - set to false to hide the border and buttons
- `mode` - set to "pencil", "brush", "line", "box", "circle", "triangle", "fill", or "dropper" to pick a mode
- `color` - set to a number to pick a custom color
- `width, height` - set the dimensions of the canvas
- `background` - the default background color
- `palette` - the available palette

**Methods**
- `undo()` - undoes the last action
- `export(filepath)` - exports the canvas to the specified filepath in `.bmp` format
- `import(filepath)` - imports a bitmap from a `.bmp` file to the canvas

  
## Other Info  
- `Widgets` is a global variable that keeps track of all the widgets.  `Widgets:draw()` must be in your main loop.  You can access useful info such as which widgets are currently hovered over or selected by reading the `Widgets.hover` or `Widgets.selected` attributes.
  - The Widget Manager doesn't know what you named the widgets, so it gives each new widget an ID number as its `ID` attribute, and uses those to track them.  
- You can delete widgets programmatically by calling their `del()` method. `Example: inputfield:del()`
- If you want to bind more than one function to a widget, you'll have to make an intermediary function that calls more functions
  - Example: `inputfield.func = function(text) exec(text); print(text); parse(text) end`
- Widgets have a `selected` attribute, which is only active if the widget is currently selected, and is set to either `right` or `left` depending on which mouse button you clicked on it with
- If you want a smaller file, you can delete any unused widgets below the big `--WIDGETS--` comment in the code without breaking anything
