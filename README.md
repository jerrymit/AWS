# AWS
Hosting a WordPress Website on AWS
Overview
This guide details the deployment of a scalable and secure WordPress website on Amazon Web Services (AWS). The setup includes a highly available architecture utilizing multiple AWS services for optimized performance and reliability.

Prerequisites
AWS account
Basic understanding of AWS services such as EC2, RDS, EFS, ELB, and VPC
SSH access to EC2 instances
Domain name (optional for custom domain setup)
Architecture
VPC with public and private subnets in two Availability Zones for improved fault tolerance.
Internet Gateway to enable internet access for the VPC.
NAT Gateway in the public subnet to allow outbound internet access for instances in private subnets.
Application Load Balancer (ALB) to distribute incoming traffic across multiple EC2 instances.
Auto Scaling Group to automatically adjust the number of instances.
RDS MySQL instance for the WordPress database.
EFS for shared WordPress file storage across multiple instances.
Route 53 for domain name management.
Security Groups to control access to EC2 instances and other services.
Deployment Steps
1. Environment Setup
Set up your AWS environment according to the architecture diagram. This includes setting up the VPC, subnets, internet and NAT gateways, route tables, security groups, and IAM roles.

2. Database Setup
Create an RDS MySQL instance in your VPC. Secure your database and note down the database endpoint, username, and password for later use.

3. File System Setup
Set up EFS and mount it to your EC2 instances for shared file storage. Ensure that your security groups allow traffic between your EC2 instances and EFS.

4. Load Balancer and Auto Scaling Setup
Create an Application Load Balancer and an Auto Scaling Group. Configure the ALB to listen on the appropriate port and forward requests to your EC2 instances. Set up the Auto Scaling Group to use your desired launch template and policies.

5. DNS Configuration
If using a custom domain, configure your DNS settings in Route 53 or your domain registrar to point to your ALB.

6. WordPress Deployment
Deploy WordPress using the provided scripts. Customize the scripts as necessary for your environment, particularly the EFS mount points and database details.

Deploy Script
bash
Copy code
#!/bin/bash
# WordPress deployment script

# Update system packages
sudo yum update -y

# Install Apache, PHP, and necessary PHP extensions
sudo yum install -y httpd php php-cli php-curl php-mbstring php-mysqlnd php-xml

# Start and enable Apache
sudo systemctl start httpd
sudo systemctl enable httpd

# Mount EFS
sudo mkdir -p /var/www/html
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport [EFS_DNS_NAME]:/ /var/www/html

# Set proper permissions
sudo chown apache:apache /var/www/html
sudo chmod 2775 /var/www/html
find /var/www/html -type d -exec sudo chmod 2775 {} \;
find /var/www/html -type f -exec sudo chmod 0664 {} \;

# Download and configure WordPress
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
sudo cp -a wordpress/. /var/www/html/
sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
# Remember to configure wp-config.php with your database details

# Restart Apache to apply changes
sudo systemctl restart httpd
7. Finalizing Installation
After deploying WordPress, navigate to your website's URL. Follow the WordPress installation steps to complete the setup.

Security Considerations
Ensure your RDS database is not publicly accessible and is only accessible from your EC2 instances.
Update your security groups to allow the minimum necessary traffic.
Regularly update your WordPress site, plugins, and themes.
Troubleshooting
Verify that your EC2 instances can connect to RDS and EFS.
Ensure your EC2 instances are receiving traffic from the ALB.
Check the security group and network ACL settings if you encounter connectivity issues.
Contributions
Contributions to this project are welcome! Please fork the repository and submit a pull request with your improvements.
