- [ ] ***`JAVAScript笔记`***

变量:var 变量名=初始化值

typeof运算符;获取变量的类型

一元运算符:++

算数:+-*/

三元: ?  :

跟java一样

```javascript
<script>
    var num=3;
    var a=++num;
    document.write(num);
    document.write(a);
    var b=+3;
    var b=3;
    alert(b);
</script>
```

注意:在JS中如果运算符不是运算符所要求的类型,那么js引擎会自动转换

其他类型转number

​    *String转number 不是字符的数字转为NaN

![image-20200528073611375](C:\Users\qq422\AppData\Roaming\Typora\typora-user-images\image-20200528073611375.png)

alert 窗口弹出

比较运算符:如果类型不同先进行类型转换在比较

  *字符串,按照字典顺序比较按位逐一比较

 *===全等于在比较之前先判断类型如果不同直接返回false

算术运算:!非

```javascript
var flag=true;
document.write(!flag+"<br>");
```

​      *其他类型转boolean:

​               1.number:0假非0真

​               2.string除了空字符串(""),其他都是true

​              3.null&undefined都是falase

​               4.对象,所有对象都为true

```javascript
var obj="123";
var obj2;
if (obj!=null&&obj.length>0){//防止空指针异常
    alert(123)
}
if (obj){//防止空指针异常
    alert(111);
}
```

三元运算符

```javascript
var a=3;
var b=4;
var c=a>b?1:0;
```

变量定义使用var关键字也可以不用

 *用 : 定义的是局部变量

  *不用就是全局变量

Function:函数对象

1.创建:

​       (1).var fun=new Function(形式参数列表,方法体);

```javascript
var fun1=new Function("a","b","alert");
fun1(3,4);
```

​         (2).function 方法名称(形式参数列表){

​              方法体}

```javascript
function fun2( a, b) {
 alert(a+b);
}
fun2(3,4);
```

  (3)var 方法名 function(形式参数列表){

​             方法体   }

   1. ```javascript
      var fun3=function (a,b) {
         alert(a+b);
      }
      fun3(3,4);
      ```

2.方法:



3.属性:

​         length形参个数

4.特点:

​             方法定义是形参类型不用写 返回值类型也不用写

​            方法是一个对象,如果定义名称相同的方法会覆盖

​            在jS中,方法的调用只与方法的名称有关,和参数列表无关

​            在方法声明中有一个隐藏的内置对象(数组),arguments,凤凰庄所有的实际参数

```javascript
<script>
    var arr1=new Array(1,2,3);
    var arr2=new Array(5);
    var arr3=[1,2,3,4,];
    document.write(arr1+"<br>");
    document.write(arr2+"<br>");
    document.write(arr3+"<br>");
</script>
```

Array数组对象
  1.创建
     var arr=new Array(元素列表)
     var arr=new Array(默认长度)
     var arr=[元素列表]
  2.方法

​        join(参数)将数组中的元素按指定分隔符拼接字符串

  3.属性
  4.特点

