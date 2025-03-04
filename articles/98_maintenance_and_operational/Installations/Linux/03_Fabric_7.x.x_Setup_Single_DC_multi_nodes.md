# Fabric V7.xx + TDM 7.xx Installation 1 DC  Multi Nodes

Fabric 7.x.x requires JDK 17 (included in the Fabric installation package). However, the certified version of Cassandra and Kafka require JDK 8.* package (latest tested version included with each supplied package).


## Pre-Installation Steps

Click [here](01_Fabric_7.xx_Installation_intro.md) to get the full list of activities that should be performed prior to either Fabric or TDM installation. Note that this is mandatory and should be performed at least once per each server.

## Setup for Cassandra

### Load The Package 

1. Retrieve the latest Cassandra package (located at: [here](https://download.k2view.com/index.php/s/dMH2PWuIErPFszK)).

2. Log in to the Linux server as the user "Cassandra" and copy the package to the home directory.

3. Untar the package (the package name varies according to the version) as follows:

   ~~~bash
   tar -zxvf k2v_cassandra-3.11.xxx.tar.gz
   ~~~

4. Updated the .bash_propile to use python 2.7.

   ~~~bash
   sed -i '11i\alias python='/usr/bin/python2.7'\' ~/.bash_profile
   source ./.bash_profile
   # verified the Python version 
   python --version
   ~~~


### Set up the first 3 Cassandra nodes

Run the commands as shown below for each node in turn. When doing so, update the parameters that are unique to your project. 

1.  Run the pre setup commands.

2.  Start Cassandra.

3.  Run the post setup commands.

**Pre setup run on the 3 node**, update the following as needed:

- `dc=`
- `cluster_name=`
- `seeds:` - should be the IP of the first Cassandra node that will be started 

~~~bash
sed -i "s@INSTALL_DIR=.*@INSTALL_DIR=$(pwd)@" .bash_profile
sed -i 's@dc=.*@dc=DC1@'  $INSTALL_DIR/cassandra/conf/cassandra-rackdc.properties
sed -i 's@cluster_name: .*@cluster_name: 'PreProd1'@'  $INSTALL_DIR/cassandra/conf/cassandra.yaml
sed -i s/seeds:.*/"seeds: \"172.31.47.245\""/g $INSTALL_DIR/cassandra/conf/cassandra.yaml
sed -i s/listen_address:.*/"listen_address: $(hostname -I |awk {'print $1'})"/g $INSTALL_DIR/cassandra/conf/cassandra.yaml
sed -i s/broadcast_rpc_address:.*/"broadcast_rpc_address: $(hostname -I |awk {'print $1'})"/g $INSTALL_DIR/cassandra/conf/cassandra.yaml
sed -i 's@endpoint_snitch:.*@endpoint_snitch: GossipingPropertyFileSnitch@'  $INSTALL_DIR/cassandra/conf/cassandra.yaml
sed -i 's@LOCAL_JMX=.*@LOCAL_JMX='no'@'  $INSTALL_DIR/cassandra/conf/cassandra-env.sh
sed -i "s@-Djava.rmi.server.hostname=.*@-Djava.rmi.server.hostname=$(hostname -I |awk {'print $1'})\"@"  $INSTALL_DIR/cassandra/conf/cassandra-env.sh
sed -i "s@-Dcom.sun.management.jmxremote.password.file=.*@-Dcom.sun.management.jmxremote.password.file=$INSTALL_DIR/cassandra/conf/.jmxremote.password\"@" $INSTALL_DIR/cassandra/conf/cassandra-env.sh
sed -i "s@num_tokens:.*@num_tokens: 16@" $INSTALL_DIR/cassandra/conf/cassandra.yaml

~~~

If the Cassandra node has 64G RAM, run the following:

~~~bash
sed -i 's@#-Xmx4G@-Xmx18G@' $INSTALL_DIR/cassandra/conf/jvm.options
sed -i 's@#-Xms4G@-Xms18G@' $INSTALL_DIR/cassandra/conf/jvm.options
~~~



#### Start Cassandra 

Start Cassandra on all 3 nodes:

~~~bash
cassandra
~~~

#### Post Setup Run on One Node

Create a new superuser for Cassandra, and change the **cassandra** default user password, update the RF.

Update the `DC1` to the DC name you used above:

~~~bash
echo "create user k2admin with password 'Q1w2e3r4t5' superuser;" |cqlsh -u cassandra -p cassandra
echo "ALTER KEYSPACE system_auth WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'DC1': '3'};" | cqlsh -ucassandra -pcassandra
echo "ALTER KEYSPACE system_distributed WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'DC1': '3'};" | cqlsh -ucassandra -pcassandra
echo "ALTER KEYSPACE system_traces WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'DC1': '3'};" | cqlsh -ucassandra -pcassandra
echo "CREATE KEYSPACE keyspace_with_replication_factor_3 WITH replication = {'class': 'NetworkTopologyStrategy', 'DC1': 3} AND durable_writes = true;"|cqlsh -u cassandra -p cassandra

echo "ALTER user cassandra with PASSWORD 'ZBU3Ld35NvXU3qud' superuser;" |cqlsh -u k2admin -p 'Q1w2e3r4t5'
~~~

Run **nodetool repair** command on all the 3 nodes:

~~~bash
nodetool -u k2view -pw Q1w2e3r4t5 repair
~~~

**Note**: If you decide to change the password in the above setting, you would be required to run the following commands, when setting up the Fabric server. We refer to the following SED lines:


~~~bash
sed -i "s@#PASSWORD=.*@PASSWORD=Q1w2e3r4t5@" $K2_HOME/config/config.ini
sed -i "s@#PASSWORD=.*@PASSWORD=Q1w2e3r4t5@" $K2_HOME/config/iifConfig.ini
~~~

### Set up Cassandra Nodes 4 and Above

**Pre setup run on the 3 node**, update the following as needed:

- `dc=`
- `cluster_name=`
- `seeds:` should be the IP of the first Cassandra node that will be started. (replace the IP 172.31.47.245 from the following)

~~~bash
sed -i 's@dc=.*@dc=DC1@'  $INSTALL_DIR/cassandra/conf/cassandra-rackdc.properties
bash -l
sed -i 's@cluster_name: .*@cluster_name: 'PreProd1'@'  $INSTALL_DIR/cassandra/conf/cassandra.yaml
sed -i s/seeds:.*/"seeds: \"172.31.47.245\""/g $INSTALL_DIR/cassandra/conf/cassandra.yaml
sed -i s/listen_address:.*/"listen_address: $(hostname -I |awk {'print $1'})"/g $INSTALL_DIR/cassandra/conf/cassandra.yaml
sed -i s/broadcast_rpc_address:.*/"broadcast_rpc_address: $(hostname -I |awk {'print $1'})"/g $INSTALL_DIR/cassandra/conf/cassandra.yaml
sed -i 's@endpoint_snitch:.*@endpoint_snitch: GossipingPropertyFileSnitch@'  $INSTALL_DIR/cassandra/conf/cassandra.yaml
sed -i 's@LOCAL_JMX=.*@LOCAL_JMX='no'@'  $INSTALL_DIR/cassandra/conf/cassandra-env.sh
sed -i "s@-Djava.rmi.server.hostname=.*@-Djava.rmi.server.hostname=$(hostname -I |awk {'print $1'})\"@"  $INSTALL_DIR/cassandra/conf/cassandra-env.sh
sed -i "s@-Dcom.sun.management.jmxremote.password.file=.*@-Dcom.sun.management.jmxremote.password.file=$INSTALL_DIR/cassandra/conf/.jmxremote.password\"@" $INSTALL_DIR/cassandra/conf/cassandra-env.sh
sed -i "s@num_tokens:.*@num_tokens: 16@" $INSTALL_DIR/cassandra/conf/cassandra.yaml
sed -i "s@# allocate_tokens_for_keyspace:.*@allocate_tokens_for_keyspace: keyspace_with_replication_factor_3@" $INSTALL_DIR/cassandra/conf/cassandra.yaml
~~~

If the Cassandra node has 64G RAM, run the following:

~~~bash
sed -i 's@#-Xmx4G@-Xmx18G@' $INSTALL_DIR/cassandra/conf/jvm.options
sed -i 's@#-Xms4G@-Xms18G@' $INSTALL_DIR/cassandra/conf/jvm.options
~~~

#### Start Cassandra 

~~~bash
cassandra
~~~



## Set up Kafka Cluster

### Load the Package 

1. Download the package from: [here](https://download.k2view.com/index.php/s/tFnDRJEUyHiXPYL).

2. Log in to the Linux as the user "Kafka" and copy the package to the home directory.

3. Untar the package (the package name varies according to the version) as follows:

   ~~~bash
   tar -zxvf k2view_Confluent_5.xxx.tar.gz && bash -l
   ~~~

4. Pre setup:

   - Updade the IP's of `kserver1` , `kserver2` , `kserver3` 

   ~~~bash
   export kserver1=172.31.11.198
   export kserver2=172.31.35.204
   export kserver3=172.31.31.69

   if [ "$(hostname -I |awk {'print $1'})" == "$kserver1" ]; then echo 1 > $K2_HOME/zk_data/myid; fi
   if [ "$(hostname -I |awk {'print $1'})" == "$kserver2" ]; then echo 2 > $K2_HOME/zk_data/myid; fi
   if [ "$(hostname -I |awk {'print $1'})" == "$kserver3" ]; then echo 3 > $K2_HOME/zk_data/myid; fi
   if [ "$(hostname -I |awk {'print $1'})" == "$kserver1" ]; then sed -i "s@broker.id=.@broker.id=1@" $CONFLUENT_HOME/server.properties ; fi
   if [ "$(hostname -I |awk {'print $1'})" == "$kserver2" ]; then sed -i "s@broker.id=.@broker.id=2@" $CONFLUENT_HOME/server.properties ; fi
   if [ "$(hostname -I |awk {'print $1'})" == "$kserver3" ]; then sed -i "s@broker.id=.@broker.id=3@" $CONFLUENT_HOME/server.properties ; fi

   sed -i "s@log.retention.minutes=.*@log.retention.hours=48@" $CONFLUENT_HOME/server.properties
   sed -i "s@advertised.listeners=.*@advertised.listeners=PLAINTEXT:\/\/$(hostname -I |awk {'print $1'}):9093@" $CONFLUENT_HOME/server.properties
   sed -i "s@advertised.host.name=.*@advertised.host.name=PLAINTEXT:\/\/$(hostname -I |awk {'print $1'}):9093@" $CONFLUENT_HOME/server.properties
   sed -i "s@listeners=PLAINTEXT:\/\/.*@listeners=PLAINTEXT:\/\/$(hostname -I |awk {'print $1'}):9093@" $CONFLUENT_HOME/server.properties
   sed -i "s@zookeeper.connect=.*.@zookeeper.connect=$kserver1:2181,$kserver2:2181,$kserver3:2181@" $CONFLUENT_HOME/server.properties
   echo "default.replication.factor=3" >> $CONFLUENT_HOME/server.properties
   sed -i "s@log.dirs=.*@log.dirs=$K2_HOME/zk_data@" $CONFLUENT_HOME/server.properties
   sed -i "s@dataDir=.*@dataDir=$K2_HOME/zk_data@" $CONFLUENT_HOME/zookeeper.properties
   sed -i "s@server.1=.*@#server.1=$(hostname -I |awk {'print $1'}):2888:3888@" $CONFLUENT_HOME/zookeeper.properties
   echo "server.1=$kserver1:2888:3888" >> $CONFLUENT_HOME/zookeeper.properties
   echo "server.2=$kserver2:2888:3888" >> $CONFLUENT_HOME/zookeeper.properties
   echo "server.3=$kserver3:2888:3888" >> $CONFLUENT_HOME/zookeeper.properties

   ~~~

5. Start Zookeeper on all 3 nodes:

   ~~~bash
   $K2_HOME/kafka/bin/zookeeper-server-start -daemon $K2_HOME/kafka/zookeeper.properties
   ~~~

6. Start Kafka on all 3 nodes:

   ~~~bash
   $K2_HOME/kafka/bin/kafka-server-start -daemon $K2_HOME/kafka/server.properties
   ~~~

7. Verify that the Kafka and Zookeeper are running:

   ~~~bash
   $CONFLUENT_HOME/bin/zookeeper-shell localhost:2181 <<< "ls /brokers/ids"
   ~~~




## Setup for Fabric & TDM 7

### Load the Package 

1. Download the package from the links that were provided to you.

2. Untar the package:

   ~~~bash
   tar -zxvf [package name].tar.gz 
   ~~~

3. Pre setup:

   ~~~bash
   sed -i "s@K2_HOME=.*@K2_HOME=$(pwd)@" .bash_profile
   bash -l 
   ~~~

   - Update the IP's for: `cserver1` / `cserver2` / `cserver3` ....   and  `kserver1` / `kserver2` / `kserver3`  

   ~~~bash
   ## update the cassandra IP's
   export cserver1=192.168.168.212
   export cserver2=192.168.168.213
   export cserver3=192.168.168.214
   
   ## update the fafka IP's 
   export kserver1=172.31.11.198
   export kserver2=172.31.35.204
   export kserver3=172.31.31.69
   
   cp -r $K2_HOME/fabric/config.template $K2_HOME/config
   
   sed -i 's@-Xmx2G@-Xmx8G@' $INSTALL_DIR/config/jvm.options
   sed -i 's@-Xms2G@-Xms8G@' $INSTALL_DIR/config/jvm.options
   
   sed -i 's@#REPLICATION_OPTIONS=.*@REPLICATION_OPTIONS={ '"'"'class'"'"' : '"'"'NetworkTopologyStrategy'"'"', '"'"DC1"'"' : 3}@' $K2_HOME/config/config.ini
   sed -i "s@#HOSTS=.*@HOSTS=$cserver1,$cserver2,$cserver3@" $K2_HOME/config/config.ini
   sed -i "s@#USER=.*@USER=k2admin@" $K2_HOME/config/config.ini
   sed -i "s@#PASSWORD=.*@PASSWORD=Q1w2e3r4t5@" $K2_HOME/config/config.ini
   sed -i "s@#MESSAGES_BROKER_TYPE=.*@MESSAGES_BROKER_TYPE=KAFKA@" $K2_HOME/config/config.ini
   sed -i "s@#BOOTSTRAP_SERVERS=.*@BOOTSTRAP_SERVERS=$kserver1:9093,$kserver2:9093,$kserver3:9093@" $K2_HOME/config/config.ini
   sed -i "s@HOSTS=.*@HOSTS=$cserver1,$cserver2,$cserver3@" $K2_HOME/config/iifConfig.ini
   sed -i "s@#USER=.*@USER=k2admin@" $K2_HOME/config/iifConfig.ini
   sed -i "s@#PASSWORD=.*@PASSWORD=Q1w2e3r4t5@" $K2_HOME/config/iifConfig.ini
   sed -i "s@#KAFKA_BOOTSTRAP_SERVERS=.*@KAFKA_BOOTSTRAP_SERVERS=$kserver1:9093,$kserver2:9093,$kserver3:9093@" $K2_HOME/config/iifConfig.ini
   sed -i "s@#ZOOKEEPER_BOOTSTRAP_SERVERS=.*@ZOOKEEPER_BOOTSTRAP_SERVERS=$kserver1:2181,$kserver2:2181,$kserver3:2181@" $K2_HOME/config/iifConfig.ini
   sed -i 's@#IIF_REPLICATION_OPTIONS=.*@IIF_REPLICATION_OPTIONS={ '"'"'class'"'"' : '"'"'NetworkTopologyStrategy'"'"', '"'"DC1"'"' : 3}@' $K2_HOME/config/iifConfig.ini
   sed -i "s@#BOOTSTRAP_SERVERS=.*@BOOTSTRAP_SERVERS=$kserver1:9093,$kserver2:9093,$kserver3:9093@" $K2_HOME/config/iifConfig.ini
   ~~~
   
      :notebook_with_decorative_cover: **Note** that the Fabric Admin user is defined now as **k2consoleadmin**.


4. Start Fabric:

   - Run the following on the first node of Fabric (you can start other Fabric nodes only after it is ready):

     ~~~bash
     cp config/adminInitialCredentials.template config/adminInitialCredentials
     sed -i 's@user.*@k2consoleadmin/KW4RVG98RR9xcrTv@' config/adminInitialCredentials
     k2fabric start && k2fabric status
     ~~~
   
   
      - Start the other Fabric nodes: 
   
        ~~~bash
        k2fabric start && k2fabric status
        ~~~
   


5. Connect to the Fabric console using the following command:

   ~~~bash
   fabric -u k2consoleadmin -p KW4RVG98RR9xcrTv
   ~~~

   - The same login details should be used to log in to the Web Framework.



## PGSQL 

TDM 7.xx is certified with PGSQL 9.6 & 13. You can provide access to the existing PostgreSQL DB if you have one.
TDM requires login details with full **create**, **delete** and **update** privileges. 

The customer can provide the **PGSQL**, or find below the installation instructions for **K2View** **PGSQL**:

<ul>      
<li>
<a href="/articles/98_maintenance_and_operational/Installations/Linux/PGSQL_setup.md">Setup PGSQL 13.3</a></li>

