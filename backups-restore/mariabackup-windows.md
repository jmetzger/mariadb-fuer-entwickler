# Mariabackup (Windows)

## Installation mariabackup 

  * Installation wird mit Installation des Servers durchgeführt.
  * Ist also in der Grundinstallation beinhalten


## Walkthrough (Ubuntu/Debian)

```
# user eintrag in /root/.my.cnf
[mariabackup]
user=root 
# pass is not needed here, because we have the user root with unix_socket - auth 

mkdir /backups 
# target-dir needs to be empty or not present 
mariabackup --target-dir=/backups/20210120 --backup 
# apply ib_logfile0 to tablespaces 
# after that ib_logfile0 ->  0 bytes 
mariabackup --target-dir=/backups/20210120 --prepare 

## Recover 
systemctl stop mariadb 
mv /var/lib/mysql /var/lib/mysql.bkup 
mariabackup --target-dir=/backups/20200120 --copy-back 
chmod -R mysql:mysql /var/lib/mysql
chmod 755 /var/lib/mysql # otherwice socket for unprivileged user does not work
systemctl start mariadb 
```

## Walkthrough (Redhat/Centos)

```
# user eintrag in /root/.my.cnf
[mariabackup]
user=root 
# pass is not needed here, because we have the user root with unix_socket - auth 
# or generic 
# /etc/my.cnf.d/mariabackup.cnf
[mariabackup]
user=root

mkdir /backups 
# target-dir needs to be empty or not present 
mariabackup --target-dir=/backups/20210120 --backup 
# apply ib_logfile0 to tablespaces 
# after that ib_logfile0 ->  0 bytes 
mariabackup --target-dir=/backups/20210120 --prepare 

## Recover 
systemctl stop mariadb 
mv /var/lib/mysql /var/lib/mysql.bkup 
mariabackup --target-dir=/backups/20200120 --copy-back 
chmod -R mysql:mysql /var/lib/mysql
chmod 755 /var/lib/mysql # otherwice socket for unprivileged user does not work
## important for selinux 
restorecon -vr /var/lib/mysql 
systemctl start mariadb 
```



## Ref. 
https://mariadb.com/kb/en/full-backup-and-restore-with-mariabackup/
