Step 1: Install Java
Install OpenJDK 11 on RHEL 8 / CentOS 8 / Rocky Linux 8
OpenJDK is a free and open-source implementation of the Java Platform, Standard Edition licensed under the GNU General Public License version 2.
```sudo yum -y install  java-11-openjdk java-11-openjdk-devel```
To check, use command
``` java -version ```
and wanna see Repo, use command"
``` which java
/usr/bin/java```

set Java environment variables

```sudo tee /etc/profile.d/java11.sh <<EOF
export JAVA_HOME=\$(dirname \$(dirname \$(readlink \$(readlink \$(which javac)))))
export PATH=\$PATH:\$JAVA_HOME/bin
export CLASSPATH=.:\$JAVA_HOME/jre/lib:\$JAVA_HOME/lib:\$JAVA_HOME/lib/tools.jar
EOF```
Source the file to start using it without logging out.

```source /etc/profile.d/java11.sh```

## Install Java SE Development Kit 11 (JDK 11) on RHEL 8 / CentOS 8 / Rocky Linux 8
Download the latest release of JDK 11.

```wget --no-check-certificate -c --header "Cookie: oraclelicense=accept-securebackup-cookie" https://download.oracle.com/otn-pub/java/jdk/11.0.12%2B8/f411702ca7704a54a79ead0c2e0942a3/jdk-11.0.12_linux-x64_bin.rpm```
Then install the package with the rpmcommand

```$ sudo rpm -Uvh jdk-11.0.12_linux-x64_bin.rpm```

```warning: jdk-11.0.12_linux-x64_bin.rpm: Header V3 RSA/SHA256 Signature, key ID ec551f03: NOKEY
Verifying...                          ################################# [100%]
Preparing...                          ################################# [100%]
Updating / installing...
   1:jdk-11.0.12-2000:11.0.12-ga      ################################# [100%]
Confirm Java version installed```

```$ java -version```
```java version "11.0.12" 2021-07-20 LTS
Java(TM) SE Runtime Environment 18.9 (build 11.0.12+8-LTS-237)
Java HotSpot(TM) 64-Bit Server VM 18.9 (build 11.0.12+8-LTS-237, mixed mode)```

###Step 2: Install MySQL 5.7 on CentOS 
 Add MySQL repository
Disable MySQL default AppStream repository:
```sudo dnf remove @mysql
sudo dnf module reset mysql && sudo dnf module disable mysql```
There is no MySQL repository for EL 8, so we’ll use EL 7 repository instead. Create a new repository file.

```sudo vi /etc/yum.repos.d/mysql-community.repo```
Paste below data into the file.
```[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/$basearch/
enabled=1
gpgcheck=0

[mysql-connectors-community]
name=MySQL Connectors Community
baseurl=http://repo.mysql.com/yum/mysql-connectors-community/el/7/$basearch/
enabled=1
gpgcheck=0

[mysql-tools-community]
name=MySQL Tools Community
baseurl=http://repo.mysql.com/yum/mysql-tools-community/el/7/$basearch/
enabled=1
gpgcheck=0```


### Install MySQL 5.7 on CentOS 
Once the repository has been added, now install MySQL 5.7 on CentOS 8 / RHEL 8.

Disable MySQL 8 repository:

```sudo dnf config-manager --disable mysql80-community```
Then enable channel for MySQL 5.7.

```sudo dnf config-manager --enable mysql57-community```
Then install MySQL 5.7 on CentOS 8 / RHEL 8:

