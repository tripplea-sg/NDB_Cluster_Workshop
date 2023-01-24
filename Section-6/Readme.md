# MySQL Enterprise Audit with NDB Cluster
login to 3306 and 3307 to install audit
```
mysql -uroot -h::1 -e "source /usr/share/mysql-8.0/audit_log_filter_linux_install.sql"
mysql -uroot -h::1 -P3307 -e "source /usr/share/mysql-8.0/audit_log_filter_linux_install.sql"
```
Adding Audit Filter per SQL node
```
mysql -uroot -h::1 -e "select audit_log_filter_set_filter('log_all', '{ \"filter\": { \"log\": true } }')"
mysql -uroot -h::1 -e "select audit_log_filter_set_user('%', 'log_all');"

mysql -uroot -h::1 -P3307 -e "select audit_log_filter_set_filter('log_all', '{ \"filter\": { \"log\": true } }')"
mysql -uroot -h::1 -P3307 -e "select audit_log_filter_set_user('%', 'log_all');"
```
Check if audit log exist
```
ls /home/opc/mysql-cluster/mcm-data/clusters/mycluster/146/data/audit.log

ls /home/opc/mysql-cluster/mcm-data/clusters/mycluster/147/data/audit.log
```
