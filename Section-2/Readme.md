# Setup a MySQL Cluster
## Disable firewall
```
sudo systemctl stop firewalld;
```
## Create site
```
mcm
mcm> create site --hosts=127.0.0.1 mysite;
mcm> list sites;
```
## Add Package
```
mcm> add package --basedir=/usr cluster_8;
mcm> list packages mysite;
```
## Create NDB Cluster
The following command creates an NDB Cluster, named as mycluster, with 1 management node, 2 data nodes, and 2 SQL nodes.
```
mcm> ccreate cluster --package=cluster_8 --processhosts=ndbd@10.0.0.93,ndbd@10.0.0.93,ndb_mgmd@10.0.0.93,mysqld@10.0.0.93,mysqld@10.0.0.93,ndbapi@10.0.0.93,ndbapi@10.0.0.93 mycluster;
mcm> list clusters mysite;
mcm> show status mycluster;
mcm> show status --process mycluster;
mcm> show status -r mycluster;
```
## List and change configuration on Management Node
```
mcm> get *:ndb_mgmd mycluster;
mcm> get -d *:ndb_mgmd mycluster;
mcm> get -d *data*:ndb_mgmd mycluster;
```
## List and change configuration on Data Nodes
```
mcm> get *:ndbd mycluster;
mcm> get *:ndbd:1 mycluster;
mcm> get *:ndbd:2 mycluster;
```
## List and change configuration on SQL Nodes
```
mcm> get *:mysqld:146 mycluster;
mcm> get *:mysqld:147 mycluster;
mcm> set port:mysqld:146=3306 mycluster;
mcm> set port:mysqld:147=3307 mycluster;
```
The above command will also set ports for each MySQL instance (3306 and 3307). We differentiate the port since they are running on the same server. </br>
## Change backup directory for all nodes
```
mcm> get backupdatadir:ndbd:1 mycluster;
mcm> get backupdatadir:ndbd:2 mycluster;
mcm> \! mkdir -p /home/opc/backup/data-1 /home/opc/backup/data-2 /home/opc/backup/sql-3306 /home/opc/backup/sql-3307
mcm> set backupdatadir:ndbd:1=/home/opc/backup/data-1 mycluster;
mcm> set backupdatadir:ndbd:2=/home/opc/backup/data-2 mycluster;
mcm> set backupdatadir:mysqld:146=/home/opc/backup/sql-3306 mycluster;
mcm> set backupdatadir:mysqld:146=/home/opc/backup/sql-3307 mycluster;
mcm> get backupdatadir:mysqld:146 mycluster;
mcm> get backupdatadir:mysqld:147 mycluster;
mcm> get backupdatadir:ndbd:1 mycluster;
mcm> get backupdatadir:ndbd:2 mycluster;
```
## Set DataMemory (Optional on this workshop)
```
mcm> get -d datamemory:ndbd mycluster;
mcm> set datamemory:ndbd=513802240 mycluster;
mcm> get -d datamemory:ndbd mycluster;
```
## Start NDB Cluster
```
mcm> start cluster --background mycluster;
```
Do the following until all processes are running.
```
mcm> show status --progress mycluster;
mcm> show status --progressbar mycluster;
mcm> show status --process mycluster;
mcm> exit;
```
## Login to NDB Cluster 
login to 3306:
```
$ mysql -uroot -h127.0.0.1
mysql> show databases;
mysql> create database test;
mysql> create table test.test (i int) engine=ndbcluster;
mysql> insert into test.test values (1), (2), (3);
mysql> select * from test.test;
mysql> exit;
```
Check table test.test from instance 3307
```
$ mysql -uroot -h127.0.0.1 -P3307 -e "select * from test.test"
```
## Terminate one of the Data Node
Terminate one of the data node as follow:
```
$ ps -ef | grep ndbd | grep ndb-nodeid=1
$ kill -9 <PID>
```
start the failed process
```
mcm> show status --process mycluster;
mcm> start process 1 mycluster;
mcm> show status --progress mycluster;
mcm> show status --process mycluster;
```
## Run Autotune 
baseline to cluster using autotune. Check first the recommendation using --dryrun mode
```
mcm> autotune --dryrun --writeload=low test mycluster;
```
Apply the recommmendation
```
mcm> autotune --writeload=low test mycluster;
mcm> show status -r mycluster;
mcm> exit;
```
## Implement Distributed Privileges and create a user
Connect to 3306 and query the new user
```
$ mysql -uroot -h127.0.0.1 
mysql> select user from mysql.user;
mysql> create user apps@'%' identified by 'apps';
mysql> grant ndb_stored_user on *.* to apps@'%';
mysql> exit;
```
Connect to 3307 and query users
```
mysql -uroot -h::1 -P3307 -e "select user from mysql.user"
mysql -uroot -h::1 -P3307 -e "grant all privileges on *.* to apps@'%';"
mysql -uapps -papps -h::1 -e "show grants;"
```
## Install world_x schema
```
wget http://downloads.mysql.com/docs/world_x-db.zip
unzip world_x-db.zip
mysql -uroot -h::1 -e "source world_x-db/world_x.sql"
mysql -uroot -h::1 -e "show databases"
mysql -uroot -h::1 -P3307 -e "show databases"
mysql -uroot -h::1 -e "use world_x; show tables;"
mysql -uroot -h::1 -P3307 -e "use world_x; show tables;"
```
Change table's storage engine to NDBCLUSTER
```
mysql -uroot -h::1 -e "alter table world_x.city engine=ndbcluster"
mysql -uroot -h::1 -e "alter table world_x.countryinfo engine=ndbcluster"
mysql -uroot -h::1 -e "alter table world_x.countrylanguage drop constraint countrylanguage_ibfk_1"
mysql -uroot -h::1 -e "alter table world_x.country engine=ndbcluster;"
mysql -uroot -h::1 -e " alter table world_x.countrylanguage engine=ndbcluster;"
mysql -uroot -h::1 -P3307 -e "use world_x; show tables"
mysql -uroot -h::1 -e "alter table world_x.countrylanguage add CONSTRAINT countrylanguage_ibfk_1 FOREIGN KEY (CountryCode) REFERENCES country (Code)"
mysql -uroot -h::1 -P3307 -e "use world_x; show create table countrylanguage"
mysql -uroot -h::1 -e "use world_x; show create table countrylanguage"
```
