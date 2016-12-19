#MySQL 手册

随着公司技术中心内部即将部署的多套开源系统的部署，MySQL在公司内部的应用将逐渐增多。本篇手册意在帮助技术中心人员在刚开始接触mysql时，可以快速的掌握基本的操作。

## 1.MySQL安装

由于公司内部大部分使用的linux发行版为CentOS 6.5，本节就以CentOS 6.5为例，说明MySQL的安装过程。

在进行MySQL安装之前，先删除系统上遗留的MySQL相关rpm包，以免和安装版本产生冲突:

	sudo yum remove mysql-libs

在CentOS上快速安装MySQL的方法是通过yum的方式进行安装，MySQL的[官方网站](http://dev.mysql.com/downloads/repo/yum/)提供了官方yum源(rpm格式)的下载链接。

以在CentOS 6版本上安装MySQL 5.7版本为例，可以使用下述命令获取对应yum源的rpm安装包。

		wget http://dev.mysql.com/get/mysql57-community-release-el6-7.noarch.rpm
在下载了对应的yum源的rpm安装包后，通过命令进行安装：

		sudo yum install mysql57-community-release-el6-7.noarch.rpm
		
官方mysql源安装完成后，可以使用**yum repolist**命令查看MySQL官方源是否安装成功，如果安装成功，可以在列表中看见下述源信息(mysql57为例):

	mysql-connectors-community                                              
	mysql-tools-community  
	mysql57-community
	

上述执行完成，即可真正开始安装mysql:

	sudo yum install mysql-community-server
	sudo yum install mysql-community-devel
	
##2.MySQL安装完成后的注意事项:

在安装完成MySQL后，可以使用下面命令启动MySQL:

	service mysqld start
	
同时可以使用下述命令来查看MySQL的运行状态:
	
	service mysqld status
	
在MySQL 5.7版本或者更高版本安装完成之后,会在/var/log/mysqld.log下生成一个临时的随机密码，可以通过查看该文件来获取这个临时密码:

	grep 'temporary password' /var/log/mysqld.log
	
	
一旦你获取了临时密码，可以通过运行**mysql_secure_installation**来安全化你的MySQL安装，例如重新设置root密码，移除匿名用户，移除远程root登入等等。

完成上诉一些列动作后，就可以开始登入MySQL了:

	mysql -u root -p
	
	
**注:MySQL Server默认的监听端口为3306**


##3.MySQL目录结构
通过yum安装的MySQL所包含的主要目录结构如下:

* /usr/bin     包含客户端工具以及脚本
* /usr/sbin    包含mysqld服务端应用
* /var/lib/mysql    包含日志文件以及数据目录(默认位置)
* /usr/share/info     Info格式手册
* /usr/share/man      man格式手册
* /usr/include/mysql  头文件
* /usr/lib/mysql      库文件
* /usr/share/mysql    各种支持文件包含错误消息、字符集文件、配置样例文件、已经MySQL安装脚本等
* /usr/share/sql-bench     基准测试工具


##4.MySQL配置文件
MySQL的配置文件名为my.cnf,MySQL在启动的时候会按照下列顺序读取配置文件:

	/etc/my.cnf /etc/mysql/my.cnf /usr/etc/my.cnf ~/.my.cnf

如果存在多份配置文件，MySQL会依次读取直到读取完最后一份配置文件。


##5.MySQL常用操作
