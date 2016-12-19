#Redmine手册

[Redmine](http://www.redmine.org/) 是基于web的项目管理与缺陷跟踪管理系统的自由及开放源代码软件工具。它集成了项目管理所需的各项功能：日历、燃尽图和甘特图 以协助可视化表现项目与时间限制，问题跟踪和版本控制。此外，Redmine也可以同时处理多个项目。

Redmine 是以 Ruby on Rails 撰写的架构，它横跨多个平台与数据库。


##1.安装
目前公司内部采用基于Ruby 2.0.3的Redmine 3.2.3版本，将以该版本为例讲述Redmine服务的搭建过程。


###1.1 数据库建立
Redmine支持多种后端数据库存储，分别如下:

* MySQL
* PostgreSQL
* SQL Server

这里我们将以MySQL为例。

在MySQL创建提供给Redmine使用的数据库:

	CREATE DATABASE redmine CHARACTER SET utf8;
	CREATE USER 'redmine'@'localhost' IDENTIFIED BY 'my_password';
	GRANT ALL PRIVILEGES ON redmine.* TO 'redmine'@'localhost';
	
数据库建立完成后，我们需要修改Redmine的配置，使其连接到指定的数据库。在Redmine根目录下的config子目录,复制database.yml.example并更名为database.yml后，对其进行编辑修改，如下:

	production:
	  adapter: mysql2
	  database: redmine
	  host: localhost
	  username: redmine
	  password: my_password
	
如果是使用ruby1.8或者JRuby，配置如下:

	production:
	  adapter: mysql
	  database: redmine
	  host: localhost
	  username: redmine
	  password: my_password
	 
###1.2 依赖包安装
Redmine是基于Bundler进行依赖包管理，可以在Redmine根目录下运行下述命令进行依赖包(gem)的安装:
	
	bundle install --without development test
	
####1.2.1  可选依赖包
Redmine包含一个可选的依赖:RMagick, 这个依赖(gem)可以允许Redmine通过ImageMagick来进行pdf和png文件的导出操作,如果需要使用RMagick则必须要安装ImageMagick，可以通过yum包管理器来进行安装:

	sudo yum install imagemagick-devel
	
如果不需要该功能，则可以使用下述命令跳过该依赖包的安装:
	
	bundle install --without development test rmagick

####1.2.3 自定义依赖包
如果需要在Redmine中包含附加或者自定义的依赖包(gem),可以在Redmine的根目录下创建文Gemfile.local文件，并向其中添加自定义的依赖包:

	# Gemfile.local
	gem 'unicorn'
	
其中的依赖会在运行bundle install命令时被自动获取。
	
	
###1.3 数据库表创建
在Redmine的根目录运行下述命令创建Redmine的相关数据库表结构:

	RAILS_ENV=production bundle exec rake db:migrate
###1.4 基础数据导入
数据库表创建完毕后，可以开始导入初始的相关基础数据:

	RAILS_ENV=production bundle exec rake redmine:load_default_data
	
	
###1.5 文件系统权限
运行redmine的用户必须具备以下目录的写权限:

* files
* log
* tmp and tmp/pdf
* public/plugin_assets

假设运行Redmine的用户为redmine，可以通过下述命令确保redmine用户具备对上述目录的写权限:

	mkdir -p tmp tmp/pdf public/plugin_assets
	sudo chown -R redmine:redmine files log tmp public/plugin_assets
	sudo chmod -R 755 files log tmp public/plugin_assets
	
###1.6 安装测试
在完成上述步骤后，我们既可通过运行Ruby内置的web server来测试Redmine安装的正确性:

	bundle exec rails server webrick -e production
	
	
##2.生产架构 nginx + unicorn + redmine
Ruby自带的webrick的性能并不能满足生产的访问需求,通过多方面的考虑，决定采用nginx + unicorn + redmine架构进行Redmine的生产部署。

###2.1 unicorn安装

	sudo gem install unicorn
	
	
###2.2 uicorn 启动

	 bundle exec unicorn_rails -c ${CONF} -E ${ENVIROMENT} -D






	
	
