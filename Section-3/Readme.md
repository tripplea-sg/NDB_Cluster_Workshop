# Backup and Recovery
## Run a backup and monitor
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

