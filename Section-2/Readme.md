# Setup a MySQL Cluster
Open a ssh terminal (t1) and connect to the server. Choose profile that points to MySQL Cluster 7.6.16
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
## List and change configuration on Management Node
```
mcm> get *:ndb_mgmd mycluster;
mcm> get -d *:ndb_mgmd mycluster;
mcm> get -d *data*:ndb_mgmd mycluster;
mcm> set datadir:ndb_mgmd=/home/opc/mgm mycluster;
mcm> get *data*:ndb_mgmd mycluster;
mcm> get -d datadir:ndb_mgmd mycluster;
```
## List and change configuration on Data Nodes
```
mcm> get *:ndbmtd mycluster;
mcm> get *:ndbmtd:1 mycluster;
mcm> get *:ndbmtd:2 mycluster;
mcm> set datadir:ndbmtd:1=/home/opc/data/data1 mycluster;
mcm> set datadir:ndbmtd:2=/home/opc/data/data2 mycluster;
mcm> get *:ndbmtd:1 mycluster;
mcm> get *:ndbmtd:2 mycluster;
```

