# AWS Three-Tier Architecture Deployment for Student Registration Application
# INTRODUCTION 
This project demonstrates the deployment of a secure and scalable Three-Tier Architecture on AWS using Amazon Linux, Nginx, Apache Tomcat, and Amazon RDS MariaDB.

The application is a Java-based Student Registration System deployed on Apache Tomcat. User requests are routed through an Nginx Reverse Proxy hosted in a public subnet, while the application server and database resources remain isolated in private subnets for enhanced security.
# Architecture Diagram
# Architecture Flow
               User
                │
                ▼
    Nginx Reverse Proxy Server
          (Public Subnet)
                │
                ▼
    Apache Tomcat Application Server
          (Private Subnet)
                │
                ▼
       Amazon RDS MariaDB
         (Database Layer)

# AWS Services Used
## Service	
* Amazon VPC->	Network Isolation
* Public Subnet->Hosts Nginx Proxy Server
* Private Subnet->	Hosts Application Server
* Internet Gateway->	Internet Connectivity
* NAT Gateway-> Internet Access for Private Resources
* Route Tables	-> Network Routing
* Security Groups	->Traffic Control
* EC2 Instances	->Proxy, App and DB Management Servers
# Technology Stack
## Operating System
* Amazon Linux
## Web Server
* Nginx
## Application Server
* Apache Tomcat
## Programming Language
* Java
## Database
* MariaDB (Amazon RDS)
# Infrastructure Setup
## EC2 Instance 1 - Proxy Server
### Configuration
* Amazon Linux
* Nginx Installed
* Located in Public Subnet
* Accessible from Internet
### Purpose

Acts as a Reverse Proxy and forwards requests to the Application Server.
## EC2 Instance 2 - Application Server
### Configuration
* Amazon Linux
* Java Installed
* Apache Tomcat Installed
* Student Registration Application Deployed
### Purpose

Hosts the Java-based Student Registration Application.
## EC2 Instance 3 - Database Management Server
### Configuration
* Amazon Linux
* MariaDB Client Installed
### Purpose

Used to connect and manage Amazon RDS MariaDB.

## Amazon RDS
### Configuration
* MariaDB Engine
* Private Access
* Integrated with Application Server
### Purpose

Stores Student Registration Records securely.
# Deployment Steps
## Step 1: Create VPC
* Create a custom VPC
* Enable DNS Resolution
* Enable DNS Hostnames
![alt text](imgs/create_vpc.png)

## Step 2: Create Three Subnets
### Public Subnet

Used for:

* Nginx Proxy Server
* NAT Gateway
### Private Subnet 1

Used for:

* Application Server
### Private Subnet 2
Used for:

* Database Management Server
* Amazon RDS
![alt text](imgs/three_subnets.png)
## Step 3: Configure Internet Gateway
* Create Internet Gateway
* Attach Internet Gateway to VPC
![alt text](imgs/internet_gatewaye_create.png)

## Step 4: Create NAT Gateway
* Allocate Elastic IP
* Create NAT Gateway in Public Subnet
![alt text](imgs/nat_gateway_connect.png)

## Step 5: Configure Route Tables
### Public Route Table

Route:<br>

0.0.0.0/0 → Internet Gateway<br>

Associate with Public Subnet.<br>

![alt text](imgs/adding_entry_of_igw_in_private_rt.png)
### Private Route Table

Route:<br>

0.0.0.0/0 → NAT Gateway<br>
![alt text](imgs/adding_nat_entry_in_private_rt.png)
Associate with both Private Subnets.<br>

![alt text](imgs/nat_gateway_associate_sto_both_privatesub.png)

## Step 6: Configure Security Groups
### Proxy Server Security Group

Allow:

* SSH (22)
* HTTP (80)
* HTTPS (443)
### Application Server Security Group

Allow:

* SSH (22)
* Port 8080 from Proxy Server
### Database Security Group

Allow:

* Port 3306 from Application Server and Database Management Server
## Step 7: Launch Proxy Server EC2
![alt text](imgs/connect_proxy_server.png)
## Copy the key
![alt text](imgs/copying_key_to_ec2-user.png)
## Install Nginx

