## 字符编码
javascript采用的是Unicode字符集编码。
Javascript中每个字符都是用2个字节表示的。（因为是16位编码）
大家也许见过用中文写脚本，比如：
```js
function 我的函数() {  
}  
```

## 数据类型
* 3种基本的类型；数字，字符串和布尔值。
* 2种小数据类型：null 和 undefined . (为什么叫小数据类型？因为他们只定义了一个值)
* 1种复合类型：object. (在这个类型中，它的值可以是基本数据类型，也可以是复合类型，比如其他的object. 在对象中有一个特殊的对象function.它是一个可以执行代码的对象.)
* 数组：
* Date类 ： 是日期的对象
* RegExp类： 正则表达式的对象
* Error 类：js中发生错误的对象

## 创建对象：
```js
//方法1：  
var point = new Object();  
point.x = 3;  
point.y = 5;  
  
//方法2：使用对象直接量  
var point = {x:3 , y:5 }  
//当然json也可以咯。  
```

## 数组：
* 常规数组：以非负整数做为下标。image[0]
* 关联数组：以字符做为下标。如：image[“width”]
* js不支持多维数组，但数组里面可以嵌套数组。
* 创建数组：
```js
//方法1：  
var a = new Array();  
a[0] = “1”;  
a[1] = 2;  
a[2] = { x:1, y:3};  
  
//方法2：   
var  a  =  new Array(“1” , 2 , {x:1,y:3} );  
//注意下：如果只传了一个参数；比如  
var a = new Array(3);  
//那么它是表示：3个未定义元素的新数组。  
  
//方法3：  
var  a =[“1” ,  2 , {x:1 , y :3 }];   //注意外面的括号,  不是花括号。  
```

## 异常处理：
* 抛出异常： throw
* 捕捉异常：try / catch / finally
```js
// 抛出异常  
If(x>0)  
  throw new Error(“x must not be negative!”);  
  
// 捕获异常  
try{  
}  
catch(e){  
}  
finally{  //总是被最后执行  。通常进行 消除操作。  
}  
```

## with语句：
```js
var form =  frame[1].document.forms[0];  
form.name.value = "";  
form.address.value ="";  
这样可以使用with语句代替;比如：
view plaincopy to clipboardprint?
with(frame[1].document.forms[0])｛  
   name.value = "";  
   address.value ="";  
｝  
```

## for / in 语句
```js
# 一种遍历（枚举）对象属性的方法，可以循环我们呢事先不知道的属性。
# 从对象中移除了属性，在删除之后，用for/in将不会枚举该属性，并且用width in book 也检测不到该属性。
# for/in 的另一个重要的用途就是跟关联数组一起使用：

for(stoct  in  port ){  
    value +  = get_value(stoct)  *   port[stoct]  ;   
}  
```
   
## 创建数组：
```js
var es = [ ] ;  
var es = [ [ 1, {x:1   , y : 2}] , [ 2, {x:3  , y : 4}]  ];  
var a  = new Array();    //空数组，和 var a =[ ] 相等 ；  
var  a = new Array( 1,2,3,”tt”) ;     //可以看出直接量定义 简单些。  
var  a  = new Array (3);    //具有3个元素的数组，每个元素的值为 undefined ;  
```

## length属性经常用于遍历数组元素；比如：
```js
var  a  = [“a” , “b “ ,”c” ];  
for(var i = 0 ; i< a.length ; i++){  
    alert(a[i]);  
}  
```
```js
# 这个是在假定元素是连续的，如果不是这种情况：必须检测每个元素是否被定义 ： 比如：

for(var i = 0 ; i< a.length ; i++){  
   if(a[i]){   
        alert(a[i]);   
    }  
}  
```

## 数组的一些方法：
* join() 方法：把一个数组的所有元素都转换成字符串。比如：
```js
var  a  = [1,2,3];  
var s =   a.join();   // 输出 s==1,2,3  
// 当然也可以 指定一个分隔符；比如;  
s = a.join(“,”);    
// 这个方法跟String.split()相反， split()将一个字符串分割成几个片段来创建数组；  
```

* reverse () 方法：把一个数组颠倒。
```js
var  a   = new Array(1,2,3);  
a.reverse();  
var s  =  a.join();  //s == “3,2,1”  
```

* sort() 方法：排序.
```js
// 如果不给传参数，那么按照字母顺序对数组元素排序。  
var a = new Array(“ee”,”df”,”b”);  
a.sort()  
var s = a.join(“, ”);  // s ==  “b, df, ee”   
  
// 如果传参数：比如：  
var a = [33,4,1111,222]  
a.sort(); // 排序 :  1111 ,222 ,33,4  
a.sort(function(x,y){  
       return x-y;  
});  
var s  = a.join(); //输出  4, 33,222,1111  
  
//可以从例子看出，排序如果x > y ，那么第一个参数就排在第2个参数后，  
//比如;  1111 ,222   -     1111-222>0  - 则  222 , 1111  
/另外注意下 字母排序：因为js是区分大小写，所以排序的时候，把字符统一成大写或者小写，再排序。  
```

