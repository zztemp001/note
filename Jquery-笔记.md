## 判断元素是否存在：
```js
if($(selector)[0]){…}  
#或  
if($(selector).length){…}  
```

## 使用 each 实现循环
```js
var myArr = ['apple','banana','orange'];  
   
$.each(myArr, function(index, item) {  
    // Do something with 'item'  
    // return false to BREAK  
    // return true to CONTINUE  
});  
```

## 使用变量来缓存元素
```js
var myInjectedDiv = $('<div/>').appendTo('body');  
   
// Use 'myInjectedDiv' to reference the element:  
myInjectedDiv.bind('click', function(){  
    // ...  
});  
```

## 通过 map 由其中的函数返回值生成序列
```js
// Create an array containing all anchor HREF attributes:  
var URLs = $.map($('a'), function(elem, index){  
    return elem.href;  
});  
   
// URLs = ['http://google.com', 'http://whatever.com', 'http://yahoo.com']  
```

## 向 firebug 注册返回信息
```js
console.time('My first method');  
// Do something...  
console.timeEnd('My first method');  
   
console.time('My second method');  
// Do something else...  
console.timeEnd('My second method');  
   
// Firebug will log the time (in milliseconds) taken  
// to complete each chunk...  
```

## 在任意对象中绑定时间
```js
function Widget() {  
    // Do something...  
};  
   
var myPhotoWidget = new Widget('photos');  
   
jQuery(myPhotoWidget).bind('photoAdd', function() {  
    // Custom event handling...  
});  
   
// Trigger event:  
jQuery(myPhotoWidget).trigger('photoAdd');  
```

## 通过数组形式访问对象集
```js
var HTMLCollection = $('div').get();  
   
// Alternatively, if you only want the first element:  
$('div').get(0);  
$('div').get()[0];  
$('div')[0];  
```

## 一条语句即可生成Dom树
```js
// Create and inject in one chain:  
jQuery('<div/>')  
    .append('<p><a href="#">Foo</a></p>')  
    .find('p a')  
        .click(function(){  
            // Do something...  
            return false;  
        })  
        .end()  
    .append('<p><a href="#">Bar</a></p>')  
    .find('p:eq(1) a')  
        .click(function(){  
            // Do something else...  
            return false;  
        })  
        .end()  
    .appendTo('body');  
```

## 判断元素是否可见
```js
if ($('.warning').is(':visible')) {  
    $('.warning').hide();  
}  
```

## 提前缓存需要的文件
```js
(function($) {  
   $.ajax({ url:"/js/file1.js", cache:true, dataType:"text" });  
   $.ajax({ url:"/js/file2.js", cache:true, dataType:"text" });  
})(jQuery);

/*
The 'text' dataType means jQuery won't try to evaluate the JavaScript it fetches. 
The 'cache' parameter defaults to 'true', but I prefer adding it.

I tested several browsers with a proxy, and the JavaScript files are cached - cool! 
I presume this technique would work with any resource, not just JS.
*/
```

## 使用Google jQuery CDN
```html
// Jquery 1.51  
<script src=”http://ajax.googleapis.com/ajax/libs/jquery/1.5.1/jquery.min.js”></script>  
  
//Jquery UI 1.81  
<script src="https://ajax.googleapis.com/ajax/libs/jqueryui/1.8.11/jquery-ui.min.js"></script>

// JQuery 及其他的库可以参考 Google 页面，随时更新的：http://code.google.com/apis/libraries/devguide.html
```

