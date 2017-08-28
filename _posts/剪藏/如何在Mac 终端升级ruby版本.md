如何在Mac 终端升级ruby版本

rvm是什么？为什么要安装rvm呢，因为rvm可以让你拥有多个版本的Ruby，并且可以在多个版本之间自由切换。
第一步：安装rvm

$ curl -L get.rvm.io | bash -s stable
$ source ~/.rvm/scripts/rvm
等待终端加载完毕,后输入：
rvm -v
如果能显示版本好则安装成功了。

第二步：安装ruby

列出ruby可安装的版本信息
rvm list known
安装一个ruby版本
rvm install 2.1.4
如果想设置为默认版本，可以用这条命令来完成

rvm use 2.1.4 --default
查看已安装的ruby
rvm list
卸载一个已安装ruby版本
rvm remove 2.1.4
第三步：更换源

查看已有的源
gem source
显示会如下：

CURRENT SOURCES
http://rubygems.org/
然后我们需要来修改更换源（由于国内被墙）所以要把源切换至淘宝镜像服务器 在终端执行以下命令
$ gem update --system
$ gem uninstall rubygems-update
$ gem sources -r http://rubygems.org/
$ gem sources -a http://ruby.taobao.org