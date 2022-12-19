# 学习路线(尚硅谷)

## 第一阶段

JAVA基础

mysql

JDBC

Maven

linux+shell

## 第二阶段

hadoop2.x

hadoop3.x

zookeeper

hadoop3.x高可用集群

HA

Hive/Hive源码解析及优化

Flume

Kafka3.0

HBase2.X

DolphinScheduler2.X

Maxwell

Canal

Scala

Spark/Spark调优

Git

Flink/FlinkSQL/Flink内核源码解析/Flink性能调优

clickhouse

Flink CDC

SuperSet

Atlas

Prometheus

Zabbix

Kylin4.0

## 第三阶段

电商数仓2.0

电商数仓3.0

电商数仓4.0

电商数仓5.0

电商项目（实时）

大数据电信客服案例

实时项目Spark Streaming

机器学习与推荐系统实战

大数据项目实战：电商推荐系统

基于阿里云搭建数据仓库（离线）/(实时)

Flink实时数仓

Flink实时数仓3.0

# 选学

IDEA

SVN

Redis6

JUC

Flink1.13

Oozie

Docker

Elasticseach7.X+8.X

Kubernetes

数据结构算法

DATAX

Stream X

SeaTunnel

Doris

Azkaban

Telegraf



# Hadoop

## 一、Hadoop相关

Hadoop是一种分布式的计算平台，两大核心组件分别是HDFS文件系统和分布式计算处理框架

Mapreduce

 

Hadoop优势：

1.高可靠性：Hadoop底层维护多个数据副本，所以即使某个计算元素或存储出现故障，也不会导致数据丢失

2.高扩展性：在集群间分配任务数据，可方便的扩展数以千计的节点

3.高效性：在mapreduce思想下，hadoop是并行工作的，以加快任务处理速度

4.高容错性：能够自动将失败任务重新分配



### 1.hadoop 搭建

选用VM16+centOS7，装载虚拟机

修改静态IP，vm8网关和本机IP

修改主机名，添加映射

**安装jdk**

```shell
#在opt目录下新建两个文件夹
[root@hadoop100 opt]# mkdir module
[root@hadoop100 opt]# mkdir software   

#上传jdk jar包至software,解压至module
[root@hadoop100 software]# tar -zxvf jdk-8u212-linux-x64.tar.gz -C /opt/module/

#获取JDK路径
[root@hadoop100 jdk1.8.0_212]# pwd
/opt/module/jdk1.8.0_212

#配置环境变量
[root@hadoop100 jdk1.8.0_212]# sudo cd /etc/profile.d
[root@hadoop100 jdk1.8.0_212]# cd /etc/profile.d

#创建.sh脚本
[root@hadoop100 profile.d]# sudo vim my_env.sh
#JAVA_HOME
export JAVA_HOME=/opt/module/jdk1.8.0_212
export PATH=$PATH:$JAVA_HOME/bin
                                                                  
#使文件生效
[root@hadoop100 profile.d]# source /etc/profile

#测试jdk是否安装成功
[root@hadoop100 profile.d]# java -version
openjdk version "1.8.0_262"
OpenJDK Runtime Environment (build 1.8.0_262-b10)
OpenJDK 64-Bit Server VM (build 25.262-b10, mixed mode)

```



**安装hadoop**

```shell
#上传hadoop包至software
#解压文件
[root@hadoop100 ~]# cd /opt/software/
[root@hadoop100 software]# tar -zxvf hadoop-3.1.3.tar.gz -C /opt/module

#配置环境变量
[root@hadoop100 hadoop-3.1.3]# pwd
/opt/module/hadoop-3.1.3
[root@hadoop100 hadoop-3.1.3]# sudo vim /etc/profile.d/my_env.sh
#HADOOP_HOME
export HADOOP_HOME=/opt/module/hadoop-3.1.3

export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin

#使文件生效
[root@hadoop100 hadoop-3.1.3]# source /etc/profile

#测试是否安装成功
[root@hadoop100 hadoop-3.1.3]# hadoop

```



**hadoop目录结构**

```shell
[root@hadoop100 hadoop-3.1.3]# ll
drwxr-xr-x. 2 ricardo ricardo    183 9月  12 2019 bin
drwxr-xr-x. 3 ricardo ricardo     20 9月  12 2019 etc
drwxr-xr-x. 2 ricardo ricardo    106 9月  12 2019 include
drwxr-xr-x. 3 ricardo ricardo     20 9月  12 2019 lib
drwxr-xr-x. 4 ricardo ricardo    288 9月  12 2019 libexec
-rw-rw-r--. 1 ricardo ricardo 147145 9月   4 2019 LICENSE.txt
-rw-rw-r--. 1 ricardo ricardo  21867 9月   4 2019 NOTICE.txt
-rw-rw-r--. 1 ricardo ricardo   1366 9月   4 2019 README.txt
drwxr-xr-x. 3 ricardo ricardo   4096 9月  12 2019 sbin
drwxr-xr-x. 4 ricardo ricardo     31 9月  12 2019 share
```

重要目录

（1）bin目录：存放对Hadoop相关服务（HDFS,YARN）进行操作的脚本

（2）etc目录：Hadoop的配置文件目录，存放Hadoop的配置文件

（3）lib目录：存放Hadoop的本地库（对数据进行压缩解压缩功能）

（4）sbin目录：存放启动或停止Hadoop相关服务的脚本

（5）share目录：存放Hadoop的依赖jar包、文档、和官方案例





### 2.hadoop运行模式

Hadoop运行模式包括：

本地模式:单机运行

伪分布式模式：单机运行，但具备hadoop集群所有功能，一台服务器模拟整个分布式环境

完全分布式模式：多台服务器组成的分布式环境，生产环境使用



