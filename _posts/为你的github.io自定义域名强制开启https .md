关于开启https的诸多好处我就不说了。。。

最近注册了一个域名，然而通过CNAME绑定上域名后，却发现无法用https访问。如果强制访问，则会出现浏览器不信任的错误（实质上就是不支持）。

以前，我们可以通过 cloudflare 来实现，原理可以参见  

后面无意中发现github竟然 [自己支持强制 https 了](https://neue.v2ex.com/t/434553) 



![图片](https://raw.githubusercontent.com/lwdgit/blog/gh-pages/media/201804170102790.png)

如果你足够幸运，那么你将会看到如图所示的 Enforce HTTPS 选项，不幸的话，你将会看到


![图片](https://raw.githubusercontent.com/lwdgit/blog/gh-pages/media/201804170112346.png)

抱着试一试的态度，我打开了开发者工具。


![图片](https://raw.githubusercontent.com/lwdgit/blog/gh-pages/media/201804170114993.png)
![图片](https://raw.githubusercontent.com/lwdgit/blog/gh-pages/media/201804170114827.png)
此时按钮已经可以点击，然后我并不抱什么希望的点了一下，
![图片](https://raw.githubusercontent.com/lwdgit/blog/gh-pages/media/201804170114572.png)

奇迹发生了，github 接受了点击行为，并且做出了响应 **√** 。

于是果断的输入了 blog.wepwa.com，果然chrome下的https错误警告消失了。
