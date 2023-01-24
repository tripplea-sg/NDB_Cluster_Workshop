# Install NDB Cluster
## Install MySQL Cluster 8.0.32 Management Node
```
sudo yum -y localinstall mysql-cluster-commercial-management-server-8.0.32-1.1.el8.x86_64.rpm
ndb_mgmd --version
ndb_mgm --version
```
## Install MySQL Cluster 8.0.32 Data Node
```
sudo yum -y localinstall mysql-cluster-commercial-data-node-8.0.32-1.1.el8.x86_64.rpm 
ndbd --version
ndbmtd --version
```
## Install MySQL Cluster 8.0.22 SQL Node
```
sudo yum -y localinstall mysql-cluster-commercial-client-plugins-8.0.32-1.1.el8.x86_64.rpm
sudo yum -y localinstall mysql-cluster-commercial-common-8.0.32-1.1.el8.x86_64.rpm
sudo yum -y localinstall mysql-cluster-commercial-libs-8.0.32-1.1.el8.x86_64.rpm 
sudo yum -y localinstall mysql-cluster-commercial-client-8.0.32-1.1.el8.x86_64.rpm
sudo yum -y localinstall mysql-cluster-commercial-icu-data-files-8.0.32-1.1.el8.x86_64.rpm
sudo yum -y localinstall mysql-cluster-commercial-server-8.0.32-1.1.el8.x86_64.rpm
mysqld --version
mysql --version
```
## Install MySQL Cluster Manager 8.0.32 
```
sudo yum -y localinstall mcm-8.0.32-linux-el8-x86-64bit.rpm 
sudo ln -s /usr/mcm8.0.32/bin/mcmd /bin/mcmd
sudo ln -s /usr/mcm8.0.32/bin/mcm /bin/mcm
mcmd --version
mcm --version
```
## Getting started with mcm
Create /home/opc/mcm.ini
```
# Copyright (c) 2018, 2021, Oracle and/or its affiliates.
#
# MySQL Cluster Manager sample configuration
#

[DEFAULT]
logging_folder = /home/opc/mysql-cluster/log
data_folder = /home/opc/mysql-cluster/mcm-data
pid_file = /home/opc/mysql-cluster/mcmd.pid

[logger]
#level = INFO
#filename = mcmd.log

# The MySQL Cluster Manager plugin
[mcmd]
#mcmd_user = mcmd
#mcmd_password = super
#bind_address = ::
#bind_port = 1862
#xcom_port = 18620
#copy_port = 0
```
Start mcmd
```
mkdir -p /home/opc/mysql-cluster/log /home/opc/mysql-cluster/mcm-data
chmod 400 /home/opc/mcm.ini
mcmd --config=/home/opc/mcm.ini &
```
Login to mcm
```
mcm
mcm> status;
mcm> list commands;
mcm> exit;
```
