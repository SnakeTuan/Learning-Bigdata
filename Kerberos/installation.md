# This document demonstrates how to install and configure two kerberos KDCs as master/slave


## Environment
```
ubuntu 22.04
```


## Servers:

We gonna install on 2 servers:

| no  | hostname | ip            | role       |
| --- | -------- | ------------- | ---------- |
| 1   | k-master | 192.168.55.11 | kdc master |
| 2   | k-slave  | 192.168.55.12 | kdc slave  |


## Ports used

Kerberos uses UDP protocol primarily, although it falls back to TCP for large Kerberos tickets

| no  | service name   | port            | comment                         |
| --- | -------------- | --------------- | ------------------------------- |
| 1   | kerberos       | 88 udp and tcp  | kerberos ticket service         |
| 2   | kerberos-admin | 749 udp and tcp | kerberos administration service |
| 3   | kpropd         | 754 tcp         | for database propagation        |


## Map ip addresses to hostnames

Run the command: ``` sudo nano /etc/hosts ``` and add mapping configuration:

```
192.168.55.11   k-master
192.168.55.12   k-slave 
```


## Installation

### install, configure and create a realm

| no | operation                                                                                                                                  | user | server            | comment                                                                                                          |
|----|--------------------------------------------------------------------------------------------------------------------------------------------|------|-------------------|------------------------------------------------------------------------------------------------------------------|
| 1  | ```sudo DEBIAN_FRONTEND=noninteractive apt-get install krb5-user krb5-kdc krb5-admin-server -y```                                          | root | k-master, k-slave | when run ```sudo systemctl status krb5-kdc```, the service will not start because it doesn't have a database yet |
| 2  | ```sudo nano /etc/krb5.conf```                                                                                                             | root | k-master, k-slave | example configuration in the folder /configurations/krb5.conf                                                    |
| 3  | ```sudo nano /etc/kdc.conf```                                                                                                              | root | k-master, k-slave | example configuration in the folder /configurations/kdc.conf                                                     |
| 4  | ```sudo nano /lib/systemd/system/krb5-kdc.service```                                                                                       | root | k-master, k-slave | add the /var/log at the end of the ReadWriteDirectories line so kdc can write log into that folder               |
| 5  | ```sudo krb5_newrealm```                                                                                                                   | root | k-master          | create the realm, don't forget the password (this one is for restore the db when the stash file is lost)         |
| 6  | ```sudo systemctl daemon-reload```                                                                                                         | root | k-master          |                                                                                                                  |
| 7  | ```sudo systemctl restart krb5-kdc``` </br>```systemctl status krb5-kdc``` </br>```systemctl enable krb5-kdc```                            | root | k-master          | start and check if the status for service is active or not                                                       |
| 8  | ```sudo systemctl restart krb5-admin-server``` </br>```systemctl status krb5-admin-server``` </br>```systemctl enable krb5-admin-server``` | root | k-master          | start and check if the status for service is active or not                                                       |

### Preparation for database propagation

#### Create principals and keytabs

During the database propagation, kdc uses the special principals (the host principals) for authorization between the master and slave, so we gonna create them:

| no | operation                                                               | user | server   | comment                                                                |
| -- |-------------------------------------------------------------------------| ---- |----------|------------------------------------------------------------------------|
| 1  | ```sudo kadmin.local -q 'addprinc -randkey host/k-master@MY.CLUSTER'``` | root | k-master | create the principal with random key (key = password of the principal) |
| 2  | ```sudo kadmin.local -q 'addprinc -randkey host/k-slave@MY.CLUSTER'```  | root | k-master | create the principal with random key                                   |
| 3  | ```sudo kadmin.local -q 'ktadd host/k-master@MY.CLUSTER'```             | root | k-master | store the principal's key in the master's default keytab file          |
| 4  | ```sudo kadmin.local -q 'ktadd host/k-slave@MY.CLUSTER'```              | root | k-master | store the principal's key in the master's default keytab file          |

#### Add configuration

| no | operation                                                                      | user | server              | comment                                                                              |
| -- |--------------------------------------------------------------------------------| ---- |---------------------|--------------------------------------------------------------------------------------|
| 1  | ```sudo nano /etc/krb5kdc/kadm5.acl```                                         | root | k-master, k-slave   | saving the rule for admin kdc principals                                             |
| 2  | ```sudo nano /etc/krb5kdc/kpropd.acl```                                        | root | k-master, k-slave   | saving the principals used for the propagation                                       |
| 3  | copy the file /etc/krb5kdc/stash from k-master to k-slave (should use distcp)  | root | k-master            | store the key of the database (need this to run kdc service)                         |
| 4  | copy the file /etc/krb5.keytab from k-master to k-slave  (should use distcp)   | root | k-master            | store the principal's key in the master's default keytab file that we created before |


### Start database propagation

| no. | operation                                                                                                              | user | server   | comment                                       |
|-----|------------------------------------------------------------------------------------------------------------------------|------|----------|-----------------------------------------------|
| 1   | ```sudo apt install krb5-kpropd -y```                                                                                  | root | k-slave  | install kpropd for db propagation  	          |  
| 2   | ```sudo systemctl status krb5-kpropd```                                                                                | root | k-slave  | check is the service's status is active       |
| 3   | ```sudo kdb5_util dump /var/lib/krb5kdc/slave_trans```                                                                 | root | k-master | dump kdc database                             |  
| 4   | ```kprop -f /var/lib/krb5kdc/slave_trans 192.168.55.12```                                                              | root | k-master | propagate database from master to slave       |  
| 5   | ```sudo systemctl daemon-reload``` </br>```sudo systemctl restart krb5-kdc```</br>```sudo systemctl status krb5-kdc``` | root | k-slave  | check if the status is in active state or not |  

so that is the basic steps for propagating the kdc database manually, you may want to do the propagation after you change the kdc database (like creating new principals)

and you can write a script for propagating db automatically

## Testing the KDC cluster

test 1: can get the ticket from the kdc service (you can do it on another machine by installing the kdc client and )

| no | operation                                             | user | server            | comment                                                                                  |
|----|-------------------------------------------------------|------|-------------------|------------------------------------------------------------------------------------------|
| 1  | ```sudo kadmin.local -q 'addprinc test@MY.CLUSTER'``` | root | k-master          | create the principal for testing (will ask for password since there is no flag -randkey) |
| 2  | ```kinit test@MY.CLUSTER```                           | root | k-master, k-slave | try to get the ticket                                                                    |
| 3  | ```klist``` <br/> ```kdestroy```                      | root | k-master, k-slave | show the ticket, and destroy it                                                          |
|    |                                                       |      |                   |                                                                                          |

where the logs saved:
```
/var/log/kadmin.log
/var/log/kadmind.log
/var/log/krb5kdc.log
```

## related documentationaa:

https://web.mit.edu/kerberos/krb5-1.12/doc/index.html

https://uit.stanford.edu/service/kerberos/firewalls#:~:text=Kerberos%20is%20primarily%20a%20UDP,replies%20from%20the%20Kerberos%20servers.

https://web.mit.edu/kerberos/krb5-1.5/krb5-1.5.4/doc/krb5-install/Introduction.html#Introduction

https://web.mit.edu/Kerberos/krb5-1.5/krb5-1.5.4/doc/krb5-install/Set-Up-the-Slave-KDCs-for-Database-Propagation.html

https://www.oreilly.com/library/view/kerberos-the-definitive/0596004036/ch02s04s01s01.html

https://docs.redhat.com/en/documentation/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/Configuring_a_Kerberos_5_Server.html#Setting_Up_Secondary_KDCs