​      (1在JS中数组的元素类型是可变的

​      (2在JS中数组的长度是可变的

```javascript
var arr=[1,"abc",true];

document.write(arr+"<br>");
document.write(arr[0]+"<br>");
document.write(arr[1]+"<br>");
document.write(arr[2]+"<br>");
alert(arr.length);
document.write(arr.join("--")+"<br>");
var arr1=new date;
```

Date对象

 1.创建

var date=new Date();

2.方法

toLocalsString();返回当前date对象昂对象的时间本地字符串格式

getTime()获取毫秒值,返回当前如期对象描述的时间到1970年1月1日0点的毫秒值差

```javascript
var date=new Date();

    document.write(date+"<br>");
    document.write(date.toLocaleDateString()+"<br>");
    document.write(date.getTime()+"<br>");
```

Math对象

1.创建

直接使用

Math.方法名();

2.使用

random()返回一个0~1随机数(含0不含1)

cail(x)对数进行上舍入

floor(x)对数进行下舍入

round(x)四舍五入最接近的整数

3.属性

Math.PI

```javascript
document.write(Math.PI+"<br>");
document.write(Math.random());
document.write(Math.round(3.14)+"<br>");
document.write(Math.ceil(3.14)+"<br>");
document.write(Math.floor(3.14)+"<br>");
```

RegExp正则表达式

1.单个字符:[]

如:[a] [ab] [a-zA-Z0-9]

特殊符号代表特殊含义的单个字符:

 \d;单个数字字符[0-9]

 \w:单个单词字符[a-zA-Z0-9]

2.量词符号

? 表示出现0次或1次

*表示出现0次或多次

+出现1次或多次

{m,n}表示m<=数量<=n

  *m如果缺省:{,n}:最多n次

  *n如果缺省:{m,}最少m次

^开始符号

$结束符号

3.正则表达式创建

1.创建

​          1.var reg=new RegExp("正则表达式");

​          2.var reg=/正则表达式/;

2.方法

test()

```javascript
<script>
    var reg=new RegExp("\w{6,12}");
    var reg2=/^w(6,12)$/;
    alert(reg);
    alert(reg2);
    var username="zhangsanhdksfhkds";
    var flag=reg2.test(username);
    alert(flag);
</script>
```

Global对象

 1.特点:全局对象这个Global中封装的方法不需要对象就可以直接调用 方法名()

  2.方法:

​     encodeURL()url编码

​     decodeURL()url解码

​     encodeURLComponent():url编码更多字符

​      decodeURLComponent():url解码

```javascript
<script>
    var str="https://www.bilibili.com传播智客";
    var encode=encodeURI(str);
    document.write(encode+"<br>");
    var s=decodeURI(encode);
    document.write(s+"<br>");
</script>
```

  parseInt()将字符串转为数字

  *逐一判断每一个字符是否是数字知道不是数字为止将前面数字部分转为number

```javascript
<script>
    var str="123abc";
    var number=parseInt(str);
    alert(number+1);
</script>
```

isNaN()判断一个值是否是NaN

   *六亲不认

```javascript
<script>
    var a =NaN;
    document.write(a==NaN);
    document.write(isNaN(a));
</script>
```

eval()将javascript字符串转成脚本执行

```javascript
<script>
    var jscode="alert(123)";
    eval(jscode);
</script>
```

Windwos窗口对象

1.创建

2.方法

1)与弹出框有关的方法

​        alert()显示带有一段消息和一个确认按钮的警告框

​        confirm()显示带有一段消息以及确认按钮和取消按钮的对话框

```javascript
<script>
    var flag=confirm("您确定退出吗");
    if (flag){
        alert("欢饮再次光临");
    }else{
        alert("手别抖....");
    }
var nu=prompt("请输入用户名");
    alert(nu)
</script>
```

​          prompt()显示可提示用户输入的对话框

setTimeout()在指定毫秒数后调用函数或表达式

clearTimeout()取消由setTimeout()方法设置的timeout

setInterval()按照指定的周期(以毫秒计)来调用函数或计算表达式

clearInterval()取消由setInterval()设置的timeout

```javascript
<script>
    var id=setTimeout("alert('boom');",3000);
    clearTimeout(id);//剪断定时器
    setTimeout(fun,2000);
    function fun(){
        alert('boom...');
    }
    setInterval(fun,2000);//循环
    clearInterval(fun);
</script>
```

3.属性

4.特点

​       *windows对象不需要创建可以直接使用window使用

​       *window引用可以省略      方法名();

```javascript
alert("hello win10");
window.alert("hello a");
```

案例打开关闭窗口

```javascript
<input id="openBtn" type="button" value="代开狂口">
<input id="openBtn1" type="button" value="关闭狂口">
<script>
    var openbtn=document.getElementById("openBtn");
    openbtn.onclick=function () {
     open("http://www.baidu.com");
    }
    var openbtn1=document.getElementById("openBtn1");
    openbtn.onclick=function () {
        close();
    }
</script>
```

定时器案例

```javascript
<script>
        var number=1;
        function fun() {
            number++;
            if (number>3){
                number=1;
            }
      var img=document.getElementById("img")
            img.src=""+number+".jpg";
        }
        setInterval(fun,2000);
    </script>
</head>
<body>

<img id="img" src="1.jpg" width="100%">

</body>
```

history

```javascript
<script>
   var h1=window.history;
   var h2=history;
   alert(h1);
   alert(h2);
   window.document.getElementById("");
   document.getElementById("");
</script>
```

Location对象

1.创建

2.方法

 *reload() 刷新页面

3.属性

```
案例按钮点击刷新跳转
```

```javascript
<input type="button" id="btn" value="刷新">
<input type="button" id="btn1" value="去传智">
<script>
 var btn=document.getElementById("btn");
 btn.onclick=function(){
     location.reload();
 }
 var btn1=document.getElementById("btn1");
 btn1.onclick=function(){
     location.href="http://www.baidu.com"
 }
  var href=location.href;
  alert(href);
</script>
```

案例自动跳转

```javascript
    <title>自动跳转</title>
    <style>
        p{
            text-align: center;
        }
        span{
            color: red;
        }
    </style>
</head>
<body>
<p>
    <span id="time">5</span>秒之后自动跳转
</p>
<script>
    var second=5;
    function showTime() {
        second--;
        if(second<=0){
            location.href="http://www.baidu.com"
        }
       var time=document.getElementById("time");
       time.innerHTML=second+"";
    }
    setInterval(showTime,1000);

</script>
</body>
```

Document文档对象

1.创建:在html dom模型中可以使用window对象来获取

​       1)window.document

​       2)document

