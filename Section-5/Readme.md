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
mcm> start process --added mycluster;
```

