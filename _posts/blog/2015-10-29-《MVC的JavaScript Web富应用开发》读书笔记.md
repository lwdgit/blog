### 第1章 MVC和类

[Holla(一个聊天应用)](http://github.com/maccman/holla)贯穿本书始终，它用到并实现了：

* 使用CSS3和HTML5来构建美观的界面
* 拖拽上传文件
* 使用Sprockets和Less来编写代码
* 使用WebSockets将数据发送给客户端
* 创建带有状态的Javascript应用

JavaScript: The Good Parts将会向你展示如何搭建复杂的JavaScript应用，教你创造不可思议的用户体验

________________________________________

构建大型的JavaScript应用的秘决是不要构建大型JavaScript应用。相反，你应当把你的应用解耦合成一系列相互平等且独立的部分。

> 开始构建应用时，要花点精力来做应用的构架，会为最终带来意想不到的改观。


MVC是一种设计模式，它将应用划分为3个部分：数据（模型）、展现层（视图）、和用户交互层（控制器）。

一个事件的发生过程是这样的：

1. 用户和应用产生交互。
2. 控制器的事件处理器被触发。
3. 控制器从模型中请求数据，并将其交给视图。
4. 视图将数据呈现给用户。


使用代理保持上下文

```javascript
var proxy = function(func, thisObject) {
    return(function() {
        return func.apply(thisObject, arguments);
    });
}
```

**模型**用来存放应用的所有数据对象。它不必知晓视图和控制器的细节，只需包含数据及直接和这些数据相关的逻辑。

模型中的数据也是面向对象的(object oriented),如下：
不要这样做：

```javascript
var user = users['foo'];
destoryUser(user);
```

而要这样做：

```javascript
var user = User.find('foo');
user.destory();
```

**视图**是呈现给用户的，用户与之产生交互。它不必知晓模型和控制器中的细节，它们是相互独立的。

将逻辑混入视图之中是编程的大忌，这并不是说MVC不允许包含视觉呈现相关的逻辑，只要这部分逻辑没有定义在视图之内即可。

**控制器**是模型与视图之间的纽带。 

使用命名空间和匿名函数封装一个作用域，可以避免对全局作用域造成污染。

new运算会改变函数执行上下文，同时也会改变return语句的行为。

不使用new构造出来的对象其上下文是window(全局对象)

`class`一直是保留字，虽然它从未被实现过

一种继承的简易实现:

```javascript
var Class = function() {
    var klass = function() {
        this.init.apply(this, arguments);
    };

    //使klass也可以继承
    if (parent) {
        var subclass = function() {};
        subclass.prototype = parent.prototype;
        klass.prototype = new subclass;
    }

    klass.prototype.init = function() {};
    klass.fn = klass.prototype;//定义prototype的别名
    klass.fn.parent = klass;//定义别名
    klass._super = klass.__proto__;//不是所有浏览器(IE不支持)支持设置__proto__
    klass.extend = function(obj) {
    var extended = obj.extended;
        for (var i in obj) {
            klass[i] = obj[i];
        }
        if (extended) extended(klass);
    };
    klass.include = function(obj) {
       var included = obj.included;
        for (var i in obj) {
            klass.fn[i] = obj[i];
        }
        if (included) included(klass);
    };
    
    klass.proxy = function(func) {
        var self = this;
        return (function() {//确保在正确的作用域中
            return func.apply(self, arguments);
        });
    };
    
    klass.fn.proxy = klass.proxy;

    return klass;
}

var Person = new Class;
Person.inclue({//继承
    save: function(id) {...},
    destory: function(id) {...}
});

var person = new Person;//实例化
person.save();
```
    
为了让子类继承父类的属性，首先需要定义一个构造函数

apply和call的作用：

* 动态改变函数上下文(context，也就是this指针)
* 委托，如`console.log.apply(console, arguments)`


**bind实现**
```javascript
if (!Function.prototype.bind) {
    Function.prototype.bind = function(obj) {
        var slice = [].slice,
        args = slice.call(arguments, 1);
        self = this,
        nop = function() {},
        bound = function() {
            return self.apply(this instanceof nop ? this : (obj || {}), args.concat(slice.call(arguments)));
        };
        nop.prototype = self.prototype;
        bound.prototype = new nop();
        return bound;
    };
}
```

______________________________________

### 第2章 事件和监听

不同的浏览器对事件类型支持也不尽同，但所有现代浏览器支持这些事件：

* click
* dbclick
* mouseover
* mousemove
* mouseout
* foucus
* blur
* change(表单输入框特有)
* submit(表单特有)

使用delegate事件委托可以使得动态添加的子元素都具有事件监听。

jQuery中可以使用`trigger()`函数来触发自定义事件，可以通过命名空间形式来管理事件名称，如：
```javascript
$('.class').bind('refresh.widget', function(event, data) {});//绑定自定义事件
$('.class').trigger('refresh.widget',data);//触发自定义事件
```

自定义事件和jQuery插件
html:

```html
<ul id="tabs">
    <li data-tab="users">Users</li>
    <li data-tab="groups">Groups</li>
</ul>
```

javascript:

```javascript
jQuery.fn.tabs = function(control) {
    var element = $(this),
    control = $(control);

    element.delegate('li', 'click', function() {
        var tabName = $(this).attr('data-tab');
        element.trigger('change.tabs', tabName);
    });

    element.bind('change.tab', function(e, tabName) {
        element.find('li').removeClass('active');
        element.find('>[data-tab=' + tabName + ']').addClass('active');
    });

    element.bind('change.tabs', function(e, tabName) {
        control.find('>[data-tab]').removeClass('active');
        control.find('>[data-tab=' + tabName + ']').addClass('active');
    });

    //激活第一个选项卡
    var firstName = element.find('li:first').attr('data-tab');
    element.trigger('change.tabs', firstName);

    return this;
};

/*******************
******修改事件******
******************/

//demo1
$('#tabs').trigger('change.tabs', 'users');

//demo2
$('#tabs').bind('change.tabs', function(e, tabName) {
    window.location.hash = tabName;
});
$(window).bind('hashchange', function() {
    var tabName = window.location.hash.slice(1);
    $('#tabs').trigger('change.tabs', tabName);
})

```
用法：

```javascript

$("ul#tabs").tabs("#tabContent");

```

事件本质上与DOM无关

发布/订阅(Pub/Sub)是一种解耦方法，它有两个参与者：发布者与订阅者

```javascript
//jQuery Tiny Pub/Sub
(function($) {
    var o = $({});
    $.subscribe = function() {
        o.bind.apply(o, arguments);
    };
    $.unsubscribe = function() {
        o.unbind.apply(o, agruments);
    };
    $.publish = function() {
        o.trigger.apply(o, arguments);
    };
})(jQuery);

//usage
$.subscribe('/some/topic', function(event, a, b, c) {
    console.log(event.type, a + b + c);
});
$.publish('/some/topic', 'a', 'b', 'c');
```

_______________

### 第3章 模型和数据

将模型的属性保存至命名空间中可以避免冲突。

**对象关系映射**(Object-relational mapper,简称ORM)是在除Javascript以外有编程语言中常用的一种数据结构。

> 本质上讲，ORM是一个包装了一些数据的对象层，以往ORM常用于抽象SQL数据库，但在这里ORM只是用于抽象JavaScript数据类型。这个额外的层可以通过给它添加自定义的函数数来增强基础数据的功能。比如添加数据的合法性验证，监听，数据持久化及服务器端的回调处理等，以增加代码的重用率。

GUID生成器

```javascript
Math.guid = function() {
    return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
        var r = Math.random() * 16 | 0, 
        v = c == 'x' ? r : (r & 0x3 | 0x8);
        return v.toString(16);
    }).toUpperCase();
};
```

CORS(cross-origin resource sharing)打破了同源策略的限制，赋予了前端代码访问可信的远程服务的权限。

支持CORS的浏览器：

- [x] IE >= 8（需装caveat),使用XDomainRequet函数且有限制
- [x] Firefox >= 3
- [x] Safari
- [x] Chrome
- [ ] ~~Opera不支持~~

