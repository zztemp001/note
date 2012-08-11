## 包含
```css
<link rel="stylesheet" href="css/blueprint/screen.css" type="text/css" media="screen, projection">  
<link rel="stylesheet" href="css/blueprint/print.css" type="text/css" media="print">      
<!--[if IE]><link rel="stylesheet" href="css/blueprint/ie.css" type="text/css" media="screen, projection"><![endif]--> 
```
```
By default, the grid is 950px wide, with 24 columns spanning 30px, and a 10px margin between columns.
```

## 使用插件
```css
<link rel="stylesheet" href="blueprint/plugins/fancy-type/screen.css" type="text/css" media="screen, projection" /> 
```
  
## 一般使用
```html
<div class="container">  
    <div class="span-24">  
        Header  
    </div>  
    <div class="span-4">  
        Left sidebar  
    </div>  
    <div class="span-16">  
        Main content  
    </div>  
    <div class="span-4 last">  
        Right sidebar  
    </div>      
</div> 
```
```
Notice the “last” class, which every last element inside a container or another column needs.
```

## 递归使用
```html
<div class="container">  
    <div class="span-24">  
        Header  
    </div>  
    <div class="span-4">  
        Left sidebar  
    </div>  
  
    <div class="span-16">  
        <div class="span-8">  
            Box1  
        </div>  
        <div class="span-4">  
            Box2  
        </div>  
        <div class="span-4 last">  
            Box3  
        </div>  
        <div class="span-16 last">  
            Main content  
        </div>  
    </div>  
  
    <div class="span-4 last">  
        Right sidebar  
    </div>  
    <div class="span-24">  
        Footer  
    </div>  
</div> 
```
```
Use the “last” class in your last column
Make sure the columns in a row add up to 24 (i.e. “last” does not “fill in” the remaining columns for you)
```
```
Grid.css can do a lot more than this, however. 
You can prepend and append empty columns, 
pull or push images across columns, 
add borders between columns, 
and use multiple containers to create almost any layout.
Check out the comments in grid.css and the example pages for more information.
```