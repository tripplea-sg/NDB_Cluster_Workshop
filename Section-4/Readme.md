# Upgrade NDB Cluster
On t1 terminal, add new package
```
mcm> add package --basedir=/home/opc/source/mysql-cluster-commercial-8.0.22-el7-x86_64 cluster_8.0.22;
mcm> list packages test;
```
On t1 terminal, upgrade NDB Cluster and monitor the upgrade status
```
mcm> upgrade cluster --package=cluster_8.0.22 --background mycluster;
mcm> show status --process mycluster;
mcm> show status --progress mycluster;
mcm> list clusters test;
```
Upgrade is completed. Open new terminal (t2) and choose profile for 8.0.22, then check table mysql.user
```
$ mysql -uroot -h127.0.0.1
mysql> select table_name, engine from information_schema.tables where table_schema='mysql';
mysql> exit;
```
As you see, the mysql.user table uses InnoDB storageengine and no longer on ndbcluster storage engine. MySQL Cluster 8.0 introduces new way to handle distributed privileges. On t2, test the following:
```
$ mysql -uroot -h127.0.0.1
mysql> create user test8@'%' identified by 'test8';
mysql> select user from mysql.user;
mysql> exit;

$ mysql -uroot -h127.0.0.1 -P3307
mysql> select user from mysql.user;
mysql> exit;

$ mysql -uroot -h127.0.0.1 
mysql> grant ndb_stored_user on *.* to ‘test8’;
mysql> exit;

$ mysql -uroot -h127.0.0.1 -P3307
mysql> select user from mysql.user;
mysql> exit;
```

