# hadoop伪分布式安装

## 1、安装jdk

### 安装JDK

可以将所需的软件安装包放在一块，例如：~/software

#### （1）准备软件

JDK的安装包已经为大家准备好，在/root/software目录下，可以使用如下命令进行查看：

```
cd /root/software/		# 进入目录
ll					# 罗列出当前文件或目录的详细信息，是ls -l的别名
```

#### （2）解压压缩包

```
tar -zxvf jdk-8u221-linux-x64.tar.gz
```

#### （3）在此处我们配置**系统环境变量**，使用命令：

```
vim /etc/profile或者
vim ~/.bashrc                  //建议

```

#### （4）在最后加入以下两行内容：

**路径为自己jdk路径**

```
export JAVA_HOME=/root/software/jdk1.8.0_221  # 配置Java的安装目录，路径为自己jdk路径
export PATH=$PATH:$JAVA_HOME/bin  # 在原PATH的基础上加入JDK的bin目录
```

一定要注意PATH值的修改，**一定要在引用原PATH值**，否则Linux的很多操作命令就不能使用了。

**注意：**

- export 是把这两个变量导出为全局变量。
- 大小写必须严格区分。

#### （5）**让配置文件立即生效**，使用如下命令：

```
上面修改那个文件就用那个命令
source /etc/profile

source ~/.bashrc 
```

#### （6）检测JDK是否安装成功，使用命令查看JDK版本：

```
java -version
whereis java
```

执行此命令后，若是出现JDK版本信息说明配置成功

## 2、免密登录

### 配置SSH免密登录

#### （1）下载SSH服务并启动

SSH服务（openssh-server和openssh-clients）已经为大家下载好，所以此处直接启动即可：

```shell
/usr/sbin/sshd
sudo apt-get update
sudo apt install openssh-server
sudo apt install openssh-clients
sudo ps -e |grep ssh //查看是否启动如果没有则启动ssh
sudo service ssh start 

sudo vi /etc/ssh/sshd_config
在行"#PermitRootLogin prohibit-password"后

添加行

"PermitRootLogin yes"

并保存。
输入命令"sudo update-rc.d ssh defaults"开启ssh服务开机自启动
输入命令"sudo service ssh start"启动服务

输入命令"sudo service ssh status"查看服务运行状态

"active(running)"说明服务正常

```

安装完毕，运行命令

```

sudo vi /etc/ssh/sshd_config
//在行"#PermitRootLogin prohibit-password"后添加行并保存。

PermitRootLogin yes

//输入命令开启ssh服务开机自启动
sudo update-rc.d ssh defaults
//输入命令启动服务
sudo service ssh start
//输入命令查看服务运行状态
sudo service ssh status

//"active(running)"说明服务正常
```

SSH服务启动成功后，默认开启**22（SSH的默认端口）端口号**，使用以下命令进行从查看：

```shell
netstat -tnulp
```

