## 要设置容器的大小才能显示图表
* 可以在html中设置
```html
<div id="placeholder" style="width:600px;height:300px"></div>  
```
* 也可以在css中设置
* 可以通过js动态设置

## 基本用法
```js
// 基本使用形式  
$.plot($("#placeholder"), data, options);  
// 其中，data是一个数组，options是一个object，含有各种设置属性  
  
// 例子  
$.plot($("#placeholder"), [ [[0, 0], [1, 1]] ], { yaxis: { max: 1 } });  
  
// 程序立刻绘制图表，并返回一个 Plot object，通过此object可以进行进一步操作。  
```

## data的格式要求
* 数据是若干个数据系列的数组
```
[ series1, series2, ... ]
```
* 可以是原始数据，用[坐标, 值]对表示的系列
```
[ [x1, y1], [x2, y2], ... ]
[ [1, 3], [2, 14.01], [3.5, 3.14] ]

# 两者最好都使用数值，如果是通过json传递时发生问题，就要检查是否数据类型有问题
# 如果某一个值对或其中一个值包含null值时，或该点不能被转变为数值时，该点将不会被绘制，
# 可以使用这个特性来绘制有断点的图表
```
* data也可以是object的数组
```js
var data =  [ { label: "Foo", data: [ [10, 1], [17, -14], [30, 5] ] },  
              { label: "Bar", data: [ [11, 13], [19, 11], [30, -7] ] } ];  
```
* 每一个object的格式如下：
```js
  {  
    color:    // "rgb(255, 100, 123)" 的形式（字符串） 或者是代表颜色的数字（自己编写的颜色索引）  
    data:    // 原始数据，如上  
    label:    // 用字符串表示的系列名称，显示在数据凡例上，如果不指定，则不显示  
    lines:    // specific lines options  
    bars:    // specific bars options  
    points:    // specific points options  
    threshold:    // specific threshold options  
    xaxis:    // 1 or 2（使用上边的X轴）  
    yaxis:    // 1 or 2（使用右边的Y轴）  
    clickable:    // boolean （如果全局设定打开了互动，这里设为false则可使该系列取消互动）  
    hoverable:    // boolean （同上）  
    shadowSize:    // 用数值代表的阴影的大小  
  }  
  
// 通过给特定的系列赋予以上设置，则可让该系列绘制时有自己的特性，覆盖全局的options  
```

