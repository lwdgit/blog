---
title: chrome-automator，一个和 NightmareJS 一样简单的爬虫
---

刚一开始使用接触到Nightmare的时候着实为它着迷：终于找到一个好用的自动化工具，API简单够用，支持自定义拓展，还支持录制，当然，稳定可靠是我选择它的最主要原因（PS:曾经有一款和它写法差不多简单的框架后面没有人维护了，T T）。

我开始把玩这个玩具，研究其源码。当我知道 electron 支持 集成 nodejs 时，于是我便鸡冻的基于 nightmare 做了一个 node-nightmare。是的，它可以做到在 webview 里面调用 nodejs 方法，也就是可以同时支持 window 和 global 对象。换句话说就是可以做到让网页具有读写本地数据。当然，这个是所有前端开发者的梦想，但却是浏览器开发商头疼的。一直以来，为了浏览器的安全可靠，为了让网站访问者不会无端受到 js 代码的攻击，浏览器厂商不得不把 js 限制在一个安全的沙箱之内。而Electron却不用过于关注这个问题，于是这个能力便被开放了出来。

后面碰到一个线上主动错误上报的需求，于是我自然而然地想到了 Nightmare 。因为其api简洁易用，很快我便完成了自己的开发工作，但是在实际应用中，问题便一点点暴露了出来。

严重的问题主要有：

1. Nightmare 所驱动的Electorn 并不是 chrome 的blink内核，而是 safari 的 webkit 内核。所以有时只能抓取到一些 safari 下的错误，而 safari 并不是一个主流浏览器。
2. electron 不支持 app 自定义的 protocol ，这会在爬虫爬到 app download 或 app 唤起页面时让页面的js出现异常。
3. electron 对内核进行一些定制修改，在页面 unload 时可能会导致 js 报错（一般错误信息为: n is not a constructor）。

因为上面的种种原因，监控体系上线以来，一直会有一些小的误报。后面增加了自动重试来降低误报率，但这也同时漏掉了一些偶发性的错误。这个倒还可以忍，主要还是第2点。

