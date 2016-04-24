# 基于shadowscoks的科学上网配置

# 1. 前言
随着技术中心的发展，用到越来越多新型的技术，但是由于中国大陆的网络环境，一些优质的开发资源站点因为被墙而无法访问，为了让大家能够方便的访问这些资源，这里整理了基于shadowsocks的科学上网方式，并提供了稳定可靠的后端socks5节点列表供大家使用。

## 1.1 SOCKS5代理
SOCKS是一种网络传输协议，主要用于客户端与外网服务器之间通讯的中间传递。SOCKS是"SOCKetS"的缩写。

这个协议最初由David Koblas开发，而后由NEC的Ying-Da Lee将其扩展到版本4。最新协议是版本5，与前一版本相比，增加支持UDP、验证，以及IPv6。

根据OSI模型，SOCKS是会话层的协议，位于表示层与传输层之间。
## 1.2 什么是shadowsosks
Shadowsocks（中文名称：影梭）是使用Python等语言开发的、基于Apache许可证开源的代理软件。Shadowsocks使用socks5代理，用于保护网络流量。在中国大陆被广泛用于突破防火长城（GFW），以浏览被封锁的内容。

Shadowsocks分为服务器端和客户端。在使用之前，需要先将服务器端部署在支持Python的服务器上面，然后通过客户端连接并创建本地代理。此外用户也可以选择购买基于Shadowsocks的商业服务，以获得更加稳定可靠的服务，或者免去自行部署的麻烦。

## 1.3 PAC文件
PAC， 代理自动配置(Proxy auto-config，简称PAC）是一种网页浏览器技术，用于定义浏览器该如何自动选择适当的代理服务器来访问一个网址。

一个PAC文件包含一个JavaScript形式的函数“FindProxyForURL(url, host)”。这个函数返回一个包含一个或多个访问规则的字符串。用户代理根据这些规则适用一个特定的代理器或者直接访问。当一个代理服务器无法响应的时候，多个访问规则提供了其他的后备访问方法。浏览器在访问其他页面以前，首先访问这个PAC文件。PAC文件中的URL可能是手工配置的，也可能是是通过网页的网络代理自发现协议（WPAD）自动配置的

## 1.4 shaodwsocks与VPN方式上网比较
很多同学之前都有使用VPN来翻墙上网的经验，但是相比于shadowsocks，VPN只能是全局翻墙，意思就是连接上VPN后，不管是访问国内未被墙的网站还是国外被强的网站，都必须通过VPN代理服务器。这在一定程度上拖慢了访问国内网站的速度。而shadowsocks可以通过配置PAC文件，有选择的确认哪些网站需要经过代理服务器，这样子就不会影响到访问那些未被强的网站速度。

# 2. 工具下载地址
各平台shadowsocks客户端下载地址

[下载地址](https://portal.shadowsocks.com/downloads.php?action=displaycat&catid=1)

# 3. 配置流程
这里以MAC版本客户端为例，windows下配置流程类似。

# 3.1 shadowsocks客户端配置
打开shadowsocks客户端，进入服务器配置选项

![](https://www.evernote.com/l/ATdqFiLDjeFAJKcfkggbr56iSXiYnfzEuFUB/image.png)

在服务器配置中新增一个socks5服务器

![](https://www.evernote.com/l/ATeDaQYL9ZhFYrt7h9aysaMfWb6NrEE799oB/image.png)

输入后端服务提供的代理地址、端口、加密方式、密码信息。填写完毕后一个shasowsocks服务器就配置好了。

选择我们刚刚设置的服务器，并将shadowsocks的模式选择为自动代理模式。(shadowsocks内部已经内置了一份PAC文件)

![](https://www.evernote.com/l/ATc4_paA-6ZB2qGXv03fEa5vHZd-u5_8AosB/image.png)

上述全部完成后，我们就可以通过shadowsocks开始科学上网，可以看下是都能够打开google页面进行验证。

# 3.2 chrome浏览器switchyomgega配置
switchyomgea是一款chrome浏览器下的自动切换代理插件，虽然大部分shadowsocks的客户端都内置了一份PAC文件，但是难免存在某些被墙的站点不在PAC列表中或者并没有提供一份默认的PAC文件，通过使用switchyomega我们可以更方便的确认哪些站点需要经过sock5代理。

switchyomega可以通过chrome网上商店进行下载。

打开switchyomega，先进行本地代理地址的设置。shadowsocks运行的时候会在本地打开1080端口，通过1080端口访问的站点都经过socks5代理服务。

![](https://www.evernote.com/l/ATexsM2gkMlETbq8zU13sJ1U72Fup1lrkL0B/image.png)

本地代理设置完毕后，选择auto switch情景模式，设置代理切换规则。在规则列表格式中选择:AutoProxy,规则列表网址中输入:https://autoproxy-gfwlist.googlecode.com/svn/trunk/gfwlist.txt 点击立即更新情景模式。

![](https://www.evernote.com/l/ATdw4ik-PMVLq6HN7QmFGULelHkIUgxWDToB/image.png)

点击应用选项，在chrome中选当前使用的情景模式为auto switch，后续对站点的访问就会自动规则列表判断是否要经过proxy代理

![](https://www.evernote.com/l/ATed5kvxtB1DuJCTzNEonNGHBzCFPB97HDcB/image.png)

后续如果碰到不在规则列表而无法自动切换到代理模式的网站，switchyomega会自动进行提示，这个时候我们可以手工的添加到规则列表中。

![](https://www.evernote.com/l/ATfnof0O4PBFTZ4FfMrFDyyYzFmTOgF6BucB/image.png)

点击*资源未加载未加载项，进行手工设置

![](https://www.evernote.com/l/ATdrXmuSCJRFbaN0yOLkFSiiTLFjGC9YPOcB/image.png)

# 4. shadowsocks账号信息
端口：53389	密码：yR9serfQLY4P	加密方式：aes-256-cfb
	
* 日本VIP线路1    	vip-jp01-5.ssv7.net
* 日本VIP线路3    	vip-jp03-5.ssv7.net
* 日本VIP线路4    	vip-jp04-5.ssv7.net
* 日本VIP线路5    	vip-jp05-5.ssv7.net
* 日本VIP线路6    	vip-jp06-5.ssv7.net
* 美国VIP线路1    	vip-us01-5.ssv7.net
* 美国VIP线路2    	vip-us02-5.ssv7.net
* 美国VIP线路3    	vip-us03-5.ssv7.net
* 新加坡VIP线路2  		vip-sg02-5.ssv7.net
* 新加坡VIP线路3	 	vip-sg03-5.ssv7.net
* 新加坡VIP线路4	 	vip-sg04-5.ssv7.net
* 新加坡VIP线路5	 	vip-sg05-5.ssv7.net
* 香港VIP线路2	 		vip-hk02-5.ssv7.net
* 香港VIP线路3	 		vip-hk02-5.ssv7.net
* 台湾VIP线路1	 		vip-tw01-5.ssv7.net
* 韩国VIP线路1	 		vip-kr01-5.ssv7.net




