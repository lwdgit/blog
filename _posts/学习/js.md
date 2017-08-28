## JavaScript的解析与执行过程

1. 全局预处理，先扫描函数，再扫描变量声明
  命名冲突时，函数具有优先权，变量会直接被忽略，而函数会被覆盖（仅预扫描时符合这个过程）
  
2. 全局执行过程
  依次覆盖
  
3. 函数预处理阶段
 
 1. 每调用一次，产生一个词法环境（或执行上下文Execution Context）
 2. 先传入函数的参数，若参数值为空，初始化undefined
 3. 然后是内部函数声明，若发生命名冲突，会覆盖
 4. 接着就是内部var变量，若发生命名冲突，会忽略

4. 函数执行
 
 1. 给预处理阶段的成员赋值
 2. 无var声明，会变成全局成员


## 标记清除与引用计数

标记清除，进入scope时，打一个标记，当离开执行环境时，清除标记
引用计数，当变量被引用时，记数加1，引用解除时，记数减1  问题：无法解决循环引用问题

## 闭包

闭包是指有权访问另一个函数里的方法和变量的函数。

一般情况下，一个函数执行完成后，所有的变量因为已经不需要了，将会被清除。闭包执行完之后，闭包内的变量依旧可以被某个特定的方法访问。

* 优点：让变量长驻内存 避免全局变量污染 私有化变量
* 缺点：闭包会携带它包含的函数作用域，因此比其他函数占用更多内存 引起内存泄漏


## 如何避免内存泄漏？

* ie下，避免循环引用
* 手工解除引用
* 注意清理定时器
* 注意防止事件重复注册



```javascript
function Class() {}
Class.extend = function extend(props) {

    var prototype = new this();
    var _super = this.prototype;

    for (var name in props) {

        if (typeof props[name] == "function" 
            && typeof _super[name] == "function") {

            prototype[name] = (function (super_fn, fn) {
                return function () {
                    var tmp = this.callSuper;

                    this.callSuper = super_fn;

                    var ret = fn.apply(this, arguments);

                    this.callSuper = tmp;

                    if (!this.callSuper) {
                        delete this.callSuper;
                    }
                    return ret;
                }
            })(_super[name], props[name])
        } else {
            prototype[name] = props[name];    
        }
    }

    function Class() {}

    Class.prototype = prototype;
    Class.prototype.constructor = Class;

    Class.extend =  extend;
    Class.create = Class.prototype.create = function () {
        var instance = new this();
        if (instance.init) {
            instance.init.apply(instance, arguments);
        }

        return instance;
    }

    return Class;
}
```


```
function objectPlus(o, stuff) {
	var F = function() {};
	F.prototype = o;
	var c = new F();
	c.uber = o;
	
	for (var i in stuff) {
		c[i] = stuff[i];
	}
	return c;
}
```


```
const composeMixins = (...mixins) => (
  instance = {},
  mix = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x)
) => mix(...mixins)(instance);

var a = (arg) => { return arg[0] + arg[1] }
var b = (c) => { return c * c }
composeMixins(a, b)([1,2])
```


```
var flow = function(funcs) {
    var length = funcs.length
    var index = length
    while (index--) {
        if (typeof funcs[index] !== 'function') {
            throw new TypeError('Expected a function');
        }
    }
    return function(...args) {
        var index = 0
        var result = length ? funcs[index].apply(this, args) : args[0]
        while (++index < length) {
            result = funcs[index].call(this, result)
        }
        return result
    }
}
var flowRight = function(...funcs) {
    return flow(funcs.reverse())
}
```


```
function testWebP(callback) {
    var webP = new Image();
    webP.src = 'data:image/webp;base64,UklGRjoAAABXRUJQVlA4IC4AAACyAgCdASoCAAIALmk0mk0iIiIiIgBoSygABc6WWgAA/veff/0PP8bA//LwYAAA';
    webP.onload = webP.onerror = function () {
        callback(webP.height === 2);
    };
};

function notify(supported) {
  console.log((supported) ? "webP supported!" : "webP not supported.");
}

testWebP(notify);​
```
```
function canUseWebP() {
    var elem = document.createElement('canvas');

    if (!!(elem.getContext && elem.getContext('2d'))) {
        // was able or not to get WebP representation
        return elem.toDataURL('image/webp').indexOf('data:image/webp') == 0;
    }
    else {
        // very old browser like IE 8, canvas not supported
        return false;
    }
}
```



redux

applyyMiddleware 的做用是增强dispatch



