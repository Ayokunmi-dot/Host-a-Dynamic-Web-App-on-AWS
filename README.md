# Dynamic Website on AWS

## Overview
This project demonstrates how to host a dynamic website on AWS using a combination of AWS services and DevOps principles. The infrastructure is designed for high availability, fault tolerance, and security.

## Architecture
The project employs the following AWS resources:

1. **Virtual Private Cloud (VPC)**: Configured with both public and private subnets across two Availability Zones.
2. **Internet Gateway**: Enables connectivity between VPC instances and the wider Internet.
3. **Security Groups**: Implemented as a firewall mechanism.
4. **Multi-AZ Deployment**: Ensures high availability and fault tolerance.
5. **Public Subnets**: Used for NAT Gateway and Application Load Balancer.
6. **EC2 Instance Connect Endpoint**: Provides secure connections to both public and private subnets.
7. **Private Subnets**: Houses web servers (EC2 instances) for enhanced security.
8. **NAT Gateway**: Allows instances in private subnets to access the Internet.
9. **EC2 Instances**: Hosts the dynamic website.
10. **Application Load Balancer (ALB) & Target Group**: Distributes traffic across EC2 instances.
11. **Auto Scaling Group (ASG)**: Manages EC2 instances for scalability and fault tolerance.
12. **AWS Certificate Manager**: Secures communications.
13. **AWS Simple Notification Service (SNS)**: Sends notifications related to Auto Scaling activities.
14. **Route 53**: Handles domain registration and DNS configuration.
15. **Amazon S3**: Stores application code for deployment.

## Deployment Steps
### 1. Setup VPC and Networking
- Create a VPC with public and private subnets.
- Attach an Internet Gateway.
- Configure Security Groups and NAT Gateway.
- Enable EC2 Instance Connect Endpoint.

### 2. Configure EC2 Instances for Dynamic Website
- Deploy EC2 instances in private subnets.
- Install Apache, PHP, and MySQL.
- Configure an Auto Scaling Group.
- Use S3 to store and sync application code.

### 3. Install and Configure Dynamic Web Application
Run the following script on the EC2 instance:
```bash
#!/bin/bash
sudo yum update -y
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd
sudo dnf install -y \
php \
php-pdo \
php-openssl \
php-mbstring \
php-exif \
php-fileinfo \
php-xml \
php-ctype \
php-json \
php-tokenizer \
php-curl \
php-cli \
php-fpm \
php-mysqlnd \
php-bcmath \
php-gd \
php-cgi \
php-gettext \
php-intl \
php-zip

# Install MySQL Server
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
dnf repolist enabled | grep "mysql.*-community.*"
sudo dnf install -y mysql-community-server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Configure Apache for .htaccess
sudo sed -i '/<Directory "/var/www/html">/,/</Directory>/ s/AllowOverride None/AllowOverride All/' /etc/httpd/conf/httpd.conf

# Sync Application Code from S3
S3_BUCKET_NAME=anike-project-web-files
sudo aws s3 sync s3://"$S3_BUCKET_NAME" /var/www/html
cd /var/www/html
sudo unzip shopwise.zip
sudo cp -R shopwise/. /var/www/html/
sudo rm -rf shopwise shopwise.zip

# Set Permissions
sudo chmod -R 777 /var/www/html
sudo chmod -R 777 storage/

# Edit .env File for Database Configuration
sudo vi .env

# Restart Apache
sudo service httpd restart
```



### Architecture Diagram
![Architecture Diagram](Dynamic Web App on AWS.png)

## Deployment Verification
1. Ensure EC2 instances are running and accessible.
2. Verify Auto Scaling Group behavior.
3. Confirm the dynamic website is accessible via the domain name.
4. Check SNS notifications and CloudWatch logs.

## Security Measures
- Used Security Groups for access control.
- Hosted web servers in private subnets.
- Enforced HTTPS using AWS Certificate Manager.
- Utilized IAM roles for EC2 permissions.

## Conclusion
This AWS-based dynamic website deployment ensures scalability, high availability, and security, leveraging AWS best practices and automation techniques.

