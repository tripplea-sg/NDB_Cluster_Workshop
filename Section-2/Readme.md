# Setup a MySQL Cluster
Open two ssh terminal (t1 and t2) and connect each termminal to the server. Choose profile that points to MySQL Cluster 7.6.16
## Create site
On t1 terminal:
```
$ mcm
mcm> create site --hosts=127.0.0.1 test;
mcm> list sites;
```
## Add Package
On t1 terminal:
```
mcm> add package --basedir=/home/opc/source/mysql-cluster-advanced-7.6.16-el7-x86_64  cluster_7.6.16;
mcm> list packages test;
```
## Create NDB Cluster
The following command creates an NDB Cluster, named as mycluster, with 1 management node, 2 data nodes, and 2 SQL nodes.
On t1 terminal:
```
mcm> create cluster --package=cluster_7.6.16 --processhosts=ndbmtd@127.0.0.1,ndbmtd@127.0.0.1,ndb_mgmd@127.0.0.1,mysqld@127.0.0.1,mysqld@127.0.0.1,ndbapi@127.0.0.1,ndbapi@127.0.0.1 mycluster;
mcm> list clusters test;
mcm> show status mycluster;
mcm> show status --process mycluster;
```
## List and change configuration on Management Node
On t1 terminal:
```
mcm> get *:ndb_mgmd mycluster;
mcm> get -d *:ndb_mgmd mycluster;
mcm> get -d *data*:ndb_mgmd mycluster;
mcm> set datadir:ndb_mgmd=/home/opc/mgm mycluster;
mcm> get *data*:ndb_mgmd mycluster;
mcm> get -d datadir:ndb_mgmd mycluster;
```
## List and change configuration on Data Nodes
On t1 terminal:
```
mcm> get *:ndbmtd mycluster;
mcm> get *:ndbmtd:1 mycluster;
mcm> get *:ndbmtd:2 mycluster;
mcm> set datadir:ndbmtd:1=/home/opc/data/data1 mycluster;
mcm> set datadir:ndbmtd:2=/home/opc/data/data2 mycluster;
mcm> get *:ndbmtd:1 mycluster;
mcm> get *:ndbmtd:2 mycluster;
```
## List and change configuration on SQL Nodes
On t1 terminal:
```
mcm> set port:mysqld:51=3307,datadir:mysqld:50=/home/opc/data/sql1,datadir:mysqld:51=/home/opc/data/sql2,log_error:mysqld:50=/home/opc/data/sql1/mysqld.log,log_error:mysqld:51=/home/opc/data/sql2/mysqld.log,socket:mysqld:50=/home/opc/data/sql1/mysqld.soc,socket:mysqld:51=/home/opc/data/sql2/mysqld.soc,log_bin:mysqld:50=/home/opc/data/sql1/bin,log_bin:mysqld:51=/home/opc/data/sql2/bin,binlog_format:mysqld=row,server_id:mysqld:50=100,server_id:mysqld:51=200 mycluster;
```
The above command will also set ports for each MySQL instance (3306 and 3307). We differentiate the port since they are running on the same server.
## Change backup directory for all nodes
On t1 terminal:
```
mcm> set backupdatadir:ndbmtd:1=/home/opc/backup/data1,backupdatadir:ndbmtd:2=/home/opc/backup/data2,backupdatadir:mysqld:50=/home/opc/backup/sql1,backupdatadir:mysqld:51=/home/opc/backup/sql2 mycluster;
```
## Start NDB Cluster
On t1 terminal:
```
mcm> start cluster --background mycluster;
```
On t1 terminal, do the following until all processes are running.
```
mcm> show status --progress mycluster;
mcm> show status --progressbar mycluster;
mcm> show status --process mycluster;
```
## Login to NDB Cluster 
On t2 terminal, login to 3306:
```
$ mysql -uroot -h127.0.0.1
mysql> show databases;
mysql> create database test;
mysql> create table test.test (i int) engine ndbcluster;
mysql> insert into test.test values (1), (2), (3);
mysql> select * from test.test;
mysql> exit;
```
On t2 terminal, check table test.test from instance 3307
```
$ mysql -uroot -h127.0.0.1 -P3307 -e "select * from test.test"
```
## Terminate one of the Data Node
On t2 terminal, terminate one of the data node as follow:
```
$ ps -ef | grep ndbmtd
$ kill -9 <PID>
```
On t1 terminal, start the failed process
```
mcm> show status --process mycluster;
mcm> start process <processID> mycluster;
mcm> show status --progress mycluster;
mcm> show status --process mycluster;
```
## Run Autotune 
On t1 terminal, baseline to cluster using autotune. Check first the recommendation using --dryrun mode
```
mcm> autotune --dryrun --writeload=low test mycluster;
mcm> \! cat /var/opt/mcm/clusters/mycluster/tmp/autotune.*.mcm
```
Apply the recommmendation
```
mcm> autotune --writeload=low test mycluster;
```



