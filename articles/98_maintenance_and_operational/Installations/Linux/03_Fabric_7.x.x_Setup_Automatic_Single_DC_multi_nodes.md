# Fabric V7.x.x + TDM 7.xx Installation 1 DC  Multi Nodes

Fabric V7.x.x requires JDK 17 (included in the Fabric installation package). However, the certified versions of Cassandra and Kafka require JDK 8.* package (the latest tested version is included with each supplied package).


## Pre-Installation Steps

Click [here](01_Fabric_7.xx_Installation_intro.md) to get the full list of activities to be performed prior to the installation of either Fabric or TDM. Note that this is mandatory and should be performed at least once per each server.

## Setup for Cassandra

### Load The Package 

1. Retrieve the latest Cassandra package (located [here](https://download.k2view.com/index.php/s/dMH2PWuIErPFszK)).

2. Log in to the Linux server as a 'Cassandra' user and copy the package to the home directory.

3. Untar the package (the package name varies according to the version) as follows:

   ~~~bash
   tar -zxvf k2v_cassandra-4.x.xxx.tar.gz && source ~/.bash_profile
   ~~~

4. Verify that Python version 2.7 is being used

   ~~~bash
   python --version
   ~~~


### Set up the first Cassandra nodes

The number of seed nodes should match the chosen replication factor number.
The script should be run separately on each node, in the order of designated node numbers. It should not be run simultaneously as this may cause configuration issues.

**Mandatory required details:**
* Seed node IPs
* Cassandra admin user
* Cassandra admin password
* Replication factor number

**Optional required details:**
* DataCenter name – unless defined, will be set as DC1
* Cluster name – unless defined, will be set as Cassandra
* Hardening and SSl configuration, TBD 

>Assuming you prepare to run the Cassandra with:
>* Replication factor 3 
>* User ‘k2admin’ 
>* Password ‘password’ 
>* Seed IPs 10.0.0.1, 10.0.0.2, 10.0.0.3


1. 	Run the following command: (start with the 1st node, then, one by one on the 2nd and 3rd:
~~~bash
./Cassandra_init -i 10.0.0.1,10.0.0.2,10.0.0.3 -u k2admin -p password -r 3
~~~

2.  Once all seed nodes are up and running, run the same command on all the rest of the Cassandra nodes (one by one).
~~~bash
./Cassandra_init -i 10.0.0.1,10.0.0.2,10.0.0.3 -u k2admin -p password -r 3
~~~


## Set Up Kafka Cluster

### Load the Package 

1. Download the package from [here](https://download.k2view.com/index.php/s/tFnDRJEUyHiXPYL).

2. Log in to the Linux as a 'Kafka' user and copy the package to the home directory.

3. Untar the package (the package name varies according to the version) as follows:

   ~~~bash
   tar -zxvf k2view_Confluent_7.xxx.tar.gz && source ~/.bash_profile
   ~~~

### Set up the Kafka nodes

The number of cluster nodes should be either greater than or equal to the replication factor number.

The script should be run separately on each node, in the order of designated node numbers. It should not be run simultaneously as this may cause configuration issues.

**Mandatory required details:**
+ Node IPs
+ Replication factor

**Optional required deatils:**
+ Kafka home directory (if not specified, current folder will be used).


Assuming you prepare to run the Kafka with:
>* Replication factor 3 
>* Node IPs 10.0.0.1, 10.0.0.2, 10.0.0.3

1. 	Run the following command: (start with the 1st node, then, one by one on the 2nd and 3rd:
~~~bash
./Cassandra_init -i 10.0.0.1,10.0.0.2,10.0.0.3 -u k2admin -p password -r 3
~~~


2. Verify that the Kafka and Zookeeper are running: 
 (run on one of the nodes)
   ~~~bash
   $CONFLUENT_HOME/bin/zookeeper-shell localhost:2181 <<< "ls /brokers/ids"
   ~~~




## Setup for Fabric & TDM 7

### Load the Package 

1. Download the package from the links that were provided to you.

2. Untar the package:

   ~~~bash
   tar -zxvf [package name].tar.gz && source ~/.bash_profile
   ~~~

3. In case you are installing Fabric on a folder different than /opt/apps/fabric, run:

   ~~~bash
   sed -i "s@K2_HOME=.*@K2_HOME=$(pwd)@" .bash_profile
   source ~/.bash_profile
   ~~~

### Set up the Fabric nodes
The script should be run separately on each node, in the order of designated node numbers. It should not be run simultaneously as this may cause configuration issues.

**Mandatory required details:**
+ Cassandra seed node IPs
+ Cassandra Replication factor
+ Cassandra username & password (if set different than the default in the Cassandra setup)
+ Kafka node IPs
+ Memory in GB for Fabric heap (or set automatic to ¼ of total ram).

**Optional required details:**
+ Cassandra DC name

Assuming you prepare to run the Fabric with the following:
+ Memory set to automatic
*Cassandra cluster with:*
+ Replication factor 3 
+ Seed IPs 10.0.0.1, 10.0.0.2, 10.0.0.3
+ User ‘k2admin’
+ Password ‘password’
*Kafka cluster with:*
+ 3 nodes
+ Node IPs 10.0.0.4, 10.0.0.5, 10.0.0.6

Run the following command: (start with the 1st node, then, one by one until the last node)
~~~bash
./fabric7_init -u k2admin -p password -c 10.0.0.1,10.0.0.2,10.0.0.3 -r 3 -k 10.0.0.4,10.0.0.5,10.0.0.6 -m auto
~~~


5. Connect to the Fabric console using the following command:

   ~~~bash
   fabric -u k2consoleadmin -p KW4RVG98RR9xcrTv
   ~~~

   - The same login details should be used for logging in to the Web Framework.



## PGSQL 

TDM 7.xx is certified with PGSQL 9.6 & 13. You can provide access to the existing PostgreSQL DB if you have one.
TDM requires login details with full **create**, **delete** and **update** privileges. 

The customer can provide the **PGSQL**, or find the below installation instructions for **K2View** **PGSQL**:

<ul>      
<li>
<a href="/articles/98_maintenance_and_operational/Installations/Linux/PGSQL_setup.md">Setup PGSQL 13.3</a></li>