本次搭建伪分布式模式

执行步骤：

1.配置集群

```shell
#获取JDK安装路径
[root@hadoop100 ~]# echo $JAVA_HOME
/opt/module/jdk1.8.0_212

#修改JAVA_HOME路径
[root@hadoop100 ~]# export JAVA_HOME=/opt/module/jdk1.8.0_212

#配置core-site.xml
<!-- 指定HDFS中NameNode的地址 -->
	<property>
		<name>fs.defaultFS</name>
    		<value>hdfs://hadoop100:9000</value>
	</property>

<!-- 指定Hadoop运行时产生文件的存储目录 -->
	<property>
		<name>hadoop.tmp.dir</name>
		<value>/opt/module/hadoop-3.1.3/data/tmp</value>
	</property>

<!-- 配置HDFS网页登录使用的静态用户为atguigu -->
   	 <property>
       		 <name>hadoop.http.staticuser.user</name>
       		 <value>root</value>
   	 </property>

#配置hdfs-site.xml
<!-- 指定HDFS副本的数量 -->
<property>
	<name>dfs.replication</name>
	<value>1</value>
</property>
```

2.启动集群

```shell
#**格式化**NameNode（第一次启动时格式化，以后就不要总格式化）
[root@hadoop100 hadoop-3.1.3]# bin/hdfs namenode -format

#启动NameNode
[root@hadoop100 hadoop-3.1.3]# bin/hdfs --daemon start namenode

#启动DataNode
[root@hadoop100 hadoop-3.1.3]# bin/hdfs --daemon start datanode

#查看集群
[root@hadoop100 hadoop-3.1.3]# jps
3584 Jps
3500 DataNode
3407 NameNode

注：jps是JDK中的命令，不是Linux命令。不安装JDK不能使用jps

#web端查看HDFS文件系统
http://hadoop100:9870/ealth.html#tab-overview
注：hadoop3.0之后是9870端口不再是50070端口

#查看产生的log日志
[root@hadoop100 hadoop-3.1.3]# # cd logs
[root@hadoop100 logs]# cat hadoop-root-datanode-hadoop100.log 
[root@hadoop100 hadoop-3.1.3]# # cd data/tmp/dfs/name/current/
[root@hadoop100 current]# cat VERSION
#Tue Sep 20 22:01:46 CST 2022
namespaceID=884952098
clusterID=CID-d156f59b-2877-4af9-8de3-726f46e906ea
cTime=0
storageType=NAME_NODE
blockpoolID=BP-208056269-192.168.10.100-1663682506872
layoutVersion=-63


```



**问题：为什么不能一直格式化Namenode,格式化Namenode，要注意什么**

格式化NameNode，会产生新的集群id,导致NameNode和DataNode的集群id不一致，集群找不到已往数据。所以，格式NameNode时，一定要先删除data数据和log日志，然后再格式化NameNode。



操作集群

#### 2.1启动HDFS并运行Ｍareduce程序

```shell
#本地创建文件夹及测试文件
[root@hadoop100 hadoop-3.1.3]# bin/hdfs dfs -mkdir  -p /user/Ricardo/input
[root@hadoop100 hadoop-3.1.3]# mkdir wcinput
[root@hadoop100 hadoop-3.1.3]# cd wcinput/
[root@hadoop100 wcinput]# touch wc.input
[root@hadoop100 wcinput]# vi wc.input
hadoop yarn
hadoop mapreduce
atguigu
atguigu

#在HDFS文件系统上创建一个input文件夹
[root@hadoop100 hadoop-3.1.3]# hdfs dfs -mkdir  -p /user/Ricardo/input

#将测试文件内容上传到文件系统上
[root@hadoop100 hadoop-3.1.3]# hdfs dfs -put /opt/module/hadoop-3.1.3/wcinput/wc.input /user/Ricardo/input


#查看上传的文件是否正确
[root@hadoop100 hadoop-3.1.3]# hdfs dfs -ls /user/Ricardo/input
Found 1 items
-rw-r--r--   1 root supergroup         45 2022-09-20 23:09 /user/Ricardo/input/wc.input
[root@hadoop100 hadoop-3.1.3]# hdfs dfs -cat /user/Ricardo/input/wc.input
hadoop yarn
hadoop mapreduce
atguigu
atguigu
[root@hadoop100 hadoop-3.1.3]# 

#运行mapreduce程序
[root@hadoop100 hadoop-3.1.3]# hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount /user/Ricardo/input/ /user/Ricardo/output

#查看输出结果
[root@hadoop100 hadoop-3.1.3]# hdfs dfs -cat /user/Ricardo/output/*
atguigu	2
hadoop	2
mapreduce	1
yarn	1

#将测试内容下载到本地
[root@hadoop100 hadoop-3.1.3]# mkdir wcoutput
[root@hadoop100 hadoop-3.1.3]# hdfs dfs -get /user/Ricardo/output/part-r-00000 ./wcoutput/

#删除输出结果
[root@hadoop100 hadoop-3.1.3]# hdfs dfs -rm -r /user/Ricardo/output
22/09/20 23:27:49 INFO fs.TrashPolicyDefault: Namenode trash configuration: Deletion interval = 0 minutes, Emptier interval = 0 minutes.
Deleted /user/Ricardo/output

```

#### 2.2启动YARN并运行MAPREDUCE程序

2.2.1配置集群在Yarn上运行MR

2.2.2启动/测试集群增删查

2.2.3在Yarn上执行WordCount案例

执行步骤