2.方法

​        1)获取Element对象

​              getElementById()根据id属性值获取元素对象 id属性值一般唯一

​              getElementsByTagName()根据元素名称获取元素对象们 ,返回值是一个数组

​              getElementSByClassName()根据Class属性值获取元素对象们,返回值是一个数组

​              getElementsByName()根据name属性值获取元素对象们,返回值是一个数组

​       2)创建其他DOM对象

​             createAttribute(name)

​             createComment()

​             createElement()

​             createTextNode()

3.属性

创建Document对象

```javascript
<body>
<div id="div1">div1</div>
<div id="div2">div2</div>
<div id="div3">div3</div>
<div class="cls1">div4</div>
<div class="cls1">div5</div>
<input type="text" name="username">
<script>
    var divs=document.getElementsByTagName("div");
    alert(divs.length);
     var div_cls=document.getElementsByClassName("cls1");
     alert(div_cls.length);
     var ele_username=document.getElementsByName("username");
     alert(ele_username);
     //创建
     var table=document.createElement("table");
     alert(table);
</script>
</body>
```

Element元素对象

1.获取/创建:通过document来获取和创建

2.方法:

​      1).removeAttribute()删除属性

​       2).setAttribute()删除属性

案例删除添加属性

```javascript
<body>
<a href="">点我试试</a>
<input type="button" id="btn_set" value="设置属性">
<input type="button" id="btn_remove" value="删除属性">
<script>
    var btn_set=document.getElementById("btn_set");
    btn_set.onclick=function () {
        var element_a=document.getElementsByTagName("a")[0];
        element_a.setAttribute("href","http://www.baidu.com");
    }
    var btn_remove=document.getElementById("btn_remove");
    btn_remove.onclick=function () {
        var element_a=document.getElementsByTagName("a")[0];
        element_a.removeAttribute("href");
    }
</script>
</body>
```

Node节点对象

​     *特点:所有dom对象都可以被认为是一个节点

​     *方法:

​            *CRUD dom树

​                     *appendChild() 用于向节点的子节点列表的结尾添加新的子接点

​                     *removeChild() 删除(并返回)当前节点的指定子节点

​                     *replaceChild()用新节点替换一个子节点

​     *属性

​            *parentNode返回节点的父节点

```javascript
 <style>
        div{
            border: 1px solid red;
        }
        #div1{
            width:200px;
            height: 200px;
        }
        #div2{
            width: 100px;
            height: 100px;
        }
    </style>
</head>
<body>
 <div id="div1">
     <div id="div2">div2</div>
     div1
 </div>
 <a href="javascript:void(0);" id="del">删除子节点</a>
 <a href="javascript:void(0);" id="add">添加子节点</a>
 <script>
     //获取超链接
     var element_a=document.getElementById("del");
     //2.绑定点击事件
     element_a.onclick=function () {
       var div1=document.getElementById("div1");
       var div2=document.getElementById("div2");
       div1.removeChild(div2);
     }
     var element_add=document.getElementById("add");
    //添加节点
     element_add.onclick=function () {
         var div1=document.getElementById("div1");
         var div3=document.createElement("div");
         div3.setAttribute("id","div2");
         div1.appendChild(div3);
       /*超链接功能:
        1.可以被点击
        2.点击后跳转到href指定的url
        需求:保留1功能,去掉2功能
        实现:herf="javascript:void(0);"
       */

     }
     var div2=document.getElementById("div2");
     var div1=div2.parentNode;
     alert(div1);
 </script>
</body>
```

*HTML DOM

​     1)标签体的设置和获取:innerHTML

​     2)使用html元素对象的属性

​     3)控制样式

​              1)*使用元素的style属性

如:/*

```
div1.style.border="1px solid red";
div1.style.width="200px";
div1.style.fontSize="20px";
```

*/

​              2)提前定义好类选择器的样式,通过元素的className属性来设置其class属性值

##事件监听机制:

   *概念:某些组件被执行了某些操作后,触发某些代码的执行

​           *事件:某些操作,如:单机,双击,键盘按下了,鼠标移动了

​           *事件源:组件.如: 按钮   文本输入框......

​           *监听器:代码

​           *注册监听:将事件,事件源,监听器结合一起.当事件源上发生了某个事件,则触发执行某个监听器代码

   *常见的事件:

​           1)点击事件:

​                    *onclick:单击事件

​                    *ondblclick:双击事件

​            2)焦点事件:

​                    *onblur 事件会在对象失去焦点时发生。

​                    *onfocus 事件在对象获得焦点时发生。

​                     *onload 事件会在页面或图像加载完成后立即发生

​            3)加载事件:

​                    *onload一张页面或一幅图像完成加载

​                    *onmousedown	鼠标按钮被按下。

​                      onmousemove	鼠标被移动。

​                      onmouseout	鼠标从某元素移开。

