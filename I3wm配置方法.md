## I3wm配置方法

### 更新manjaro

百度搜索“manjaro安装后”

> https://www.jianshu.com/p/447003e8e482

按照步骤， 设置镜像源，更新manjaro系统，安装chrome, 搜狗拼音。

注意不要下载shadowsocks-qt5

### 设置搜狗拼音

按照以往的方式安装好搜狗拼音后， 在i3wm上并不能立刻生效，此时还要启动fcixt

打开终端，输入fcitx并运行

 * #### 设置开机自动运行fcitx

   1. 在终端输入

   ~~~ bash
   vim ~/.i3/config
   ~~~

   > 如果该目录下没有config，就从/etc/i3/config复制过去
   >
   > ~~~ bash
   > cp /etc/i3/config ~/.i3/config
   > ~~~

   2. 找到Autostart, 并在该分支下添加一句

   ~~~ bash
   exec_always --no-startup-id fcitx
   ~~~


### 修改Home下的目录为英文

修改目录映射文件名

~~~ bash
vim .config/user~dirs.dirs
#修改为一下内容
XDG_DESKTOP_DIR="$HOME/Desktop"
XDG_DOWNLOAD_DIR="$HOME/Download"
XDG_TEMPLATES_DIR="$HOME/Templates"
XDG_PUBLICSHARE_DIR="$HOME/Public"
XDG_DOCUMENTS_DIR="$HOME/Documents"
XDG_MUSIC_DIR="$HOME/Music"
XDG_PICTURES_DIR="$HOME/Pictures"
XDG_VIDEOS_DIR="$HOME/Videos"
~~~

将Home目录下的中文目录改为对应的英文名

重启系统

### 将chrome设置为默认mod+F2启动浏览器

* 修改浏览器入口, 将export BROWSER=/usr/bin/palemoon改为export BROWSER=/usr/bin/google-chrome-stable

~~~ bash
vim /home/$USER/.profile
#修改变量
~~~

* 将/home/$USER/.config/mimeapps.list中的Pale Moon全部替换为google-chrome-stable

~~~ bash
vim /home/$USER/.config/mimeapps.list
#然后在一般模式下，输入
%s/Pale Moon/google-chrome-stable/gc
~~~

* 将 ~/.i3/config下的palemoon替换为google-chrome-stable. 

~~~ bash
vim ~/.i3/config 
~~~

* 打开文件管理， 在左侧选择栏中，进入“应用程序”，“首选项”，找到“首选应用程序”，设置chrome为默认浏览器

### 使用Privoxy+Shadowsocks代理上网

* 在包管理中心下载shadosocks, 安装完成后编辑配置文件，填写对应服务器地址

~~~ bash
vim /etc/shadowsocks/config.json
#这里将配置文件放在shadowsocks的config里面，也可以放在其他地方
~~~

​	配置文件的样例如下

~~~ javascript
{
    "server":"1.1.1.1",
    "server_port":8383,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"passwd",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": true,
    "workers": 1
}
~~~

* 启用代理

~~~ bash
sudo sslocal -c /etc/shadowsocks/config.json -d start
#关闭代理时,则使用sudo sslocal -c /etc/shadowsocks/config.json -d stop
~~~

* 在包管理中心安装privoxy, 安装好后修改privoxy配置

~~~ bash
sudo vim /etc/privoxy/config
~~~

​	privoxy配置如下, 注意转发地址后面的小句号

~~~ javascript
# 转发地址
forward-socks5   /               127.0.0.1:1080 .
# 监听地址
listen-address  localhost:8118
# local network do not use proxy
forward         192.168.*.*/     .
forward            10.*.*.*/     .
forward           127.*.*.*/     .
~~~

* 设置环境, 并且启动privoxy

~~~ bash
export https_proxy=http://127.0.0.1:8118
export http_proxy=http://127.0.0.1:8118
sudo systemctl restart privoxy
~~~

* 运行需要代理的软件, 例如chrome

~~~ bash
#运行chrome
proxy google-chrome-stable
#安装软件等
proxy yay install something
~~~

* 使用代理小结

~~~ bash
#每次需要使用代理时, 需要以此输入以下命令
sudo sslocal -c /etc/shadowsocks/config.json -d start
export https_proxy=http://127.0.0.1:8118
export http_proxy=http://127.0.0.1:8118
sudo systemctl restart privoxy
#需要关闭代理时, 输入以下命令且关闭该终端即可.
sudo sslocal -c /etc/shadowsocks/config.json -d stop
#失灵时,重新打开再次键入即可

~~~

​	**参考文章**

> https://blog.csdn.net/ypbsyy/article/details/81146866
>
> https://www.jianshu.com/p/41378f4e14bc
>
> https://www.privoxy.org/user-manual/startup.html
>
> http://blog.sina.com.cn/s/blog_acc347e50102w2tn.html
>
> https://www.jianshu.com/p/bf17ccc4c817
>
> https://www.jianshu.com/p/f8d74a5f6791
>
> https://mritd.me/2016/07/22/Linux-%E5%91%BD%E4%BB%A4%E8%A1%8C%E4%B8%8B%E4%BD%BF%E7%94%A8-Shadowsocks-%E4%BB%A3%E7%90%86/
>
> https://blog.csdn.net/luluny/article/details/75213051
>

