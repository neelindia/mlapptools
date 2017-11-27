![Minimum Version](https://img.shields.io/badge/Requires-R2016a%20%28v9.0%29%20or%20newer-orange.svg)

# mlapptools
`mlapptools` is a collection of static methods for customizing the R2016a-introduced web-based `uifigure` windows and 
associated UI elements through DOM manipulations. 

For additional information, see Iliya Romm's Undocumented Matlab guest article, 
[*Customizing uifigures part 2*](http://undocumentedmatlab.com/blog/customizing-uifigures-part-2), 
published Wednesday, September 7th, 2016.

#### Contact us
- Feature requests/suggestions and bug repots - please feel free to 
[open an issue](https://github.com/StackOverflowMATLABchat/mlapptools/issues) in the repository.
- General discussion - [_MATLAB and Octave_ chatroom on Stack Overflow](https://chat.stackoverflow.com/rooms/81987/).
- Specific questions about manipulating uifigures / App Designer apps - 
[`matlab-app-designer` tag on Stack Overflow](https://stackoverflow.com/questions/tagged/matlab-app-designer).
 
## Methods
[`aboutDojo`](#aboutDojo) - Return the dojo toolkit version.  
[`fontColor`](#fontColor) - Modify font color.  
[`fontWeight`](#fontWeight) - Modify font weight.  
[`getHTML`](#getHTML) - Return the full HTML code of a `uifigure`.  
[`getWebElements`](#getWebElements) - Extract a `webwindow` handle and a widget ID from a `uifigure` control handle.  
[`getWebWindow`](#getWebWindow) - Extract a `webwindow` handle from a `uifigure` handle.  
[`getWidgetInfo`](#getWidgetInfo) - Gather information about a specific dijit widget.  
[`getWidgetList`](#getWidgetList) - Gather information about all dijit widget in a specified `uifigure`.  
[`setStyle`](#setStyle) - Modify a specified style property.  
[`setTimeout`](#setTimeout) - Override the default timeout for dojo commands, for a specific `uifigure`.  
[`textAlign`](#textAlign) - Modify text alignment.  

<a name="aboutDojo"></a>
#### *mlapptools*.**aboutDojo**()
##### Description
Returns a `struct` containing version information about the Dojo toolkit loaded into the first open `uifigure`.
If no `uifigure` is open, a temporary window is created, queried, then closed - indicating the default Dojo version.

##### Examples

```matlab
>> mlapptools.aboutDojo()

ans = 

  struct with fields:

       major: 1
       minor: 11
       patch: 2
        flag: ''
    revision: '91fa0cb'
```

<a name="fontColor"></a>
#### *mlapptools*.**fontColor**(*uiElement*, *newcolor*)
##### Description
Set the font color of the specified UI element, `'uiElement'`, to the input color, `newcolor`. `newcolor` can be a 
predefined color string or a string containing a valid CSS color method call.

Valid color specifications are:
  * Hexadecimal colors - e.g. `'#ff0000'` for red
  * RGB colors - e.g. `'rgb(255,165,0)'` for orange
  * RGBA colors - e.g. `'rgba(255,255,0,0.3)'` for yellow
  * HSL colors - e.g. `'hsl(120, 100%, 50%)'` for green
  * HSLA colors - e.g. `'hsla(240,100%,50%, 1.0)'` for blue
  * Predefined color names - e.g. `'red'`, `'orange'`, `'yellow'`, `'green'`, `'blue'`, `'indigo'`, `'violet'`. 
  For more colors, see the predefined color names [CSS color specification](https://www.w3.org/TR/css3-color/).

##### Examples
Using the demo GUI generated by `./Demo/DOMdemoGUI.m`

```MATLAB
myGUI = DOMdemoGUI;
mlapptools.fontColor(myGUI.TextArea, 'aqua');
```

```MATLAB
myGUI = DOMdemoGUI;
mlapptools.fontColor(myGUI.TextArea, 'rgb(255,165,0)');
```


<a name="fontWeight"></a>
#### *mlapptools*.**fontWeight**(*uiElement*, *weight*)
##### Description
Set the font weight of the specified UI element, `uiElement`, to the input weight string or integer, `weight`.
For this setting to have an effect, the font being used must have built-in faces that match the specified weight.

Valid font weight property values are:
  * `'normal'` - Normal characters (default)
  * `'bold'` - Thick characters
  * `'lighter'` / `'bolder'` - The closest available "lighter" or "bolder" weight, relative to the parent.
  * `100 .. 900` - Integers mapping to `'normal'`, `'bold'`, etc. 
  Intermediate integers (and floats) are accepted but generally map to the available values
  * `'initial'` - Revert to default

##### Examples
Using the demo GUI generated by `./Demo/DOMdemoGUI.m`

```MATLAB
myGUI = DOMdemoGUI;
mlapptools.fontWeight(myGUI.TextArea, 'bold');
```

```MATLAB
myGUI = DOMdemoGUI;
mlapptools.fontWeight(myGUI.TextArea, 600);
```


<a name="getHTML"></a>
#### *mlapptools*.**getHTML**(*hUIFigure*)
##### Description
A method for obtaining the HTML code of a uifigure. Intended for R2017b (and onward?) where the CEF URL cannot be 
simply opened in a browser. The returned HTML is a deep copy, meaning that any changes will **not** modify the 
`uifigure` where it originated from.

##### Examples
Using the demo GUI generated by `./Demo/DOMdemoGUI.m`
```MATLAB
myGUI = DOMdemoGUI;
fullHTML = mlapptools.getHTML(myGUI.UIFigure);
web(['text://' fullHTML]);
```


<a name="getWebElements"></a>
#### *mlapptools*.**getWebElements**(*uiElement*)
##### Description
A method for obtaining the webwindow handle and the widget ID corresponding to the provided `uifigure` control. The 
widget ID can be used to call the 4-parameter variant of [`setStyle`](#setStyle).

##### Examples
```MATLAB
myGUI = DOMdemoGUI;
[win, widgetID] = mlapptools.getWebElements(myGUI.TextArea);
```


<a name="getWebWindow"></a>
#### *mlapptools*.**getWebWindow**(*hUIFigure*)
##### Description
A method for getting the webwindow handle associated with the provided `uifigure`.

##### Examples
Using the demo GUI generated by `./Demo/DOMdemoGUI.m`
```MATLAB
myGUI = DOMdemoGUI;
win = mlapptools.getWebWindow(myGUI.UIFigure);
```


<a name="getWidgetInfo"></a>
#### *mlapptools*.**getWidgetInfo**(*hWebwindow*, *widgetID*, *verbosityFlag*)
##### Description
Query the [dijit registry](https://dojotoolkit.org/reference-guide/dijit/registry.html#dijit-registry) for a widgets 
having a specific ID, and return its details in a scalar `cell` containing a `struct`.

##### Examples
Using the demo GUI generated by `./Demo/DOMdemoGUI.m`
```MATLAB
myGUI = DOMdemoGUI;
[win, widgetID] = mlapptools.getWebElements(myGUI.TextArea);
nfo = mlapptools.getWidgetInfo(win, widgetID);
```


<a name="getWidgetList"></a>
#### *mlapptools*.**getWidgetList**(*hUIFigure*, *verbosityFlag*)
##### Description
Query the [dijit registry](https://dojotoolkit.org/reference-guide/dijit/registry.html#dijit-registry) for all widgets 
within the current page, and return them in a cell array of structs.

##### Examples
Using the demo GUI generated by `./Demo/DOMdemoGUI.m`
```MATLAB
myGUI = DOMdemoGUI;
nfoList = mlapptools.getWidgetList(myGUI.UIFigure);
```


<a name="setStyle"></a>
#### *mlapptools*.**setStyle**(*uiElement*, *styleAttr*, *styleValue*)
##### Description
Set the style attribute `styleAttr` of the specified UI element, `'uiElement'`, to the value `styleValue`. `styleAttr` 
should be any valid CSS attribute, and `styleValue` a valid setting thereof. 

This method provides a general interface to change CSS style attributes, with minimal input testing and error reporting, 
so it is up to the user to provide valid inputs.

Valid style attributes and corresponding settings can be found [here](https://www.w3schools.com/cssref/).

##### Examples
Using the demo GUI generated by `./Demo/DOMdemoGUI.m`
```MATLAB
myGUI = DOMdemoGUI;
mlapptools.setStyle(myGUI.TextArea, 'background-image',...
 'url(https://upload.wikimedia.org/wikipedia/commons/8/80/Wikipedia-logo-v2.svg)');
```


<a name="setTimeout"></a>
#### *mlapptools*.**setTimeout**(*hUIFig*)
##### Description
Modify the amount of time allotted to dojo queries before they are considered "failed due to timeout". This value is
`uifigure`-specific. If left unmodified, the default value is `5` seconds.

##### Examples
```MATLAB
myGUI = DOMdemoGUI;
mlapptools.setTimeout(myGUI.UIFigure, 3); % This will wait less for dojo queries to finish.
```


<a name="textAlign"></a>
#### *mlapptools*.**textAlign**(*uiElement*, *alignment*)
##### Description
Set the horizontal text alignment of the specified UI element, `uiElement`, to that specified by the input alignment 
string, `alignment`. 

Valid alignment strings are:
  * `'left'` - Left align (default)
  * `'center'` - Center align
  * `'right'` - Right align
  * `'justify'` - Each line has equal width
  * `'initial'` - Revert to default

##### Examples
Using the demo GUI generated by `./Demo/DOMdemoGUI.m`

```MATLAB
myGUI = DOMdemoGUI;
mlapptools.textAlign(myGUI.TextArea, 'center');
```

```MATLAB
myGUI = DOMdemoGUI;
mlapptools.textAlign(myGUI.TextArea, 'right');
```