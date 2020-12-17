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
Alternative:
```
mcm> create cluster --package=cluster_7.6.16 --processhosts=ndbmtd@127.0.0.1,ndbmtd@127.0.0.1,ndbmtd@127.0.0.1,ndbmtd@127.0.0.1,ndb_mgmd@127.0.0.1,mysqld@127.0.0.1,ndbapi@127.0.0.1,ndbapi@127.0.0.1 mycluster;
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
Alternative:
```
mcm> set datadir:ndbmtd:1=/home/opc/data/data1 mycluster;
mcm> set datadir:ndbmtd:2=/home/opc/data/data2 mycluster;
mcm> set datadir:ndbmtd:3=/home/opc/data/data3 mycluster;
mcm> set datadir:ndbmtd:4=/home/opc/data/data4 mycluster;
```
## List and change configuration on SQL Nodes
On t1 terminal:
```
mcm> set port:mysqld:51=3307,datadir:mysqld:50=/home/opc/data/sql1,datadir:mysqld:51=/home/opc/data/sql2,log_error:mysqld:50=/home/opc/data/sql1/mysqld.log,log_error:mysqld:51=/home/opc/data/sql2/mysqld.log,socket:mysqld:50=/home/opc/data/sql1/mysqld.soc,socket:mysqld:51=/home/opc/data/sql2/mysqld.soc,log_bin:mysqld:50=/home/opc/data/sql1/bin,log_bin:mysqld:51=/home/opc/data/sql2/bin,binlog_format:mysqld=row,server_id:mysqld:50=100,server_id:mysqld:51=200 mycluster;
```
The above command will also set ports for each MySQL instance (3306 and 3307). We differentiate the port since they are running on the same server. </br>
Alternative: No change
## Change backup directory for all nodes
On t1 terminal:
```
mcm> set backupdatadir:ndbmtd:1=/home/opc/backup/data1,backupdatadir:ndbmtd:2=/home/opc/backup/data2,backupdatadir:mysqld:50=/home/opc/backup/sql1,backupdatadir:mysqld:51=/home/opc/backup/sql2 mycluster;
```
Alternative:
```
mcm> set backupdatadir:ndbmtd:1=/home/opc/backup/data1,backupdatadir:ndbmtd:2=/home/opc/backup/data2,backupdatadir:ndbmtd:3=/home/opc/backup/data3,backupdatadir:ndbmtd:4=/home/opc/backup/data4,backupdatadir:mysqld:50=/home/opc/backup/sql1 mycluster;
```
## Thread Config (Optional on this workshop)
Check CPU configuration
```
$ lscpu | egrep 'Model name|Socket|Thread|NUMA|CPU\(s\)'
```
Check if NUMA is enabled / disabled
```
$ numactl --show
```
Set ThreadConfig on MCM:
```
mcm > set ThreadConfig:ndbmtd:1="ldm={count=4,cpubind=16,18,20,22},main={cpubind=19},io={cpubind=19}, \
tc={count=1,cpubind=17},recv={count=1,cpubind=23}, send={count=1,cpubind=21}" mycluster;
mcm > set ThreadConfig:ndbmtd:2="ldm={count=4,cpubind=24,26,28,30},main={cpubind=27},io={cpubind=27}, \
tc={count=1,cpubind=25},recv={count=1,cpubind=31}, send={count=1,cpubind=29}" mycluster;
```
Alternative:
```
mcm > set ThreadConfig:ndbmtd:1="ldm={count=4,cpubind=16,18,20,22},main={cpubind=19},io={cpubind=19}, \
tc={count=1,cpubind=17},recv={count=1,cpubind=23}, send={count=1,cpubind=21}" mycluster;
mcm > set ThreadConfig:ndbmtd:2="ldm={count=4,cpubind=24,26,28,30},main={cpubind=27},io={cpubind=27}, \
tc={count=1,cpubind=25},recv={count=1,cpubind=31}, send={count=1,cpubind=29}" mycluster;
mcm > set ThreadConfig:ndbmtd:1="ldm={count=4,cpubind=16,18,20,22},main={cpubind=19},io={cpubind=19}, \
tc={count=1,cpubind=17},recv={count=1,cpubind=23}, send={count=1,cpubind=21}" mycluster;
mcm > set ThreadConfig:ndbmtd:2="ldm={count=4,cpubind=24,26,28,30},main={cpubind=27},io={cpubind=27}, \
tc={count=1,cpubind=25},recv={count=1,cpubind=31}, send={count=1,cpubind=29}" mycluster;
```
## Set DataMemory (Optional on this workshop)
```
mcm > set DataMemory:ndbmtd=5000M mycluster;
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
mysql> source /home/opc/sql/create_ndb_test_data.sql;
mysql> select * from ted.test;
mysql> exit;
```
On t2 terminal, check table test.test from instance 3307
```
$ mysql -uroot -h127.0.0.1 -P3307 -e "select * from test.test"
$ mysql -uroot -h127.0.0.1 -P3307 -e "select * from ted.test"
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
Optional:
```
mcm > autotune --dryrun --writeload=high realtime mycluster;
mcm > autotune --writeload=high realtime mycluster;
mcm > stop process 50 mycluster;
mcm > stop process 51 mycluster;
mcm > exit
$ cd /home/opc/source/mcm-1.4.8-cluster-7.6.13-linux-el7-x86-64bit/mcm_data/clusters/mycluster/50/cfg
$ numactl -C 0,1,2,3,4,5,6,7 mysqld --defaults-file=my.cnf &
$ cd /home/opc/source/mcm-1.4.8-cluster-7.6.13-linux-el7-x86-64bit/mcm_data/clusters/mycluster/51/cfg
$ numactl -C 8,9,10,11,12,13,14,15 mysqld --defaults-file=my.cnf &
```
## Implement Distributed Privileges and create a user
On t2 terminal, login to 3306 to configure distributed privileges
```
$ mysql -uroot -h127.0.0.1
mysql> source /home/opc/source/mysql-cluster-advanced-7.6.16-el7-x86_64/share/ndb_dist_priv.sql
mysql> select mysql.mysql_cluster_privileges_are_distributed();
mysql> select table_name, engine from information_schema.tables where table_schema='mysql';
mysql> call mysql.mysql_cluster_move_privileges();
mysql> select mysql.mysql_cluster_privileges_are_distributed();
mysql> select table_name, engine from information_schema.tables where table_schema='mysql';
```
On t2 terminal, still on 3306, create user and run "call mysql.mysql_cluster_move_privileges()". 
```
mysql> create user test@'%' identified by 'test';
mysql> grant all privileges on *.* to test@'%';
mysql> select user from mysql.user;
mysql> select user from mysql.ndb_user_backup;
mysql> call mysql.mysql_cluster_move_privileges();
mysql> select user from mysql.ndb_user_backup;
mysql> exit;
```
On t2 terminal, connect to 3307 and query the new user
```
$ mysql -uroot -h127.0.0.1 -P3307
mysql> select user from mysql.user;
mysql> select user from mysql.ndb_user_backup;
```
On t2 terminal, still on 3307, install the same package
```
mysql> source /home/opc/source/mysql-cluster-advanced-7.6.16-el7-x86_64/share/ndb_dist_priv.sql
mysql> select mysql.mysql_cluster_privileges_are_distributed();
mysql> exit;
```
On t2 terminal, test login to 3306 and 3307 using test@'%' user
```
$ mysql -utest -ptest -h127.0.0.1 -P3307 -e "select * from ted.test"
$ mysql -utest -ptest -h127.0.0.1 -P3306 -e "select * from ted.test"
```
## Benchmark (Optional)
```
$ mysqlslap -h127.0.0.1 -uroot --auto-generate-sql --auto-generate-sql-guid-primary --auto-generate-sql-secondary-indexes=2 --auto-generate-sql-load-type=read --auto-generate-sql-write-number=200000 --auto-generate-sql-execute-number=100 --concurrency=6 --engine=ndbcluster 
$ mcm

mcm > stop cluster mycluster;
```
Create innodb database and run mysqlslap again
```
$ mysqlslap -h127.0.0.1 -uroot --auto-generate-sql --auto-generate-sql-guid-primary --auto-generate-sql-secondary-indexes=2 --auto-generate-sql-load-type=read --auto-generate-sql-write-number=200000 --auto-generate-sql-execute-number=100 --concurrency=6 --engine=innodb
```