```bash
sudo yum update -y
sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```
![alt text](imgs/installing_nginx_on_proxyserver.png)

## Step 8: Launch Application Server EC2
![alt text](imgs/connect_app_server.png)

### Install Java:

```bash 
sudo yum install java-17-amazon-corretto -y
java -version 
```
![alt text](imgs/instatlation_javaon_app_server.png)

### Install Apache Tomcat9.
![alt text](imgs/instalation_of_tomcat9.png)

Deploy Student Registration Application  file into:<br>
![alt text](imgs/d_tar_tomcat_9_in_opt.png)

/opt/tomcat/webapps/<br>

Start Tomcat Service.<br>
## Step 9: Launch Database Management Server
![alt text](imgs/conecct_to_db_server.png)

Install MariaDB Client:
```bash

sudo yum install mariadb105-server -y
```
![alt text](imgs/instalation_of_mariadb_on_db_server.png)
![alt text](imgs/three_instances.png)
## Step 10: Create Amazon RDS MariaDB
* Select MariaDB Engine
* Configure Database Credentials
* Place RDS in Private Subnet
* Attach Security Group
![alt text](imgs/creating_database_2.png)
![alt text](imgs/creating_database_copied_query.png)
### Connect to RDS:
```bash
mysql -h <RDS-ENDPOINT> -u admin -p
```
![alt text](imgs/connect_rds_with_db_server.png)
Create Database and Tables.
## Step 11: Configure Application Database Connectivity

Update application configuration to connect with Amazon RDS.<br>

Example:
```bash
db.url=jdbc:mysql://<RDS-ENDPOINT>:3306/studentapp
db.username=admin
db.password=********
```
![alt text](imgs/configuring_context.xml.png)
## Step 12: Configure Nginx Reverse Proxy

## Configuration:
![alt text](imgs/go_into_nginx_conf_directory.png)
server {
    listen 80;

    location / {
        proxy_pass http://<APP_SERVER_PRIVATE_IP>:8080;
    }
}
![alt text](imgs/adding_server_vlog_into_the_nginx_conf_file_2.png)
## Install Connector
![alt text](imgs/install_connector.png)
### Restart Nginx:
```bash
sudo systemctl restart nginx
```
### Restart Tomcat
![alt text](imgs/tomcat_restart.png)
## Step 13: Test the Application

Access the application using:
```bash
http://<Proxy-Server-Public-IP>
```
Verify:

* Student Registration Form Loads Successfully
* Data Submission Works
* Records Stored in Amazon RDS
![alt text](imgs/output_of_registration_form.png)
![alt text](imgs/output_of_form_filled_with_shravani.png)
![alt text](imgs/data_stored.png)
![alt text](imgs/database_stored_in_database.png)
# Application Workflow
1. User accesses the Student Registration Application.
2. Request reaches Nginx Proxy Server.
3. Nginx forwards request to Apache Tomcat.
4. Tomcat processes the request.
5. Application communicates with Amazon RDS MariaDB.
6. Student information is stored in the database.
7. Response is returned to the user.

# Security Implementation
* Public and Private Subnet Segregation
* Reverse Proxy Architecture
* Controlled Access Using Security Groups
* Database Layer Isolated in Private Subnet
* NAT Gateway for Secure Internet Access

# Project Outcome

Successfully deployed a production-style Three-Tier Architecture on AWS using Nginx, Apache Tomcat, Java, and Amazon RDS MariaDB. The architecture follows cloud networking and security best practices by separating the web, application, and database layers.
# Learning Outcomes
* AWS Networking Fundamentals
* VPC and Subnet Design
* Internet Gateway and NAT Gateway Configuration
* EC2 Instance Management
* Apache Tomcat Deployment
* Nginx Reverse Proxy Setup
* Amazon RDS Integration
* Security Group Configuration
* Java Web Application Deployment
* Three-Tier Architecture Design
# Acknowledgement

Special Thanks to Trupti Ma'am for her valuable guidance, continuous support, and mentorship throughout this project.
# Author

Shravani Jadhav<br>

DevOps Engineer | AWS Cloud Enthusiast | Linux Administrator