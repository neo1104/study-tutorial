# Ruby 简介

因为即将在团队内部推广的redmine是基于ruby进行开发，所以在这里简单的介绍下ruby的相关知识点，便于今后应对redmine的各种维护工作。


## 1.什么是Ruby

Ruby，中文名为"红宝石", 是一种面向对象、命令式、函数式、动态的通用编程语言。在20世纪90年代中期由日本人松本行弘设计并开发。
## 2.什么是 Ruby on Rails
Ruby on Rails（官方简称为 Rails。也有人简称为 RoR），是一个使用Ruby语言写的开源Web应用框架，它是严格按照MVC结构开发的。它努力使自身保持简单，来使实际的应用开发时的代码更少，使用最少的配置。
## 2.gem介绍
RubyGems是Ruby的一个包管理器，提供了分发Ruby程序和函式库的标准格式“gem”，旨在方便地管理gem安装的工具，以及用于分发gem的服务器。这类似于Python的pip。RubyGems大约创建于2003年11月，从Ruby 1.9版起成为Ruby标准库的一部分。

gem通常是依照“.gemspec”文件构建的，其为包含了有关Gem信息的YAML文件。

### 2.1 gem目录结构
一个gem遵循以下目录结构

	mygem/
	├── bin/
	│   └── mygem
	├── lib/
	│   └── mygem.rb
	├── test/
	│   └── test_mygem.rb
	├── README
	├── Rakefile
	└── mygem.gemspec
	
其中:

* lib目录包含gem的源代码
* test目录测试相关代码
* gem通常也包含Rakefile文件，rake通过其来执行自动化测试、代码生成以及其它一些任务
* bin目录包含gem的二进制文件
* .gemspec文件包含了gem的相关信息，例如gem的相关文件、对应平台、版本信息以及作者的姓名和邮箱。


gem在CentOS下通常的全局安装目录位于/usr/lib/ruby/gems/[ruby版本]目录。实际目录可以通过

	gem env
	
命令查看。

### 2.2 gem在中国大陆的使用
ruby仓库托管在Amazon S3上，在中国大陆，由于防火长城对Amazon S3的封锁，在使用官方源进行安装时可能会长时间无响应，并返回“Connection reset by peer”等错误。因此在中国大陆可能需要更换软件源才能正常使用Gem进行安装。

可以通过修改gem源到国内大陆镜像的方式来解决访问问题，命令如下:

	gem sources --remove https://rubygems.org/    #删除默认源
	gem sources --add https://ruby.taobao.org/    #添加淘宝镜像源
	gem sources -l                                #显示当前源列表
	
	
### 2.3 gem升级
目前公司常用linux发布版本CentOS 6.5版本中默认安装的ruby版本为1.8.7,而大部分基于ruby的应用均要求ruby1.9或者以上版本(例如redmine),所以需要对ruby进行升级操作，这里以CentOS 6.5发行版为例，说明ruby的升级流程:

	$ sudo yum remove ruby ruby-devel		删除原先基于yum安装的ruby版本
	$ sudo yum groupinstall "Development Tools"  安装相关依赖
	$ sudo yum install openssl-devel
	$ wget https://cache.ruby-lang.org/pub/ruby/2.2/ruby-2.2.5.tar.gz						           获取新的ruby版本
	$ tar xvzf ruby-2.1.2.tar.gz
	$ cd ruby-2.1.2
	$ ./configure                               进行编译安装ruby
	$ make
	$ sudo make install
	$ sudo gem update --system                  升级gem版本
	$ sudo gem install bundler
			
### 2.4 gem用法

安装:
		
		gem install mygem

卸载:
		
		gem uninstall mygem
		
列出已安装的gem:

		gem list --local
		
列出可用的gem:

		gem list --remote
		
下载一个gem,但不安装:

		gem fetch mygem
		
从可用的gem中搜索:

		gem search STRING --remote
		
		
###3.什么是Bundler
Bundle来管理项目中所有gem依赖，该命令只能在一个含有Gemfile的目录下执行.如果系統已經有裝了Gemfile中指定的gem，就用系統的，不然會裝到 $BUNDLE_PATH 下，默认在当前用户下 ~/.bundle。		
		