## 写在前面的话：

- 写文目的：总结前端面试过程中没回答上来的点，深化印象，也方便日后温习。

- 写文方法：写之前先自己看相关知识，理解好后过段时间根据记忆来写，最后对比纠正。

- 目前笔者面试的职位是1-3年工作经验岗，阅读者可将此作为前端面试参考题目。

## 准备面试流程：

1.更新简历，多参考其他的优秀简历

2.复习面试题

笔者就是因为只更新了简历没有复习导致基础不过关，错过两家心仪的公司。

顺便说下，平时工作中的确很多时候用不到也不会去背某些面试中的问题，不过面试题中基础部分很重要，理解好基础才更轻松掌握新框架新知识。时不时去看看面试题也不失为一种巩固基础的方法。一般一面面基础，二面面项目，项目经验再多，一面过不了，什么戏也不会有。

## 被面过的面试题：

> 什么是闭包

闭包是由函数与创建该函数的环境所组成的

优点：减少全局变量污染

缺点：影响脚本性能

>3==true 打印出什么

会打印出false，这里会将true转变成1

Number 和 String 或者 Bool 类型比较，会对String 或者 Bool 类型进行ToNumber()转换
```
Number(true) // 1
Number(false) // 0
1==true //true
1===true //false
3==true //false
```

>变量声明提升

变量声明无论出现在代码的任何位置，都会在代码执行前处理。所以在代码的任何位置声明变量就好像在代码开头声明一样。

```
var a = 100;
function fn() {
    alert(a);
    var a = 200;
    alert(a);
}
fn();
alert(a);
var a;
alert(a); //这里alert出100
var a = 300;
alert(a);
```
>判断变量是不是数组的几个方法
```
var a=[];
a.constructor===Array //true
a instanceof Array === true //true
```
**⚠️ 注意：以上方法在跨frame时会有问题，跨frame实例化的对象不共享原型**

```
var iframe = document.createElement('iframe');   //创建iframe  
document.body.appendChild(iframe);   //添加到body中  
xArray = window.frames[window.frames.length-1].Array;     
var arr = new xArray(1,2,3); // 声明数组[1,2,3]     
  
alert(arr instanceof Array); // false     
  
alert(arr.constructor === Array); // false   
```

**解决：**

```
Object.prototype.toString.call(a) // "[object Array]"
Array.isArray(a) //true
```

> bind,call,apply用法与区别

用法：都是改变函数内this指向

bind:返回一个新的函数，调用新的函数才执行，新函数里this永久地被绑定到了bind的第一个参数上

而call与apply能直接执行
```
fuc.call(thisObj,a,b,c)

fuc.apply(thisObj,[a,b,c])
```

>js三大组成部分

1.ECMAScript
2.dom
3.bom

> http返回码100 200 300 400分别代表什么，即响应的5种类型

100:信息响应

200:成功

300:重定向

400:客户端错误

500:服务端错误


>理解重定向


>http无状态含义


>解释三次握手，四次挥手

**三次握手**

1.客户端发syn包给服务端，等待服务器确认（syn:同步序列编号（Synchronize Sequence Numbers））

2.服务端发syn+ack包给客户端

3.客户端发确认包ack给服务端

**四次挥手**

中断连接端可以是Client端，也可以是Server端。

1.关闭主动方发送fin包

2.被动方发送ack包

3.被动方关闭连接，发送fin包

4.主动方发送ack包确认

> 自己实现个jQuery，可以传选择器进去，然后实现css()与height()方法

```
var myJquery4 = function (selector) {
        //console.log(this) //window
        return new jqNodes(selector) //new会创造一个对象实例，对象实例继承自构造函数的prototype,这里是jqNodes.prototype
    }

    var jqNodes = function (selector) {
        console.log(this)
        //1.以new调用时this指向即将创建的新对象 
        //2.直接调用则指向 window
        this._items = document.querySelectorAll(selector)
        return this
    }

    var myJqueryCore = {
        //放核心方法
        css: function () {
            console.log(this) //myJquery4('li').css('color') 这样调用时是作为new出来的对象原型里的方法调用的，this指向new出来的对象
            var prop = arguments[0],
                val = arguments[1]
            if (!val) return getComputedStyle(this._items[0]).getPropertyValue(prop);

            Array.prototype.map.call(this._items, function (c) {
                return c.setAttribute('style', prop + ':' + val)
            })
            return this
        }
    }

    jqNodes.prototype = myJqueryCore //关键
```


> 写个轮播图