CORS实现方式：在HTTP协议的响应头里添加几行：
```html
Access-Control-Allow-Origin: example.com
Access-Control-Request-Method: GET,POST
```
也可以使用Access-Control-Request-Headers头字段来认证自定义的请求头：
```javascript
Access-Control-Request-Headers: Authorization

//js
var req = new XMLHttpRequest();
req.open('POST', '/endpoint', true);
req.setRequestHeader('Authorization', oauth_signature);
```

```javascript
jQuery.getJSON("http://example.com/data.json?callback=?", function(result){
// 处理返回结果的相关逻辑
});
```
jQuery 将上面 URL 中最后的问号替换为一个由它创建的随机命名的临时函数。服务器
会获取这个 callback 参数,使用这个名字作为回调函数名称返回给客户端。

使用CORS/JSONP需要注意：

* 不要暴露任何敏感信息，比如电子邮件
* 禁用任何操作，如Twitter的"follow"操作
* 只使用开放认证(OAuth)身份识别验证


本地存储数据的支持情况（一般至少可以为每个域名提供5M的存储空间）:

* IE >= 8
* Firefox >= 3.5
* Safari >= 4
* Chrome >= 4
* Opera >= 10.6

WebStorage API:

* localStorage['someData'] = ...
* localStorage.
    * setItem(key, vlaue)
    * getItem(key)
    * removeItem(key)
    * clear()
    * length

