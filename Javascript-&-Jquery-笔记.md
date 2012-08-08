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