```sudo dnf install mysql-community-server```
Press y to start the installation.
```Last metadata expiration check: 0:02:41 ago on Mon 06 Jan 2020 08:54:52 PM EAT.
Dependencies resolved.
========================================================================================================================================================
 Package                                   Arch                      Version                                 Repository                            Size
========================================================================================================================================================
Installing:
 mysql-community-server                    x86_64                    5.7.28-1.el7                            mysql57-community                    199 M
Installing dependencies:
 ncurses-compat-libs                       x86_64                    6.1-7.20180224.el8                      BaseOS                               331 k
 mysql-community-client                    x86_64                    5.7.28-1.el7                            mysql57-community                     43 M
 mysql-community-common                    x86_64                    5.7.28-1.el7                            mysql57-community                    311 k
 mysql-community-libs                      x86_64                    5.7.28-1.el7                            mysql57-community                    4.2 M

Transaction Summary
========================================================================================================================================================
Install  5 Packages

Total download size: 247 M
Installed size: 1.0 G
Is this ok [y/N]: y```
Check package rpm details to confirm it is 5.7.

```$ rpm -qi mysql-community-server ```
```Name        : mysql-community-server
Version     : 5.7.28
Release     : 1.el7
Architecture: x86_64
Install Date: Mon 06 Jan 2020 08:58:52 PM EAT
Group       : Applications/Databases
Size        : 910635041
License     : Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved. Under GPLv2 license as shown in the Description field.
Signature   : DSA/SHA1, Mon 30 Sep 2019 11:05:08 AM EAT, Key ID 8c718d3b5072e1f5
Source RPM  : mysql-community-5.7.28-1.el7.src.rpm
Build Date  : Fri 27 Sep 2019 11:11:06 AM EAT
Build Host  : loki02.no.oracle.com
Relocations : (not relocatable)
Packager    : MySQL Release Engineering <mysql-build@oss.oracle.com>
Vendor      : Oracle and/or its affiliates
URL         : http://www.mysql.com/
Summary     : A very fast and reliable SQL database server```
### Step 3: Configure MySQL 5.7 on CentOS 8 / RHEL 8
2.1 – After the installation, start mysqld service.
```sudo systemctl enable --now mysqld.service```
2.2 – Copy the generated random password for the root user

```sudo grep 'A temporary password' /var/log/mysqld.log |tail -1```
Take note of the printed password:

2020-01-06T18:06:19.947403Z 1 [Note] A temporary password is generated for root@localhost: AS*5Rx%YY5+c
2.3 – Start MySQL Secure Installation to change the root password, Disallow root login remotely, remove anonymous users and remove test database.
```$ sudo mysql_secure_installation```
Securing the MySQL server deployment.
Enter password for user root:
Authenticate with your generated temporary password. This will ask you to set a new password for the root user.

Change the password for root ? ((Press y|Y for Yes, any other key for No) : Yes

New password: 
Re-enter new password: 

Estimated strength of the password: 100 
Do you wish to continue with the password provided?: Yes

Remove anonymous users?: Yes
Success.

Disallow root login remotely? : Yes
Success.

Remove test database and access to it? : Yes
 - Dropping test database...
Success.
 - Removing privileges on test database...
Success.

Reload privilege tables now? (Press y|Y for Yes) : Yes
Success.

All done!
You can use online password generator to get a complex password.

###2.4 – Connect to MySQL Database as root user and create a test database.

``$ mysql -u root -p```
Enter password: <Enter Root Password>
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 15
Server version: 5.7.28 MySQL Community Server (GPL)

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

```mysql> SELECT VERSION();
+-----------+
| VERSION() |
+-----------+
| 5.7.28    |
+-----------+
1 row in set (0.00 sec)

mysql> QUIT
Bye
2.5 – Create a test database and user:

mysql> CREATE DATABASE test_db;
Query OK, 1 row affected (0.09 sec)

mysql> CREATE USER 'test_user'@'localhost' IDENTIFIED BY "Strong34S;#";
Query OK, 0 rows affected (0.04 sec)

mysql> GRANT ALL PRIVILEGES ON test_db.* TO 'test_user'@'localhost';
Query OK, 0 rows affected (0.02 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.02 sec)

This test database and user can be dropped by running:

mysql> DROP DATABASE test_db;
Query OK, 0 rows affected (0.14 sec)

mysql> DROP USER 'test_user'@'localhost';
Query OK, 0 rows affected (0.11 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.01 sec)

mysql> QUIT
Bye```