## jQuery的$.AJAX实现
```js
// 执行AJAX请求的通用函数  
//带一个参数，是包含一系列选项的对象  
function ajax(options){  
    //如果用户没有提供某个选项则用默认值代替  
    options = {  
        //HTTP请求的类型  
        type: options.type || "POST",  
        //请求的URL  
        url: options.url || "",  
        //请求超时时间  
        timeout: options.timeout || 5000,  
        //请求失败，成功或完成（无论成功或者失败都会执行）时执行的函数  
        onComplete: options.onComplete ||  
        function(){},  
        onError: options.onError ||  
        function(){},  
        onSuccess: options.onSuccess ||  
        function(){},  
        //服务器返回的数据类型，这一默认值用于判断服务器返回的数据并做相应动作  
        data: options.data || ""  
    };  
     
    //创建请求对象  
    var xml = new XMLHttpRequest();  
    //初始化异步请求  
    xml.open(options.type, options.url, true);  
    //我们在请求后等待5秒，超时则放弃  
    var timeoutLenght = options.timeout;  
     
    //记录请求是否成功完成  
    var requestDone = false;  
     
    // 初始化一个5秒后执行的回调函数，用于取消请求 （如果尚未完成的）  
    setTimeout(function(){  
        var requestDone = true;  
    }, timeoutLenght);  
     
    // 监听文档状态的更新  
    xml.onreadystatechange = function(){  
        //保持等待，直到数据完全加载，并保证请求未超时  
        if (xml.readyState == 4 && !requestDone) {  
            //检查是否请求成功  
            if (httpSuccess(xml)) {  
                //以服务器返回的数据作为参数调用成功回调函数  
                options.onSuccess(httpData(xml, options.type));  
            }  
            else {  
                options.onError();  
            }  
            //调用完成回调函数  
            options.onComplete();  
            //为避免内存泄漏，清理文档  
            xml = null;  
        }  
    };  
     
    //建立与服务器连接  
    xml.send();  
     
    //判断HTTP响应是否成功  
    function httpSuccess(r){  
        try {  
            //如果得不到服务器状态，且我们正在请求本地文件，认为成功  
            return !r.status && location.protocol == "file:" ||  
             
            //所有200到300间的状态表示为成功  
            (r.status >= 200 && r.status < 300) ||  
             
            //文档未修改也算成功  
            r.status == 304 ||  
             
            //Safari 在文档未修改时返回空状态  
            navigator.userAgent.indexOf("Safari") >= 0 && typeof r.status == "undefined";  
        }  
        catch (e) {  
        }  
        //若检查状态失败，就假定请求是失败的  
        return false;  
    }  
     
    //从HTTP响应中解析正确的数据  
    function httpData(r, type){  
        //获取 content-type 的首部  
        var ct = r.getResponseHeader("content-type");  
         
        //若是没有提供默认的类型，判断服务器返回的是否是xml形式  
        var data = !type && ct && ct.indexOf("xml") >= 0;  
         
        //若是，获得xml文档对象，否则返回文本内容  
        data = type == "xml" || data ? r.responseXML : r.responseText;  
         
        //若指定类型是“script”，则以Javascript形式执行返回文本  
        if (type == "script")  
            eval.call(window, data);  
        //返回响应数据（或为XML文档，或为文本字符串）  
        return data;  
         
    }  
}  
```

## 使用 blackbird 跟踪输出信息
```
官方网址：www.gscottolson.com/blackbirdjs/
```
```html
<head>  
    <script type="text/javascript" src="/PATH/TO/blackbird.js"></script>  
    <link type="text/css" rel="Stylesheet" href="/PATH/TO/blackbird.css" />  
</head>  
```

## 使通过ajax方法或动态添加的内容正常工作
```js
//重要：动态生成，或通过ajax获取的DOM元素将无法正常工作，因为页面元素的事件响应已在生成之前完成
//可通过继承，或重新为元素绑定事件的方法，是这一类动态生成的元素正常工作

//继承
$('#mydiv').click(function(e){
    if( $(e.target).is('a') )
       fn.call(e.target,e);
});
$('#mydiv').load('my.html');

//为生成的元素重新绑定事件，注意：在1.4.2版本中，为同一元素绑定多次事件，会导致事件多次触发
$('a').click(fn);
 $('#mydiv').load('my.html',function(){
   $('#mydiv a').click(fn);
});
```

## 使用Callback函数来确保jquery按次序执行语句
```js
//错误，因为js语句逐条执行，alert语句将在hide动作一开始执行就执行
$("p").hide(1000);
alert("The paragraph is now hidden");

//正确，采用Callback函数，确保alert语句在hide执行完毕后再执行
$("p").hide(1000,function(){
   alert("The paragraph is now hidden");
});
```

## 利用css的cursor属性可改变一定范围内的鼠标形状
```js
span.crosshair {cursor:crosshair;}
span.help {cursor:help;}
span.wait {cursor:wait;}
```
鼠标形状的详细列表：​http://www.w3school.com.cn/css/pr_class_cursor.asp 可以使用cursor属性的url()方法，制定一个自定义的光标地址。

