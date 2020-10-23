# Scale Up NDB Cluster (Adding Data Nodes)
On t1 terminal, do the following:
```
mcm> add process --processhosts=ndbmtd@127.0.0.1,ndbmtd@127.0.0.1 --background mycluster;
mcm> show status --progress mycluster;
``
Repeat "show status --progress" until 100% complete.
```
mcm> set datadir:ndbmtd:1=/home/opc/data/data3,datadir:ndbmtd:2=/home/opc/data/data4 mycluster;
```
On t1 terminal, start the added nodes
```
mcm> show status --process mycluster;
mcm> start process --added mycluster;
mcm> show status --process mycluster;
```
On t2 terminal, check the distribution of table ted.test and test.test as follow:
```
$ ndb_desc -pn -dted -p test
$ ndb_desc -pn -dtest -p test
```
The distribution does not even. Only data node group 0 is populated with data. On t2 terminal, do the following to redistribute the data.
```
$ mysql -uroot -h127.0.0.1
mysql> ALTER TABLE ted.test REORGANIZE PARTITION;
mysql> ALTER TABLE test.test REORGANIZE PARTITION;
mysql> exit;

$ ndb_desc -pn -dted -p test
$ ndb_desc -pn -dtest -p test
```
