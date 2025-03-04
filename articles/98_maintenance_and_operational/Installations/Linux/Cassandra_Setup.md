# Cassandra Setup

### Load The Package 

1. Retrieve the latest Cassandra package (located [here](https://download.k2view.com/index.php/s/dMH2PWuIErPFszK)).

2. Log in to the Linux server as the user "Cassandra" and copy the package to the home directory.

3. Untar the package (the package name varies according to the version) as follows:

   ~~~bash
   tar -zxvf k2v_cassandra-3.11.xxx.tar.gz && bash -l
   ~~~


### Set Up Single Node Cassandra

The supplied Cassandra package is preconfigured to run as a single node with Fabric on the same host. In order to achieve this, the following settings are predefined:

* DC=DC1
* cluster_name:integration
* Listen_address: 127.0.0.1 (localhost)

To start working, run the commands as shown below.

1.  Start Cassandra.

2.  Run the post setup commands.

### Start Cassandra

~~~bash
cassandra
~~~

### Post Setup on One Node

Create a new superuser for Cassandra, and change the **cassandra** default user's password:

~~~bash
echo "create user k2admin with password 'Q1w2e3r4t5' superuser;" |cqlsh -u cassandra -p cassandra
~~~

**Note**: If you decide to change the password in the above setting, you would be required to run the following commands, when setting up the Fabric server. We refer to the following SED lines:


~~~bash
sed -i "s@#PASSWORD=.*@PASSWORD=Q1w2e3r4t5@" $K2_HOME/config/config.ini
sed -i "s@#PASSWORD=.*@PASSWORD=Q1w2e3r4t5@" $K2_HOME/config/iifConfig.ini
~~~

## 