之前感觉比较困扰的是从最后一页到第一页如何无缝滑动。无缝滑动的关键在于在第一页前放上最后一页，在最后一页后面再放上第一页。在最后一页点击后一页时，先滑动到放置在后边的第一页，滑动完成后立刻改变父元素的left值到排列在第二个的第一页。

html结构：
```
<div id="list" style="left: -600px;">
    <div>5</div>
    <div>1</div>
    <div>2</div>
    <div>3</div>
    <div>4</div>
    <div>5</div>
    <div>1</div>
</div>
```
关键js:
```
$('#list').animate({ left: newLeft }, function () {
    if (newLeft < -3000) {
        list.style.left = -600 + 'px';
    } else if (newLeft > -600) {
        list.style.left = -3000 + 'px';
    }
})
```

完整代码可点击下面链接：https://github.com/lujing2/jsexercise/tree/master/carousel

>菜单高亮滚动监听
主要参考了 http://blog.csdn.net/sinrryzhang/article/details/51177774
我修改成了可以有多个固定导航的
```
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
    <style type="text/css">
        * {
            margin: 0;
            padding: 0;
        }

        .main {
            width: 1200px;
            margin: 0 auto;
            padding-bottom: 100px;
        }

        .m-cloumn {
            height: 300px;
            margin-bottom: 15px;
            border: 1px solid #ddd;
            box-shadow: 0 5px 5px -5px #f00;
        }

        .right-cloumn {
            position: fixed;
            width: 120px;
            top: 0px;
            right: 50px;
            border: 1px solid #ddd;
        }

        #menu2 {
            top: 300px;
        }

        .right-cloumn a {
            display: block;
            height: 40px;
            line-height: 40px;
            border-bottom: 1px solid #eee;
            text-align: center;
        }

        .right-cloumn .curr {
            background: #F0AD4E;
            color: #fff;
        }
    </style>

</head>

<body>
    <div class="main" id="main1">
        <div class="m-cloumn" id="c1">
            <h2>栏目1内容</h2>
        </div>
        <div class="m-cloumn" id="c2">
            <h2>栏目2内容</h2>
        </div>
        <div class="m-cloumn" id="c3">
            <h2>栏目3内容</h2>
        </div>
        <div class="m-cloumn" id="c4">
            <h2>栏目4内容</h2>
        </div>
        <div class="m-cloumn" id="c5">
            <h2>栏目5内容</h2>
        </div>
        <div class="m-cloumn" id="c6">
            <h2>栏目6内容</h2>
        </div>
        <div class="m-cloumn" id="c7">
            <h2>栏目7内容</h2>
        </div>

    </div>
    <div class="right-cloumn" id="menu1">
        <a href="#c1" class="curr">栏目1</a>
        <a href="#c2">栏目2</a>
        <a href="#c3">栏目3</a>
        <a href="#c4">栏目4</a>
        <a href="#c5">栏目5</a>
        <a href="#c6">栏目6</a>
        <a href="#c7">栏目7</a>
    </div>


    <div class="main" id="main2">
        <div class="m-cloumn" id="c2-1">
            <h2>栏目2-1内容</h2>
        </div>
        <div class="m-cloumn" id="c2-2">
            <h2>栏目2-2内容</h2>
        </div>
        <div class="m-cloumn" id="c2-3">
            <h2>栏目2-3内容</h2>
        </div>
        <div class="m-cloumn" id="c2-4">
            <h2>栏目2-4内容</h2>
        </div>
        <div class="m-cloumn" id="c2-5">
            <h2>栏目2-5内容</h2>
        </div>
        <div class="m-cloumn" id="c2-6">
            <h2>栏目2-6内容</h2>
        </div>
        <div class="m-cloumn" id="c2-7">
            <h2>栏目2-7内容</h2>
        </div>

    </div>
    <div class="right-cloumn" id="menu2">
        <a href="#c2-1">栏目2-1</a>
        <a href="#c2-2">栏目2-2</a>
        <a href="#c2-3">栏目2-3</a>
        <a href="#c2-4">栏目2-4</a>
        <a href="#c2-5">栏目2-5</a>
        <a href="#c2-6">栏目2-6</a>
        <a href="#c2-7">栏目2-7</a>
    </div>

    <script src="../jquery.min.js" type="text/javascript"></script>
    <script>
        (function ($, win, doc) {
            var scroll_highlight = function (obj) {
                return new scroll_rsilder(obj)
            }
            var scroll_rsilder = function (obj) {
                this.init(obj)
            }
            scroll_rsilder.prototype = {
                win_evet: function () {
                    var _this = this._this
                    $(win).on("scroll", function () {
                        var scrollTop = $(this).scrollTop();
                        _this.ele_evet(scrollTop);

                    })
                },
                ele_evet: function (scrollTop) {
                    var _this = this._this
                    $(this.cloumn).each(function (index) {
                        var offsetTop = $(this).offset().top;
                        var xd = parseInt(offsetTop - scrollTop);
                       
                        //console.log(index, offsetTop, scrollTop, xd, _this)

                        if (xd < _this.spacing) {
                            $(_this.silder).eq(index).addClass(_this.curr).siblings().removeClass();
                        }
                    })
                },
                init: function (obj) {
                    this._this = this,
                        this.cloumn = obj.cloumn,
                        this.silder = obj.silder,
                        this.spacing = obj.spacing || 100,
                        this.curr = obj.curr || "curr";
                    if (!this.cloumn) return;
                    this.win_evet();
                }

            }
            win.scroll_highlight = scroll_highlight;
        })(jQuery, window, document)
    </script>
    <script>
        $(function () {
            scroll_highlight({
                cloumn: "#main1 .m-cloumn",
                silder: "#menu1 a",
                spacing: 80,
                curr: "curr"
            })

            scroll_highlight({
                cloumn: "#main2 .m-cloumn",
                silder: "#menu2 a",
                spacing: 80,
                curr: "curr"
            })

        })
    </script>



</body>

</html>
```

