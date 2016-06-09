Setting up Hadoop can be an arduous process.

This is what worked for Tuhin.


#### Edit /etc/hosts

```
192.168.10.102 hadoop-master
192.168.10.103 hadoop-slave-1
```

You may need to reserve IPs on the router


#### Create user hadoop
```
useradd hadoop
passwd hadoop
```


#### Set up key-based (passwordless) login:

```
su hadoop
ssh-keygen -t rsa
ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@hadoop-master
ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@hadoop-slave-1
chmod 0600 ~/.ssh/authorized_keys
```


#### Install Oracle JDK

Download latest Oracle JDK and save it in the /opt directory.

On hadoop-master, expand Java:

```
cd /opt
tar -zxf jdk-8u91-linux-x64.tar.gz
mv jdk1.8.0_91 jdk
```


#### Propagate /opt/jdk to all the slaves
```
scp -r jdk hadoop-slave-1:/opt
```


#### Use the alternatives tool

```
alternatives --install /usr/bin/java java /opt/jdk/bin/java 2
alternatives --config java # select appropriate program (/opt/jdk/bin/java)
alternatives --install /usr/bin/jar jar /opt/jdk/bin/jar 2
alternatives --install /usr/bin/javac javac /opt/jdk/bin/javac 2
alternatives --set jar /opt/jdk/bin/jar
alternatives --set javac /opt/jdk/bin/javac
```	


#### Edit /etc/bashrc
```
export JAVA_HOME=/opt/jdk
export JRE_HOME=/opt/jdk/jre
export PATH=$PATH:/opt/jdk/bin:/opt/jdk/jre/bin
```


#### Install Hadoop 2.7.2

On hadoop-master:
```
cd /opt
wget http://www.eu.apache.org/dist/hadoop/common/hadoop-2.7.2/hadoop-2.7.2.tar.gz
tar -zxf hadoop-2.7.2.tar.gz
rm hadoop-2.7.2.tar.gz
mv hadoop-2.7.2 hadoop
```


#### Propagate /opt/hadoop to slave nodes

```
scp -r hadoop hadoop-slave-1:/opt
```


#### Set the following environment variables to /home/hadoop/.bashrc on every node

```
export HADOOP_PREFIX=/opt/hadoop
export HADOOP_HOME=$HADOOP_PREFIX
export HADOOP_COMMON_HOME=$HADOOP_PREFIX
export HADOOP_CONF_DIR=$HADOOP_PREFIX/etc/hadoop
export HADOOP_HDFS_HOME=$HADOOP_PREFIX
export HADOOP_MAPRED_HOME=$HADOOP_PREFIX
export HADOOP_YARN_HOME=$HADOOP_PREFIX
export PATH=$PATH:$HADOOP_PREFIX/sbin:$HADOOP_PREFIX/bin
```


#### Configure /opt/hadoop/etc/hadoop/core-site.xml – (NameNode URI) on all nodes

```
<configuration>
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://hadoop-master:9000/</value>
</property>
</configuration>
```


#### Create HDFS DataNode data folders on every node and make hadoop the owner of /opt/hadoop

```
chown hadoop /opt/hadoop/ -R
chgrp hadoop /opt/hadoop/ -R
mkdir /home/hadoop/datanode
chown hadoop /home/hadoop/datanode/
chgrp hadoop /home/hadoop/datanode/    
```


#### Configure /opt/hadoop/etc/hadoop/hdfs-site.xml  (DataNodes)

```
<configuration>
<property>
  <name>dfs.replication</name>
  <value>3</value>
</property>
<property>
  <name>dfs.permissions</name>
  <value>false</value>
</property>
<property>
   <name>dfs.datanode.data.dir</name>
   <value>/home/hadoop/datanode</value>
</property>
</configuration>
```


#### Create HDFS NameNode data holder on hadoop-master and make hadoop the owner and group

```
mkdir /home/hadoop/namenode
chown hadoop /home/hadoop/namenode/
chgrp hadoop /home/hadoop/namenode/    
```


#### Configure /opt/hadoop/etc/hadoop/hdfs-site.xml on hadoop-master.

```
<property>
        <name>dfs.namenode.data.dir</name>
        <value>/home/hadoop/namenode</value>
</property>
```


#### Configure /opt/hadoop/etc/hadoop/mapred-site.xml on hadoop-master.

```
<configuration>
 <property>
  <name>mapreduce.framework.name</name>
   <value>yarn</value>
 </property>
</configuration>
```


#### Configure /opt/hadoop/etc/hadoop/yarn-site.xml (ResourceManager and NodeManagers):

```
<configuration>
<property>
        <name>yarn.resourcemanager.hostname</name>
        <value>hadoop-master</value>
</property>
<property>
        <name>yarn.nodemanager.hostname</name>
        <value>hadoop-slave-1</value>
</property>
<property>
  <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>
</configuration>
```


#### Configure /opt/hadoop/etc/hadoop/slaves on master

```
hadoop-master
hadoop-slave-1
```


#### Disable firewall and IPv6

```
systemctl stop firewalld
```

Add to /etc/sysctl.conf:

```
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
```


#### Format NameNode

```
su hadoop
hdfs namenode -format
```


#### Start HDFS (as user hadoop):

```
start-dfs.sh
```


#### Start YARN on hadoop-master:

```
start-yarn.sh 
```


Now jps should show the NodeManagers on all nodes and one ResourceManager on hadooop-master.


hadoop-master node consists of a ResourceManager, NodeManager (YARN), NameNode and DataNode (HDFS). 

hadoop-slave1 consists of NodeManager and a DataNode.


### Test

```
hdfs dfsadmin -safemode leave # ?????? -- Turn off safe mode
hdfs dfs -mkdir /input                       -- Create an input folder
hdfs dfs -copyFromLocal test.txt /input   -- Create test.txt first and then issue the command. You can put whatever you want in test.txt. This copies the file in hdfs.
hdfs dfs -cat /input/test.txt | head       -- Show the contents of test.txt from hdfs
hadoop jar /opt/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.0.jar wordcount /input/test.txt /output1 -- Run the actual test.
```