# Hbase实验一

zookeeper-3.4.14

**~/.bashrc 内容**

```
software下的解压后创建软连接避免输入错误
例：
in -s hbase-2.2.7 hbase
...jdk
...hadoop
```



```
# 配置Hadoop的安装目录
export HADOOP_HOME=/home/hadoop/software/hadoop
# 在原PATH的基础上加入Hadoop的bin和sbin目录
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
export JAVA_HOME=/home/hadoop/software/jdk  # 配置Java的安装目录
export PATH=$PATH:$JAVA_HOME/bin  # 在原PATH的基础上加入JDK的bin目录
export HBASE_HOME=/home/hadoop/software/hbase
export PATH=$PATH:$HBASE_HOME/bin
export ZOOKEEPER_HOME=/home/hadoop/software/zookeeper
export PATH=$PATH:$ZOOKEEPER_HOME/bin

```



### 1、HBase的安装

##### 解压hbase

 进入/home/hadoop/**software**

```
 tar -zxvf hbase-2.7.7-bin.tar.gz
```

##### 配置环境变量

```
vi ~/.bashrc
```

```
export HBASE_HOME=/home/hadoop/software/hbase
export PATH=$PATH:$HBASE_HOME/bin

```

##### 使环境变量生效

```
source ~/.bashrc
```

### 2、伪分布式集群

##### 修改hbase-env.sh

```
cd ~/hbase/conf
vi hbase-env.sh
```


打开在其中添加

```
export JAVA_HOME=/home/hadoop/software/jdk
export HBASE_MANAGES_ZK=true
```

##### 修改hbase-env.sh

```
cd ~/hbase/conf
vi hbase-site.xml
```

修改为 

**注：hdfs节点名称与hdfs的core-site.xml文件相同

```
<property>
<name>hbase.rootdir</name>
<value>hdfs://localhost:9000/hbase</value>
</property>

<property>
<name>hbase.cluster.distributed</name>
<value>true</value>
</property>

<property>
<name>hbase.zookeeper.quorum</name>
<value>hadoop</value>
</property>

<property>
<name>dfs.replication</name>
<value>1</value>
</property>

<property>
<name>hbase.zookeeper.property.dataDir</name>
<value>/home/hadoop/software/hbase/zookeeper</value>
</property>




```

##### 启动HBase

```
start-hbase.sh
```

##### 检查进程

```
jps
```