## 网上看的面试题&自己发现的面试知识点

注：自己发现的面试知识点一般是在工作学习中发现的基础重要但之前被自己忽略的知识点。
>全等号优先级大于逻辑与
```
var a=1===2&&3?4:5
```

>逗号操作符

逗号操作符  对它的每个操作数求值（从左到右），并返回最后一个操作数的值。

```
var x=(0,1) //x=1
```

> 比较对象

两个独立声明的对象永远也不会相等，即使他们有相同的属性，只有在比较一个对象和这个对象的引用时，才会返回true.

>  encodeURI   encodeURIComponent

- encodeURI
encodeURI 会替换所有的字符，但不包括以下字符，即使它们具有适当的UTF-8转义序列：
```
类型	     包含
保留字符	; , / ? : @ & = + $
非转义的字符	字母 数字 - _ . ! ~ * ' ( )
数字符号	#
```
encodeURI自身无法产生能适用于HTTP GET 或 POST 请求的URI，例如对于 XMLHTTPRequests, 因为 "&", "+", 和 "=" 不会被编码，然而在 GET 和 POST 请求中它们是特殊字符。然而encodeURIComponent这个方法会对这些字符编码。

- encodeURIComponent
转义除了字母、数字、(、)、.、!、~、*、'、-和_之外的所有字符。

dangerouslysethtml会过滤掉__html属性里的 \ ，是因为用了encodeURI函数来避免xss攻击
 ```
encodeURI('/\/\//') -->”////“
encodeURI('\') --> Uncaught SyntaxError: Invalid or unexpected token
encodeURIComponent('/\/\//') -->"%2F%2F%2F%2F"
encodeURIComponent('/') --> "%2F"
encodeURIComponent('\') --> Uncaught SyntaxError: Invalid or unexpected token
```

>xss

cross-site scripting，跨站脚本，属于代码注入的一种。它允许恶意用户将代码注入到网页上，其他用户在观看网页时就会受到影响。这类攻击通常包含了html以及用户端脚本语言。
攻击实例：用户A提交表单事加入类似以下代码，如果提交表单时没有做检测，而之后的结果页面是其他用户也能看到的，那么其他页面看到的结果页就会受到影响。
```
<script>
    document.write('...')
</script>
```
解决办法：不信赖用户输入，对特殊字符如”<”,”>”转义。


>uri url

uri:统一资源标识符
url:统一资源定位符
url与urn是uri的子集。
让uri能成为url的是那个“访问机制”，“网络位置”。e.g. http:// or ftp://.。
urn是唯一标识的一部分，就是一个特殊的名字。

>etag是什么


>鉴于http很重要，深化下对http的认识

>一级域名，二级域名

以www.baidu.com为例子
最右边一个点右边的为顶级域名（一级域名）com
一级域名左边是二级域名 baidu

>主域，子域

子域名域名系统等级中，属于更高一层域的域。比如，mail.example.com
和calendar.example.com是example.com的两个子域，而example.com
则是顶级域.com的子域




##非技术问题

>为什么换工作

不要说是因为钱少。上一家的薪资不要做假，因为公司可能会查。期望薪资可以要高点，说明。