____________________

### 第4章 控制器和状态

> 滑坡理论（slippery slope）：也称为滑坡谬误，是一种逻辑谬论，即不合理地使用连串的因果关系，将"可能性"转换为”必然性“，以达到某种意欲结论。

避免将状态或数据保存在DOM中，这会导致逻辑变得错综复杂且混乱不堪。

模块模式：

```javascript
(function($, exports) {
    exports.Foo = "data";
})(jQuery, window);
console.log(Foo);


var exports = this;
(function($) {
    var mod = {};

    mod.create = function(includes) {
        var result = function() {
            this.init.apply(this, arguments);
        };
        result.fn =result.prototype;
        result.fn.init = function() {};

        result.proxy = function(func) {return $.proxy(func, this);};
        result.fn.proxy = result.proxy;
        
        result.include = function(obj) {$.extend(this.fn, obj);};
        result.extend = function(obj) {$.extend(this, obj);};
        if (indcludes) result.include(includes);

        return result;
    };

    exports.Controller = mod;
})(jQuery);

```

使用状态机(FSM)可以管理很多控制器。

使用hash不会引起页面刷新，但是太过频繁设置hash会在移动终端造成页面频繁滚动。

检测hash变化
```javascript
window.addEventListener('hashchange', function() {...}, false);


$(window).bind('hashchange', function(event) {...});
```
以下浏览器支持：

* IE >= 8
* Firefox >= 3.6
* Chrome
* Safari
* Opera >= 10.6

使用ugly URL(一种Ajax抓取规则)给爬虫用。
如：
`http://some.com/#!/somepage`
会被爬虫转换为`http://some.com/?_escaped_fragment_=/somepage`,
这里的 hash 替换成了 URL 中的 _escaped_fragment_ 参数

支持HMTL5 History API的浏览器有：

- [x] Firefox >= 4.0
- [x] Safari >= 5.0
- [x] Chrome >= 7.0
- [ ] ~~IE:不支持~~
- [x] Opera >= 11.5

```javascript
var dataObject = {
    createAt: '2015-1-1',
    author: 'wen'
};

var url = '/somepage';
history.pushState(dataObject, document.title, url);

$(window).bind('popstate', function(event) {
    event = event.originalEvent;
    if (event.state) {//如果调用了pushState
        ...
    }
});
```

____________________________________

###第5章 视图和模板

`jQuery.tmpl()`