​                      onmouseover	鼠标移到某元素之上。

​                      onmouseup	鼠标按键被松开。

​            选择和改变:

​             1)onchange鱼的内容被改变

​              2)onselect文本被选中

​             表单事件:

​              1)onsubmit确认按钮被点击

​                            *可以阻止表单的提交

​                            *方法返回false则表单被阻止提交

​                 2)onreset 重置按钮被点击

Boostrap框架 

* 一个前端开发框架
* 响应式布局:

   *同一套页面可以兼容不同分辨率的设备

   *实现:依赖于栅格系统:将一行平均分成12个格子.可以指定元素占几个格子

   *步骤:  1.定义容器   ==table

​                              *container:两边留白

​                              *container_fluid:100%宽度

​                 2.定义行    ==tr      样式:row

​                 3.定义元素  指定该元素在不同的设备上,所占的格子数目.样式:col-设备代号-格子数目

​                                *设备代号

​                                          1.xs:超小屏幕  手机  (<768px):col-xs-12                          

​                                           2.sm 小屏幕 平板  (>=768px)

​                                           3.md 中等屏幕 桌面显示器 (>=992px)

​                                           4.lg 大屏幕 大桌面显示器 (>=1200px)

​               一行中如果格子数目超过12,则超过部分自动换行

​               栅格类属性可以向上兼容,栅格类适用于与屏幕宽度大于或等于分界点大小的设备

​               如果真是设备宽度小于了设置栅格类属性的设备代码的最小值,会一个元素沾满一整行

全局css样式和js插件

​        *按钮 class="btn btn-default"

​        * 100%显示缩放限制class="img-responsive"

图片格式

```html
<img src="..." alt="..." class="img-rounded">
<img src="..." alt="..." class="img-circle">
<img src="..." alt="..." class="img-thumbnail">
```

XML

  概念: 可扩展标记语言

​          *可扩展性:标签都是自定义的

​         *功能:

​                *存储数据

​                               1.配置文件 

​                               2.在网络中传输

​         *xml和html 一个存储数据 语法严谨一个展示数据  语法松散

1.基本语法

​         1.xml文档的后缀名.xml

​         2.文档声明必须写在第一行

​         3.有且只有一个根标签

​         4.属性值必须使用引号引起来

​         5.标签必须正确关闭

​         6.区分大小写

2.使用

​      *文档声明

​       1.格式<?xml 属性列表 ?>

​        2.属性列表:

​                 *version:版本号,必须的属性

​                 *encoding:编码方式   默认iso

​                 *standalone:是否独立 yesorno  依赖其他文件

4.属性:

​               *id属性值唯一

5.文本

CDATA区

6.约束

​       规定xml文档的书写规则

   分类:DTD:一种简单的约束技术

​             Schema:一种复杂的约束技术

 &DTD:

​       *引入DTD文档到xml文档中

​           *内部dtd:将约束规则定义在xml文档中

​           *外部DTD

解析: 操作xml文档,将数据读取到内存中

​        *操作xml文档

​              1.解析(读取):将文档读取内存中

​              2.写入

​        *解析xml方式:

​                1.DOM:将xml一次加载到内存中生成DOM树

​                       优点:操作方便

​                       缺点:占内存

​                2.SAX:逐行读取,基于事件驱动的

​                         缺点:只能读取不能改写

​                     PULL:Android操作系统内置的解析器,sax方式的

​                3.Jsoup:是一款java的html解析器,可直接解析某个url底子,heml文本内容,他提供了一套非常省力的API

​                     可通过DOM,css以及类似于jQuery的操作方法来取出和操作数据

​                 Jsoup:工具类,可以解析html或xml文档,返回Document

​                      *parse(File in,String charsetName):解析xml或html文档

​                      *parse(String html)解析xml或html 字符串

​                      *parse(URL url,int timeoutMillis):通过网络路径获取指定的html

​                 Document文档对象,代表内存中的DOM树

​                 Elements元素Element对象的集合,可以当做Arrayslist<Element>来使用

​                 Element元素方法:

​                 获取子元素对象

​                          *getElementById(string id):根据id属性值获取唯一的element对象

​                          *getElementByTag(String tagName)根据标签名称获取元素对象集

​                          *getElementByAttribute(String key)根据属性名称获取元素对象

​                获取属性值:

​                          *String attr(String key):根据属性名称获取属性值

​                获取文本内容

​                            *String text():获取文本内容

​                            *String html():获取标签体的所有内容(包括字标签的字符串内容)

​                 Node节点对象

​                     是Element和Document的父类



 select快捷查询

​                

XML:

配置tomcat

1.直接将项目放到webapps目录下即可

​       */hello:项目的访问路径-->虚拟目录

​       *简化部署:将项目打成一个war包,再将war放置到webapps目录

​                    war包会自动解压缩

