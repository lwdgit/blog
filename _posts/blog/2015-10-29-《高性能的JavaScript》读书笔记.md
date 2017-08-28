#### 第1章 加载与执行

当浏览器遇到`<script>`标签时，当前HTML页面无从获知JavaScript是否会向页面动态添加元素，因此，这时它会停止处理页面，先执行JavaScript代码，然后再继续解析和渲染页面。同样的情况也发生在使用scc属性加载JavaScript的过程中。

> 如果想在页面加载前决定是否应该显示页面(如不允许IE显示)，可以把JavaScript写在最前。

HTML4为`<script>`标签定义了一个扩展属性：defer。该属性只有IE4+和Firefox 3.5+的浏览器支持，其它浏览器会被直接忽略。


动态脚本元素
```javascript
function loadScript(url, callback) {
    var script = document.createElement('script');
    script.type = 'text/javsscript';

    if (script.readyState) {
        script.onreadystatechange = function () {
            if (script.readyState == 'loaded' || script.readyState == 'complete') {
                script.onreadystatechange = null;
                callback();
            }
        } else {
            script.onload = function () {
                callback();
            };
        }
    }
    script.src = url;
    document.getElementsByTagName('head')[0].appendChild(script);
}
```
这个新创建的`<script>`元素加载了file1.js文件。这种方法无论在何时启动下载，文件的下载和执行过程不会阻塞页面其他进程。

> 通常来讲，把新创建的`<script>`标签添加到`<head>`标签里比添加到`<body>`里更保险。原因：当`<body>`中的内容没有完全加载完成时，IE可能会抛出一个“操作已终止”的错误信息。


XMLHttpRequest脚本注入
```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'file1', true);
xhr.onreadystatechange = function () {
    if (xhr.readyState == 4) {
        if (xhr.status >= 200 && xhr.status < 300 || xhr.status == 304) {
            var script = document.createElement('script');
            script.type = 'text/javascript';
            script.text = xhr.responseText;
            document.body.appendChild(script);
        }
    }
}
```
存在跨域问题

----------------------
#### 第2章 数据访问
管理作用域：如果某个跨作用域的的值在函数中被引用一次以上，那么就把它存储到局部变量里，如document。

