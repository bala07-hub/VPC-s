AWS Production-Grade VPC Implementation
This project demonstrates how to deploy a secure, highly available application using a Public/Private subnet architecture within an AWS VPC.

1. Architecture Overview
The setup follows AWS best practices for security and high availability:

Multi-AZ Deployment: Resources are spread across two Availability Zones (AZs).

Public Subnets: Host the Application Load Balancer (ALB) and NAT Gateways.

Private Subnets: Host the EC2 application servers (no direct internet access).

NAT Gateway: Allows private instances to download updates from the internet securely.

Bastion Host: A "Jump Server" in the public subnet used for administrative SSH access to private instances.

2. Infrastructure Setup (VPC)
Navigate to the VPC Dashboard and click Create VPC.

Select "VPC and more" to automate the creation of subnets and gateways.

Project Settings:

Name tag: aws-prod-vpc

IPv4 CIDR block: 10.0.0.0/16

Number of Availability Zones: 2

Number of Public Subnets: 2

Number of Private Subnets: 2

NAT Gateways: 1 per AZ (requires Elastic IPs).

VPC Endpoints: None (unless using S3/DynamoDB locally).

Click Create VPC and wait for the "Workflow complete" status.

3. Auto Scaling Group (ASG) Configuration
A. Create Launch Template
Name: app-launch-template

AMI: Ubuntu Server 22.04 LTS.

Instance Type: t2.micro (Free Tier eligible).

Security Group: Create a new group allowing:

SSH (Port 22): Source from Bastion SG or your IP.

Custom TCP (Port 8000): Source from the Load Balancer SG.

B. Create ASG
Select the Launch Template created above.

VPC: Select aws-prod-vpc.

Subnets: Select only the Private Subnets in both AZs.

Group Size: Desired: 2, Min: 2, Max: 4.

4. Bastion Host (Jump Server)
Launch a standalone EC2 instance in a Public Subnet.

Auto-assign Public IP: Enable.

Security Group: Allow SSH (Port 22) from your local IP address.

Purpose: This instance acts as the only entry point to reach the private app servers.

5. Application Deployment
To deploy the app inside the private subnets, follow these steps:

Transfer Key to Bastion:

Bash

scp -i your-key.pem your-key.pem ubuntu@<Bastion-Public-IP>:/home/ubuntu/
SSH into Bastion:

Bash

ssh -i your-key.pem ubuntu@<Bastion-Public-IP>
SSH into Private Instance:

Bash

ssh -i your-key.pem ubuntu@<Private-Instance-IP>
Start Python App:

python3 -m http.server 8000
6. Application Load Balancer (ALB)
A. Create Target Group
Target Type: Instances.

Protocol: HTTP, Port: 8000.

Health Checks: HTTP on / path.

Register Targets: Select the two instances launched by the ASG.

B. Create Load Balancer
Type: Application Load Balancer (ALB).

Scheme: Internet-facing.

Network Mapping: Select your VPC and the Public Subnets in both AZs.

Security Group: Create/Select a group that allows HTTP (Port 80) from anywhere (0.0.0.0/0).

Listeners: Forward Port 80 to your Target Group.

7. Verification
Copy the ALB DNS Name.

Paste it into your browser.

If successful, the ALB will route your request to one of the private instances, and you will see your HTML page.