2.配置conf/server.xml文件

<!--部署项目-->
        <Context docBase="D:\hello" path="/hehe" />  

3. 在conf/Catalina\localhost创建任意名称的xml文件,在文件中编写

​       *<Context docBase="D:\hello" />

​       *虚拟目录:xml文件的名称

###静态项目和动态项目:

​       *目录结构

​            *java动态项目的目录结构:

​                 --项目的根目录

​                 --WEB-INF目录:

​                        --WEB.xml:web项目的核心配置文件

​                       --classes目录:放置字节码文件的目录

​                       --lib目录:放置依赖的jar包

*将tomcat集成到IDEA中,并且创建

配置servlet:

```java
<servlet>
    <servlet-name>demo01</servlet-name>
    <servlet-class>Servlet.ServletDemo01</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>demo01</servlet-name>
    <url-pattern>demo01</url-pattern>
</servlet-mapping>
```

执行原理:

​    1.当服务器接收到客户端浏览器请求后,会解析请求url路径,获取访问servlet的资源路径

​    2.查找web.xml文件,是否有对应的<url-pattern>标签体内容

​    3.如果有,则在找到对应的<servlet-class>全类名

​    4.tomcat会将字节码文件加载进内存,并且创建其对象

​    5.调用其方法

* Servlet中的生命周期

Servlet--接口

GendricServlet--抽象类

HttpServlet--抽象类

​    *GenericServlet:将Servlet接口中其他的方法默认空实现,只讲service()方法作为抽象

 *HttpServlet:对http协议的一种封装,简化操作

​        *定义类继承HttpServlet

​         *复写doget/doPost方法

~Servlet相关配置

   1.urlpartten:servlet访问路径

​          *一个Servlet可以定义多个访问路径:@WebSerblet({"/d4","/dd4","/ddd4"})

request功能:

1.获取请求消息数据

​         *GET /day14/demo1?name=zhangsan HTTP/1.1

​         *方法:

​                 1.获取请求方式:GET

​                    *String getMethod()

​                 2.获取虚拟目录:/day14

​                     *String getContextPath()

​                 3.获取Servlet路径:/demo1

​                     *String getServletPath()

​                 4.获取get方式请求参数:name=zhangsan

​                      *String getqueryString()

​                 5.获取请求URL:/day14/demo1

​                      *String GetRequestURL();

​                      *StringBUffer getRequestURL()    

​                 6.获取协议及版本:HTTP/1.1

​                       *String getProtocol()

​                 7.获取客户机的ip地址:

​                      *String getRemothAddr()

URI统一资源标识符>url统一资源定位符

2.获取请求头数据

   *方法:

* String getHeader(String name):通过请求头的名称获取请求头的值
* Enumberation<String> getHeaderNames():获取所有的请求头名称

3.获取请求体数据

* 请求体:只有POST请求方式,才有请求体,在请求体中封装了POST请求的请求参数

* 步骤:

  ​      1.获取流对象

  ​          BufferedReader getReader():获取字符输入流,只能操作字符数据

  ​          ServletInputStream getInputStream():获取字节输入流

  ​       2.再从流对象中放数据

  

其他功能:

​      1.获取请求参数通用方法

​              1.String getParameter(String name):根据参数名称获取参数值  

​               2.String[] getParameterValues(String name):根据参数名称获取参数值的数组

​               3.Enumeration<String > ? = getParameterNames():获取所有参数

​               4.Map<String,String[]> getParameterMap():获取所有参数的map集合

​              *中文乱码问题

​                      *get方式:tomcat 8 已经将get方式乱码解决了

​                      *post方式;会乱码        如下:                

```
//设置流的编码
  request.setCharacterEncoding("utf-8");
```

请求转发:一种在服务器内部的资源跳转方式

  1.通过request对象获取请求转发器对象:RequestDispatcher

  2.使用RequestDispatcher对象来进行转发

 特点:

​           1.只能转发到服务器内部资源中

​           2.服务器路径不发生变化

​           3.转发是一次请求

共享数据:

​     *域对象:一个有作用范围 的对象,可以在范围内共享数据

​     *request域:代表一次请求的范围,一般用于请求转发的多个资源中共享数据

​     *方法:

​           1.setAttribute(String name,Object obj):存储数据

​           2.Object getAttibute(String name):通过键获取值

​           3.void  removeAttribute(String name):通过键移除键值对

  login.html中form表单的action路径的写法

​      *虚拟目录+Servlet的资源路径

*BeanUtils工具类,简化数据封装

​    *用于封装JavaBean

1.JavaBean:标准的java类

   1.要求:

​          1.类必须被public修饰

​          2.必须提供空参的构造器

​          3.成员变量必须使用private修饰

​          4.提供公共setter和getter方法

​    2.功能:封装数据

​        1.setProperty()

​        2.getProperty()

