# Percona-XtraBackup-Master-Slave-Replication-of-MYSQL

# percona Installation packages.

<< yum -y install https://repo.percona.com/yum/percona-release-latest.noarch.rpm

<< percona-release setup ps80

# Then install the latest Percona Server and Percona Xtrabackup

<< yum -y install percona-server-server percona-xtrabackup-80

<< systemctl enable mysql

<< systemctl start mysql

# change the root password 

<< cat /var/log/mysqld.log | grep temporary
<< mysql_secure_installation

# Asynchronous Replication
# Now add this config to master mysql server inside /etc/my.cnf (it must be under the [mysqld] section):

<< vi /etc/my.cnf

#-----------------------------------------

##Compatibility

default-authentication-plugin = mysql_native_password
 
##Replication

server_id = 61 # must be distinct on all nodes in the cluster

binlog_format = ROW

log_bin = binlog

log_slave_updates = 1

gtid_mode = ON


enforce_gtid_consistency = 1

binlog_expire_logs_seconds = 604800 # 7 days

sync_binlog = 1

report_host = 192.168.0.61 # IP address of this host

read_only = OFF # Set ON on slave

super_read_only = OFF # Set ON on slave
 
##Replication safety

master_info_repository = TABLE

relay_log_info_repository = TABLE

relay_log_recovery = ON


#-------------------------------------------------##

# Now add this config to SLAVE-MYSQL server inside /etc/my.cnf (it must be under the [mysqld] section):

<<vi /etc/my.cnf

#-----------------------------------------

##Compatibility

default-authentication-plugin = mysql_native_password
 
##Replication

server_id = 62 # must be distinct on all nodes in the cluster

binlog_format = ROW

log_bin = binlog

log_slave_updates = 1

gtid_mode = ON

enforce_gtid_consistency = 1

binlog_expire_logs_seconds = 604800 # 7 days

sync_binlog = 1

report_host = 192.168.0.62 # IP address of this host

read_only = ON # Set ON on slave, it means slave-db have read_only access

super_read_only = ON # Set ON on slave
 
##Replication safety

master_info_repository = TABLE

relay_log_info_repository = TABLE

relay_log_recovery = ON

#-------------------------------------------------

# Setting up the Replication Link

# login to Master mysql with root enter below cmd with urs credentails 

<< mysql> CREATE USER 'slave'@'Slave-IP' IDENTIFIED WITH mysql_native_password BY 'c7SdB239@8XMn2&9';

<< GRANT REPLICATION SLAVE ON *.* TO 'slave'@'Slave-IP';

#login to Slave mysql with root and enter the below cmd with urs credentials 

<< RESET MASTER;

<< CHANGE MASTER TO MASTER_HOST = 'MASTER-IP', MASTER_USER = 'slave', MASTER_PASSWORD = 'c7SdB239@8XMn2&9', MASTER_AUTO_POSITION = 1;

<< START SLAVE;

# see the status of slave

<< SHOW MASTER STATUS\G

------------------------------------------------------THE END--------------------------------------------------------------------
#  :  https://www.percona.com/doc/percona-xtrabackup/2.3/howtos/setting_up_replication.html

