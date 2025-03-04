# Fabric 7.x Setup Single Node

Fabric 7.x.x requires JDK 17 (included in the Fabric installation package). However, the certified version of Cassandra and Kafka require JDK 8.* package (latest tested version included with each supplied package).

[Click here to get the Cassandra setup details.](Cassandra_Setup.md)

[Click here to get the Apache Kafka setup details.](Apache_Kafka_Setup.md)

## Pre-Installation Steps

Click [here](01_Fabric_7.xx_Installation_intro.md) to get the full list of activities that should be performed prior to either Fabric or TDM installation. Note that this is mandatory and should be performed at least once per each server.

## Setup for Fabric 7.x & TDM 7.x

The supplied Fabric package is preconfigured to run as a single node with Cassandra on the same host. In order to achieve this, the following settings are predefined:

* Cassandra seed IP - 127.0.0.1 (localhost)
* DC = DC1


### Load the Package 

1.  Download the package from the links that were provided to you.

2.  Untar the package:
   ~~~bash
   tar -zxvf [package name].tar.gz && bash -l
   ~~~

3. Pre setup:

   ~~~bash
   sed -i "s@K2_HOME=.*@K2_HOME=$(pwd)@" .bash_profile
   bash -l 
   ~~~

   **Note** that the Fabric Admin user is defined now as **k2consoleadmin**.
   
   **Note:** If you decide to change the default password in the Cassandra server setup, you would be required to run the following commands; we refer to the following SED lines:
 ~~~bash
 sed -i "s@#PASSWORD=.*@PASSWORD=Q1w2e3r4t5@" $K2_HOME/config/config.ini
 sed -i "s@#PASSWORD=.*@PASSWORD=Q1w2e3r4t5@" $K2_HOME/config/iifConfig.ini
 ~~~

4. Start Fabric:

   ~~~bash
   k2fabric start && k2fabric status
   ~~~

5. Connect to the Fabric console using the following command:

   ~~~bash
   fabric -u k2consoleadmin -p KW4RVG98RR9xcrTv
   ~~~

   * The same login details should be used to log in to the Web Framework.

## PGSQL 

TDM 7.xx is certified with PGSQL 9.6 & 13. You can provide access to the existing PostgreSQL DB if you have one.
TDM requires login details with full **create**, **delete** and **update** privileges. 

The customer can provide the **PGSQL**, or find below the installation instructions for **K2View** **PGSQL**:

<ul>      
<li>
<a href="/articles/98_maintenance_and_operational/Installations/Linux/PGSQL_setup.md">Setup PGSQL 13.3</a></li>