```shell
#配置yarn-env.sh
export JAVA_HOME=/opt/module/jdk1.8.0_144

#配置yarn-site.xml
<!-- Reducer获取数据的方式 -->
	<property>
 		<name>yarn.nodemanager.aux-services</name>
 		<value>mapreduce_shuffle</value>
	</property>

<!-- 指定YARN的ResourceManager的地址 -->
	<property>
		<name>yarn.resourcemanager.hostname</name>
		<value>hadoop100</value>
	</property>

	<property>    
		<name>yarn.nodemanager.env-whitelist</name>       
<value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
 	</property>

#配置mapred-env.sh
export JAVA_HOME=/opt/module/jdk1.8.0_144

[root@hadoop100 hadoop]# vim mapred-site.xml
<!-- 指定MR运行在YARN上 -->
<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
</property>

#启动集群
启动前必须保证NameNode和DataNode已经启动

#启动ResourceManager
[root@hadoop100 hadoop-3.1.3]# yarn --daemon start resourcemanager

#启动NodeManager
[root@hadoop100 hadoop-3.1.3]# yarn --daemon start nodemanager

#集群操作
#Yarn浏览器页面查看
http://hadoop100:8088/cluster

#删除文件系统上的output文件
[root@hadoop100 hadoop-3.1.3]# hdfs dfs -rm -R /user/Ricardo/output

#执行MapReduce程序
[root@hadoop100 hadoop-3.1.3]# hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount /user/Ricardo/input  /user/Ricardo/output

#查看运行结果
[root@hadoop100 hadoop-3.1.3]# hdfs dfs -cat /user/Ricardo/output/*
atguigu	2
hadoop	2
mapreduce	1
yarn	1

```



#### 2.3配置历史服务器

为了查看程序的历史运行情况，需配置历史服务器

```shell
#配置mapred-site.xml
[root@hadoop100 hadoop]# pwd
/opt/module/hadoop-2.7.2/etc/hadoop
[root@hadoop100 hadoop]# vim mapred-site.xml 
<!-- 历史服务器端地址 -->
<property>
<name>mapreduce.jobhistory.address</name>
<value>hadoop100:10020</value>
</property>
<!-- 历史服务器web端地址 -->
<property>
    <name>mapreduce.jobhistory.webapp.address</name>
    <value>hadoop100:19888</value>
</property>

#启动历史服务器
[root@hadoop100 hadoop-3.1.3]# mapred --daemon start historyserver

#查看历史服务器是否启动
[root@hadoop100 hadoop-3.1.3]# jps
6417 Jps
5194 ResourceManager
6347 JobHistoryServer
3500 DataNode
5438 NodeManager
3407 NameNode

#查看JobHistory
http://hadoop100:19888/jobhistory

```



#### 2.4配置日志的聚集

日志聚集概念：应用运行完成以后，将程序运行日志信息上传到HDFS系统上。

日志聚集功能好处：可以方便的查看到程序运行详情，方便开发调试。

注意：开启日志聚集功能，需要重新启动NodeManager 、ResourceManager和HistoryManager。

执行步骤

```shell
#配置yarn-site.xml
[root@hadoop100 hadoop-3.1.3]# cd /opt/module/hadoop-2.7.2/etc/hadoop
[root@hadoop100 hadoop]# vim yarn-site.xml 
<!-- 日志聚集功能使能 -->
<property>
<name>yarn.log-aggregation-enable</name>
<value>true</value>
</property>

<!-- 日志保留时间设置7天 -->
<property>
<name>yarn.log-aggregation.retain-seconds</name>
<value>604800</value>
</property>

#关闭NodeManager、ResourceManager和HistoryManager
[root@hadoop100 hadoop-3.1.3]# yarn --daemon stop resourcemanager
[root@hadoop100 hadoop-3.1.3]# yarn --daemon stop nodemanager
[root@hadoop100 hadoop-3.1.3]# mapred --daemon stop historyserver

#启动NodeManager 、ResourceManager和HistoryManager
[root@hadoop100 hadoop-3.1.3]# yarn --daemon start resourcemanager
[root@hadoop100 hadoop-3.1.3]# yarn --daemon start nodemanager
[root@hadoop100 hadoop-3.1.3]# mapred --daemon start historyserver

#删除HDFS上已经存在的输出文件
[root@hadoop100 hadoop-3.1.3]# hdfs dfs -rm -R /user/Ricardo/output
22/09/21 00:10:29 INFO fs.TrashPolicyDefault: Namenode trash configuration: Deletion interval = 0 minutes, Emptier interval = 0 minutes.
Deleted /user/Ricardo/output

#执行WordCount程序
 [root@hadoop100 hadoop-3.1.3]# hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount /user/Ricardo/input /user/Ricardo/output

#查看日志
http://hadoop100:19888/jobhistory
```



#### 2.5配置文件说明

Hadoop配置文件分两类：默认配置文件和自定义配置文件，只有用户想修改某一默认配置值时，才需要修改自定义配置文件，更改相应属性值。

**默认配置文件**

| 要获取的默认文件     | 文件存放在Hadoop的jar包中的位置                            |
| -------------------- | ---------------------------------------------------------- |
| [core-default.xml]   | hadoop-common-2.7.2.jar/ core-default.xml                  |
| [hdfs-default.xml]   | hadoop-hdfs-2.7.2.jar/ hdfs-default.xml                    |
| [yarn-default.xml]   | hadoop-yarn-common-2.7.2.jar/ yarn-default.xml             |
| [mapred-default.xml] | hadoop-mapreduce-client-core-2.7.2.jar/ mapred-default.xml |

**自定义配置文件**

**core-site.xml****、****hdfs-site.xml****、****yarn-site.xml****、****mapred-site.xml**四个配置文件存放在$HADOOP_HOME/etc/hadoop这个路径上，用户可以根据项目需求重新进行修改配置。



#### 2.6配置免密