​        3.(重要)populate:

2.概念:

​      成员变量;

​      属性:getter和setter方法截取后的产物

重定向的特点:redirect

​        *地址栏发生变化

​        *重定向可以访问其他站点(服务器)的资源

​        *重定向是两次请求.不能使用request对象来共享数据

转发的特点:forward

​      *转发地址栏路径不变

​      *转发只能访问当前 服务器下的资源

​      *转发是一次请求,可以使用request对象来共享数据

*forward和redirect区别

  1/路径分类

​      *相对路径:通过相对路径不可以确定唯一资源

​              *如:./index.html

​              *不以/开头,以.开头路径

​              *规则:找到当前资源和目标资源之间的相对位置关系

  2/绝对路径:通过绝对路径可以确定唯一资源

​        *如http://localhost/tomcat04/responseDemo2

​        *以/开头的路径

注意乱码问题:

  1.PrintWriter pw=response.getWriter():获取的流的默认编码是ISO-8859-1

   2.设置该流的默认编码

   3.告诉浏览器响应体使用的编码

//简单的形式,设置编码,是在获取刘志强设置

  response.setContentType("text/html;charset=utf-8");

3.服务器输出字节数据到浏览器

​    *步骤: 

​          1.获取字节输出流

​           2.输出数据

4.验证码;

​         1.本质:图片

​         2,目的:防止恶意表单注册

​     

##ServletContext对象

​    1.概念:代表整个web应用,可以和外部容器(服务器)通信

2.获取:

​         1.通过request对象获取

​                          request.getServletContext();

​         2.通过HttpServlet获取

​                          this.getServletContext()

​     3.功能: 

​           1.获取MIME类型:

​                 *MIME类型在互联网通信过程中定义的一种文件数据类型

​                     *格式:大类型/小类型     text/html           image/jpeg

​                 *获取:String getMimeType(String file)

​           2.域对象:共享数据

​                   1.setAttribute(String name,Object value)

​                   2.getAttribute(String name)

​                   3.removeAttribute(String name)

​            *ServletContext对象范围,所有用户所有请求的数据

​           3.获取文件的真实(服务器)路径

​                 方法:String getRealPath(String path)

​                         String b=context.getRealPath("/b.txt");//WEB目录下资源访问

​                          String c=context.getRealPath("WEB-INF/c.txt");//WEB-INF目录下的资源访问

​                         String a=context.getRealPath("WEB-INF/classes/a.txt")//src目录下的资源访问  

案例:文件下载需求:

​           1.页面显示超链接

​           2.点击超链接后弹出下载提示框

​           3.完成图片文件下载        

分析:

   1.超链接指向的资源如果能够被浏览器解析,则在浏览器中展示,如果不能解析.则提示下载提示框,不满足需求

   2.任何资源都必须淡出下载提示框

   3.使用响应头设置资源的打开方式:

​        *context-disposition:attachment;filename=xxx

步骤:

​     1.定义页面,编辑超链接href属性,指向那Servlet,传递资源名称filename

​     2.定义Servlet 

​          1.获取文件名称

​          2.使用字节输入流加载文件进内存

​          3.指定response的响应头:content-disposition:attachment;filename=xxx

​          4.将数据写出到response输出流

```java
//1.获取请求参数,文件名称
  String filename=request.getParameter("filename");
  //2.使用字节输入流加载文件进内存
  ServletContext servletContext=this.getServletContext();
  String realPath=servletContext.getRealPath("/img/"+filename);
  //2.用字节流关联
  FileInputStream fis=new FileInputStream(realPath);
  //3.设置response的响应头cnotext-type
  String mimeType = servletContext.getMimeType(filename);//获取文件的mime
  response.setHeader("contenet-type",mimeType);
  //3.2设置响应头打开方式:context-disposition
 response.setHeader("context-disposition","attachment;filename");

  //4.将输入流的数据写出到输出流中
  ServletOutputStream sos = response.getOutputStream();
  byte[] buff= new byte[1024 * 8];
  int len=0;
  while((len=fis.read())!=-1){
      sos.write(buff,0,len);
  }
  fis.close();
```

问题:

​        *中文文件问题

​             *解决思路

​                1.获取客户端使用的浏览器版本信息

​                2.根据不同的版本信息,

  Cookie:

​           1.概念:客户端绘画技术,将数据保存到客户端

​           2.快速入门:

​               *使用步骤:

​                    1.创建Cookie对象,绑定数据

​                        *new Cookie(String name,String value)

​                    2.发送Cookie对象

​                      *response.addCookie(Cookie cookie)

​                    3.获取Cookie,拿到数据

​                       *Cookie[] request.getCookies()

实现原理:

​       *基于响应头set-cookie和请求头cookie实现

cookie的细节

​      1.一次可不可以发送多个cookie

​                可以

