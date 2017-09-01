---
title: Raspberry PI入手笔记
---

## 启动前

1. 在 sd 卡上建一个 ssh 目录，开启ssh
 ![图片](https://raw.githubusercontent.com/lwdgit/blog/gh-pages/media/201709020202445.png)

2. 在 cmdline.txt 最前面加上 ip=192.168.1.100 xxxxx 其它内容（注: 100后面有空格，和其它内容分开，192.168.1.100 要求你的电脑分配到的 ip 也是 192.16.1.* 不是的话，可以按照你电脑的 ip 进行微调）,如:
 ```
ip=192.168.31.110 dwc_otg.lpm_enable=0 console=serial0,115200 console=tty1 root=PARTUUID=a1072ce2-02 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait quiet splash plymouth.ignore-serial-consoles
```

 ![图片](https://raw.githubusercontent.com/lwdgit/blog/gh-pages/media/20170902015667.png)

3. 启动

 ```
ssh pi@192.168.1.100 # 为树莓派被分配的ip，可以在路由器上看到
password: raspberry # 登录密码默认为 raspberry
passwd  # 修改密码
```

> 参考资料
* [**无显示器使用PC远程控制树莓派方案（有线&无线）**](http://qiita.com/CoffeeDog/items/d1ad4e53373935701b1a)
* [树莓派启动方式及支持的系统](http://wiki.jikexueyuan.com/project/raspberry-pi/use.html)
* [新手买到树莓派之后，如何安装、配置系统？](https://zhuanlan.zhihu.com/p/25368441)
* [ raspberry pi zero通过usb进行ssh连接](http://blog.csdn.net/talkxin/article/details/53066555)
* [树莓派3命令行配置wifi无线连接和蓝牙连接](https://www.embbnux.com/2016/04/10/raspberry_pi_3_wifi_and_bluetooth_setting_on_console/)
* [树莓派Pi3设置wifi热点](http://www.jianshu.com/p/1fca72a710d5)
* [树莓派连接WiFi（最稳定的方法)](http://www.52pi.net/archives/58)
* [Mac osx如何配置树莓派3 及 远程wifi控制树莓派](http://www.cnblogs.com/tinysun/p/5616132.html)
* [[RaspberryPi 3的安装配置]](https://robocoderhan.github.io/2016/12/13/Raspberry%20Pi%203%E7%9A%84%E5%AE%89%E8%A3%85%E8%AE%BE%E7%BD%AE/)
* [树莓派学习笔记——Wifi AP热点模式](http://www.51itong.net/wifi-ap-rt5370-19784.html)

## 环境安装

### 安装 node 

1. 通过 n 安装

 ```bash
curl -L https://git.io/n-install | bash
```

2. 通过 nvm 安装

 ```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
```
 > nvm 脚本可能路径可能会经常变更，如以上脚本失效，请访问 [nvm](https://github.com/creationix/nvm/blob/master/README.md)获取最新安装命令。

3. 原始安装

 ```bash
sudo apt-get update && sudo apt-get install npm
```
> 此方法安装的 nodejs 版本很老，不建议使用。

### 配置node

1. 权限配置
 ```
sudo chown -R $(whoami) /usr/local/lib/node_modules /usr/local/bin
```

2. 切换淘宝源
 ```
alias cnpm="npm --registry=https://registry.npm.taobao.org \
--cache=$HOME/.npm/.cache/cnpm \
--disturl=https://npm.taobao.org/dist \
--userconfig=$HOME/.cnpmrc"
```
 当然，你也可以直接使用 cnpm

 ```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
 如果你想用 yarn 

 ```
npm install -g yarn
```

3. 安装远程 pm2 & tty
 ```
git clone https://github.com/krishnasrinivas/wetty
cd wetty
npm install
npm install -g pm2
pm2 start app.js -- -p 3000
```
此时通过 192.168.1.100:3000 即可访问管理你的 raspberrry。


![图片](https://raw.githubusercontent.com/lwdgit/blog/gh-pages/media/201709020121631.png)

### 安装 python & go

```
sudo apt-get install python-software-properties
bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
source ~/.gvm/scripts/gvm
gvm install go1.8.3
gvm use go1.8.3
```
### 安装 docker

```
sudo apt-get install docker
```