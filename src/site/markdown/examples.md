<!---
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at
  
   http://www.apache.org/licenses/LICENSE-2.0
  
  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->
  
# Examples

 
## Setup
 
### Setting up a YARN cluster
 
For simple local demos, a Hadoop pseudo-distributed cluster will suffice -if on a VM then
its configuration should be changed to use a public (machine public) IP.

# The examples below all assume there is a cluster node called 'master', which
hosts the HDFS NameNode and the YARN Resource Manager


# preamble

    export HADOOP_CONF_DIR=/home/hoya/conf
    export PATH=/home/hoya/hadoop/bin:/home/hoya/hadoop/sbin:~/zookeeper-3.4.5/bin:$PATH
    
    hdfs namenode -format master
  



# start all the services

    hadoop-daemon.sh --config $HADOOP_CONF_DIR --script hdfs start namenode
    hadoop-daemon.sh --config $HADOOP_CONF_DIR --script hdfs start datanode
    
    yarn-daemon.sh --config $HADOOP_CONF_DIR start resourcemanager
    yarn-daemon.sh --config $HADOOP_CONF_DIR start nodemanager
    
    ~/zookeeper-3.4.5/bin/zkServer.sh start
    
    
# stop them

    hadoop-daemon.sh --config $HADOOP_CONF_DIR --script hdfs stop namenode
    hadoop-daemon.sh --config $HADOOP_CONF_DIR --script hdfs stop datanode
    
    yarn-daemon.sh --config $HADOOP_CONF_DIR stop resourcemanager
    yarn-daemon.sh --config $HADOOP_CONF_DIR stop nodemanager
    


NN up on [http://master:50070/dfshealth.jsp](http://master:50070/dfshealth.jsp)
RM yarn-daemon.sh --config $HADOOP_CONF_DIR start nodemanager

    zookeeper-3.4.5/bin/zkServer.sh start


    # shutdown
    ./zookeeper-3.4.5/bin/zkServer.sh stop


Tip: after a successful run on a local cluster, do a quick `rm -rf $HADOOP_HOME/logs`
to keep the log bloat under control.

## get hbase in

copy to local 

    get hbase-0.97.0-SNAPSHOT-bin.tar on 


    hdfs dfs -rm hdfs://master:9090/hbase.tar
    hdfs dfs -copyFromLocal hbase-0.97.0-SNAPSHOT-bin.tar hdfs://master:9090/hbase.tar
    hdfs dfs -ls hdfs://master:9090/
    

## Clean up any existing hoya cluster details

This is for demos only, otherwise you lose the clusters and their databases.

    hdfs dfs -rm -r hdfs://master:9090/user/home/stevel/.hoya

## Create a Hoya Cluster
 
 
    hoya  create cl1 \
    --role workers 1 \
     --manager master:8032 --filesystem hdfs://master:9090 \
     --zkhosts localhost --image hdfs://master:9090/hbase.tar
    
    # create the cluster
    
    hoya create cl1 \
     --role workers 4\
      --manager master:8032 --filesystem hdfs://master:9090 --zkhosts localhost \
      --image hdfs://master:9090/hbase.tar \
      --appconf file:////Users/hoya/Hadoop/configs/master/hbase \
      --roleopt master app.infoport 8080 \
      --roleopt master jvm.heap 128 \
      --roleopt master env.MALLOC_ARENA_MAX 4 \
      --roleopt worker app.infoport 8081 \
      --roleopt worker jvm.heap 128 

    # freeze the cluster
    hoya freeze cl1 \
    --manager master:8032 --filesystem hdfs://master:9090

    # thaw a cluster
    hoya thaw cl1 \
    --manager master:8032 --filesystem hdfs://master:9090

    # destroy the cluster
    hoya destroy cl1 \
    --manager master:8032 --filesystem hdfs://master:9090

    # list clusters
    hoya list cl1 \
    --manager master:8032 --filesystem hdfs://master:9090
    
    hoya flex cl1 \
    --manager master:8032 --filesystem hdfs://master:9090 \
    --role worker 5
    
    
      