* concat() 方法：
```js
var  a  = [1,2,3];  
a= a.concat(4, [5,6],7);  
a=a.join(); //输出 1，2，3，4，5，6，7  
alert(a)  
  
// 注意：如果是数组里面还有数组 ，就不能展开了。比如：  
var a  = [1,2,3];  
a = a.concat(4,[5,[6,6]],7);  
alert(a);  //这个看不出来  
a = a.join("|");  
alert(a);  //分割后，注意有个逗号  
  
var  c  = [1,2,3];  
var  d  =new Array(1,2,3);  
alert(c); //1,2,3  
alert(d); //1,2,3  
//之所以不输出Object ,是因为数组是一个具有额外功能层的对象.我们记住他的特殊性。  
```

* slice（) 方法：返回数组某一个片段。跟字符串的substring方法类似。

* splice（) 方法：首先他跟 slice 方法 只有一个字母的差别，不过用处完全不同。
```js
// 他可以用来删除。  
var a  = [1,2,3];  
a = a.splice(0,2);  
alert(a);  // 输出 1， 2  
a = a.splice(1,2);  
alert(a);  //  输出 2  。 如果是  a = a.splice(0 , 1) ;  输出  1   
a = a.splice(1,2);  
alert(a);  //  没有删除任何数组 ， 输出 空 数组  
  
// 也可以插入数组。具体方法：  
var array1 = new Array("1","2","3","4");  
array1.splice(1,0,"5");//在 第2个元素后面，插入 5 ； 如果第2个参数为0，则不删除。  
document.write(array1+"<br>"); //输出  1, 5 ,2 ,3,4  
array1.splice(2,3,"7","8")  // 删除 第3个的 元素后的 3个元素。也就是 第3个，第4个，第5个元素。然后在这个位置上插入7,8  
document.write(array1);//输出  1, 5 ,7,8  
  
// 注意：和concat()不同，splice并不将他插入的参数展开。也就是如果插入一个数组，他就是插入数组本身，还不是数组的元素。  
// 而concat()插入数组的话，就会把数组展开，插入数组中的元素，不过当插入的数组里  
// 还有数组的时候，就不会展开了。  
```

* push（) 方法和pop()方法：
```js
// push(): 将一个或者多个数组 附加到数组的尾部。  
// pop() : 删除数组的最后一个元素。  
var array1 = new Array("1","2","3","4");  
array1.push("5");  
document.write(array1+"<br>"); //输出  1, 2 ,3 ,4,5  
array1.pop()    
document.write(array1);//输出  1, 2 ,3 ,4  
```

* unshift（) 方法和shift ()方法：跟push和pop唱反调。
```js
// unshift (): 将一个或者多个数组 附加到数组的头部。  
// shift ():删除数组的第一个元素。  
```

## 类的定义  
```js
function Person(name) {   
    //实例属性  
    this.name = name;      
      
    //实例方法  
    this.helloWorld = function(){  
       alert("hello world!");  
    };  
}  
  
//静态属性  
Person.prototype.sayHello =  "you are pig!";  
  
//静态方法  
Person.prototype.helloWorldToo = function () {  
    alert("hello world too！");  
}  
  
//类属性  
Person.working = "yes";  
  
//类方法  
Person.say = function () {  
   alert("hello world!");  
}  

//调用：  
var XiaoWang   =  new Person("小王");//实例化  --- 一个新的小王出生了。  
alert(  XiaoWang.name  );            //调用实例属性 ---- 我们想知道他的名字  
XiaoWang.helloWorld();               //调用实例方法 ---- 小王开始说 的第一句话。  
  
var XiaoLiu   =  new Person("小刘");//实例化  --- 一个新的小刘出生了。  
alert(  XiaoLiu.name  );            //调用实例属性 ---- 我们想知道他的名字.  
XiaoLiu.helloWorld();               //调用实例方法 ---- 小刘开始说 的第一句话。  
  
alert( XiaoWang.sayHello  ); //  调用静态属性 --- 小王开始骂人了。  
alert( XiaoLiu.sayHello  );  //  调用静态属性 --- 小刘开始骂人了。  
  
XiaoWang.helloWorldToo() ; //  调用静态方法 --- 小王说的第三句话。  
XiaoLiu.helloWorldToo() ;  //  调用静态方法 --- 小刘说的第三句话。  
  
alert(  Person.working  ); //调用类的属性   ---   不是 XiaoWang.working  
   
Person.say(); //调用类的方法   ---   不是 XiaoWang.say()  
```
  
## 简单的面向对象开发
* 下面代码展示了学校中的课程名和老师的信息。
```js
/* 
Lecture类 
用name和teacher作为参数 
*/  
function Lecture(name,teacher){  
 this.name=name;  
 this.teacher=teacher;  
}  
  
//‘Lecture’类的一个方法，用于生成一条信息  
Lecture.prototype.display=function(){  
 return this.name + " 是教 "+this.teacher +" 的。" ;  
}  
  
//下面用new来构造一个新的函数，然后调用display方法  
var L = new Lecture("李老师","英语");  
var L_msg = L.display();  
alert(L_msg);  
  
// 最终结果会输出“李老师 是教 英语 的。”  
```

