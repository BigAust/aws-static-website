# Host a Static Website on AWS

This project demonstrates how to host a static HTML web app on AWS using various AWS services and resources. The infrastructure is designed for high availability, fault tolerance, and scalability.

## Architecture Overview

The infrastructure for hosting the static website includes the following components:

1. **Virtual Private Cloud (VPC)**: Configured with both public and private subnets across two different availability zones.
2. **Internet Gateway**: Facilitates connectivity between VPC instances and the wider Internet.
3. **Security Groups**: Act as a network firewall mechanism.
4. **Multiple Availability Zones**: Enhance system reliability and fault tolerance.
5. **Public Subnets**: Used for infrastructure components like the NAT Gateway and Application Load Balancer.
6. **EC2 Instance Connect Endpoint**: Allows secure connections to assets within both public and private subnets.
7. **Private Subnets**: Where web servers (EC2 instances) are placed for enhanced security.
8. **NAT Gateway**: Enables instances in both private Application and Data subnets to access the Internet.
9. **EC2 Instances**: Host the website.
10. **Application Load Balancer**: Distributes web traffic evenly to an Auto Scaling Group of EC2 instances across multiple Availability Zones.
11. **Auto Scaling Group**: Automatically manages EC2 instances, ensuring website availability, scalability, fault tolerance, and elasticity.
12. **GitHub**: Stores web files for version control and collaboration.
13. **Certificate Manager**: Secures application communications.
14. **Simple Notification Service (SNS)**: Alerts about activities within the Auto Scaling Group.
15. **Route 53**: Registers the domain name and sets up a DNS record.

## Deployment Steps

Follow these steps to deploy the static website on AWS:

### Prerequisites

- AWS account
- Domain name registered (using Route 53 or another registrar)
- GitHub repository containing your static website files

### AWS Infrastructure Setup

1. **Create a VPC** with public and private subnets in two availability zones.
2. **Deploy an Internet Gateway** and attach it to the VPC.
3. **Set up Security Groups** to control inbound and outbound traffic to your instances.
4. **Deploy a NAT Gateway** in the public subnet to allow instances in private subnets to access the Internet.
5. **Launch EC2 Instances** in the private subnet to host your website.
6. **Set up an Application Load Balancer** in the public subnet and configure it to distribute traffic to the EC2 instances.
7. **Configure an Auto Scaling Group** to manage the EC2 instances automatically.
8. **Use Certificate Manager** to create and manage SSL/TLS certificates for your domain.
9. **Set up SNS** for notifications about Auto Scaling Group activities.
10. **Register your domain and configure Route 53** to route traffic to your Application Load Balancer.

### EC2 Instance Setup

Use the following user data script to configure your EC2 instances:

```bash
#!/bin/bash
# Switch to the root user to gain full administrative privileges
sudo su
# Update all installed packages to their latest versions
yum update -y
# Install Apache HTTP Server
yum install -y httpd
# Change the current working directory to the Apache web root
cd /var/www/html
# Install Git
yum install git -y
# Clone the project GitHub repository to the current directory
git clone https://github.com/aosnotes77/host-a-static-website-on-aws.git
# Copy all files, including hidden ones, from the cloned repository to the Apache web root
cp -R host-a-static-website-on-aws/. /var/www/html/
# Remove the cloned repository directory to clean up unnecessary files
rm -rf host-a-static-website-on-aws
# Enable the Apache HTTP Server to start automatically at system boot
systemctl enable httpd
# Start the Apache HTTP Server to serve web content
systemctl start httpd