```shell
#下载ssh服务并启动
检查是否安装了ssh包，输入命令rpm -qa |grep ssh，输入命令后显示出以下结果就证明安装了ssh包
[root@hadoop100 hadoop-3.1.3]# rpm -qa|grep ssh
openssh-server-7.4p1-21.el7.x86_64
openssh-7.4p1-21.el7.x86_64
libssh2-1.8.0-4.el7.x86_64
openssh-clients-7.4p1-21.el7.x86_64

#创建文件夹
[root@hadoop100 ~]# mkdir .ssh

#生成密钥，出现：后按三次回车
[root@hadoop100 ~]# ssh-keygen -t rsa
[root@hadoop100 ~]# cd .ssh
[root@hadoop100 .ssh]# cat id_rsa.pub >> authorized_keys
[root@hadoop100 .ssh]# cd ~
[root@hadoop100 ~]# chmod 700 .ssh
[root@hadoop100 ~]# chmod 600 .ssh/*
[root@hadoop100 ~]# ssh hadoop100
Last failed login: Wed Sep 21 00:25:36 CST 2022 from localhost on ssh:notty
There were 2 failed login attempts since the last successful login.
Last login: Tue Sep 20 21:20:20 2022 from 192.168.10.1


```

#### 2.7集群启动/停止方式总结

1.	各个服务组件逐一启动/停止

​	（1）分别启动/停止HDFS组件

​		hadoop-daemon.sh  start / stop  namenode / datanode / secondarynamenode

​	（2）启动/停止YARN

​		yarn-daemon.sh  start / stop  resourcemanager / nodemanager

2.	各个模块分开启动/停止（配置ssh是前提）常用

​	（1）整体启动/停止HDFS

​		start-dfs.sh  /  stop-dfs.sh

​	（2）整体启动/停止YARN

​		start-yarn.sh  /  stop-yarn.sh

​	3.问题描述

```shell
在使用start-dfs.sh/stop-dfs.sh/start-yarn.sh/stop-yarn.sh脚本时会产生错误
[root@hadoop100 hadoop-3.1.3]# stop-dfs.sh
Stopping namenodes on [hadoop100]
ERROR: Attempting to operate on hdfs namenode as root
ERROR: but there is no HDFS_NAMENODE_USER defined. Aborting operation.
Stopping datanodes
ERROR: Attempting to operate on hdfs datanode as root
ERROR: but there is no HDFS_DATANODE_USER defined. Aborting operation.
Stopping secondary namenodes [hadoop100]
ERROR: Attempting to operate on hdfs secondarynamenode as root
ERROR: but there is no HDFS_SECONDARYNAMENODE_USER defined. Aborting operation.

解决：
将start-dfs.sh，stop-dfs.sh(在hadoop安装目录的sbin里)两个文件顶部添加以下参数
HDFS_DATANODE_USER=root
HADOOP_SECURE_DN_USER=hdfs
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root

将start-yarn.sh，stop-yarn.sh(在hadoop安装目录的sbin里)两个文件顶部添加以下参数
YARN_RESOURCEMANAGER_USER=root
HADOOP_SECURE_DN_USER=yarn
YARN_NODEMANAGER_USER=root

```



#### 2.8**编写hadoop集群常用脚本**

**Hadoop 集群启停脚本（包含 HDFS**，Yarn，Historyserver）：myhadoop.sh

```shell
[root@hadoop100 bin]# pwd
/usr/bin
[root@hadoop100 bin]# vim myhadoop.sh 
#!/bin/bash
if [ $# -lt 1 ]
then
 echo "No Args Input..."
 exit ;
fi
case $1 in
"start")
 echo " =================== 启动 hadoop 集群 ==================="
 echo " --------------- 启动 hdfs ---------------"
 ssh hadoop100 "/opt/module/hadoop-3.1.3/sbin/start-dfs.sh"
 echo " --------------- 启动 yarn ---------------"
 ssh hadoop100 "/opt/module/hadoop-3.1.3/sbin/start-yarn.sh"
 echo " --------------- 启动 historyserver ---------------"
 ssh hadoop100 "/opt/module/hadoop-3.1.3/bin/mapred --daemon start 
historyserver"
;;
"stop")
 echo " =================== 关闭 hadoop 集群 ==================="
 echo " --------------- 关闭 historyserver ---------------"
 ssh hadoop100 "/opt/module/hadoop-3.1.3/bin/mapred --daemon stop 
historyserver"
 echo " --------------- 关闭 yarn ---------------"
 ssh hadoop100 "/opt/module/hadoop-3.1.3/sbin/stop-yarn.sh"
 echo " --------------- 关闭 hdfs ---------------"
 ssh hadoop100 "/opt/module/hadoop-3.1.3/sbin/stop-dfs.sh"
;;
*)
 echo "Input Args Error..."
;;
esac

保存后退出，赋予脚本执行权限
[root@hadoop100 bin]# chmod +x myhadoop.sh 

```



#### 2.9常用端口号说明(面试可能问到)

|          端口名称          |  hadoop2.X  |    hadoop3.X     |
| :------------------------: | :---------: | :--------------: |
|   NameNode 内部通信端口    | 8020 / 9000 | 8020 / 9000/9820 |
|      NameNode HTTP UI      |    50070    |       9870       |
| MapReduce 查看执行任务端口 |    8088     |       8088       |
|     历史服务器通信端口     |    19888    |      19888       |



#### 2.10 集群时间同步 略



## 二、HDFS相关

### 1.HDFS

#### 1.1HDFS定义

HDFS（Hadoop Distributed File System），**它是一个文件系统**，用于存储文件，通过目录树来定位文件；其次，它是**分布式的**，由很多服务器联合起来实现其功能，集群中的服务器有各自的角色。