## 使用getElementbyId将一个DOM元素转换为Jquery对象
```js
var myDomElement = document.getElementById('foo');
$(myDomElement).find('a');
```

## 使用jquery.is()方法来判断对象是否拥有特定属性/状态
```js
if ( $('#myDiv').is('.pretty.awesome') )  //是否拥有pretty和awesome属性
   $('#myDiv').show();

if ( $('#myDiv').is(':hidden') )  //是否处于隐藏状态
   $('#myDiv').show();
```

## 利用length属性来判断一个元素是否存在
```js
if ( $('#myDiv').length )  //如果元素存在，则长度大于0
   $('#myDiv').show();
// 事实上无需总是判断元素是否存在，如果元素不存在，jquery也不会触发错误
```

## 使用:visible和:hidden选择器选择、判断元素的状态
```js
var isVisible = $('#myDiv').is(':visible');
var isHidden = $('#myDiv').is(':hidden');

$('#myDiv:visible').animate({left: '+=200px'}, 'slow');  //选择ID为myDiv，状态为visible的元素并对其制定动画
```

## 设置disabled属性为true或false来决定表格元素是否被激活
```js
$("#x").attr('disabled', true);  //不激活
$("#x").attr('disabled', false);  //激活
$("#x").attr('disabled', 'disabled');  //不激活
$("#x").removeAttr('disabled');  //激活
```

## 设置checked属性来选择/取消选择一个checkbox或radio
```js
$("#x").attr('checked', true);  //选择
$("#x").attr('checked', false);  //取消选择
$("#x").attr('checked', 'checked');  //选择
$("#x").removeAttr('checked');  //取消选择
```

## 通过option:selected来取得下拉框中选中项的文本
```js
$("#myselect").val();  //获得选中项的value值
$("#myselect option:selected").text();  //获得选中项的文本
```

## 使用google closure compiler或YUI compressor压缩js文件
```
google closure compiler: http://code.google.com/closure/compiler/
YUI compressor: http://developer.yahoo.com/yui/compressor/
```

## 从Jquery对象中得到原始的DOM元素
```js
$("#myDiv")[0];  //最快最简单的方式
$("#myDiv").get(0);  //跟上面一样，只是略慢
$("#myDiv").get();  //不用参数使用get()，则返回一个符合条件的DOM元素数组
```

## 使用注释来避免浏览器兼容的情况
```html
<html>
<body>
   <script type="text/javascript">
      <!--
          document.write("Hello World!");
      //-->
   </script>
</body>
</html>
```

## js通过var来声明变量，变量名大小写敏感，以字母或下划线开始
```js
var x;
var y=1;
var xyz="文本值用引号";
var Y=2;  //y和Y是两个不同变量

newx=6;  //可不用声明就直接使用变量，等价于：var news=6;
```

## 使用+运算符来连接字符串
```js
text1="你好吗？";
text2="谁谁谁！";
text=text1+text2;  //结果是：你好吗？谁谁谁！
text=text1+" "+text2;  //结果是：你好吗？  谁谁谁！
result=5+5;  //结果是：10
result="5"+5;  //结果是：55，规则是：数字与字符串相加，结果为字符串
```

## 常用的js/jquery正则表达式
```
匹配中文字符的正则表达式： [u4e00-u9fa5]
匹配双字节字符(包括汉字在内)，可以用来计算字符串的长度（一个双字节字符长度计2，ASCII字符计1）：[^x00-xff]
匹配空白行的正则表达式，可以用来删除空白行：ns*r
匹配HTML标记的正则表达式：< (S*?)[^>]*>.*?|< .*? />
匹配Email地址的正则表达式：w+([-+.]w+)*@w+([-.]w+)*.w+([-.]w+)*
匹配网址URL的正则表达式：[a-zA-z]+://[^s]*
匹配帐号是否合法(字母开头，允许5-16字节，允许字母数字下划线)：^[a-zA-Z][a-zA-Z0-9_]{4,15}$
匹配国内电话号码：d{3}-d{8}|d{4}-d{7}
匹配腾讯QQ号：[1-9][0-9]{4,}
匹配中国邮政编码：[1-9]d{5}(?!d)
匹配身份证：d{15}|d{18}
匹配ip地址：d+.d+.d+.d+
其他正则表达式可参考：​http://www.html5china.com/js/jQuery/20111119_2568.html
```