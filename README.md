# Deploy Java Microservices Application on VMs using Vagrant

## Introduction
This project demonstrates the deployment of a multi-tier Java microservices application on separate virtual machines using Vagrant IaC tool and VirtualBox.

<img width="627" height="303" alt="image" src="https://github.com/user-attachments/assets/fdde26cf-ab8d-4fbc-ae52-6b9b440aa075" />

## Architecture
The application architecture is composed of 5 microservices, each one will be deployed on a separate VM, they are:

| VM         | Technology             | VM Hostname |
|-----------------|------------------------|-------------|
| Load Balancer   | Nginx                  | `web01`     |
| Application     | Java 11 + Maven + Tomcat| `app01`     |
| Message Queue   | RabbitMQ                | `rmq01`     |
| Cache           | Memcached              | `mc01`      |
| Database        | MariaDB                | `db01`      |

<img width="2048" height="1302" alt="image" src="https://github.com/user-attachments/assets/8d9c68fa-2f3c-4a95-b9e6-da550952c2be" />

## Requirements
- [VirtualBox](https://www.virtualbox.org/)
- [Vagrant](https://www.vagrantup.com/)
- Vagrant plugins.

## Setup and Deployment
- Install VirtualBox.
- Install Vagrant.
- Clone the repository.
- Install Vagrant plugins.
  ```bash
  vagrant plugin install vagrant-hostmanager

- Start creating VMs
  ```bash
  vagrant up

## VMs Provisioning
### Database (db01)
```bash
# Access VM via SSH
vagrant ssh db01
sudo -i

# Update OS with Latest Patches
sudo yum update -y

# Enable EPEL Repository
sudo yum install epel-release -y

# Install MariaDB and Git
sudo yum install git mariadb-server -y

# Start and Enable MariaDB
sudo systemctl start mariadb
sudo systemctl enable mariadb

# Secure MariaDB Installation
sudo mysql_secure_installation

# Create Database and User
mysql -u root -padmin123
CREATE DATABASE accounts;
GRANT ALL PRIVILEGES ON accounts.* TO 'admin'@'%' IDENTIFIED BY 'admin123';
FLUSH PRIVILEGES;
EXIT;

# Download Source Code and Initialize Database
git clone -b main https://github.com/hkhcoder/vprofile-project.git
cd vprofile-project
mysql -u root -padmin123 accounts < src/main/resources/db_backup.sql

# Verify Tables
mysql -u root -padmin123 accounts
Inside MySQL prompt:
SHOW TABLES;
EXIT;

# Restart MariaDB
sudo systemctl restart mariadb
```

### Cache (mc01)
```bash
# Access VM via SSH:
vagrant ssh mc01
sudo -i

# Update OS with latest patches
yum update -y

# Install, start & enable memcache on port 11211
sudo dnf install epel-release -y
sudo dnf install memcached -y
sudo systemctl start memcached
sudo systemctl enable memcached
sudo systemctl status memcached
sed -i 's/127.0.0.1/0.0.0.0/g' /etc/sysconfig/memcached
sudo systemctl restart memcached
```

### Message Queue (rmq01)
```bash
# Access VM via SSH:
vagrant ssh rmq01
sudo -i

# Update OS with latest patches
yum update -y

# Set EPEL Repository
yum install epel-release -y

# Install Dependencies
sudo yum install wget -y
cd /tmp/
dnf -y install centos-release-rabbitmq-38
dnf --enablerepo=centos-rabbitmq-38 -y install rabbitmq-server
systemctl enable --now rabbitmq-server

# Setup access to user test and make it admin
sudo sh -c 'echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config'
sudo rabbitmqctl add_user test test
sudo rabbitmqctl set_user_tags test administrator
sudo systemctl restart rabbitmq-server
```
  
### Application (app01)  
```bash
# Access VM via SSH:
vagrant ssh app01
sudo -i

# Update OS with latest patches
yum update -y

# Set Repository
yum install epel-release -y

# Install Dependencies
dnf -y install java-11-openjdk java-11-openjdk-devel
dnf install git maven wget -y

# Change dir to /tmp
cd /tmp/

# Download & Tomcat Package
wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.75/bin/apache-tomcat-9.0.75.tar.gz
tar xzvf apache-tomcat-9.0.75.tar.gz

# Add tomcat user
useradd --home-dir /usr/local/tomcat --shell /sbin/nologin tomcat

# Copy data to tomcat home dir
cp -r /tmp/apache-tomcat-9.0.75/* /usr/local/tomcat/

# Make tomcat user owner of tomcat home dir
chown -R tomcat.tomcat /usr/local/tomcat

# Setup systemctl command for tomcat
# Create tomcat service file
vi /etc/systemd/system/tomcat.service

# Update the file with below content

[Unit]
Description=Tomcat
After=network.target

[Service]
User=tomcat
WorkingDirectory=/usr/local/tomcat
Environment=JRE_HOME=/usr/lib/jvm/jre
Environment=JAVA_HOME=/usr/lib/jvm/jre
Environment=CATALINA_HOME=/usr/local/tomcat
Environment=CATALINE_BASE=/usr/local/tomcat
ExecStart=/usr/local/tomcat/bin/catalina.sh run
ExecStop=/usr/local/tomcat/bin/shutdown.sh
SyslogIdentifier=tomcat-%i

[Install]
WantedBy=multi-user.target

# Reload systemd files
systemctl daemon-reload

# Start & Enable service
systemctl start tomcat
systemctl enable tomcat

# CODE BUILD & DEPLOY (app01)
# Download Source code
git clone -b main https://github.com/hkhcoder/vprofile-project.git

# Update configuration
cd vprofile-project
vim src/main/resources/application.properties
# Update file with backend server details

# Build code
# Run below command inside the repository (vprofile-project)
mvn install

# Deploy artifact
systemctl stop tomcat
rm -rf /usr/local/tomcat/webapps/ROOT*
cp target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
systemctl start tomcat
chown tomcat.tomcat /usr/local/tomcat/webapps -R
systemctl restart tomcat
```
### Load Balancer (web01)
  ```bash
#Access VM via SSH:
vagrant ssh web01
sudo -i

# Update OS with latest patches
apt update
apt upgrade

# Install nginx
apt install nginx -y

# Create Nginx conf file
vi /etc/nginx/sites-available/vproapp

# Update with below content
upstream vproapp {
server app01:8080;
}
server {
listen 80;
location / {
proxy_pass http://vproapp;
}
}

# Remove default nginx conf
rm -rf /etc/nginx/sites-enabled/default

# Create link to activate website
ln -s /etc/nginx/sites-available/vproapp /etc/nginx/sites-enabled/vproapp

# Restart Nginx
systemctl restart nginx
```

## Packaging Vagrant Boxes
- Packaging each VM, i.e., converting it to a Vagrant Box:
  ```bash
    vagrant package db01 --output web01.box
    vagrant package mc01 --output mc01.box
    vagrant package rmq01 --output rmq01.box
    vagrant package app01 --output app01.box
    vagrant package web01 --output web01.box
  ```
## Uploading Boxes to Vagrant Cloud
- Log in to Vagrant Cloud.
- Upload the ".box" file for each VM.

### Built Boxes
- [web01](https://portal.cloud.hashicorp.com/services/vagrant/registries/multi-tier-java-web-app-project1-boxes/boxes/web01?project_id=f92dac97-833c-445f-b97f-2d0948d624c9)
- [app01](https://portal.cloud.hashicorp.com/services/vagrant/registries/multi-tier-java-web-app-project1-boxes/boxes/app01?project_id=f92dac97-833c-445f-b97f-2d0948d624c9)
- [rmq01](https://portal.cloud.hashicorp.com/services/vagrant/registries/multi-tier-java-web-app-project1-boxes/boxes/rmq01?project_id=f92dac97-833c-445f-b97f-2d0948d624c9)
- [mc01](https://portal.cloud.hashicorp.com/services/vagrant/registries/multi-tier-java-web-app-project1-boxes/boxes/mc01?project_id=f92dac97-833c-445f-b97f-2d0948d624c9)
- [db01](https://portal.cloud.hashicorp.com/services/vagrant/registries/multi-tier-java-web-app-project1-boxes/boxes/db01?project_id=f92dac97-833c-445f-b97f-2d0948d624c9)