**HDFS的使用场景：适合一次写入，多次读出的场景**。一个文件经过创建、写入和关闭之后就不需要改变。



#### 1.2 优缺点

**优点**

高容错性：数据自动保存多个副本，通过增加副本的形式，提高容错性；某一个副本丢失后，可以自动回复

适合处理大数据

​	数据规模：能够处理数据规模达到GB、TB、PB级别的数据

​	文件规模：能够处理百万规模以上的文件数量

可以构建在廉价的机器上，通过多副本机制，提高可靠性

**缺点**

不适合低延迟的数据访问，比如毫秒级的存储数据

无法高效地对大量小文件进行存储

​	存储大量小文件会占用Namenode大量的内存来存储文件目录和块信息

​	小文件存储的寻址时间会超过读取时间，违反了HDFS设计目标

不支持并发写入，文件的随即修改

​	一个文件只能有一个写，不允许多个线程同时写

​	仅支持数据追加，不支持文件修改



#### 1.3 HDFS组成架构



![img](F:\tools\Typora\Typora\data\wps1.png)

![img](F:\tools\Typora\Typora\data\wps2.png)

#### 1.4 HDFS文件块大小（面试重点）

![img](F:\tools\Typora\Typora\data\wps3.png)

**思考：为什么块的大小不能设置的太小，也不能设置的太大**

**1.HDFS的块设置太小，会增加寻址时间，程序一直在找块的开始位置**

**2.如果块设置的太大，从磁盘传输数据的时间会明显大于定位这个块开始位置所需的时间。导致程序在处理这块数据时候会非常慢**



**HDFS块的大小设置主要取决于磁盘传输速率。**



### 2.HDFS的Shell操作（重点）

#### 2.1基本语法

hadoop fs 具体命令 OR hdfs dfs 具体命令

两个是完全相同的。

#### 2.2命令大全

```shell
[root@hadoop100 ~]# cd /opt/module/hadoop-3.1.3/
[root@hadoop100 hadoop-3.1.3]# hadoop fs
Usage: hadoop fs [generic options]
	[-appendToFile <localsrc> ... <dst>]
	[-cat [-ignoreCrc] <src> ...]
	[-checksum <src> ...]
	[-chgrp [-R] GROUP PATH...]
	[-chmod [-R] <MODE[,MODE]... | OCTALMODE> PATH...]
	[-chown [-R] [OWNER][:[GROUP]] PATH...]
	[-copyFromLocal [-f] [-p] [-l] [-d] [-t <thread count>] <localsrc> ... <dst>]
	[-copyToLocal [-f] [-p] [-ignoreCrc] [-crc] <src> ... <localdst>]
	[-count [-q] [-h] [-v] [-t [<storage type>]] [-u] [-x] [-e] <path> ...]
	[-cp [-f] [-p | -p[topax]] [-d] <src> ... <dst>]
	[-createSnapshot <snapshotDir> [<snapshotName>]]
	[-deleteSnapshot <snapshotDir> <snapshotName>]
	[-df [-h] [<path> ...]]
	[-du [-s] [-h] [-v] [-x] <path> ...]
	[-expunge]
	[-find <path> ... <expression> ...]
	[-get [-f] [-p] [-ignoreCrc] [-crc] <src> ... <localdst>]
	[-getfacl [-R] <path>]
	[-getfattr [-R] {-n name | -d} [-e en] <path>]
	[-getmerge [-nl] [-skip-empty-file] <src> <localdst>]
	[-head <file>]
	[-help [cmd ...]]
	[-ls [-C] [-d] [-h] [-q] [-R] [-t] [-S] [-r] [-u] [-e] [<path> ...]]
	[-mkdir [-p] <path> ...]
	[-moveFromLocal <localsrc> ... <dst>]
	[-moveToLocal <src> <localdst>]
	[-mv <src> ... <dst>]
	[-put [-f] [-p] [-l] [-d] <localsrc> ... <dst>]
	[-renameSnapshot <snapshotDir> <oldName> <newName>]
	[-rm [-f] [-r|-R] [-skipTrash] [-safely] <src> ...]
	[-rmdir [--ignore-fail-on-non-empty] <dir> ...]
	[-setfacl [-R] [{-b|-k} {-m|-x <acl_spec>} <path>]|[--set <acl_spec> <path>]]
	[-setfattr {-n name [-v value] | -x name} <path>]
	[-setrep [-R] [-w] <rep> <path> ...]
	[-stat [format] <path> ...]
	[-tail [-f] [-s <sleep interval>] <file>]
	[-test -[defsz] <path>]
	[-text [-ignoreCrc] <src> ...]
	[-touch [-a] [-m] [-t TIMESTAMP ] [-c] <path> ...]
	[-touchz <path> ...]
	[-truncate [-w] <length> <path> ...]
	[-usage [cmd ...]]

Generic options supported are:
-conf <configuration file>        specify an application configuration file
-D <property=value>               define a value for a given property
-fs <file:///|hdfs://namenode:port> specify default filesystem URL to use, overrides 'fs.defaultFS' property from configurations.
-jt <local|resourcemanager:port>  specify a ResourceManager
-files <file1,...>                specify a comma-separated list of files to be copied to the map reduce cluster
-libjars <jar1,...>               specify a comma-separated list of jar files to be included in the classpath
-archives <archive1,...>          specify a comma-separated list of archives to be unarchived on the compute machines

The general command line syntax is:
command [genericOptions] [commandOptions]

```

#### 2.3 常用命令实操

##### 2.3.1 准备工作