## options 的设置
```js
var options = {  
  // 凡例设置（事实上是一个label和颜色方块组成的列表）  
  legend: {  
    show: boolean    // 决定是否显示凡例  
    labelFormatter: null or (fn: string -> string)    // 通过此属性（函数）对凡例格式化，例子见下面注释  
  
    //   labelFormatter: function(label) {  
    //     return '<a href="' + label + '">' + label + '</a>';  
    //   }  
  
    labelBoxBorderColor: color    // 颜色边框的颜色（填充色已经在data中设定或默认）  
    noColumns: number    // 把凡例分成多少列显示  
    position: "ne" or "nw" or "se" or "sw"    // 凡例在图表中的摆放位置  
    margin: number of pixels    // 凡例距离图表边缘的像素数  
    backgroundColor: null or color    // 凡例的背景色  
    backgroundOpacity: number in 0.0 - 1.0    // 背景的透明度（0～1）  
    container: null or jQuery object    // 如果需要在图表之外显示凡例，则在这里设置放置的地方（JQuery object）  
  }  
  
  // 坐标轴都具有以下相同的选项，可以单独设置  
  xaxis, yaxis, x2axis, y2axis: {  
    mode: null or "time"    // 坐标轴解析数据的模式，null 表示正常的十进制数字，“time"（字符串）表示使用时间系列方式  
    min: null or number    // 手动指定坐标的最大、最小值，如果忽略，系统将自己根据data计算  
    max: null or number    // 同上  
    autoscaleMargin: null or number    // 坐标最大值点距离图表边缘的距离，用于避免该点超出边缘，缺省是x周为null，y周为0.02  
    labelWidth: null or number   // 指定坐标节点label的最大宽度和高度，对统一label的大小有帮助  
    labelHeight: null or number    // 同上  
  
    ticks: null or number or ticks array or (fn: range -> ticks array)    // 指定坐标轴节点的数量，null 为系统自动制定，0 为不显示  
    //   ticks 数组的格式，两者可混合使用  
    //   ticks: [0, 1.2, 2.4]  
    //   ticks: [[0, "zero"], [1.2, "one mark"], [2.4, "two marks"]]  
    //  
    //   也可以通过函数计算（接受一个包含axis.min 和 axis.max的object，返回一个数组）  
    //     function piTickGenerator(axis) {  
    //       var res = [], i = Math.floor(axis.min / Math.PI);  
    //       do {  
    //         var v = i * Math.PI;  
    //         res.push([v, i + "\u03c0"]);  
    //         ++i;  
    //       } while (v < axis.max);  
    //       return res;  
    //     }  
    tickSize: number or array    // 指定节点的距离，如2时，产生的数组大致是2、4、6、8...  
    minTickSize: number or array    // 如果节点的距离少于这个值，就不显示  
    tickFormatter: (fn: number, object -> string) or string    // 通过函数或字符串定制节点的样式  
    //   函数接受一个tick的值和一个代表axis的object，返回字符串  
    //     function suffixFormatter(val, axis) {  
    //       if (val > 1000000)  
    //         return (val / 1000000).toFixed(axis.tickDecimals) + " MB";  
    //       else if (val > 1000)  
    //         return (val / 1000).toFixed(axis.tickDecimals) + " kB";  
    //       else  
    //         return val.toFixed(axis.tickDecimals) + " B";  
    //     }  
    tickDecimals: null or number    // 指定数值的长度（位数）  
  }  
  
  // 线、点、条的样式可以全局指定，也可以在数据列中单独指定  
  lines, points, bars: {  
    show: boolean    // 决定是否显示  
    lineWidth: number    // 线宽，单位为素数  
    fill: boolean or number    // 是否填充图形，0～1表示填充的透明度  
    fillColor: color    // 填充色  
  }  
  
  points: {  
    radius: number    // 半径（单位为像素，点的独有属性）  
  }  
  
  bars: {  
    barWidth: number    // 条形的宽度（单位为坐标轴的unit，如在时间序列中，24 * 60 * 60 * 1000表示一天的宽度  
    align: "left" or "center"    // 条形的对齐，居中或居左，没有右  
  }  
  
  colors: [ color1, color2, ... ]    // 指定图表应用到的颜色列表，可以指定任意多的颜色，如不够用，系统会据此扩充亮度来添加  
  // colors: ["#d18b2c", "#dba255", "#919733"]  
  
  shadowSize: number    // 图形的阴影大小（单位为像素，0 为无阴影）  
  
  // 设置图表的线框（包括边框）  
  grid: {  
    color: color    // 线框颜色  
    backgroundColor: color or null    // 背景色  
    tickColor: color    // 坐标轴节点label的颜色  
    labelMargin: number    // 坐标轴label与边框的距离  
    markings: array of markings or (fn: axes -> array of markings)    // 可以通过函数个性化内部线框（不常用，详情见API）  
    borderWidth: number    // 外部边框的宽度  
    clickable: boolean    // 如果可以点击，就会在container中产生plotclick事件，并发送点击的pos和就近的数据item  
    hoverable: boolean    // 如果可以移动，产生plothover事件，发送的参数同上  
    //    $.plot($("#placeholder"), [ d ], { grid: { clickable: true } });  
    //    $("#placeholder").bind("plotclick", function (event, pos, item) {  
    //        alert("You clicked at " + pos.x + ", " + pos.y);  
    //        if (item) {  
    //          highlight(item.series, item.datapoint);  
    //          alert("You clicked a point!");  
    //        }  
    //    });  
  
    //    其中，pos.x, pos.y 是相对第一轴的位置，pos.x2, pos.y2 是相对第二轴的位置，pos.pageX, pos.page.Y是相对页面的位置  
    //    item的格式如下：  
    //    item: {  
    //        datapoint:     // 点击捕捉到的数据点（在data中提供）如： [0, 2]  
    //        dataIndex:     // 这个数据点在数据系列中的索引  
    //        series:     // 数据系列object，包含所有属性，如series.label, series.color 等  
    //        seriesIndex:     // 数据系列在所有系列中的索引  
    //        pageX, pageY:     // 数据点相对于整个页面的位置信息（注意，前面提到的是点击的位置，这里是数据点本身）  
    //    }  
  
    autoHighlight: boolean    // 如果可以移动，则自动高亮就近的数据，缺省打开  
    mouseActiveRadius: number    // 鼠标对数据的检测半径  
  }  
  
  // 选择模式  
  selection: {  
    mode: null or "x" or "y" or "xy",    // null 为关闭，打开时，container将会产生plotselected事件，并发送一个范围数据ranges  
    color: color    // 选择范围的颜色  
    //    placeholder.bind("plotselected", function(event, ranges) {  
    //      alert("You selected " + ranges.xaxis.from + " to " + ranges.xaxis.to)  
    //    });  
    // similar for yaxis, secondary axes are in x2axis  
    // and y2axis if present  
  }  
```