​                可以创建多个对象,使用response调用多次addCookie方法发送cookie即可

​     2.cookie在浏览器中保存多长时间

​               1.默认情况下,当浏览器关闭后,cookie数据被销毁

​               2.持久化存储:

​                 *setMaxAge(int seconds)

​                             1.正数;将cookie数据写到硬盘的文件夹中.持久化存储.cookie存活时间

​                             2.负数:默认值

​                             3.零:删除cookie信息

 3.cookie鞥不能存中文?

​           *tomcat8之前不能直接存储中文数据

​                    *需要讲中文数据转码--一般采用URL编码

 4.cookie获取范围多大?

​        setPath(String path ):

​        不同服务器间cookie共享:

​         setDomain(String path)

​                *setDomain(".baidu.com")一级域名相同可以共享

 cookie存储在客户端浏览器

 浏览器对于cookie大小有限制(4kb)以及对同一个域名下的总cookie数量也有限制(20个)

​      cookie用于存储少量不明感完成服务器对客户端的身份识别

##JSP:

​     1.概念:

​           *java server pages:java服务器端页面

​              *可以理解为一个特殊的页面,其中既可以指定定义html标签,又可以定义java代码

​              *用于简化书写

​      2.原理:

​               *jsp本质上是一个Servlet

​      3.JSP的脚本:JSP定义java代码的方式

​      4.JSP的内置对象

​              <% 代码 %>

​              <!% 代码 %>

​              <%= 代码 %>

​      5.指令

​           *作用:用于配置JSP页面,导入资源文件

​           *格式:

​                <% 指令名称 属性名1=属性值1 属性名2=属性值2 ...%>

​           *分类:

​                1.page:配置JSP页面的

​                        *contentType:等同于response.setContentType()

​                        *import

​                        *ErrorPage

​                        *isErrorPage

​                2.include:页面包含的.导入页面的资源文件

​                 *<%@include file="500.jsp"%>

​                3.taglib:导入资源

​                   *<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

​        注释:  1.html 只能注释片段<!---->       2.jsp注释   <%-- --%>   可以注释所有脚本内容

​        内置对象:在jsp页面中不需要创建,直接使用的对象

​               *pagecontext    当前页面共享数据,还可以获取其他8个对象

​               *request 

​               *session

​               *applicaiton

​              *response

​              *page

​              *out

​              *config

​              *exception

Session:

​    1.获取HttpSession对象:

​            HttpSession session=request.getSession();

​     2.使用HttpSession对象:

​            Object getAttribute(String name)

​            void setAttribute(String name,Object value)

​            void removeAttribute(String name)

   3.原理:

​       *Session的实现是依赖于Cookie的

EL表达式:

​          1.概念:Expression Languare 表达式语言

​          2.作用:替换和简化jsp页面中java代码的编写

​          3.语法:${表达式}

​          4.注意:

​               *jsp默认支持el表达式,如果要忽略el表达式

​                        1.设置jsp中isEL

​               获取对象:

​                   1.对象:${域名城.键名.属性值}

​               pageContext.request.contextPath(动态虚拟目录)

$##JSTL:

​     1.概念:JSP标准标签库

​     2.作用:用于简化和替换jsp页面上的java代码

​     3.使用步骤:

​             1.导入jstl相关jar包

​             2.引入标签库:taglib指令  <% taglib %>

​    4.常用的jstl标签:

​           if :

​              属性:

​                         

​           foreach:

​            属性:begin:开始值

​                     end:结束值

​                     var:临时变量

​                    step:步长

​                    varStatus:循环状态对象

​                     index:索引

​                     count循环次数

​               List<USer> list;

​               属性:items容器对象

​                       var容器里的临时变量

​                       varStatus:循环状态对象

​                       index

​                       count

MVC:

技术选型:Servlet+JSP+Mysql+JDBCTempleate+Durid+BeanUtils+tomcat

数据库设计:

​       create dateabase day17;

​       use day17;

​       create table user(

​       id int primary key auto_increment,

​       name varchar(20) not null,

​       gender varchar(5),

​      age int,

​      address varchar(32),

​      qq varchar(20),

​      email varchar(50)  );

开发:

 1.环境搭建:

​                  1.创建数据库环境

​                  2.创建项目,导入需要的jar包

2.编码:

案例:::::::::::::::::::::::::::::::::

Filter:

1./index.jsp具体资源路径

