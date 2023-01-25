# Backup-Restore with MySQL Shell
## install MySQL Shell
```
yum install -y mysql-shell
```
## Create directory backup
```
mkdir -p /home/opc/backup/mysql-shell
```
## Backup database
```
mysqlsh root@localhost:3306 -- util dumpInstance '/home/opc/backup/mysql-shell'
```
## drop table
```
mysqlsh root@localhost:3306 --sql -e "use world_x; show tables"

mysqlsh root@localhost:3306 --sql -e "drop table world_x.city"

mysqlsh root@localhost:3306 --sql -e "use world_x; show tables"
```
## restore table from backup
```
mysqlsh root@localhost:3306 --sql -e 'set global local_infile=on';

mysqlsh root@localhost:3306 -- util loadDump '/home/opc/backup/mysql-shell' --includeTables='world_x.city'

mysqlsh root@localhost:3306 --sql -e "use world_x; show tables"

mysqlsh root@localhost:3307 --sql -e "use world_x; show tables"
```