```shell

启动集群
[root@hadoop100 hadoop-3.1.3]# sbin/start-dfs.sh
[root@hadoop100 hadoop-3.1.3]# sbin/start-yarn.sh

输入命令参数
[root@hadoop100 hadoop-3.1.3]# hadoop fs -help rm
-rm [-f] [-r|-R] [-skipTrash] [-safely] <src> ... :
  Delete all files that match the specified file pattern. Equivalent to the Unix
  command "rm <src>"
                                                                                 
  -f          If the file does not exist, do not display a diagnostic message or 
              modify the exit status to reflect an error.                        
  -[rR]       Recursively deletes directories.                                   
  -skipTrash  option bypasses trash, if enabled, and immediately deletes <src>.  
  -safely     option requires safety confirmation, if enabled, requires          
              confirmation before deleting large directory with more than        
              <hadoop.shell.delete.limit.num.files> files. Delay is expected when
              walking over large directory recursively to count the number of    
              files to be deleted before the confirmation.   

创建/sanguo文件夹
[root@hadoop100 hadoop-3.1.3]# hadoop fs -mkdir /sanguo

```

##### 2.3.2 上传

```shell
1）-moveFromLocal：从本地剪切粘贴到 HDFS
[root@hadoop100 hadoop-3.1.3]# vim shuguo.txt
输入：
shuguo

[root@hadoop100 hadoop-3.1.3]# hdfs dfs -moveFromLocal ./shuguo.txt /sanguo
2022-11-14 00:24:00,662 INFO sasl.SaslDataTransferClient: SASL encryption trust check: localHostTrusted = false, remoteHostTrusted = false
[root@hadoop100 hadoop-3.1.3]# hdfs dfs -ls -r /
Found 3 items
drwxr-xr-x   - root supergroup          0 2022-09-21 22:58 /user
drwx------   - root supergroup          0 2022-09-21 23:21 /tmp
drwxr-xr-x   - root supergroup          0 2022-11-14 00:24 /sanguo
[root@hadoop100 hadoop-3.1.3]# hdfs dfs -ls -r /sanguo
Found 1 items
-rw-r--r--   1 root supergroup         18 2022-11-14 00:24 /sanguo/shuguo.txt

```

```shell
2）-copyFromLocal：从本地文件系统中拷贝文件到 HDFS 路径去
[root@hadoop100 hadoop-3.1.3]# vim weiguo.txt
输入：
weiguo

[root@hadoop100 hadoop-3.1.3]# hdfs dfs -copyFromLocal weiguo.txt /sanguo
2022-11-14 00:28:30,783 INFO sasl.SaslDataTransferClient: SASL encryption trust check: localHostTrusted = false, remoteHostTrusted = false
[root@hadoop100 hadoop-3.1.3]# hdfs dfs -ls -r /sanguo
Found 2 items
-rw-r--r--   1 root supergroup         17 2022-11-14 00:28 /sanguo/weiguo.txt
-rw-r--r--   1 root supergroup         18 2022-11-14 00:24 /sanguo/shuguo.txt

```

```
3）-put：等同于 copyFromLocal，生产环境更习惯用 put
[root@hadoop100 hadoop-3.1.3]# vim wuguo.txt
输入：
wuguo

[root@hadoop100 hadoop-3.1.3]# hadoop fs -put ./wuguo.txt /sanguo
2022-11-14 00:32:03,164 INFO sasl.SaslDataTransferClient: SASL encryption trust check: localHostTrusted = false, remoteHostTrusted = false
[root@hadoop100 hadoop-3.1.3]# hdfs dfs -ls -r /sanguo
Found 3 items
-rw-r--r--   1 root supergroup         14 2022-11-14 00:32 /sanguo/wuguo.txt
-rw-r--r--   1 root supergroup         17 2022-11-14 00:28 /sanguo/weiguo.txt
-rw-r--r--   1 root supergroup         18 2022-11-14 00:24 /sanguo/shuguo.txt

```

```
4）-appendToFile：追加一个文件到已经存在的文件末尾
[root@hadoop100 hadoop-3.1.3]# vim liubei.txt
输入：
liubei

[root@hadoop100 hadoop-3.1.3]# hdfs dfs -appendToFile liubei.txt /sanguo/shuguo.txt
```



##### 2.3.3 下载

```shell
1）-copyToLocal：从 HDFS 拷贝到本地
[root@hadoop100 hadoop-3.1.3]#  hadoop fs -copyToLocal /sanguo/shuguo.txt ./

2）-get：等同于 copyToLocal，生产环境更习惯用 get
[root@hadoop100 hadoop-3.1.3]#	hadoop fs -get /sanguo/shuguo.txt ./shuguo2.txt
```



##### 2.3.4 HDFS直接操作

```shell
1）-ls: 显示目录信息

2）-cat：显示文件内容

3）-chgrp、-chmod、-chown：Linux 文件系统中的用法一样，修改文件所属权限

4）-mkdir：创建路径

5）-cp：从 HDFS 的一个路径拷贝到 HDFS 的另一个路径

6）-mv：在 HDFS 目录中移动文件

7）-tail：显示一个文件的末尾 1kb 的数据

8）-rm：删除文件或文件夹

9）-rm -r：递归删除目录及目录里面内容

10）-du 统计文件夹的大小信息

11）-setrep：设置 HDFS 中文件的副本数量

这里设置的副本数只是记录在 NameNode 的元数据中，是否真的会有这么多副本，还得看 DataNode 的数量。因为目前只有3台设备，最多也就3个副本，只有节点数的增加到10台时，副本数才能达到10。
```



### 3.HDFS的API操作

文本内用JAVA实现，暂时略过，等后期用python实现



### 4.HDFS读写流程（面试重点）

#### 4.1 写流程

##### 4.1.1写流程

![image-20221114090456274](F:\tools\Typora\Typora\data\image-20221114090456274.png)

（1）客户端通过 Distributed FileSystem 模块向 NameNode 请求上传文件，NameNode 检查目标文件是否已存在，父目录是否存在；