其他能改变作用域链的方法：with,try- catch,eval(eval可以通过`eval("var window = {};")`来动态改变作用域，一般情况下要避免使用它们。

属性层次越深，访问速度也就越慢

在Safari中，object.name比object[name]访问更快

window.location.href比location.href慢。

尽可能缓存要多次用到的对象成员。

多次调用同一个函数会导致创建多个运行期上下文，当函数执行完，**执行期上下文(execution context)**就被销毁（闭包除外）

使用with使得访问document对象的属性变快，但是却让访问局部变量变慢了，所以，最好避免使用with

闭包会经常访问大量跨作用域的标识符，每次访问都会导致性能损失（速度慢且占较多内存），因此最好小心使用闭包，建议将常用跨作用域的变量存储在局部变量中。

在Firefox,Safari,Chrome中，`__proto__`对开者可见

JavaScript中的对象都是基于原型的。

对象可以有两种成员类型：实例成员（也称为“own”成员）和原型成员。


----------------------
#### 第3章 DOM编程

减少DOM的访问次数，把运算尽量留在ECMAcript这一端处理。

节点克隆比节点创建更有效率。

访问元素合集时使用局部变量缓存。

nextSibling比childNode快，IE6快16倍，IE7快105倍。

**能区分元素节点(HTML标签)和其他节点的DOM属性**

|     属性名           |  被替换的属性    |
|----------------------|------------------|
|children              |childNodes        |
|firstElementCount     |childNodes.length |
|firstElemenChild      |firstChild        |
|lastElementChild      |lastChild         |
|nextElementSibling    | nextSibling      |
|previousElementSibling| previousSibling  |

上表列出的所有属性都被firefox3.5,safari4,chrome2以及opera9.62所支持。其中IE6,7,8只支持children属性。

在所有浏览器中，children都比childNodes要快

使用querySelectorAll()选择器API替代getElementById()的getElementsByTagName()
以下浏览器比选择器API提供了原生支持:IE8，Firefox3.5，Safari3.1,Chrome1及Opera10
还可以使用querySelector来获取第一个匹配的节点

浏览器页面发生重绘的情况

* 添加或删除可见的DOM元素
* 元素位置改变
* 元素尺寸改变(包括：外边距、内边距、边框厚度、宽度、高度等属性改变)
* 内容改变，例如：文本改变或图片被另一个不同尺寸的图片替代
* 页面渲染器初始化
* 浏览器窗口尺寸改变

获取布局信息的操作可能会导致队列刷新，如下

* offsetTop,offsetLeft,offsetWidth,offsetHeight
* scrollTop,scrollLeft,scrollWidth,scrollHeight
* clientTop,clientLeft,clientWidth,clientHeight
* getComputedStyle()(CurrentStyle in IE)
所以，尽可以避免使用上面列出的属性

使用cssText替代style设置元素样式

改变className可能会带来性能损失，因为浏览器要检查内联样式

批量修改DOM可以通过以下步骤来减少重绘和重排

1.使元素脱离文档流
2. 对其应用多重改变
3. 把元素带回文档中

有三种基本方法可以使DOM脱离文档：

* 隐藏元素，应用修改，重新显示
* （推荐）使用文档片断(document.createDocumentFragment())在当前DOM之外构建一个子树，再把它带回文档中
* 将原始元素拷贝到一个脱离文档的节点中，修改副本，完成后替换原始元素。

让元素脱离文档流可以避免页面中的大部分重排带来的性能消耗
通过以下步骤可以避免：

* 使用绝对定位页面上的动画元素，使其脱离文档流
* 让元素动起来。当它扩大时，会临时覆盖部分页面。但这只是页面一个小区域的重绘过程，不会产生重排并重绘页面的大部分内容
* 当动画结束时恢复定位，从面只会下移一次文档的其他元素。

IE与:hover
大量使用:hover伪类，会降低响应速度，IE8中更为明显

使用事件委托来减少事件处理器的数量。
事件冒泡跨浏览器兼容的部分包括：

* 访问事件对象，并判断事件源
* 取消文档树中的冒泡（可选）
* 阻止默认动作

```javascript
document.getElementById('menu').onclick = function(e) {
    e = e || window.event;
    var target = e.target || e.srcElement;

    var pageid, hrefparts;

    if (target.nodeName !== 'A') {
        return;
    }
    hrefparts = target.href.split('/');
    pageid = hrefparts[hrefparts.length - 1];
    pageid = pageid.replace('.html', '');

    //更新页面...

    if (typeof e.preventDefault == 'function') {
        e.preventDefault();
        e.stopPropagation();
    } else {//IE
        e.returnValue = false;
        e.cancelBubble = true;
    }
}

```

----------------------
#### 第4章 算法和流程控制

for-in循环只有其它类型循环的性能的1/7

倒序循环更快，如`for (var i= item.length;i--; )`

如果循环大于1000，建议使用达夫设备(Duff's Device)(在一次迭代中执行多次迭代操作，循环大于1000时性能有明显提升)
```javascript
//credit: Jeff Greenberg
var iterations = Math.floor(items.length / 8),
    startAt = items.length % 8,
    i = 0;
do {
    switch(startAt) {
        case 0: process(items[i++]);
        case 7: process(items[i++]);
        case 6: process(items[i++]);
        case 5: process(items[i++]);
        case 4: process(items[i++]);
        case 3: process(items[i++]);
        case 2: process(items[i++]);
        case 1: process(items[i++]);
    }
    startAt = 0;
} while(--iterations);

```

在所有情况下，基于循环的迭代比基于函数的迭代(如`forEach()`)快8倍

大多数条件下，switch比if-else运行得要快。

如果条件发生概率大体相同（离散），switch更合适

if-else和switch都比**查找表**慢很多

递归函数可能会遇到浏览器的“调用栈大小限制”(Call stack size limites)

使用Memoization可以极大地优化递归性能
```javascript
function memfactorial(n) {
    if (!memfactorial.cache) {
        memfactorial.cache = {
            "0": 1,
            "1": 1
        };
    }
    if (!memfactorial.cache.hasOwnProperty(n)) {
        memfactorial.cache[n] = n * memfactorial(n - 1);
    }
    return memfactorial.cache[n];
}
```



----------------------

#### 第5章 字符串和正则表达式

用+号连接字符串时，将变量放在左边，避免使用+=写法（IE7以前不适用）可以提升性能。

大多数浏览器中，数组项连接比其它字符串连接(Array.prototype.join)的方法更慢，但是IE7及以下该方法却是唯一高效的途径。

在多数情况下，使用concat比使用简单的 + 和 += 稍慢，尤其是在IE、Opera和Chrome中更明显。

正则表达式工作原理：

第一步：编译
第二步：设置起始位置
第三步；匹配每个正则表达式字元（回溯发生在这一步）
第四步：匹配成功或失败

减少回溯次数可以提升正则表达式性能。

尽可能具体化字符串匹配模式的匹配内容，这样可以减少回溯次数(针对.*这种通用匹配而言，比如.*?可以更换成更为具体的[^\r\n]*)

>使用反向引用模拟元子组加速匹配，模式为:`(?=(元子组))\1`，因为它可以阻止回溯

更多提高正则表达式效率的方法：

* 关注如何让匹配更快失败（因为正则表达式慢通常是匹配失败的过程慢）
* 正则表达式以简单，必须的字元开始（如：以`\s\s*`替代`\s+`或`\s{1,}`）
* 使用量词模式，使它们后面的字元互斥（如**不要**使用`".*?`来替代`"[^"\s\n]*`,因为它依赖回溯）
* 减少分支数量，缩小分支范围
* 使用非捕获组(可以使用`regex.exec()`返回数组中的第一项，或在替换中使用`$&`)
* 只捕获感兴趣的文本以减少二次处理
* 暴露必需的字元
* 使用合适的量词
* 把正则表达式赋值给变量并重用它们
* 将复杂的正则表达式拆分为简单的片段（化繁为简）

使用正则表达去首尾空白（这里只例举两种种相对快速并且容易书写的方法）
```javascript
    if(!String.prototype.trim) {
        String.prototype.trim = function() {
            return this.replace(/^\s+/, "").replace(/\s+$/, "");
        }
    }

    //下面这种方法是为了弥补浏览器遇到$不会直接从字符串后面开始匹配的不足
    String.prototype.trim = function() {
        var str = this.replace(/^\s+/, ""),
        end = str.length - 1,
        ws = /\s/;
        while(ws.test(str.charAt(end))) {
            end--;
        }
        return str.slice(0, end + 1);
    }
```

如果不考虑IE7及更早版本的性能，数组连接是最慢的字符串连接方法之一，推荐直接使用+和+=

----------------------

#### 第6章 快速响应的用户界面

浏览器限制

* IE4以上默认限制为500万条语句
* Firefox默认限制为10秒
* Safari默认限制为5秒
* Chrome、Opera不限制

>单个Javascript运行时间不应该超过100ms

使用定时器让出时间片段

> 如果一个处理过程满足以下两下因素，则可以使用定时器分解

>* 处理过程无需同步
>* 数据无需按序处理
 
 一个基本的异步代码模式如下：

```javascript
function processArray(items, process, callback) {
    var todo = items.concat();
    setTimeout(function() {
        var start = new Date();
        do {
            process(todo.shift());
        }while(todo.length > 0 && (+new Date() - start < 50));//执行时间小于50毫秒不间断执行

        if(todo.length > 0) {
            setTimeout(arguments.callee, 25);//25重复执行自己，最好使用至少25秒，因为再少的延时对UI更新而言不够用
        } else {
            callback(items);
        }
    }, 25);
}

var items = [1,2,54,156,4,687,1,64,5,79];
function outputValue(value) {
    console.log(value);
}

processArray(items, outputValue, function() {
    console.log('all done');
});
```

分割任务

```javascript
function mutistep(steps, args, callback) {
    var tasks = steps.concat();//克隆数组
    setTimeout(function() {
        var start = +new Date();
        do {
            var task = tasks.shift();
            task.apply(null, args || []);
        } while(task.length > 0 && (+new Date() - start < 50));
        
        if (task.length > 0) {
            setTimeout(arguments.callee, 25);
        } else {
            callback();
        }
    }, 25);
}

function demo(id) {
    var tasks = [openDocument, writeText, closeDocument, updateUI];
    mutistep(tasks, [id], function() {
        console.log('save completed!');
    });
}
//使用前提：任务可以异步处理而不影响用户体验或造成相关代码段错误。
```


使用Web Workers
```javascript
var worker = new Worker('jsonparser.js');
worker.onmessage = function(event) {
    //event.data;
    ...
}
worker.postMessage(data);
//jsonparser.js
self.onmessage = function(event) {
    //event.data
    ...
    self.postMessage(res);
}

```

在以下情况下可以考虑使用Web Workers:

* 编码/解码大字符串
* 复杂数学运算（包括图像或视频）
* 大数组排序

> 任何超过100ms的处理过程应当考虑Worker方案而不是定时器方案，当然如果你能确保浏览器支持

----------------------

#### 第7章 Ajax

五种常用技术用于向服务器请求数据：

* XMLHttpRequest(XHR)
* Dynamic script tag insertion 动态脚本注入
* iframes
* Comet
* Multipart XHR

经GET请求的数据会被缓存起来

URL长度超过2048个字符时，应该使用POST获取数据

动态脚本注入可以跨域请求数据

MXHR单次请求比各自独立请求的方式快4到10倍

只发送少量数据且需要跨域可以使用信标(Beacons)方法
```javascript
(new Image()).src="http://baidu.com?" + params;

var beacon = new Image)();
beacon.src = url + '?' + data;
beacon.onload = function() { ... }
beacon.onerror = function() { ... }
```

数组型式的JSON比标准JSON解析更快
```javascript
//解析数组型JSON
function parseJSON(responseText) {
    var users = [];
    var usersArray = eval('(' + responseText + ')');
    for (var i = 0, len = usersArray.length; i < len; i++) {
        users[i] = {
            id: usersArray[i][0],
            ...
        };
    }
    return users;
}
```


如果要处理10000以上的元素列表，建议使用JSON-P代替JSON

JSON-P是一种脚本注入(Script Injection)行为，所以有一定的安全隐患。

JSON-P可以跨域
```javascript 
function jsonpCallback(result)  
{  
    console.log(result);  
}  
 
var jsonp = document.createElement('script');
jsonp.src = "http://crossdomain.com/jsonServerResponse?jsonp=jsonpCallback";//把回调函数传给服务器
document.getElementsByTagName('body')[0].appendChild(jsonp);

```

multipart XHR使用了流功能，通过监听readyState为3的状态，我们可以在一个较大的响应还没有完成接收之前把它分段处理。

XMLHttpRequest:
```javascript
function createXhrObject() {
    var msxml_progid = [
        'MSXML2.XMLHTTP.6.0',
        'MSXML3.XMLHTTP',
        'Microsoft.XMLHTTP',//不支持readyState 3
        'MSXML2.XMLHTTP.3.0'//不支持readyState 3
    ];
    var req;
    try {
        req = new XMLHttpRequest();
    } catch(e) {
        for (var i = 0, len = msxml_progid.length; i < len; i++) {
            try {
                req = new ActiveXObject(msxml_progid[i]);
                break;//如果上面出错，就不会执行到这一句
            } catch(e2) {

            }
        } 
    }
    finally {
        return req;
    }
}

```

------------------------------

#### 第8章 编程实践

> 当在Javascript中执行另一段Javascript代码时，会导致双重求值的性能消耗。有四种方法：eval, Function, setTimeout, setInterval，所以应避免给它们传递字符串代码。

浏览器事件处理器：
```javascript
function addHandler(target, eventType, handler) {
    if (target.addEventListener) {
        target.addEventListener(eventType, handler, false);
    } else {//IE
        target.attachEvent('on' + eventType, handler);
    }
}
function removeHandler(target, eventType, handler) {
    if (target.removeEventListener) {
        target.removeListener(target, handler, false);
    } else {//IE
        target.detachEvent('on' + eventType, handler);
    }
}

//使用延迟加载(Lazy loading)
function addHandler(target, eventType, handler) {
    if (target.addEventListener) {
        addHandler = function(eventType, eventType, handler) {
            target.addEventListener(eventType, handler, false);
        }    
    } else {//IE
        addHandler = function(eventType, eventType, handler) {
            target.attachEvent('on' + eventType, handler);
        }     
    }
    addHandler(target, eventType, handler);
}
function removeHandler(target, eventType, handler) {
    if (target.removeEventListener) {
        removeHandler = function(target, eventType, handler) {
            target.removeListener(target, handler, false);
        }
    } else {//IE
        removeHandler = function(target, eventType, handler) {
            target.detachEvent('on' + eventType, handler);
        }
    }
    removeHandler(target, eventType, handler);
}


//使用条件预加载
var addHandler = document.body.addEventListener ? 
    function(eventType, eventType, handler) {
        target.addEventListener(eventType, handler, false);
    } :
    function(eventType, eventType, handler) {
        target.attachEvent('on' + eventType, handler);
    };

var removeHandler = document.body.removeEventListener ? 
    function(target, eventType, handler) {
        target.removeListener(target, handler, false);
    } :
    function(target, eventType, handler) {
        target.detachEvent('on' + eventType, handler);
    };
```

利用位运算为javascript加速：

* 判断奇偶 `num & 1`
* 给定选项是否在列表中 `var options = [1,2,4,8] if(option & options[i]){...}`
* 取整 `~~value`

尽量使用原生方法，如Math,选择器API(querySelector,querySelectorAll)

----------------------

####第9章 构建并部署高性能JavaScript应用

网站提速指南：

* 减少页面渲染所需的HTTP请求数，将多个文件合并起来
* 文件压缩，去掉不必要的注释与空白,使用YUI Compressor或Closure Compiler压缩，或者在服务器端使用Gzip压缩。 
* 缓存JavaScript文件，通过"Expires HTTP 响应头"，告诉客户端一个资源应当缓存多长时间（iphone上的Safari不允许缓存解压后超过25KB的文件）
* 使用校验和(checksum)让缓存超期，如给js文件命名为:`core-yyMMddhhmm.js`
* 使用内容分发网络(CDN)，绝大数第三方类库都有CDN服务器。
* 使用工具加速构建，如Apache Ant

---------------------

#### 第10章 工具


* new Date方式
* YUI Profiler
* Firebug
    * Profile
    * Console API
    * 网络面板
* IE开发人员工具 -- 探查器(Profiler)
* Safari WEB检查器 -- Web Inspector
    * 分析面板(Profiles)
    * 资源面板(Resources)
* Chrome开发人员工具 (部分基于Webkit/Safari Web Inspector)
    * Timeline
    * Profiles
* Page Speed
* Fiddler
* YSlow
* dynaTrace Ajax Edition


> 给匿名函数取一个名字，如`window.document.onclick = function myClick() {...}`,便于分析工具显示出更有意义的分析结果






