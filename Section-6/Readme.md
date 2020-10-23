# MySQL Enterprise Audit with NDB Cluster
On t2 terminal, login to 3306 and install audit
```
$ mysql -uroot -h127.0.0.1
mysql> source /home/opc/source/mysql-cluster-commercial-8.0.22-el7-x86_64/share/audit_log_filter_linux_install.sql
mysql> select audit_log_filter_set_filter('log_all', '{ "filter": { "log": true } }');
mysql> select audit_log_filter_set_user('%', 'log_all');
mysql> exit;
```
On t2 terminal, login to 3307 and install audit
```
$ mysql -uroot -h127.0.0.1 -P3307
mysql> source /home/opc/source/mysql-cluster-commercial-8.0.22-el7-x86_64/share/audit_log_filter_linux_install.sql
mysql> select audit_log_filter_set_filter('log_all', '{ "filter": { "log": true } }');
mysql> select audit_log_filter_set_user('%', 'log_all');
mysql> exit;
```