有时应该在视图内部使用`通用helper函数(generic helper function)`，比如格式化一个日期或数字。

模板存储需要考虑的内容：

* 在JavaScript中以行内形式存储
* 在自定义script标签里以行内形式存储（推荐）
* 远程加载
* 在HTML中以行内形式存储


__________________________________

###第6章 依赖管理

> 如果服务器开启Connection-alive的话，可以一定程度上减少TCP三次握手的次数

CommonJS模块
```javascript
//math.js
exports.per = function(value, total) {
    return (value / total) * 100;
};

//application.js
var Maths = require('./math');
console.log(Maths.per(50, 100));
```

浏览器上的模块
```javascript
//math.js
require.define('math', function(require, exports) {
    exports.per = function(value, total) {
        return value / total * 100;
    };
});

//application.js
require.define('application', function(require, exports) {
    var per = require('./math').per;
    console.log(per(60,100));
}, ['./math']);
```

RequireJS

```js
//html
<script data-main="lib/application" src="lib/require.js"></script>

//js define
define(['underscore','./utils'], function(_, Utils) {
    return ({
        size: 10
    });
});
//or 
define(function(require, exports) {
    var mod = require('./relative/name');
    exports.value = 'exposed';
});

//js use

require(['jquery','models/asset','models/user'], function($, Asset, User) {
    //...
});
```

LABjs是最简单的脚本加载器，主要解决了加载脚本时的性能问题，用法：
```html
<script>
$LAB
    .script('/jquery.js').wait()
    .script('/js/jquery.js')
    .script('core.js');
</script>
```

________________________________

### 第7章 使用文件

不是所有的浏览器都支持新的HTML5文件API

- [x] Firefox >= 3.6
- [x] Safari > 6.0
- [x] Chrome >= 7.0
- [ ] IE不支持
- [x] Opera >= 11.1

特性检测
```javascript
if (window.File && window.FileReader && window.FileList) {}
```

HMTL5多文件选择
```html
<input type="file" multiple>
//按住shift键多选
```

```javascript
var input = $('input:file');
input.change(function() {
    var files = this.files;
    for (var i = 0; i < files.length; i++) {
        console.log(files[i]);
    }
})
```

拖拽事件(至少7个)：dragstart,drag,dragover,dragover,dragenter,dragleave,dragend,drop。

支持情况:

- [x] Firefox >= 3.5
- [x] Safari >= 3.2
- [x] Chrome >= 7.0
- [x] IE >= 6.0
- [ ] Opera不支持

```html
<div id="dragme" draggable="true">Drag me!</div>

var element = $('#dragme');
element.bind('dragestart', function(event) {
    event = event.originalEvent;

    event.dataTransfer.effectAllowed = 'move';
    event.dataTransfer.setData('text/pain', $(this).text());
    event.dataTransfer.setData('text/html', $(this).html());
    event.dataTransfer.setData('text/uri-list', 'http://baidu.com');
    event.dataTransfer.setData('text/plain', 'http://google.com');

    event.dataTransfer.setDragImage('/images/test.png', -10, -10);//设置拖拽时跟随鼠标图片，可以不设

    event.dataTransfer.setData('DownloadURL', [
        'application/octet-stream',
        'File.exe',
        'http://test.com/file.png'
    ].join(":"));//可以使用户拖拽浏览器外的文件，只有Chrome支持
});

var dropEle = $('#drapArea');
dropEle.bind('drop', function(event) {
    event.stopPropagation();
    event.preventDefault();
    event = event.originalEvent;
    var files = event.dataTransfer.files;
    for (var  i = 0; i < files.length; i++) {
        console.log(files[i]);//也可以使用getData()
    }
});

```

复制和粘贴
支持情况:

- [x] Safari >= 6.0
- [x] Chrome(只能粘贴)
- [ ] Firefox不支持 
- [x] IE >= 5.0(API不一样)