（2）NameNode 返回是否可以上传；

（3）客户端请求第一个 Block 上传到哪几个 DataNode 服务器上；

（4）NameNode 返回 3 个 DataNode 节点，分别为 dn1、dn2、dn3；

（5）客户端通过 FSDataOutputStream 模块请求 dn1 上传数据，dn1 收到请求会继续调用dn2，然后 dn2 调用 dn3，将这个通信管道建立完成；

（6）dn1、dn2、dn3 逐级应答客户端；

（7）客户端开始往 dn1 上传第一个 Block（先从磁盘读取数据放到一个本地内存缓存），以 Packet 为单位，dn1 收到一个 Packet 就会传给 dn2，dn2 传给 dn3；dn1 每传一个 packet会放入一个应答队列等待应答；

（8）当一个 Block 传输完成之后，客户端再次请求 NameNode 上传第二个 Block 的服务器。（重复执行 3-7 步）



##### 4.1.2 网络拓扑-节点距离计算

在 HDFS 写数据的过程中，NameNode 会选择距离待上传数据最近距离的 DataNode 接收数据。那么这个最近距离怎么计算呢？

节点距离：两个节点到达最近的共同祖先的距离总和。

![image-20221114091231338](F:\tools\Typora\Typora\data\image-20221114091231338.png)

例如，假设有数据中心 d1 机架 r1 中的节点 n1。该节点可以表示为/d1/r1/n1。利用这种标记，这里给出四种距离描述。

![image-20221114091340124](F:\tools\Typora\Typora\data\image-20221114091340124.png)

##### 4.1.3 机架感知（副本存储节点选择）

![image-20221114091453433](F:\tools\Typora\Typora\data\image-20221114091453433.png)

#### 4.2 读流程

![image-20221114091646300](F:\tools\Typora\Typora\data\image-20221114091646300.png)

（1）客户端通过 DistributedFileSystem 向 NameNode 请求下载文件，NameNode 通过查询元数据，找到文件块所在的 DataNode 地址；

（2）挑选一台 DataNode（就近原则，然后随机）服务器，请求读取数据；

（3）DataNode 开始传输数据给客户端（从磁盘里面读取数据输入流，以 Packet 为单位来做校验）；

（4）客户端以 Packet 为单位接收，先在本地缓存，然后写入目标文件。



### 5.**NameNode** **和** **SecondaryNameNode**

#### 5.1 **NN** **和** **2NN** 工作机制

思考：NameNode 中的元数据是存储在哪里的？

首先，我们做个假设，如果存储在 NameNode 节点的磁盘中，因为经常需要进行随机访问，还有响应客户请求，必然是效率过低。因此，元数据需要存放在内存中。但如果只存在内存中，一旦断电，元数据丢失，整个集群就无法工作了。因此产生在磁盘中备份元数据的FsImage。

这样又会带来新的问题，当在内存中的元数据更新时，如果同时更新 FsImage，就会导致效率过低，但如果不更新，就会发生一致性问题，一旦 NameNode 节点断电，就会产生数据丢失。因此，引入 Edits 文件（只进行追加操作，效率很高）。每当元数据有更新或者添加元数据时，修改内存中的元数据并追加到 Edits 中。这样，一旦 NameNode 节点断电，可以通过 FsImage 和 Edits 的合并，合成元数据。

但是，如果长时间添加数据到 Edits 中，会导致该文件数据过大，效率降低，而且一旦断电，恢复元数据需要的时间过长。因此，需要定期进行 FsImage 和 Edits 的合并，如果这个操作由NameNode节点完成，又会效率过低。因此，引入一个新的节点SecondaryNamenode，专门用于 FsImage 和 Edits 的合并。

![image-20221118004029137](F:\tools\Typora\Typora\data\image-20221118004029137.png)

1）第一阶段：NameNode启动

（1）第一次启动 NameNode 格式化后，创建 Fsimage 和 Edits 文件。如果不是第一次启动，直接加载编辑日志和镜像文件到内存；

（2）客户端对元数据进行增删改的请求；

（3）NameNode 记录操作日志，更新滚动日志；

（4）NameNode 在内存中对元数据进行增删改；

2）第二阶段：Secondary NameNode工作

（1）Secondary NameNode 询问 NameNode 是否需要 CheckPoint。直接带回 NameNode

是否检查结果。

（2）Secondary NameNode 请求执行 CheckPoint；

（3）NameNode 滚动正在写的 Edits 日志；

（4）将滚动前的编辑日志和镜像文件拷贝到 Secondary NameNode；

（5）Secondary NameNode 加载编辑日志和镜像文件到内存，并合并；

（6）生成新的镜像文件 fsimage.chkpoint；

（7）拷贝 fsimage.chkpoint 到 NameNodel;

（8）NameNode 将 fsimage.chkpoint 重新命名成 fsimage。



#### 5.2 Fsimage **和** **Edits** 解析

![image-20221118004337889](F:\tools\Typora\Typora\data\image-20221118004337889.png)

1）oiv查看 Fsimage 文件

（1）查看 oiv 和 oev 命令

```shell
[root@hadoop100 ~]# hdfs
oev                  apply the offline edits viewer to an edits file
oiv                  apply the offline fsimage viewer to an fsimage
oiv_legacy           apply the offline fsimage viewer to a legacy fsimage

```

（2）基本语法

hdfs oiv -p 文件类型 -i 镜像文件 -o 转换后文件输出路径

（3）案例实操

```shell
[root@hadoop100 current]# pwd
/opt/module/hadoop-3.1.3/data/tmp/dfs/name/current
[root@hadoop100 current]#  hdfs oiv -p XML -i fsimage_0000000000000000253 -o /opt/module/hadoop-3.1.3/fsimage.xml

[root@hadoop100 current]# cat /opt/module//hadoop-3.1.3/fsimage.xml 

```

