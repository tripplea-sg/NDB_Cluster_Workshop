# Install NDB Cluster
## Install MySQL Cluster Manager (mcm) binary
```
cd /home/opc/source
tar -zxvf mcm-1.4.8-cluster-7.6.13-linux-el7-x86-64bit.tar.gz 
```
## Install MySQL Cluster 7.6.16 binary
```
tar -zxvf mysql-cluster-advanced-7.6.16-el7-x86_64.tar.gz
```
## Install MySQL Cluster 8.0.22 binary
```
tar -zxvf mysql-cluster-commercial-8.0.22-el7-x86_64.tar.gz
```
## Getting started with mcm
View mcm configuration (mcm.ini)
```
$ cat /home/opc/conf/mcm.ini
```
Start mcm
```
$ mcmd --defaults-file=/home/opc/conf/mcm.ini
```
Login to mcm
```
$ mcm
mcm> status;
mcm> exit;
```
Terminate mcmd process and relogin to mcm
```
$ ps -ef | grep mcmd
$ kill -9 <PID>
$ ps -ef | grep mcmd
$ mcm
mcm> status;
mcm> exit;
```
