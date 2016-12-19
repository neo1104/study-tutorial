#Hadoop 学习笔记

##1.基础

Hadoop主要有两部分组成:分布式文件系统HDFS 和 分布式计算框架MapReduce。其中分布式文件系统HDFS主要用于海量数据的存储，而MapReduce则是基于此分布式文件系统对存储在分布式文件系统中的数据进行分布式计算。

###1.1 HDFS
HDFS作为Hadoop的分布式文件系统，出于Hadoop生态圈的最下层，存储着所有的数据，支持着Hadoop的所有服务。它的理论基础源于Google的The Google File System这篇论文，它是GFS的开源实现。

HDFS的设计理念是以流式数据访问模式，存储超大文件，运行于廉价硬件集群之上。

构成HDFS集群的主要是两类节点，并以主从(master/slave)模式，或者说是管理者-工作者模式运行， 即一个NameNode(管理者)，多个工作者(DataNode)。还有一类节点叫做SecondaryNameNode，作为NameNode镜像数据备份。

NameNode、DataNode、SecondaryNode实际上是各物理节点上运行的守护进程，所以NameNode即是守护进程，也可以指运行NameNode守护进程的节点。

###1.2 MapReduce
MapReduce是一种编程模型，Hadoop根据Google的MapReduce论文将其实现，作为Hadoop的分布式计算模型，是Hadoop的核心。

####1.2.1 Mrv1

Mrv1是第一代MapReduce计算框架，在Mrv1中构成MapReduce集群为两类节点，JobTracker和TaskTracker。同HDFS类似，MapReduce也采用主从(master/slave)的架构。

JobTracker和TaskTracker也是两种守护进程，运行在各自的节点上。

|守护进程|集群中的数目|作用|
|-----|-----------|----|
|JobTracker|1|负责接受客户端作业提交、调度任务到TaskTracker上运行，并提供监控TaskTracker及任务进度管等管理|
|TraskTracker|多个(至少一个)|实例化用户程序，在本地执行任务并周期性地向JobTracker汇报状态|


注意: NameNode和JobTracker需要配对安装在一个节点，但NameNode和JobTracker却不一定要部署在同一个节点。

####1.2.2 YARN/Mrv2

第一代MapReduce计算框架Mrv1具有扩展性和多计算框架支持不足的的缺点，针对这些，提出了全新的资源管理框架(Yet Another Resource Manager),通过这个框架，我们可以在共用底层存储(HDFS)的情况下，计算框架采用可插拔式的配置。

在Mrv1中的jobtracker的资源管理和作业跟踪的功能分拆由ResourceManager和ApplicationManager两部份组成，增加了扩展性。

YARN包含以下组件:

1. ResourceManager
2. NodeManager
3. JobHistory


####1.2.3 Hadoop发行版选择

####1.2.3 CDH5目录结构

##2.安装
###2.1 安装准备
####2.1.1 JDK
Hadoop依赖于Java，所以在安装Hadoop之前必须先安装JDK，后续所有环境中依赖的JDK版本如下:

|JDK版本|jdk-7u80-linux-x64|
|-------|-----------------|

####2.1.2主机名设置

####2.1.3SSH配置

####2.1.4环境变量配置


|环境变量名|说明|
|--------|----|
|HADOOP_HOME||
|HADDOP_PREFIX||
|HADOOP\_CONF_DIR||
|HADOOP\_COMMON\_LIB\_NATIVE_DIR||


##3.Hadoop配置文件

|文件名|说明|
|----|----|
|hadoop-env.sh||
|core-site.xml||
|hdfs-site.xml||
|mapred-site.xml||
|yarn-site.xml||
|masters||
|slaves||



###附录1: Hadoop2.0主要配置选项

####core-site.xml


|配置项|值|说明|
|-----|---|---|
|fs.defaultFS|file:///|NameNode节点位置|
|hadoop.tmp.dir|/tmp/hadoop-${user.name}|设置临时文件存放目录|


####hdfs-site.xml

|配置项|默认值|说明|
|-----|---|---|
|dfs.namenode.name.dir|file://${hadoop.tmp.dir}/dfs/name||
|dfs.namenode.http.address|0.0.0.0:50070||
|dfs.namenode.secondary.http-address|0.0.0.0:50090||
|dfs.namenode.data.dir|file://${hadoop.tmp.dir}/dfs/data||
|dfs.blocksize|134217728||

####yarn-site.xml

|配置项|默认值|说明|
|-----|---|---|
|yarn.resourcemanager.hostname|0.0.0.0|设置ResourceManager主机|
|yarn.resourcemanager.address|${yarn.resourcemanager.hostname}:8032||
|yarn.resourcemanager.resource-tracker.address|${yarn.resourcemanager.hostname}:8031||
|yarn.resourcemanager.admin.address|${yarn.resourcemanager.hostname}:8033||
|yarn.resourcemanager.webapp.address|${yarn.resourcemanager.hostname}:8088||
|yarn.resourcemanager.scheduler.class|CapacityScheduler||
|yarn.nodemanager.aux-services|mapreduce_shuffle||




####mapred-site.xml

|配置项|默认值|说明|
|-----|---|---|
|mapred.framework.name|local||
|mapred.jobhistory.address|0.0.0.0:10020||
|mapred.jobhistory.webapp.address|0.0.0.0:19888||


###附录2: Hadoop各组件Web监控地址

|组件名称|URL|说明|
|------|---|---|
|NameNode|http://nn_host:port|默认HTTP访问端口50070|
|ResourceManager|http://rm_host:port|默认HTTP访问端口8088|
|MapReduce JobHistory Server|http://jhs_host:port|默认HTTP访问端口19888|



###附录3: Hadoop1.0与Hadoop2.0配置项变更列表


|文件|Hadoop1.0配置项|Hadoop2.0配置项|说明|
|---|--------------|--------------|---|
|core-site.xml|fs.default.name|fs.DefaultFS||
|hdfs-site.xml|dfs.name.dir|dfs.namenode.name.dir|
|hdfs-site.xml|dfs.data.dir|dfs.datanode.data.dir|
|mapred-site.xml|mapred.job.tracker|mapred.jobtracker.address|






