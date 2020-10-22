# Backup and Recovery
## Backup database and monitor
On t1 terminal, run the following command to backup database:
```
mcm> backup cluster --backupid=123456 --background mycluster;
```
Monitor the backup process using the following:
```
mcm> show status --progress mycluster;
```
On t1 terminal, list all backups for mycluster
```
mcm> list backups mycluster;
```
On t1 terminal, check the backup files from backupdatadir file location
```
mcm>\! ls /home/opc/backup/data1
mcm>\! ls /home/opc/backup/data2
mcm>\! ls /home/opc/backup/sql1
mcm>\! ls /home/opc/backup/sql2
```
## Drop database 
On t2 terminal, login to 3306 and drop database ted.
```
$ mysql -uroot -h127.0.0.1
mysql> drop database ted;
mysql> exit;
```
## Restore Database From a Backup
On t1 terminal, stop the cluster and monitor the progress. Repeat the "show status --progrees" command until cluster is stopped.
```
mcm> stop cluster --background mycluster;
mcm> show status --progress mycluster;
mcm> show status --process mycluster;
```
On t2 terminal, remmove all files from SQL Nodes' data directory
```
$ rm -Rf /home/opc/data/sql1/*
$ rm -Rf /home/opc/data/sql2/*
```
On t1 terminal, start cluster with --initial (all data will be wiped out from NDB Cluster)
```
mcm> start cluster --initial --background mycluster;
mcm> show status --progress mycluster;
mcm> show status --process mycluster;
```
Once all cluster nodes are up - on t2 terminal, prepare SQL Nodes for restoration:
```
$ mysql -uroot -h127.0.0.1
mysql> show databases;
mysql> select user from mysql.user;
mysql> select table_name, engine from information_schema.tables where table_schema='mysql';
mysql> source /home/opc/source/mysql-cluster-advanced-7.6.16-el7-x86_64/share/ndb_dist_priv.sql
mysql> drop table mysql.user;
mysql> drop table mysql.tables_priv;
mysql> drop table mysql.proxies_priv;
mysql> drop table mysql.procs_priv;
mysql> drop table mysql.db;
mysql> drop table mysql.columns_priv;
mysql> exit;

$ mysql -uroot -h127.0.0.1 -P3307
mysql> source /home/opc/source/mysql-cluster-advanced-7.6.16-el7-x86_64/share/ndb_dist_priv.sql
mysql> drop table mysql.user;
mysql> drop table mysql.tables_priv;
mysql> drop table mysql.proxies_priv;
mysql> drop table mysql.procs_priv;
mysql> drop table mysql.db;
mysql> drop table mysql.columns_priv;
mysql> exit;
```
On t1 terminal, restore database from backup
```
mcm> restore cluster --backupid=123456 mycluster;
mcm> show status --progress mycluster;
```
On t2 terminal, sync back all ndb_*_backup tables with the original tables and flush privileges
```
```