2）oev 查看 Edits文件

具体步骤同上



#### 5.3 CheckPoint时间设置

1）通常情况下，SecondaryNameNode 每隔一小时执行一次。

```xml
[hdfs-default.xml]
<property>
 	<name>dfs.namenode.checkpoint.period</name>
 	<value>3600s</value>
</property>
```

2）一分钟检查一次操作次数，当操作次数达到 **1** 百万时，SecondaryNameNode 执行一次。

```xml
<property>
 	<name>dfs.namenode.checkpoint.txns</name>
	 <value>1000000</value>
	<description>操作动作次数</description>
</property>
<property>
	 <name>dfs.namenode.checkpoint.check.period</name>
 	<value>60s</value>
	<description> 1 分钟检查一次操作次数</description>
</property>
```





### 6.DataNode

#### 6.1 **DataNode** 工作机制

![image-20221118005636754](F:\tools\Typora\Typora\data\image-20221118005636754.png)

（1）一个数据块在 DataNode 上以文件形式存储在磁盘上，包括两个文件，一个是数据本身，一个是元数据包括数据块的长度，块数据的校验和，以及时间戳。

（2）DataNode 启动后向 NameNode 注册，通过后，周期性（6 小时）的向 NameNode 上报所有的块信息。

```XML
DN 向 NN 汇报当前解读信息的时间间隔，默认 6 小时:
<property>
	<name>dfs.blockreport.intervalMsec</name>
	<value>21600000</value>
	<description>Determines block reporting interval in milliseconds.</description>
</property>

DN 扫描自己节点块信息列表的时间，默认 6 小时:
<property>
	<name>dfs.datanode.directoryscan.interval</name>
	<value>21600s</value>
	<description>Interval in seconds for Datanode to scan data	directories and reconcile the difference between blocks in memory and on the disk.Support multiple time unit suffix(case insensitive), as described in dfs.heartbeat.interval.
	</description>
</property>
```

（3）心跳是每 3 秒一次，心跳返回结果带有 NameNode 给该 DataNode 的命令如复制块

数据到另一台机器，或删除某个数据块。如果超过 10 分钟没有收到某个 DataNode 的心跳，

则认为该节点不可用;

（4）集群运行中可以安全加入和退出一些机器;



#### 6.2 数据完整性

思考：如果电脑磁盘里面存储的数据是控制高铁信号灯的红灯信号（1）和绿灯信号（0），但是存储该数据的磁盘坏了，一直显示是绿灯，是否很危险？同理 DataNode 节点上的数据损坏了，却没有发现，是否也很危险，那么如何解决呢？

如下是 DataNode 节点保证数据完整性的方法。

（1）当 DataNode 读取 Block 的时候，它会计算 CheckSum；

（2）如果计算后的 CheckSum，与 Block 创建时值不一样，说明 Block 已经损坏；

（3）Client 读取其他 DataNode 上的 Block；

（4）常见的校验算法 crc（32），md5（128），sha1（160）

（5）DataNode 在其文件创建后周期验证 CheckSum。



#### 6.3 掉线时限参数设置

![image-20221118010040486](F:\tools\Typora\Typora\data\image-20221118010040486.png)

需要注意的是 hdfs-site.xml 配置文件中的 heartbeat.recheck.interval 的单位为毫秒，dfs.heartbeat.interval 的单位为秒。

```XML
<property>
 	<name>dfs.namenode.heartbeat.recheck-interval</name>
	 <value>300000</value>
</property>
<property>
 	<name>dfs.heartbeat.interval</name>
	 <value>3</value>
</property>
```



## 三、MapReduce

### 1.MapReduce概述

#### 1.1 定义

MapReduce 是一个分布式运算程序的编程框架，是用户开发“基于 Hadoop 的数据分析应用”的核心框架。

MapReduce 核心功能是将用户编写的业务逻辑代码和自带默认组件整合成一个完整的分布式运算程序，并发运行在一个 Hadoop 集群上。



#### 1.2 优缺点

优点：

1）MapReduce 易于编程

它简单的实现一些接口，就可以完成一个分布式程序，这个分布式程序可以分布到大量廉价的 PC 机器上运行。也就是说你写一个分布式程序，跟写一个简单的串行程序是一模一样的。就是因为这个特点使得 MapReduce 编程变得非常流行；

2）良好的扩展性

当你的计算资源不能得到满足的时候，你可以通过简单的增加机器来扩展它的计算能力；

3）高容错性

MapReduce 设计的初衷就是使程序能够部署在廉价的 PC 机器上，这就要求它具有很高的容错性。比如其中一台机器挂了，它可以把上面的计算任务转移到另外一个节点上运行，不至于这个任务运行失败，而且这个过程不需要人工参与，而完全是由 Hadoop 内部完成的；

4）适合 **PB** 级以上海量数据的离线处理

可以实现上千台服务器集群并发工作，提供数据处理能力。



缺点：

1）不擅长实时计算

MapReduce 无法像 MySQL 一样，在毫秒或者秒级内返回结果；

2）不擅长流式计算

流式计算的输入数据是动态的，而 MapReduce 的输入数据集是静态的，不能动态变化。这是因为 MapReduce 自身的设计特点决定了数据源必须是静态的

3）不擅长DAG（有向无环图）计算

多个应用程序存在依赖关系，后一个应用程序的输入为前一个的输出。在这种情况下，MapReduce 并不是不能做，而是使用后，每个 MapReduce 作业的输出结果都会写入到磁盘，会造成大量的磁盘 IO，导致性能非常的低下。



#### 1.3
