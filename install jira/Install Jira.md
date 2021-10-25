### 🚀 Topics covered: **`Jira on Centos`** ☁️

# Jira requires Java installed in your CentOS
# Jira doesn’t work with MySQL 8 database server, install MySQL 5.7

---
**Author:** *Doan Van Phuc* 
**Date of issue**: *Sep 25th 2021*
> Welcome back!
## TABLE OF CONTENTS ✔✔
**Prepared**🐱‍🐉🐱‍🐉
- Step 1: Install Java
- Step 2: Install MySQL 5.7 database Server


*Edit the MySQL 5.7 configuration server section to add below lines.*


```
$ sudo vi /etc/my.cnf

[mysqld]
default-storage-engine=INNODB
character_set_server=utf8mb4
innodb_default_row_format=DYNAMIC
innodb_large_prefix=ON
innodb_file_format=Barracuda
innodb_log_file_size=2G
sql_mode = NO_AUTO_VALUE_ON_ZERO
```
**Restart mysqld service:**

```
sudo systemctl restart mysqld
```



![local (5)](https://user-images.githubusercontent.com/83824403/138641567-a92879d0-d244-4d68-8443-02db346bbdf5.png)




**Once the database server is installed, login as root user and create database and user for `Jira`**

```
$ mysql -u root -p

CREATE DATABASE jira CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;

CREATE USER 'jira'@'localhost' IDENTIFIED BY 'Str0ngDBP@ssw%rd';

GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,REFERENCES,ALTER,INDEX on jira.* TO 'jira'@'localhost' IDENTIFIED BY 'Abc1234@';

FLUSH PRIVILEGES;

QUIT;

```
## Step 3 Download and `Install JIRA` on CentOS  🎆🎆🎆
 - Download JIRA’s binary file. Check the latest version on the Jira Downloads page.

```
sudo yum -y install wget
wget https://product-downloads.atlassian.com/software/jira/downloads/atlassian-jira-software-8.19.1-x64.bin -O atlassian-jira-software.bin
```
- Give the `file execution` bit:

```
chmod +x atlassian-jira-software.bin
```

#### Now start the installer:

```
sudo ./atlassian-jira-software.bin
```
- Press *`<Enter>`* key to start Jira setup.

```
  Unpacking JRE ...
Starting Installer ...

This will install Jira Software 8.19.1 on your computer.
OK [o, Enter], Cancel [c]
Click Next to continue, or Cancel to exit Setup.
```
  **And choose and follow to tutor in screen**
  
 ### Install `Java MySQL` 🔑🔑  connector:
  
  ```
  wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.18.zip
unzip mysql-connector-java-8.0.18.zip
sudo cp mysql-connector-java-8.0.18/mysql-connector-java-8.0.18.jar /opt/atlassian/jira/lib
  ```
-  Restart Jira:

```
  sudo /etc/init.d/jira stop
sudo /etc/init.d/jira start
  ```
  ## Step 4: Configure `JIRA Nginx Proxy` 🗝🗝  on CentOS
  - Add MySQL repository
- Disable MySQL default AppStream repository:

``` 
  sudo dnf remove @mysql
sudo dnf module reset mysql && sudo dnf module disable mysql
  ```
 - And then, we install: 
  
  ```
  sudo yum -y install nginx
sudo systemctl enable --now nginx
  ```
  - Configure the HTTP Connector
Edit the Jira server configuration file.

```
  sudo vi /opt/atlassian/jira/conf/server.xml
  ```
  - `Locate this code segment`:
  
 ```
  <Connector port="8080" relaxedPathChars="[]|" relaxedQueryChars="[]|{}^&#x5c;&#x60;&quot;&lt;&gt;"
                   maxThreads="150" minSpareThreads="25" connectionTimeout="20000" enableLookups="false"
                   maxHttpHeaderSize="8192" protocol="HTTP/1.1" useBodyEncodingForURI="true" redirectPort="8443"
                   acceptCount="100" disableUploadTimeout="true" bindOnInit="false" scheme="http"
- And add the proxyName and proxyPort elements (replacing them with the appropriate properties)
             and another connector below – this is used for troubleshooting to bypass the proxy:

#### proxyName="`localhost`" proxyPort="80"/>

  ```
  Also add these new lines for bypassing proxy.
  <!-- Standard HTTP Connector -->
       <Connector port="8082" maxThreads="150" minSpareThreads="25" connectionTimeout="20000" enableLookups="false"
                  maxHttpHeaderSize="8192" protocol="HTTP/1.1" useBodyEncodingForURI="true"
                  redirectPort="8443" acceptCount="100" disableUploadTimeout="true"/>     
  ```
  ```
 ### Don’t forget to replace `localhost in your configurations`. Your configs should look like below: 😂😂😂
 
 
  if we are going together in this step. We will done it!! 
  
  
  💕
  
![local (3)](https://user-images.githubusercontent.com/83824403/138640635-bf703b5a-8f8a-4169-afc3-ccb97bccac18.png)

  
- **`Restart Jira` :🛴🛴🛴**


 ```
  sudo /etc/init.d/jira stop
sudo /etc/init.d/jira start
  ```
 - Configure Nginx
**
  Create a new Nginx configuration file for Jira.
  **

- sudo vi /etc/nginx/conf.d/jira.conf
  ***Update the Nginx settings to have the below server `(replacing localhost` with the FQDN and localhost with the hostname of the server if Nginx sits in a different server):
  ***
  
  ```
```
  server {
    listen 80;
    server_name jira.computingforgeeks.com;
    location / {
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 
        proxy_pass http://localhost:8080;
        client_max_body_size 10M;
    }
}
  
  ```
- **`Disable`** Nginx and restart Jira and Nginx

```
sudo setenforce 0
sudo sed -i 's/^SELINUX=.*/SELINUX=permissive/g' /etc/selinux/config
sudo  /etc/init.d/jira stop
sudo  /etc/init.d/jira start
sudo systemctl restart nginx
  
  ```
  
 ![jara8 (2)](https://user-images.githubusercontent.com/83824403/138640018-48d0156d-4f98-4309-bb98-97cc646309c8.png)
 
  
  
  
  
  
 ### Confirm the status of the two services:
  
  ![local (4)](https://user-images.githubusercontent.com/83824403/138639837-05b3f957-c281-4656-b3ca-80b88695880d.png)
  
- **`Allow http port`** in the firewall:

```
  ```
  sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload
  ``

  
  
  
  
  
 Now, ***`enjoy`*** with Jira 😎😎😎
  
  
![jira done (2)](https://user-images.githubusercontent.com/83824403/138640084-476db8d0-5513-4ae6-97c7-6c2eba8b96d1.jpg)




  ### EX: Select “I’ll set it up myself“.
  
  
  
  
  
  ![jira (2)](https://user-images.githubusercontent.com/83824403/138640185-430ed512-a73b-493a-9625-f061c8a46b4f.png)

  
  
 ## ok, we're done. Now enjoy it ✈✈✈ ##
