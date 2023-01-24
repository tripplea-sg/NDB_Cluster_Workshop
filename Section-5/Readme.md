# Scale Up NDB Cluster (Adding Data Nodes)
## Adding new processes
```
mcm
mcm> add process --processhosts=ndbd@10.0.0.93,ndbd@10.0.0.93 --background mycluster;
mcm> show status --progress mycluster;
```
Repeat "show status --progress" until 100% complete.
```
mcm> show status -r mycluster;

mcm> \! mkdir -p /home/opc/mysql-cluster/mcm-data/clusters/mycluster/3/data /home/opc/mysql-cluster/mcm-data/clusters/mycluster/4/data
```
On t1 terminal, start the added nodes
```
mcm> start process --added --background mycluster;

mcm> show status --process mycluster;

mcm> exit;
```
Check data nodes utilization
```
mysql -uroot -h::1 -e "select * from ndbinfo.memoryusage"
```
Reorganize partition
```
mysql -uroot -h127.0.0.1 -e "alter table world_x.city reorganize partition"
mysql -uroot -h127.0.0.1 -e "alter table world_x.country reorganize partition"
mysql -uroot -h127.0.0.1 -e "alter table world_x.countryinfo reorganize partition"
mysql -uroot -h127.0.0.1 -e "alter table world_x.countrylanguage reorganize partition"
```
Check data nodes utilization
```
mysql -uroot -h::1 -e "select * from ndbinfo.memoryusage"
```
