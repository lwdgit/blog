关于开启https的诸多好处我就不说了。。。

最近注册了一个域名，然而通过CNAME绑定上域名后，却发现无法用https访问。如果强制访问，则会出现浏览器不信任的错误（实质上就是不支持）。

以前，我们可以通过 cloudflare 来实现，原理可以参见  

后面无意中发现github竟然 [自己支持强制 https 了](https://neue.v2ex.com/t/434553) 



![图片](https://raw.githubusercontent.com/lwdgit/blog/gh-pages/media/201804170102790.png)

如果你足够幸运，那么你将会看到如图所示的 Enforce HTTPS 选项，不幸的话，你将会看到

![图片](./2)