## 🌞 Allumez les VMs et effectuez la conf élémentaire :

```
[root@efrei-xmg4agau1 ~]# ping kvm1.one
PING kvm1.one (10.3.1.11) 56(84) bytes of data.
64 bytes from kvm1.one (10.3.1.11): icmp_seq=1 ttl=64 time=0.577 ms
64 bytes from kvm1.one (10.3.1.11): icmp_seq=2 ttl=64 time=0.396 ms
64 bytes from kvm1.one (10.3.1.11): icmp_seq=3 ttl=64 time=0.353 ms
rc

3 packets transmitted, 3 received, 0% packet loss, time 2065ms
rtt min/avg/max/mdev = 0.353/0.442/0.577/0.097 ms
[root@efrei-xmg4agau1 ~]# ping kvm2.one
PING kvm2.one (10.3.1.12) 56(84) bytes of data.
64 bytes from kvm2.one (10.3.1.12): icmp_seq=1 ttl=64 time=0.644 ms
64 bytes from kvm2.one (10.3.1.12): icmp_seq=2 ttl=64 time=0.376 ms
64 bytes from kvm2.one (10.3.1.12): icmp_seq=3 ttl=64 time=0.308 ms

- kvm2.one ping statistics
3 packets transmitted, 3 received, 0% packet loss, time 2032ms
rtt min/avg/max/mdev = 0.308/0.442/0.644/0.145 ms

kvm1.one ping statistics
```
# I - Frontend
## 🌞 Installer un serveur MySQL
```
[root@frontend ~]# rpm -i mysql80-community-release-el9-5.noarch.rpm
warning: mysql80-community-release-el9-5.noarch.rpm: Header V4 RSA/SHA256 Signature, key ID 3a79bd29: NOKEY

[root@frontend ~]# dnf install mysql-community-server
Last metadata expiration check: 0:00:24 ago on Fri Mar 21 10:18:47 2025.
Dependencies resolved.
```

## 🌞 Démarrer le serveur MySQL
```
[root@frontend ~]# sudo systemctl start mysqld
[root@frontend ~]# sudo systemctl enable mysqld
```

## 🌞 Setup MySQL
```
[root@frontend ~]# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 8.0.41

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'Password123!';
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE USER 'oneadmin' IDENTIFIED BY 'Password123!';
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE DATABASE opennebula;
Query OK, 1 row affected (0.01 sec)

mysql> GRANT ALL PRIVILEGES ON opennebula.* TO 'oneadmin';
Query OK, 0 rows affected (0.00 sec)

mysql> SET GLOBAL TRANSACTION ISOLATION LEVEL READ COMMITTED;
Query OK, 0 rows affected (0.00 sec)
```

# B. OpenNebula
## 🌞 Ajouter les dépôts Open Nebula
```
[root@frontend yum.repos.d]# dnf makecache -y
MySQL 8.0 Community Server                                                               17 kB/s | 2.6 kB     00:00
MySQL Connectors Community                                                               24 kB/s | 2.6 kB     00:00
MySQL Tools Community                                                                    28 kB/s | 2.6 kB     00:00
OpenNebula Community Edition                                                            2.3 kB/s | 833  B     00:00
OpenNebu##la Community Edition                                                             18 kB/s | 3.1 kB     00:00
Importing GPG key 0x906DC27C:
...
```
## 🌞 Configuration OpenNebula
```
DB = [ BACKEND = "mysql",
       SERVER  = "localhost",
       PORT    = 0,
       USER    = "oneadmin",
       PASSWD  = "O_O",
       DB_NAME = "opennebula",
       CONNECTIONS = 25,
       COMPARE_BINARY = "no" ]

```

## 🌞 Créer un user pour se log sur la WebUI OpenNebula
```
sudo su - oneadmin
vi /var/lib/one/.one/one_auth
user:password
```

## 🌞 Démarrer les services OpenNebula
```
[root@frontend ~]# sudo systemctl start opennebula
[root@frontend ~]# sudo systemctl enable opennebula
Created symlink /etc/systemd/system/multi-user.target.wants/opennebula.service → /usr/lib/systemd/system/opennebula.service.
[root@frontend ~]# sudo systemctl enable opennebula-sunstone
Created symlink /etc/systemd/system/multi-user.target.wants/opennebula-sunstone.service → /usr/lib/systemd/system/opennebula-sunstone.service.
[root@frontend ~]# sudo systemctl start opennebula-sunstone
```

# C. Conf système
## 🌞 Ouverture firewall
```
firewall-cmd --add-port=9869/tcp --add-port=22/tcp --add-port=2633/tcp --add-port=4124/tcp --add-port=4124/udp --add-port=29876/tcp --permanent && firewall-cmd --reload
```

# II. Noeuds KVM
# A. KVM
## 🌞 Ajouter des dépôts supplémentaires
```
dnf install -y epel-release
```

## 🌞 Installer KVM
```
dnf install opennebula-node-kvm
```

## 🌞 Démarrer le service libvirtd
```
systemctl start libvirtd
systemctl enable libvirtd
```
# B. Système
## 🌞 Ouverture firewall
```
[root@kvm1 yum.repos.d]# firewall-cmd --add-port=22/tcp --add-port=8472/udp --permanent
success
[root@kvm1 yum.repos.d]# firewall-cmd --reload
```
## 🌞 Handle SSH
```
[oneadmin@frontend ~]$ ssh-keyscan 10.3.1.12 >> ~/.ssh/known_hosts
[oneadmin@frontend ~]$ cat .ssh/id_rsa.pub
[oneadmin@kvm1 .ssh]$ vi authorized_keys
[oneadmin@frontend ~]$ ssh oneadmin@10.3.1.11
Last login: Fri Mar 21 12:26:06 2025 from 10.3.1.10
```

## 🌞 Setup de kvm2.one, à l'identique de kvm1.one excepté :
