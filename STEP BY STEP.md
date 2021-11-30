## Install Java

Install OpenJDK 11
1. Update the package repository to ensure you download the latest software:

sudo yum update
2. Then, install the Java Development Kit with the following command:

sudo yum install java-11-openjdk-devel
The output displays the downloaded and installed JDK, as in the image below:

output displaying downloaded and installed JDK
Install OpenJRE 11
Java Runtime Environment 11 (Open JRE 11) is a subset of OpenJDK. Therefore, both packages are included in the command:

sudo yum install java-11-openjdk

## Install Mysql
- Create New SQL 

On CentOS and RHEL 7 -- 
yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm

On CentOS and RHEL 7/6

yum install mysql-community-server


Get Temporary root Password:

grep 'A temporary password' /var/log/mysqld.log |tail -1

3. Start MySQL Service
After installing rpms use following command to start MySQL Service.

service mysqld start
4. Initial MySQL Configuration
Execute mysql_secure_installation script and follow the wizard. It will prompt for root password. Use the temporary root password got in above step.

/usr/bin/mysql_secure_installation

## Install Nginx

Step One—Add Nginx Repository
To add the CentOS 7 EPEL repository, open terminal and use the following command:

sudo yum install epel-release
Step Two—Install Nginx
Now that the Nginx repository is installed on your server, install Nginx using the following yum command:

sudo yum install nginx
After you answer yes to the prompt, Nginx will finish installing on your virtual private server (VPS).

Step Three—Start Nginx
Nginx does not start on its own. To get Nginx running, type:

sudo systemctl start nginx
If you are running a firewall, run the following commands to allow HTTP and HTTPS traffic:

sudo firewall-cmd --permanent --zone=public --add-service=http 
sudo firewall-cmd --permanent --zone=public --add-service=https
sudo firewall-cmd --reload

## Install Jira
- Config Jira nginx proxy -add port-
- Config /etc/nginx/conf.d/jira.conf - open port's nginx-
### restart service
### Accept selinux, firewall
### Restart Jira
### Access to IP In oder to Acess Jira
