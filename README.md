#Three-Tier Architecture Deployment using AWS CloudFormation

Overview

This project provisions a Three-Tier Architecture on AWS using CloudFormation. The architecture consists of:

Web Tier: Nginx Web Server (EC2) behind an Application Load Balancer (ALB)

App Tier: Flask API Server (EC2) for backend logic

Database Tier: Amazon RDS (MySQL) for data storage

Security Groups: Ensuring secure communication between the tiers

Architecture Components

VPC & Networking:

A VPC with multiple subnets across two Availability Zones for high availability.

Public subnets for ALB & WebServer (Nginx).

Private subnets for AppServer (Flask API) and Database (RDS).

Security Groups:

ALBSecurityGroup: Allows HTTP (80) from the internet and forwards to WebServer.

WebSecurityGroup: Allows HTTP (80) only from ALB and forwards to AppServer.

AppSecurityGroup: Allows requests only from WebServer on port 5000.

DBSecurityGroup: Allows only AppServer to connect to RDS on port 3306.

Load Balancer:

An Application Load Balancer (ALB) in public subnets.

Routes incoming HTTP traffic to the WebServer.

EC2 Instances:

WebServer (Nginx) handles frontend requests.

AppServer (Flask API) handles backend logic.

Database Layer:

Amazon RDS (MySQL) for persistent data storage.

Deployed in private subnets for security.

Deployment Instructions

1️⃣ Launch CloudFormation Stack

Navigate to AWS Management Console → CloudFormation.

Click on Create Stack → With new resources.

Upload the CloudFormation YAML template.

Provide necessary parameters (VPC CIDR, Subnet IDs, Instance Type, etc.).

Click Create Stack and wait for deployment completion.

2️⃣ Verify the Deployment

Go to EC2 Dashboard and verify:

WebServer and AppServer instances are running.

Go to RDS Dashboard and check:

The database instance is active.

Go to Load Balancer Section in EC2 and verify:

The ALB is active and associated with EC2 instances.

Check Security Groups to confirm:

ALB allows inbound HTTP (80) traffic.

WebServer allows traffic only from ALB.

AppServer allows traffic only from WebServer.

RDS allows connections only from AppServer.

3️⃣ Test the Application

Get ALB DNS Name:

Navigate to EC2 → Load Balancers.

Copy the ALB DNS Name from the output section.

Open in Browser:

Visit http://<ALB_DNS_Name> to check the frontend (WebServer).

The request should be forwarded to the backend (AppServer).

The backend should interact with the database (RDS).

Manual Changes Required

Update the AMI ID for EC2 instances based on the region.

Modify Key Pair Name for SSH access.

Adjust Instance Types as per your requirements.

Ensure RDS username & password are correctly configured.

Troubleshooting

Stack Stuck in CREATE_IN_PROGRESS:

Check CloudFormation Events for errors.

Ensure IAM permissions allow creating VPC, EC2, RDS, ALB.

Verify Subnet and Security Group configurations.

Cannot Connect to ALB:

Ensure ALB Security Group allows inbound HTTP (80).

Check if EC2 instances are in the target group.

Verify that WebServer is running and serving requests.

Database Connection Issues:

Confirm AppServer Security Group allows outbound traffic on port 3306.

Ensure RDS Security Group allows inbound traffic from AppSecurityGroup.

Cleanup

To delete all resources, simply delete the CloudFormation Stack:

Go to CloudFormation → Stacks.

Select your stack and click Delete.

Wait for AWS to remove all resources automatically.

✅ Successfully Deployed a Three-Tier Architecture on AWS using CloudFormation! 🚀