执行命令，可以看到**22号端口**已经开启，证明我们SSH服务启动成功：
![image.png](http://assets.qingjiaoclass.com/image/20200206/8PJXrFA1Ij1580965846.png)

只要将SSH服务启动成功，我们就可以**进行远程连接访问**了。

#### （2）**首先生成密钥对，使用命令：**

```shell
ssh-keygen
## 或者
ssh-keygen -t rsa
```

上面一种是简写形式，提示要输入信息时不需要输入任何东西，**直接回车三次即可**。

从打印信息中可以看出，私钥id_rsa和公钥id_rsa.pub都已创建成功，并放在 **/root/.ssh**（**隐藏文件夹（以.开头）**）目录中：

#### （3）将公钥放置到**授权列表文件 authorized_keys**中，使用命令：

```shell
cd /root/.ssh
cp id_rsa.pub authorized_keys
```

注意：一定要**授权列表文件 authorized_keys**写对，不能改名。

#### （4）修改授权列表文件 authorized_keys 的权限，使用命令：

```shell
chmod 600 authorized_keys
```

设置**拥有者可读可写**，其他人无任何权限（不可读、不可写、不可执行）。

#### （5）验证免密登录是否配置成功，使用如下命令：

```shell
ssh localhost  
## 或者
ssh e2d670ea9ad7
## 或者
ssh 10.141.0.42
```

- **localhost**：意为“本地主机”，指“这台计算机”
- **e2d670ea9ad7**：本机主机名，可以使用**hostname**命令进行查看
- **10.141.0.42**：本机IP地址，可以使用**ifconfig**命令进行查看

#### （6）远程登录成功后，若想退出，可以使用**exit**命令。

## 3、安装hadoop

### 1. 进入到/root/software目录，解压Hadoop；

```
cd /root/software
tar -zxvf hadoop-2.7.7.tar.gz -C /root/software
```

### 2. 配置Hadoop系统变量

#### (1) 首先打开/etc/profile文件（系统环境变量：对所有用户有效）：

```shell
vim /etc/profile或者

vim ~/.bashrc #建议用这种方式
```

#### (2) 在文件底部添加如下内容：

```shell
# 配置Hadoop的安装目录
export HADOOP_HOME=/root/software/hadoop-2.7.7
# 在原PATH的基础上加入Hadoop的bin和sbin目录
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

生效环境变量：

```
//上面用那种方式就用那种方式，第二种（亲测）
source /etc/profile

source ~/.bashrc
```



```
whereis hdfs
whereis start-all.sh
//显示出路径则为环境配置成功
```



## 4、配置HDFScor

### 配置HDFS

#### 1. 配置环境变量hadoop-env.sh

打开hadoop-env.sh文件：

```shell
vim /root/software/hadoop-2.7.7/etc/hadoop/hadoop-env.sh
```

找到JAVA_HOME参数位置，**修改为本机安装的JDK的实际位置**：



在命令模式下输入 `:set nu` 可以为vi设置行号。

#### 2. 配置核心组件core-site.xml

该文件是**Hadoop的核心配置文件**，其目的是**配置HDFS地址**、**端口号**，以及**临时文件目录**。使用如下命令**打开“core-site.xml”文件**：

```shell
vim /root/software/hadoop-2.7.7/etc/hadoop/core-site.xml
```

将下面的配置内容添加到 `<configuration></configuration>` 中间：

```xml
<!-- HDFS集群中NameNode的URI（包括协议、主机名称、端口号），默认为 file:/// -->
<property>
<name>fs.defaultFS</name>
<!-- 用于指定NameNode的地址 -->
<value>hdfs://localhost:9000</value>
</property>
<!-- Hadoop运行时产生文件的临时存储目录 -->
<property>
<name>hadoop.tmp.dir</name>
    <!-- 该路径为自己用户 -->
<value>/root/hadoopData/temp</value>
</property>
```

#### 3. 配置文件系统hdfs-site.xml

该文件主要用于**配置 HDFS 相关的属性**，例如**复制因子（即数据块的副本数）**、**NameNode 和 DataNode 用于存储数据的目录**等。在**完全分布式模式**下，默认数据块副本是**3 份**。 使用如下命令**打开“hdfs-site.xml”文件**：

```shell
vim /root/software/hadoop-2.7.7/etc/hadoop/hdfs-site.xml
```

将下面的配置内容添加到 `<configuration></configuration>` 中间：

```xml
<!-- NameNode在本地文件系统中持久存储命名空间和事务日志的路径 -->
<property>
<name>dfs.namenode.name.dir</name>
<value>/root/hadoopData/name</value>
</property>
<!-- DataNode在本地文件系统中存放块的路径 -->
<property>
<name>dfs.datanode.data.dir</name>
    <!-- 该路径为自己用户 -->
<value>/root/hadoopData/data</value>
</property>
<!-- 数据块副本的数量，默认为3 -->
<property>
<name>dfs.replication</name>
<value>1</value>
</property>
```

#### 4. 配置slaves文件（无需修改）

该文件用于**记录Hadoop集群所有从节点**（HDFS的DataNode*和*YARN的NodeManager所在主机）的主机名，用来配合**一键启动**脚本启动集群从节点（并且还需要保证关联节点配置了**SSH免密登录**）。

打开该配置文件：

```shell
vim /root/software/hadoop-2.7.7/etc/hadoop/slaves
```

我们看到其默认内容为**localhost**，因为我们搭建的是**伪分布式集群**，就只有一台主机，所以从节点也需要放在此主机上，所以**此配置文件无需修改**。

#### 5.格式化文件系统

```
hdfs namenode -format
```

#### 6. 脚本一键启动hdfs

启动集群**最常使用的方式**是使用脚本一键启动，前提是**需要配置 slaves 配置文件和 SSH免密登录**。

- **在本机上使用如下方式一键启动HDFS集群：**

```shell
start-dfs.sh
```

在本机上执行 `jps` 命令，在打印结果中会看到**4** 个进程，分别是 **NameNode**、**SecondaryNameNode**、**Jps**、和**DataNode**，如果出现了这 4 个进程表示HDFS启动成功。

## 5、安装yarn

Yarn主要配置文件说明如下：
![img](https://assets.qingjiaoclass.com/qyzpbaerkbax/20210414/bkqoojnq_bfZGTPFz1UaOe8TXCf0Q)

### 1. 配置环境变量yarn-env.sh

该文件是**YARN框架运行环境的配置**，同样需要**修改JDK所在位置**。

使用如下命令打开“yarn-env.sh”文件：

```shell
vim /root/software/hadoop-2.7.7/etc/hadoop/yarn-env.sh
```

找到JAVA_HOME参数位置，将前面的#去掉，将其值修改为本机安装的JDK的实际位置：
![image.png](http://assets.qingjiaoclass.com/image/20200207/Zn1Xo7erlh1581045238.png)

在命令模式下输入 `:set nu` 可以为vi设置行号。

### 2. 配置计算框架mapred-site.xml

在$HADOOP_HOME/etc/hadoop/目录中默认没有该文件，需要先通过如下命令将文件**复制并重命名为“mapred-site.xml”**：

```shell
cp mapred-site.xml.template mapred-site.xml
```

接着，打开“mapred-site.xml”文件进行修改：

```shell
vim /root/software/hadoop-2.7.7/etc/hadoop/mapred-site.xml
```

将下面的配置内容添加到 中间：

```xml
<!-- 指定使用 YARN 运行 MapReduce 程序，默认为 local -->
<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
</property>
```

效果如下图所示：
![image.png](http://assets.qingjiaoclass.com/image/20200207/B6gnrtwbWK1581045450.png)

### 3. 配置YARN系统yarn-site.xml

本文件是**YARN框架的核心配置文件**，用于**配置 YARN 进程及 YARN 的相关属性**。

使用如下命令打开该配置文件：

```shell
vim /root/software/hadoop-2.7.7/etc/hadoop/yarn-site.xml
```

将下面的配置内容加入中间：

```xml
<!-- NodeManager上运行的附属服务，也可以理解为 mapreduce 获取数据的方式 -->
<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>
```

### 4. 启动集群

在本机上使用如下方式**一键启动YARN集群**：

```shell
start-yarn.sh
```

ps：start-dfs.sh和start-yarn.sh也是sbin目录下的脚本文件。

**效果图如下所示：**

**打印信息：**

- 在本机上启动了 ResourceManager守护进程
- 在本机上启动了 NodeManager 守护进程

通过本机的浏览器访问**http://localhost:8088**或**http://本机IP地址:8088**查看YARN集群状态，效果如下图所示：

## 测试hadoop

```
创建一个data.txt用wordcount测试
hadoop@hadoop:~$ vi data.txt
创建input文件加夹
hadoop@hadoop:~$ hdfs dfs -mkdir /input

```

hdfs命令

``` 
hdfs dfs -linux命令 /input或者/output
```

上传data.txt

```
hadoop@hadoop:~$ hdfs dfs -put data.txt /input

```

查看上传是否成功

```
hadoop@hadoop:~$ hdfs dfs -ls /input
Found 1 items
-rw-r--r--   1 hadoop supergroup         25 2021-04-30 23:47 /input/data.txt

```

运行wordCount程序

```
hadoop@hadoop:~$ cd ~/software/hadoop-2.7.7/share/hadoop/mapreduce
hadoop@hadoop:~/software/hadoop-2.7.7/share/hadoop/mapreduce$ hadoop jar hadoop-mapreduce-examples-2.7.7.jar wordcount /input/data.txt /output
```

查看output文件夹

```
hdfs dfs -ls /output
Found 2 items
-rw-r--r--   1 hadoop supergroup          0 2021-04-30 23:56 /output/_SUCCESS
-rw-r--r--   1 hadoop supergroup         25 

```

查看运行结果

```
2021-04-30 23:56 /output/part-r-00000
hadoop@hadoop:~/software/hadoop-2.7.7/share/hadoop/mapreduce$ hdfs dfs -cat /output/part-r-00000
hadoop	1
hello	2
world	1
```