下决心换chrome来做爬虫是在 chrome 正式宣称 支持 headless 模式不久之后，此时[chrome-remote-interface](https://github.com/cyrus-and/chrome-remote-interface) 刚好也同步推出。

本着对 Nightmare 的喜爱，便模仿它开发了这样一个工具 -- [chrome-automation](https://github.com/lwdgit/chrome-automator) 。

API兼容上本着最大兼容的原则。目前除 header 及 on 之外，其他均 100% 兼容。部分实现直接照搬 Nightmare 。

目前支持的API列表如下：

 - [x] constructor 支持如下参数

    - [x] port
    - [x] show
    - [x] chromePath
    - [x] waitTimeout
    - [x] executionTimeout
    - [x] loadTimeout

 - [x] goto
 - [x] url
 - [x] title
 - [x] action 只支持 Promise 和 async 方式的异步写法，不支持 callback 方式
 - [x] evaluate
 - [x] click
 - [x] type
 - [x] insert
 - [x] wait
 - [x] mouseup
 - [x] mouseover
 - [x] mousedown
 - [x] check
 - [x] uncheck
 - [x] select
 - [x] scrollTo
 - [x] visible
 - [x] exists
 - [x] path
 - [x] back
 - [x] forward
 - [x] refresh
 - [x] end
 - [x] focusSelector
 - [x] blurSelector
 - [x] pdf 仅支持headless模式，设置 show: false 开启
 - [x] screenshot
 - [x] viewport
 - [x] useragent
 - [x] html
 - [x] authentication
 - [x] cookies

    - [x] set
    - [x] get
    - [x] clear
 
 - [x] inject
 - [x] on 暂时支持的事件与Nightmare不同

 <details>
 <summary>列表如下: 详细说明见 https://chromedevtools.github.io/devtools-protocol/tot/Network/#event-loadingFailed </summary>
 
  - [x] Page.javascriptDialogOpening  弹窗事件
  - [x] Console.messageAdded  旧console事件，不建议使用
  - [x] Runtime.consoleAPICalled  console事件
  
  - [x] Network.resourceChangedPriority
  - [x] Network.requestWillBeSent
  - [x] Network.requestServedFromCache
  - [x] Network.responseReceived
  - [x] Network.dataReceived
  - [x] Network.loadingFinished
  - [x] Network.loadingFailed
  - [x] Network.webSocketWillSendHandshakeRequest
  - [x] Network.webSocketHandshakeResponseReceived
  - [x] Network.webSocketCreated
  - [x] Network.webSocketClosed
  - [x] Network.webSocketFrameReceived
  - [x] Network.webSocketFrameError
  - [x] Network.webSocketFrameSent
  - [x] Network.eventSourceMessageReceived
  - [x] Network.requestIntercepted

  - [x] Page.domContentEventFired
  - [x] Page.loadEventFired
  - [x] Page.frameAttached
  - [x] Page.frameNavigated
  - [x] Page.frameDetached
  - [x] Page.frameStartedLoading
  - [x] Page.frameStoppedLoading
  - [x] Page.frameScheduledNavigation
  - [x] Page.frameClearedScheduledNavigation
  - [x] Page.frameResized
  - [x] Page.javascriptDialogClosed
  - [x] Page.screencastFrame
  - [x] Page.screencastVisibilityChanged
  - [x] Page.interstitialShown
  - [x] Page.interstitialHidden

 </details>

 > 两种模式: 
  * 非阻塞式 默认方式，建议在 goto 之前使用
  * 阻塞式，使用方式 `on(eventName, fn, { detach: false })`。 如想取消监听，可以 `return { cancled: true }` 继续接下来的流程。
  例子见 [test4](./tests/test4.js)，[test5](./tests/test4.js)

 
 - [x] once 只监听事件一次，监听完成后可以继续后续的动作

 - [ ] halt 暂时不支持，chrome在此场景不太适用 https://github.com/segmentio/nightmare/issues/835
 - [ ] header 目前可以使用setExtraHTTPHeaders代替部分功能
 

### 拓展功能及API:

 - [x] iframe 进入iframe，方便iframe里面的操作
 - [x] pipe 支持流程衔接，如登录流程，和 then 一样，pipe 也可以接收上个流程的返回值，建议在中间流程使用 pipe 替代 then
 - [x] 支持新窗口打开时自动跟踪，防控制跳失 注: headless模式下存在bug
 - [x] setExtraHTTPHeaders
 - [x] ignoreSWCache 忽略service worker缓存

> PS：目前原有框架(Nightmare)回调写法全部去除，仅保留 Promise 写法。

> Tips: 执行过程中手动进行某些操作（如打开开发者工具）可能会使用动作失效。
> 因为 Promise 无法取消的原因，所以在流程执行完 end 操作后node可能并不会立即退出，一般会在 30s 左右自动退出，可以缩短 loadTimeout 和 executionTimeout 解决
> Promise 异步流程目前在node下还无法显示完整的错误堆栈信息，可以考虑使用 `node --trace-warnings` 查看，也可以使用 `global.Promise = require('bluebird')`解决，使用过程中记得使用 try catch 包裹执行段

## Examples:

```javascript
const chrome = require('chrome-automator')

chrome({ show: false })
.goto('https://www.baidu.com/')
.wait('body')
.insert('input#kw', 'hello world\r')
.wait('.c-container a')
.evaluate(() => document.querySelector('.c-container a').href)
.pipe(function (url) {
  console.log(url)
  return chrome().goto(url)
})
.wait('[id^="highlighter_"]')
.evaluate(() => document.querySelectorAll('.para-title.level-3')[9].nextElementSibling.querySelector('.code').textContent)
.then((code) => console.log(code))
```

```javascript
var Nightmare = require('chrome-automator')
Nightmare.action('hello', function () {
  console.log('Get url')
  return this.evaluate_now(function () {
    return document.querySelector('#links_wrapper a.result__a').href
  })
})

var nightmare = Nightmare({ show: true })
try {
  nightmare
  .goto('https://duckduckgo.com')
  .type('#search_form_input_homepage', 'github nightmare')
  .click('#search_button_homepage')
  .wait('#zero_click_wrapper .c-info__title a')
  .hello()
  .end()
  .then(function (result) {
    console.log(result)
  })
} catch (e) {
  console.log(e)
}

```

## LICENSE

MIT

## 感谢
 
 * [nightmare](https://github.com/segmentio/nightmare)
 * [chrome-remote-interface](https://github.com/cyrus-and/chrome-remote-interface)
 * [lighthouse](https://github.com/GoogleChrome/lighthouse) 使用了 lighthosue 的浏览器主动唤起功能