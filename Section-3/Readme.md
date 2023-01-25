# Backup and Recovery
## Backup database and monitor
Run the following command to backup database:
```
mcm
mcm> get backupdatadir:ndbd:1 mycluster;
mcm> get backupdatadir:ndbd:2 mycluster;
mcm> backup cluster --backupid=1 --background mycluster;
```
Monitor the backup process using the following:
```
mcm> show status --progress mycluster;
```
list all backups for mycluster
```
mcm> list backups mycluster;
mcm> exit
```
On t1 terminal, check the backup files from backupdatadir file location
```
ls /home/opc/backup/data-1/BACKUP/BACKUP-1
ls /home/opc/backup/data-2/BACKUP/BACKUP-1
ls /home/opc/backup/sql-3306/BACKUP/BACKUP-1
ls /home/opc/backup/sql-3307/BACKUP/BACKUP-1
```
## Stop and Start Cluster with Initial
```
mcm
mcm> stop cluster --background mycluster;
mcm> show status --progress mycluster;

mcm> \! rm -Rf /home/opc/mysql-cluster/mcm-data/clusters/mycluster/146/data/*
mcm> \! rm -Rf /home/opc/mysql-cluster/mcm-data/clusters/mycluster/147/data/*

mcm> start cluster --initial --background mycluster;
mcm> show status --progress mycluster;
mcm> show status -r mycluster;
mcm> exit;
```
## check content of database
```
mysql -uroot -h::1 -e "show databases"

mysql -uroot -h::1 -P3307 -e "show databases"
```
## restore from backup using mcm
```
mcm
mcm> list backups mycluster;
mcm> restore cluster --backupid=1 --background mycluster;
mcm> show status --progress mycluster;
mcm> exit;
```
Check Data from SQL node
```
mysql -uroot -h::1 -e "show databases"

mysql -uroot -h::1 -P3307 -e "show databases"
```
## Partial restore using mcm
delete table
```
mysql -uroot -h::1 -e "drop table world_x.city"
```
restore table using mcm
```
mcm
mcm> restore cluster --backupid=5 --include-tables=world_x.city mycluster; 
mcm> exit
```
