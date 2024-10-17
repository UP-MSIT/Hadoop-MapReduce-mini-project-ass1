# Hadoop-MapReduce-mini-project Cluster Configuration
Setup Apache Hadoop Cluster and implement a mini project which reads, processes and analyzes data on HDFS using MapReduce

## Prerequisite 
- 2 ubuntu VMs machines running namenode and datanode 
- Hadoop Installation
- Java Installation
- Hadoop cluster configuration 
- Python (Optional)

**Common Installation**

> If not yet install, ping, netstate, CLI

    sudo apt-get update -y
    sudo apt-get install -y iputils-ping
    sudo apt install net-tools
    sudo apt-get install vim


**install java 8**

    sudo apt-get install openjdk-8-jdk
**SSH** 

    ssh localhost
    ssh-keygen -t rsa -b 4096
    cat /home/parallels/.ssh/id_rsa.pub

**Download & install hadoop**

    wget -O hadoop.tar.gz https://dlcdn.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
    tar -xzf hadoop.tar.gz 

**Config Hadoop**

    mv hadoop-3.3.6 hadoop
    sudo mv hadoop /usr/local/
    cd /usr/local/hadoop/etc/hadoop
    
    vi core-site.xml 
    vi hdfs-site.xml


**Add cli path**

    vi ~/.bashrc
        
    export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-arm64
    export HADOOP_HOME=/usr/local/hadoop
    export PATH=$PATH:$HADOOP_HOME/bin/$HADOOP_HOME/sbin

**Config JAVA_HOME in hadoop sh script**

    cd /usr/local/hadoop/etc/hadoop
    vi hadoop-env.sh 
    export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-arm64


**Start, stop dfs service**

    start-dfs.sh
    stop-dfs.sh

**Start, stop Yarn service**

    start-yarn.sh
    stop-yarn.sh

**Give hadoop home directory permission**

    hdfs dfs -chmod 777 /

**Config clusters**

    sudo vi /etc/hosts
    
    127.0.0.1 localhost
    10.211.55.8 namenode
    10.211.55.9 datanode1

**- On NameNode VM:**

    sudo hostnamectl set-hostname namenode

**- On DataNode1 VM:**

    sudo hostnamectl set-hostname datanode1

**- Verify the Setup try to ping hostname to check if they can communicate:**

    ping namenode
    ping datanode1


**- Configure core-site.xml (on DataNode)**
Modify the file located at $HADOOP_HOME/etc/hadoop/core-site.xml

    <configuration>
      <property>
        <name>fs.defaultFS</name>
        <value>hdfs://namenode:9000</value>
      </property>
    </configuration>

**- Configure hdfs-site.xml (on DataNode)**
Update the file $HADOOP_HOME/etc/hadoop/hdfs-site.xml

    <configuration>
      <!-- Replication factor can be set here -->
      <property>
        <name>dfs.replication</name>
        <value>3</value> <!-- or any appropriate number -->
      </property>
    
      <property>
        <name>dfs.datanode.data.dir</name>
        <value>/usr/local/hadoop/hdfs/data</value>
      </property>
    </configuration>

> *Ensure that the data directory (/usr/local/hadoop/hdfs/data) exists:*

    mkdir -p /usr/local/hadoop/hdfs/data

**- Configure mapred-site.xml (on DataNode)**
If you’re using MapReduce, update the $HADOOP_HOME/etc/hadoop/mapred-site.xml:

    <configuration>
      <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
      </property>
    </configuration>


**- Configure yarn-site.xml (on DataNode)**
Update the $HADOOP_HOME/etc/hadoop/yarn-site.xml file:

    <configuration>
      <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>namenode</value>
      </property>
    
      <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
      </property>
    </configuration>

**- Start DataNode and NodeManager**
On each DataNode server, start the necessary Hadoop services:

**- Start the HDFS DataNode:**

    hadoop-daemon.sh start datanode

**- Start the YARN NodeManager:**

    yarn-daemon.sh start nodemanager

**- Start the Hadoop Services on the NameNode**
Ensure the NameNode and ResourceManager are running on the NameNode server:

**- Start the HDFS NameNode:**

    hadoop-daemon.sh start namenode
    
    or
    
    hdfs --daemon start namenode

**- Start the YARN ResourceManager:**

    yarn-daemon.sh start resourcemanager
    
    or
    
    yarn --daemon start resourcemanager

**- Check the Cluster Status**

    hdfs dfsadmin -report

>Format the HDFS (only on NameNode)

    hdfs namenode -format

**- list dir in hadoop directory**

    hdfs dfs -ls  /

**- Example: Upload a text file to HDFS**

    echo "any text" > test.txt
    hdfs dfs -mkdir -p /user/hadoop/input
    hdfs dfs -put /path/to/local/inputfile.txt /user/hadoop/input/
    hdfs dfs -put test.txt /user/hadoop/input/

**- Run the Default Hadoop WordCount Example**
MapReduce programs (like WordCount) located in the share/hadoop/mapreduce/ directory.

To run the WordCount example using Java, use this command:

    hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar wordcount /user/hadoop/input /user/hadoop/output

**- Check the Output: Once the job is complete,**

    hdfs dfs -cat /user/hadoop/output/part-r-00000

**- Remove the Existing Output Directory**

    hdfs dfs -rm -r /user/hadoop/output

**- Re-run the MapReduce job:**

    hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar wordcount /user/hadoop/input /user/hadoop/output

























