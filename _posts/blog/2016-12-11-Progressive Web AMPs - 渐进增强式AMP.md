---
title: Progressive Web AMPs - 渐进增强式AMP
---

如果你最近一直在关注web开发社区新动态，你可能会读到不少关于[PWA(progressive web apps)](https://www.smashingmagazine.com/2016/08/a-beginners-guide-to-progressive-web-apps/)的文章。
> 注: PWA是一种通过最大化利用浏览器自身能力，通过渐进增强的方式，来达到类app体验的技术体系，它可以实现: 离线访问，添加到桌面，消息推送, 全屏展示等。

尽管[Service worker API](https://developers.google.com/web/fundamentals/primers/service-worker/) 允许网站缓存所以想缓存的资源，但是首次加载依旧是个问题(因为这个时候service worker还刚刚跑起来)。[DobuleClick的研究](https://www.doubleclickbygoogle.com/articles/mobile-speed-matters/)表明，首屏超过3s，53%的用户将会放弃。

实现3s内打开页面，是一个相当难以实现的目标。在移动网络上，因为带宽不够或者是信号弱的原因，大多数请求会有300ms左右的延迟。除去其他因素，真正在开发过程中，我们就得让加载时间小于1s。

![](media/14813820370516.png)￼
<sub>这张图片展示了用户访问网站延时产生的几个方面</sub>

当然，我们有很多方法去解决首次加载缓慢的问题,如使用服务器端渲染，代码懒加载等。但是你想把这些方法实施的足够好，你需要专业的性能优化人员。

所以，要想让首屏足够快，快到像app一样，我们应该怎么做呢？

## AMP, For Accelerated Mobile Pages（AMP， 为高速移动网页而生）

一个网站存在的最大意义是它有天然的优势 -- 众多的流量来源，不需要安装。用户只需要轻轻一点即可马上呈现。

为了保持这些天然的优势，并且在短暂的浏览中抓住足够多的用户，你得让你的网站加载速度足够快。

一个适当的解决方案是：不要使用过大的图片，不要加载阻止首屏渲染的广告，不要写超过100000行的代码。

[AMP](https://www.ampproject.org/),全称为 `Accelerated Mobile Pages`，就是为[解决加载速度慢的问题而生](https://www.ampproject.org/docs/get_started/technical_overview.html)。实际上，它的存在，看起来更像是交警的角色（可以让车辆在拥挤的道路上开得更快）。为了让你的页面加载更快，你需要遵循一系列严格的规范。在这个定制化的环境下，你可以通过其他平台（如google搜索)[预载首屏](https://www.ampproject.org/docs/get_started/technical_overview.html#load-pages-in-an-instant)来达到”即时呈现“的效果。


![](media/14813835276401.png)￼
<sub>图片中上面的部分会当用户还未访问此页面前加载好(如：google会在搜索结果页将amp网站的资源加载好)，所以用户看起来就像是即时看到了这个页面一样，也就是秒开</sub>

## AMP Or PWA？ 

为了达到这个可靠的秒开体验，在制作AMP页面时你需要理解一些概念。AMP并不是一个可高度拓展地页面，它既不支持离线推送，也不能用于网页支付，包括其他需要自定义JS来支持的页面。此外，一个AMP页面通常是被AMP服务器缓存（google会把AMP页面分片缓存在它的服务器上），所以在用户首次点击页面（也就是访问第二个页面）时不能享受PWA的离线缓存，因为你自己的service worker此时还没有运行。也就是说,在用户首次点击（也就是访问第二个页面）时,PWA页面不可能比AMP页面快。


![](media/14813843883695.png)￼
<sub>当用户点击内链离开AMP页面后，你就可以通过安装service worker实现网站离线访问</sub>

> 注：上面说到了AMP与PWA不同时共存的情况: AMP可以利用google的cdn预缓存来达到秒开的目标。但是局限性在于页面失去了动态性，因为不能写js。而PWA因为首次加载service worker还没有起做用，所以首次加载依旧很慢。


## 一个完美的用户体验

一个理想的用户体验应该是这样的：

1. 用户发现一个链接，点击进入到你的网站
2. 网站秒开(AMP版本)
3. 用户被提示自动升级到高级版本(PWA版本)，这个版本支持类app的导航，消息推送，和离线浏览的能力
4. 当用户还在好奇为什么要这样做时，他们已经被自动重定向到PWA版本，并且他们可以把这个网站添加到首屏。


第1步应该是秒开（利用AMP就可以做到），接下来的浏览过程中用户体验将会逐渐增强。

是不是听起来很棒？现在让我们开始尝试整合这两种技术（PWA&AMP) -- 尽管直观的感觉告诉我们，它俩根本不是一回事。


## PWAMP Combination Patterns（PWAMP合并大法）
为了实现秒开，并且自动升级体验。你需要做的仅仅是将AMP和PWA结合。方式有以下几种：

* AMP as PWA, 保持AMP的能力的同时，利用PWA来增强它
* AMP to PWA, 让AMP平滑切换到PWA
* AMP in PWA, 在PWA页面里面重用AMP

接下来让我们详细说来。


### AMP AS PWA（让AMP页面和PWA页面一样）

许多网站并不需要AMP全部的特性。在AMP的官方例子里，一个AMP网页同时也是一个PWA网站。

 * 它有service worker，支持离线访问等。
 * 它有manifest，支持弹出”添加到桌面“的对话框
 
 
当一个用户通过google搜索访问到AMP的页面时，他们先会被带到google的AMP缓存页面，这个页面使用AMP的开发库开发，并且使用主站域名（通过iframe方式实现同源）。因为同源，所以支持service worker，也支持添加到桌面。

你可以使用这些技术来实现离线访问你的AMP站点的能力。因为同源，所以你可以使用service worker去修改页面呈现。如：

```js
function createCompleteResponse (header, body) {

  return Promise.all([
    header.text(),
    getTemplate(RANDOM STUFF AMP DOESN’T LIKE),
    body.text()
  ]).then(html => {
    return new Response(html[0] + html[1] + html[2], {
      headers: {
        'Content-Type': 'text/html'
      }
    });
  });

}
```

这项技术允许你在随后的点击行为中插入自定义script以及更高级的功能。

### AMP TO PWA（把AMP页面切换到PWA页面）

前面提到的这些还不够，你还可能通过一些细小的改变来达到PWA的体验。

* 翻页效果，一种近似app秒开的效果。
* AMPs使用特殊的标签<amp-install-serviceworker>来缓存PWA页面骨架，这些行为在用户浏览你的页面内容时悄悄完成。
* 当用户点击其他链接时，service worker将会干预请求，接管页面同时加载PWA页面骨架。

如果你熟悉service worker，你可以很容易地做到上面3步(如果你不熟悉，建议你看看这篇文章[Jake’s Udacity course](https://www.udacity.com/course/offline-web-applications--ud899)). 首先，在你的AMP页面安装service worker:

```html
<amp-install-serviceworker
      src="https://www.your-domain.com/serviceworker.js"
      layout="nodisplay">
</amp-install-serviceworker>
```

第二步，在service worker安装的同时，缓存所以PWA需要的资源。

```js
var CACHE_NAME = 'my-site-cache-v1';
var urlsToCache = [
  '/',
  '/styles/main.css',
  '/script/main.js'
];

self.addEventListener('install', function(event) {
  // Perform install steps
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(function(cache) {
        console.log('Opened cache');
        return cache.addAll(urlsToCache);
      })
  );
});
```
最后，使用service worker来干预请求，将AMP请求替换为PWA的页面。（代码如下，一个非常简单的demo,更高级的demo在文章末)

```js
self.addEventListener('fetch', event => {
    if (event.request.mode === 'navigate') {
      event.respondWith(fetch('/pwa'));

      // Immediately start downloading the actual resource.
      fetch(event.request.url);
    }

});
```

现在无论用户点击什么AMP缓存的页面，都会被service worker接管，然后返回一个完整的PWA缓存页面。

![](media/14813763357466.png)￼
如果安装了service worker，你可以得到增强的体验。而对于不支持service worker的页面，仍然使用AMP页面来展示。


在上面我们通过使用渐进增强的方式让AMP转换到了PWA。然而对且那些不支持service worker的浏览器，仍然的AMP页面之前跳转，永远无法浏览到真正的PWA页面。

AMP通过一个叫”框架URL重定向“(shell URL rewriting)的方式解决了这个问题。方法是通过在`<amp-install-serviceworker>标签上添加一个*兜底URL匹配*(fallback URL pattern)属性，当检测到不支持service worker时，你的AMP页面上的链接将会替换为原本的URL(即PWA的URL)。

```html
<amp-install-serviceworker
      src="https://www.your-domain.com/serviceworker.js"
      layout="nodisplay"
      data-no-service-worker-fallback-url-match=".*"
      data-no-service-worker-fallback-shell-url="https://www.your-domain.com/pwa">
</amp-install-serviceworker>
```
这些属性可以让随后的点击从AMP跳转到PWA。


### AMP IN PWA（把AMP内置到PWA中去）

当用户在PWA页面访问时，你就可以通过AJAX驱动页面跳转，通过JSON来呈现内容。为了达到这个目标，你现在需要提供两个版本的页面: 一个是AMP页面(有利于google爬取，并加速首屏展示时间)，一个是基于JSON的PWA页面(需要做到前后端分离)。

现在想想AMP真实面貌是什么：它其实算不是一个完整的页面，它仅仅只是用于快速展示一个轻量便携的页面。**一个AMP页面可以被安全的嵌套在其他网站里面**。如果我们想让后端变得简单，我们就应该重新设计一个JSON API给PWA使用，而不是复用AMP页面来提供。
![](media/14813777547418.png)￼
<sub>AMP页面可以安全地被嵌套在任何其他网站内（因为AMP所能使用的JS都是官方提供的并且预编译好的，而PWA的资源只会加载一次)</sub>

当然，另一个超级简单的加载AMP页面的方式是通过iframe(google搜索结果就使用该方法实现).但是iframe太慢了，所以你需要一次次重新编译并初始化AMP的JS库。现在，页面切片技术提供了一个更好的方法：shadow DOM.

这个浏览过程如下：

1. 全局接管任何的跳转点击事件
2. 然后，XMLHttpRequest发一个请求去加载AMP页面
3. 把返回的内容放到一个新的shadow dom root中去。
4. 通知AMP代码库：”我有一个新的文档要给你，接着“(运行时调用`attachShadowDoc`方法）。

使用这个方法，AMP的代码库只会编译加载一次，当每次PWA文档内容变更时，你就把这段shadow dom重新附加上去。因为你此时是用XMLHttpRequest来加载页面的，所以你可以在把AMP资源插入到shadow document中时动态修改它。比如：

 * 去掉不必要的内容，如header和footer
 * 添加额外的内容，如广告和喜欢的提示框。
 * 替换特定内容为动态内容。
 
现在，你已经让你的PWA页面稍微简单了一些，同时也减轻了后端重构的负担。


## 一个Demo

为了说明shadow DOM的用法(如：PWA内的AMP)，AMP小组设计一个基于React的demo [React-based demo called The Scenic](https://choumx.github.io/amp-pwa/)，一个山寨的旅行杂志:
![](media/14813791330546.png)￼

[整个demo](https://github.com/ampproject/amp-publisher-sample/blob/master/amp-pwa)已经放在github上，最关键核心的代码在`amp-document.js`里面 [React component](https://github.com/ampproject/amp-publisher-sample/blob/master/amp-pwa/src/components/amp-document/amp-document.js#L92)


## 一点干货

想看真实的例子，请访问 [(Mic’s new PWA) (in beta)](https://beta.mic.com/)。如果你shift-reload(Shift+Reload，可以临时让service worker失效)一篇文章，你会发现它其实是一个AMP页面。现在尝试点击 三明治Menu: 它会重新加载当前页面，当`<amp-install-serviceworker>` 安装好PWA骨架时，页面刷新几乎是即时呈现的，而且Menu在刷新依旧为打开状态，这一切看起来就和没有刷新一样。此时你已经在浏览PWA页面(内嵌AMP页面)。


### 最后的思考

说实话，能够将这两个技术结合起来使用，我内心是相当的激动。这是一次让两者同时达到最好的尝试。


最后总结一下PWAMP的特点:

 * 一直都很快(无论是首次加载还是后续的加载)
 * 超赞的分布式编译(通过AMP平台)
 * 渐进式增强
 * 一套后端代码解决所有问题
 * 更小的代码复杂度
 * 更小的投入，更大的产出

 
在移动Web体验优化上，我们已经开始发现各种各样的新花样，甚至是全新的方法。2016年我们已经做到了最佳的web体验，在不久的将来，web将会迎来新篇章。


原文: https://www.smashingmagazine.com/2016/12/progressive-web-amps/

