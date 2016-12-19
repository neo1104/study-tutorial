# Yum 手册

## 1.什么是Yum

Yum（Yellow dog Updater, Modified）是一个基于RPM包管理的字符前端软件包管理器。能够从指定的服务器自动下载RPM包并且安装，可以处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。被Yellow Dog Linux本身，以及Fedora、Red Hat Enterprise Linux采用。


## 2.Yum配置
Yum的配置分为两个部分: **main** 、**repository**

* main 定义了全局配置，整个Yum配置应该只有一个main，常位于/etc/yum.conf中
* repository 定义了每个源的具体配置，可以有一个或者多个。常位于/etc/yum.repo.d目录