我们再定义个函数，用来输出所有的课程信息。代码如下所示：
```js
//新定义一个'AllLecture'类, 用'lec'作为参数，lec是一个数组  
function AllLecture( lec ){  
    this.lec = lec;  
}  
  
//‘AllLecture’类的一个方法，用于生成所有的课程信息, 需要循环输出‘lec’  
AllLecture.prototype.display=function(){  
    var str = "";  
    for(var i=0;i<this.lec.length;i++){  
    str += this.lec[i] + "\n";  
    }  
    return str;  
}  
  
//下面使用new来够造一个新的函数，用于生成所有的信息, 函数的参数是数组。使用'Lecture'类来生成数组的值。  
var B = new AllLecture( [ new Lecture("李老师","英语").display() , new Lecture("张老师","数学").display() , new Lecture("刘老师","物理").display() ] );  
var B_str = B.display();  
alert(B_str);  
```

## javascript 的循环技术
* while 语句
```js
var body = document.body;  
while (body.firstChild) {  
    body.removeChild(body.firstChild);  
}  
```
```js
// #1 : regular  
var myArray = ['apple','orange','lemon'],  
    length = myArray.length,  
    counter = 0;  
while (counter < length) {  
    alert ( myArray[counter] );  
    counter++;  
} // alerts 'apple', then 'orange', then 'lemon'  

// #2 : in reverse  
var myArray = ['apple','orange','lemon'],  
    length = myArray.length;  
while (length--) {  
    alert ( myArray[length] );  
} // alerts 'lemon', then 'orange', then 'apple'  
```
```js
var bigObject = {  
    smallProp : [1,2,3,4,5,6,7]  
};  
   
// BAD:  
var counter = 0;  
while ( counter < bigObject.smallProp.lentgh ) {  
    doSomethingWith ( bigObject.smallProp[counter] );  
}  
   
// GOOD: (with caching)  
var arr = bigObject.smallProp,  
    length = arr.length,  
    counter = 0;  
while ( counter < length ) {  
    soSomethingWith ( arr[counter] );  
}  
   
// It's a common naming convention to have the 'counter'  
// variable named as 'i' which I think stands for 'iterator'.  
// The name used doesn't really matter though, as long as  
// it's clear to you...  
```

for 语句
view plaincopy to clipboardprint?
var myArray = [1,2,3,4,5,6];  
for ( var i = 0, arrLength = myArray.length; i < arrLength; i++ ) {  
    // Notice that we've named the counter 'i'  
    alert ( myArray[i] );  
}  
view plaincopy to clipboardprint?
// BAD:  
for (var i = 0; i < myArray.length; i++) {  
    foo.bar.special.method ( myArray[i] );  
}  
   
// GOOD:  
for (var i = 0,  
         fn = foo.bar.special.method,  
         length = myArray.length; i < length; i++) {  
    fn ( myArray[i] );  
}  
   
// Better, In my opinion:  
var i = 0,  
    fn = foo.bar.special.method,  
    length = myArray.length;  
   
for (; i < length; i++) {  
    fn ( myArray[i] );  
}  
break 语句
view plaincopy to clipboardprint?
myOuterLoop : while (condition) {  
    myInnerLoop : while (condition) {  
        // Do something...  
    }  
}  
view plaincopy to clipboardprint?
while (condition) {  
    var success = doSomething();  
    if (!success) { break; }  
}  
view plaincopy to clipboardprint?
var alpha = ['A','B','C','D','E'],  
    i = 0,  
    length = alpha.length,  
    resultString = '';  
   
for (; i < length; i++) {  
    if (alpha[i] === 'D') break;  
    resultString += alpha[i];  
}  
   
// resultString === 'ABC'  
view plaincopy to clipboardprint?
myOuterLoop : while (condition) {  
    myInnerLoop : while (condition) {  
        if (whatever) {  
            break myOuterLoop;  
        }  
        if (whatever2) {  
            break; // Same as 'break myInnerLoop;'  
        }  
    }  
}  
continue 语句
view plaincopy to clipboardprint?
var alpha = ['A','B','C','D','E'],  
    i = 0,  
    length = alpha.length,  
    resultString = '';  
   
for (; i < length; i++) {  
    if (alpha[i] === 'D') continue;  
    resultString += alpha[i];  
}  
   
// resultString === 'ABCE'  
// The 'D' is skipped...  
view plaincopy to clipboardprint?
myOuterLoop : while (condition) {  
    myInnerLoop : while (condition) {  
        if (whatever) {  
            continue myOuterLoop;   
        }  
        if (whatever2) {  
            continue; // Same as 'continue myInnerLoop;'  
        }  
    }  
}  
Do..while 语句
view plaincopy to clipboardprint?
do {  
    something();  
} while (theCondition);  
   
// something() will be executed at least once,  
// even if theCondition evaluates to false.  