```javascript
$('textarea').bind('copy', function(event) {
    event.stopPropagation();
    event.preventDefault();

    var cd = event.originalEvent.clipboardData;

    if (!cd) cd = window.clipboardData;//IE

    if (!cd) return;//Firefox

    cd.setData('text/plain',$(this).text());
});
$('textarea').bind('paste', function(event) {
    event.stopPropagation();
    event.preventDefault();

    event = event.originalEvent;
    var cd = event.clipboardData;

    if (!cd) cd = window.clipboardData;//IE

    if (!cd) return;//Firefox

    $('#result').text(cd.getData('text/plain'));

    var files = cd.files;//Safari中的事件支持文件的粘贴
});
```

读文件FileReader API:

* new FileReader().
    * readAsBinaryString(Blob|File)
    * readAsDataURL(Blob|File)
    * readAsText(Blob|File, encoding='UTF-8')
    * readAsArrayBuffer(Blob|File)
* onerror：出错
* inprogress：正在读取中
* onload：数据加载完成 

XMLHttpRequest Level 2（XHR2）可以实时监控文件的上传进度，支持列表如下：

- [x] Safari >= 5.0
- [x] Firefox >= 4.0
- [x] Chrome >= 7.0
- [ ] IE不支持
- [ ] Opera不支持

用法：
```javascript
var formData = new FormData($('form')[0]);
formData.append('stringKey', 'stringData');
formData.append('fileKey', file);

jQuery.ajax({
    data: formData,
    processData: false,
    url: 'http://test.com',
    type: 'post'
});

//or
var req = new XMLHttpRequest();
req.open('POST', 'http://test.com');
req.send(file);

//or jQuery

$.ajax({
    url: 'http://test.com',
    type: 'POST',
    success: function() {},
    processData: false,
    data: file
});

//or use jquery.upload.js
$.upload(url, file, {upload: {progress: onProgress}, sucess: onSucess});
```

Ajax进度条(XHR2)

```javascript
var req = new XMLHttpRequest();
req.addEventListener('progress', updateProgress, false);
req.addEventListener('load', transferComplete, false);
req.open();
function updateProgress(event) {
    var percentage = Math.round((event.position / event.total) * 100);
}
```

_______________________

###第8章 实时Web

实时数据更新方法:

* 周期轮询
* Comet,通过使用长连接实现服务器推送(server push)
* WebSocket

支持WebSocket的浏览器:

* Chrome >= 4
* Safari >= 5
* iOS >= 4.2
* Firefox >= 4*
* Opera >= 11*

```javascript
var supported = ('WebSocket' in window);
if (supported) console.log('WebSocket are supported');
var socket = new WebSocket('ws://test.com');
socket.onopen = function() {...};
socket.onmessage = function(data) {...};
socket.onclose = function() {...};
```

WebSocket使用`ws://`,`wss://`前缀,前者为80端口，后者为443端口，表示使用TLS加密

可以使用Flash实殃的Socket API和HMTL5标准实现浏览器兼容

基于Node.js实现的Socket.IO：

* Safari >= 4
* Chrome >= 5
* IE >= 6
* iOS
* Firefox >= 3
* Opera > 10.61

```javascript
var socket = new io.Socket();
socket.on('connect', function() {
    socket.send('hello');
});
socket.on('message', function(data) {
    console.log(data);
});
socket.on('disconnect', function() {});
```

______________________________

###第9章 测试和调试

jQuery支持的浏览器包括：

* Safari: 3.2,4,5...
* Chrome: 8,9,10,11...
* Internet Explorer: 6,7,8,9...
* Firefox: 2,3,4...
* Opera: 9.6,10,11... 

> JavaScript的自动化测试非常困难，且不具备可伸缩性。因为相比传统的软件开发，Web的应用需求变化更加频繁，因此测试用例需要不断变化。另外，多数JavaScript功能的模块化和API的设计并不完善，编程模式的约定也不统一，导致某个功能测试用例很难被复用。甚至测试代码会随着需求的增加呈指数级增长。

####单元测试

单元测试主要用于发现浏览器兼容性bug

断言是测试的核心，理论上来说它是测试用例的子集。

