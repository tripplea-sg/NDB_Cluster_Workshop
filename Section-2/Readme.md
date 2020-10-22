# Setup a MySQL Cluster
Login to your server and choose profile that points to MySQL Cluster 7.6.16
## Create site
```
$ mcm
mcm> create site --hosts=127.0.0.1 test;
mcm> list sites;
```

## Add Package
```
mcm> add package --basedir=/home/opc/source/mysql-cluster-advanced-7.6.16-el7-x86_64  cluster_7.6.16;
mcm> list packages test;
```
## Create NDB Cluster
The following command creates an NDB Cluster, named as mycluster, with 1 management node, 2 data nodes, and 2 SQL nodes.
```
mcm> create cluster --package=cluster_7.6.16 --processhosts=ndbmtd@127.0.0.1,ndbmtd@127.0.0.1,ndb_mgmd@127.0.0.1,mysqld@127.0.0.1,mysqld@127.0.0.1,ndbapi@127.0.0.1,ndbapi@127.0.0.1 mycluster;
mcm> list clusters test;
mcm> show status mycluster;
mcm> show status --process mycluster;
```