2./user/*拦截目录

3.*.jsp后缀名拦截

4./*

多个过滤器值小的先执行

JQuery:

1.内容操作

​          1.html():获取:设置元素标签体内容   "<a><font>内容</font></a>"--><font>内容</font>

​          2.text():获取/设置元素的标签体纯文本内容  <a><font>内容</font></a>-->内容

​          3.val():获取/设置元素的value属性值

2.属性操作:

​         1.通用属性操作:

​                 1.attr():获取/设置元素的属性

​                 2.removeAttr():删除属性

​                 3.prop():获取/设置元素的属性

​                 4.removeProp():删除属性

//操作自定义属性用attr

​        2.对class属性操作:

​                 1.addClass():添加class属性值

​                 2.removeClass():删除class属性值

​                 3.toggleClass():切换class属性

​                                *有则删除属性,没有就创建

3.CRUD操作

​       1.append();父元素将子元素追加到末尾

​           *对象1.append(对象2):将对象2添加到对象1元素内部,并且在末尾

​       2.prepend():父元素将子元素追加到开头

​           *对象1.prepend(对象2):将对象2添加到对象1元素内部,并且在开头

​       3.appendTo():

​           *对象1.appendTo(对象2):将对象1添加到对象2内部,并且在末尾

​       4.prependTo():

​           *对象1.prependTo(对象2):将对象1添加到对象2内部,并且在开头

​       5.after():添加元素到元素后边

​           *对象1.after(对象2):将对象2添加到对象1后边.对象1和对象2是兄弟

​       6.before():添加元素到元素前边

​           *对象1.before(对象2):讲对象2添加到对象1前边.对象1和对象2是兄弟

​       7.insertAfter():

​           *对象1.insertAfter(对象2):将对象2添加到对象1后边,对象1和对象2是兄弟

​       8.insertBefore():

​           *对象1.insertBefore(对象2):将对象2添加到对象1前边.对象1和对象2是兄弟

​       9.remove():移除元素

​           *对象.remove():将对象删除掉

​       10.empty():清空元素的所有后代元素

​           *对象.empty():将对象的后代元素全部清空,但是保留当前对象已及其属性节点

JQuery动画

​        1.动画

​                    1.三种方式显示和隐藏元素

​                         1.默认显示和隐藏方式

​                          show([speed,[easing],[fn])    速度,指定切换效果,执行次数

​                          hide([speed,[easing],[fn])  

​                          toggle([speed,[easing],[fn])  

​       2.遍历

​                  1.js的遍历方式 

​                      *for(初始化值;循环结束条件;步长)

​                  2.jq的遍历方式

​                      *jq对象.each(callback)

​                      *$.each(object,[callback])

​                      *for..of

​      3.事件绑定

​               1.jquery标准绑定方式

​                  jq对象.事件方法

​               2.on绑定事件/off解除绑定

​                jq.on    jq.off

​               3. 事件切换"toggle           

​                     *jq对象.toggle(fn1,fn2...)

插件:增强JQuery的功能:

​           1.$.fn.extend(object)

​               *增强通过Jquery获取的对象的功能

​           2..extend()                         

#AJAX:

​       1.概念ASynchronous JavaScript and XML

```javascript
function fun() {
   //发送异步请求
    //1.创建核心对象
    var xmlHttp;
    if (window.XMLHttpRequest){
        xmlHttp=new XMLHttpRequest();
    }else{
        xmlHttp=new ActiveXObject("Microsoft.XMLHTTP");
    }
    //2.建立连接
    xmlhttp.open("GET","ajaxServlet?username=tom",true);
    //3.发送请求
    xmlhttp.send()
    //4.接受并处理来自服务器的响应结果
    //获取方式:xmlhttp.responseText
    //当xmlhttp对象的就绪状态改变时,触发事件onreadystatechange
    xmlhttp.onreadystatechange=function () {
       if(xmlhttp.readyState==4&&xmlhttp.status==200){
           var responseText=xmlhttp.responseText;
           alert(responseText);
       }
    }
}
```

​       2.Jquery实现方式

​         *$.ajax():

​         *$.get():发送get请求

​              语法:$.get(url,[data],[callback],[type])

​              参数:

​                       *url:请求路径

​                       *data:请求参数

​                       *callback:回调参数

​                       *type:响应结果类型

#JSON:

1.概念: JavaScript Object Notation   Javascript对象表示法

​     Person p=new Person();

​     p.setName("张三");

​     p.setAge(23);

var p={"name":"张三","age":23,"gender":"男"};

Json多用于存储和交换文本信息的语法

进行数据的传输

jsonbixml更小更快更易解析

2.语法:

(1)基本规则:

数据在名称/值对中:json数据是由键值对构成的

​    *键用引号(单双都行)引起来,也可以不使用引号

​    *值的取值类型:

​        1.数字(整数浮点)

​        2.字符串(双引号中)

​        3.逻辑值true

​        4.数组(在方括号中[])

​        5.对象

​        6.null

数据逗号分割

使用{}定义json格式

(2)遍历

(3)

#Redis

概念:redis是一款高性能的NOSQL非关系型数据库

hash  hset  hdel

lpush  rpush   lrange   lpop  rpop

set  sadd smenbers  srem

sortedset    zadd  zrange  zren    

keys *