#nginx 手册 

## 1.nginx的编译安装
nginx的安装方式有很多种，可以从各大linux发行版的包管理工具直接进行安装，例如CentOS下:
	
	$sudo yum install nginx
也可以从nginx的[官网](http://nginx.org/)下载源代码，从源代码进行编译安装。
因为从源代码编译安装，对nginx的控制和个性化定制更高，这里主要介绍从源代码编译安装方式，这种方式也将作为后续整个团队中nginx部署的主要方式。

### 1.1 准备工作
#### 1.1.1 nginx版本
本文中的所有内容基于nginx的1.8.1稳定版本。
#### 1.1.2 PCRE库
PCRE(Perl Compatible Regular Expressions中文含义：perl语言兼容正则表达式)是一个用C语言编写的正则表达式函数库.该库被nginx的location指令以及ngx\_http\_rewrite_module模块使用。
#### 1.1.3 OpenSSL库
OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及SSL协议，并提供丰富的应用程序供测试或其它目的使用。该库被Nginx用于对HTTPS协议处理。
#### 1.1.4 ZLIB库
zlib是提供数据压缩用的函式库，由Jean-loup Gailly与Mark Adler所开发.该库被nginx的ngx\_http\_gzip_module模块使用。
### 1.2 编译配置选项

#### 1.2.1  --prefix=path
指定nginx安装目录路径。该路径同时也做为configure脚本其它配置选项的相对根目录(除了依赖库源代码路径)和nginx.conf配置文件使用的根目录。默认为/usr/local/nginx。
#### 1.2.2  --sbin-prefix=path
指定nginx可执行文件的安装路径。默认为prefix/sbin/nginx。
#### 1.2.3  --conf-path=path
指定nginx配置文件的安装路径。默认为prefix/conf/nginx.conf。nginx可以在命令行中通过-c选项指定配置文件的具体路径。
#### 1.2.4  --pid-path=path
指定nginx pid文件的安装路劲。默认为prefix/logs/nginx.pid。该路径可以后续在nginx.conf中通过pid指令被改变。
#### 1.2.5  --error-log-path=path
指定nginx的主日志文件路径。默认为prefix/logs/error.log。该路径可以在后续的nginx.conf中通过error_log指令改变。
#### 1.2.6  --http-log-path=path
指定nginx的主请求日志问津路径。默认为prefix/logs/access.log。该路径可以在后续的nginx.conf中通过access_log指令改变。
#### 1.2.7  --user=name
指定nginx工作进程的运行用户。默认为nobody。该选项可以在后续的nginx.conf中的user指令改变。
#### 1.2.8  --group=name
指定nginx工作进程的运行组。默认为nobody。该选项可以在后续的nginx.conf中的group指定改变。
#### 1.2.9  --with-select\_module --without-select_module
启用/禁止构建一个模块来使nginx服务支持select模式。
#### 1.2.10 --with-poll\_module --without-poll_module
启用/禁止构建一个模块来使nginx服务支持poll模式。
#### 1.2.11 --without-http\_gzip\_module
禁止构建http响应压缩模块。该模块需要zlib库支持。
#### 1.2.12 --without-http_rewrite\_module
禁止构建http重写模块。该模块需要pcre库支持
#### 1.2.13 --without-http_proxy\_module
禁止构建http代理模块
#### 1.2.14 --with-http\_ssl\_module
启动构建https请求处理模块
#### 1.2.15 --with-pcre=path
指定pcre库源代码路径
#### 1.2.16 --with-pcre-jit
指定使用just-in-time模式编译pcre库
#### 1.2.17 --with-zlib=path
指定zlib库源代码路径
#### 1.2.18 --with-cc-opt=parameters
设置附加的编译参数。
#### 1.2.19 --with-ld-opt=parameters
设置附加链接参数。

### 1.3 常用编译配置

	./configure 
				--prefix=/usr/local/nginx 
				--with-https_ssl_module 
				--with-zlib=../zlib-1.2.8
				--with-pcre=../pcre-8.37
				--with-pcre-jit
	./make
	./make install

## 2.nginx进程的启动和控制
### 2.1 以默认配置文件启动nginx
	$sudo ./nginx
#### 2.2 以指定配置文件启动nginx
	$sudo ./nginx -c <path_to_nginx_conf_file>
#### 2.3 nginx进程控制
当nginx的服务进程启动后，我们可以通过信号的方式对linux进程进行控制:

	$sudo ./nginx -s signal
	
这里信号分别如:

1. stop 	快速关闭
2. quit	安全退出
3. reload	重新载入配置文件
4. reopen 重启打开配置文件

## 3.nginx的配置文件结构

### server_name指令
虚拟主机名通过server\_name指令进行配置，用来定位处理请求的server配置块。server\_name可以有以下3种配置方式:

1. 完整的虚拟主机名
2. 通配符
3. 正则表达式


		server {
    		listen       80;
		    server_name  example.org  www.example.org;
		    ...
		}

		server {
			listen       80;
			server_name  *.example.org;
	    	...
		}

		server {
	    	listen       80;
	    	server_name  mail.*;
	    	...
		}

		server {
		    listen       80;
		    server_name  ~^(?<user>.+)\.example\.net$;
		    ...
		}

当按照名字进行虚拟主机配置匹配时，按照以下顺序进行选择:

1. 完整配置的主机名
2. 以星号(*)开始的最长的通配符主机名，例如: \*.example.org
3. 以星号(*)结束的最长的通配符主机名,例如: mail.\*
4. 第一个匹配的正则表达式主机名,例如: ~^(?<user>.+)\.example\.net$

##### 统配符主机名
通配符主机名只能在开始或者结束位置包含星号，且只能紧邻一个'.'域名分隔符。例如虚拟主机名www.*.example.org和w\*.example.org均是非法的虚拟主机名。但是类似这种的虚拟主机名可以使用正则表达式主机名实现。一个星号可以匹配多个名称部分，例如:\*.example.org 不仅仅匹配  www.example.com 也同时匹配www.sub.example.com。

一种特殊的通配符主机名格式.example.com 即可以用来匹配完整的主机名:example.com,也可以用作通配符主机名:*.example.com。

#### 正则表达式主机名
nginx使用的正则表达式语法兼容Perl programming language (PCRE).在主机名中使用正则表达式必须以短横线'~'作为开始字符,例如

	server_name  ~^www\d+\.example\.net$;
	
否则将被当做完整的主机名称,或者如果包含'*'号，则被当做通配符名称(通常会是一个非法的统配符名称)。
记住设置^(句首锚点)和$(句尾锚点)，语法上并没有此要求，但是逻辑上应该对其进行设置。同样需要记住，要对域名分割符'.'进行转意'\'。

如果一个正则表达式包含'{'和'}'符号，需要对其加上引号'"',否则nginx会报以下错误 :

	directive "server_name" is not terminated by ";" in ...
	
nginx的正则表达式主机名支持名称捕获，捕获的名称后续可以作为变量进行使用，例如:

	server {
    server_name   ~^(www\.)?(?<domain>.+)$;

    location / {
        root   /sites/$domain;
    	}
	}
	
PCRE库支持以下格式的名称捕获:

* ?\<name\>	Perl 5.10 compatible syntax, supported since PCRE-7.0
* ?'name'	Perl 5.10 compatible syntax, supported since PCRE-7.0
* ?P\<name\>	Python compatible syntax, supported since PCRE-4.0

如果nginx在启动过程中报错，并输出以下错误信息:

	pcre_compile() failed: unrecognized character after (?< in ...
	
则意味着PCRE库版本过低，应该尝试使用旧的名称捕获语法格式:?P\<name\>。

#### 特殊的主机名表达方式
nginx会对一些虚拟主机名进行特殊对待

如果需要处理不存在HOST字段的http请求，空串需要被设备为虚拟主机名，如下:

	server {
    	listen       80;
    	server_name  example.org  www.example.org  "";
    	...
	}
	
如果server配置块中没有设备server_name,则该server配置块的默认主机名为空串。
如果server_name包含$hostname(0.9.4后版本),则当前主机名称将被用作虚拟主机名。

如果一些请求使用IP地址作为HOST值，则server_name中则需包含对应的IP地址。

如果想要捕获所有的非法域名，则可以使用下面配置:

	server {
    	listen       80  default_server;
    	server_name  _;
    	return       444;
	}
	
这里的'_'并没有特别多的含义，它仅仅是众多非法域名中的其中一个。其它非法的域名像'--'和'!@#'可以被等价的使用。

#### 虚拟主机名的匹配优化
完整的主机名配置、以星号起始的通配符主机名配置、以星号结尾的通配符主机名配置分别存储在3张哈希表中并绑定到对应的监听端口。这些哈希表的大小在配置文件解析阶段经过了优化，所以以这3种方式配置的虚拟主机均能够以较短的时间查找到。

存储完整主机名配置的哈希表最先被进行搜索、如果没有找到，紧接着在存储以星号起始的通配符主机名的哈希表中进行查找，如果同样未搜索到，再到存储以星号结尾的通配符主机名的HASH表中进行查找。

搜索统配服主机名比搜索完整的主机名配置的速度要慢，这是因为通配符主机名是以域名端进行存储。

特殊的主机名格式:.exmaple.com是存储在通配符主机名哈希表中，而不是存储在玩这个主机米哈希表中。

正则表达式主机名则是按照顺序进行一一解析比对，所以对其的搜索速度是最慢的。

综上，最好尽可能的使用完整主机名配置。例如，如果被经常访问的主机名为example.com和www.example.com,比较有效率的配置如下:

	server {
    	listen       80;
    	server_name  example.org  www.example.org  *.example.org;
    	...
	}
	
而不是写起来更为简单的：

	server {
    	listen       80;
    	server_name  .example.org;
    	...
	}

如果一个配置中出现大量的主机名配置，或者超长主机名被使用，你可能需要在http块配置 server\_names\_hash\_max\_size 和 server\_names\_hash\_bucket\_size 指令。

默认的 server\_names\_hash\_bucket\_size大小可能等于32、64或者其它值，取决于CPU的缓存行大小。如果其默认大小是32，但是你配置了一个像这样的主机名:too.long.server.name.example.org,nginx会启动失败，并报告以下错误:

	could not build the server_names_hash,
	you should increase server_names_hash_bucket_size: 32
	
这个时候， server\_names\_hash\_bucket\_size 指令的值需要被调整为下个2的整数倍。

	http {
    	server_names_hash_bucket_size  64;
	    ...
	  
如果你配置了巨大数量的主机名，nginx启动的时候你可能会看到另外一个错误:

	could not build the server_names_hash,
	you should increase either server_names_hash_max_size: 512
	or server_names_hash_bucket_size: 32
	
这个时候你应该先尝试设置 server\_names\_hash\_max\_size 指令的值接近你所配置的主机名数量，只有在这没有帮助的情况下或者nginx的启动时间延长的不可接受的的时候，尝试增长 server\_names\_hash\_bucket\_size 指令的值。

如果nginx的配置对一个端口只配置了一个server块，nginx不会该端口匹配主机名，也就不会为该端口建立对应的主机名哈希表。但是，如果这个唯一的server块主机名采用正则表达式配置，nginx不得不为此进行表达式计算和匹配。

 
### log_format和access_log指令




## 4.nginx相关处理流程
### 4.1 http请求处理流程
#### 4.1.1 基于主机名判定的处理流程
当一个http请求到来时，nginx获取http请求头中的HOST字段来判定该有哪一个虚拟服务来处理当前的HTTP请求,查看下面一段简单的虚拟服务配置:

	server {
    listen      80;
    server_name example.org www.example.org;
    ...
	}

	server {
    listen      80;
    server_name example.net www.example.net;
    ...
	}

	server {
    listen      80;
    server_name example.com www.example.com;
    ...
	}
	
如果获取到的HOST字段值无法匹配到任意一个虚拟服务主机名(server_name)或者无法获取到HOST值得时候,则nginx将会转发该请求到对应接受请求端口的默认的虚拟服务进行处理，对于上面的配置则为第一段server配置(这个个选择是nginx的默认行为),你也可以使用default\_server在listen指令指定哪个虚拟服务配置为默认的处理配置。

**注意:default\_server参数是在listen指令上，而不是server_name上**。
#### 4.1.2 如何阻止访问一个未定义的主机名访问
如果想要禁止无法获取到HOST值得http请求，则可以按照下述配置一个虚拟服务返回错误444:

	server {
    listen      80;
    server_name "";
    return      444;	
	}

这里，将虚拟主机名设置为空串匹配无法获取到HOST值的HTTP请求。

#### 4.1.3 基于主机名和IP的混合判定处理流程
让我们在看一段稍微复杂的配置，不同的虚拟主机监听不同的IP:
	
	server {
    listen      192.168.1.1:80;
    server_name example.org www.example.org;
    ...
	}

	server {
    listen      192.168.1.1:80 default_server;
    server_name example.net www.example.net;
    ...
	}

	server {
    listen      192.168.1.2:80 default_server;
    server_name example.com www.example.com;
    ...
	}
	
在这种配置下，nginx首先会匹配listen指令中监听的IP和端口，然后再根据HTTP请求头中的HOST字段进行虚拟主机名的匹配。

在上面中提到，默认虚拟主机可以在listen指令使用default_server参数指定，监听不同IP、端口的虚拟主机都可以指定各自的默认虚拟主机。


## 4.nginx的常用配置
### 4.1 静态HTTP服务配置
### 4.2 SSL配置
### 4.3 反向代理配置
### 4.4 负载均衡配置

## 5 附录
### 4.1 URL和URI的区别
URI: 统一资源描述符

URL: 统一资源定位符，URL同时可以是一个URI，但是URI不一定是URL。
