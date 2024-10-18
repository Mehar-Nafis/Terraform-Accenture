## Deploy 2 tier architecture using terraform

The given Terraform configuration sets up a two-tier architecture on AWS with the following key components:

1. Backend Configuration (`backend.tf`):
* Uses an S3 bucket to store Terraform state files, with a DynamoDB table to lock the state files, preventing concurrent updates.
     
2. Database Setup (`db.tf`):
* Creates an RDS instance running MySQL, with configuration settings like storage, engine version, and instance class.
A Database Subnet Group is set up to host the RDS instance in private subnets.
4. Web Server Instances (ec2.tf):
Deploys two EC2 instances (web1 and web2) in different availability zones.
Configures Apache, PHP, and WordPress on each instance.
Uses user_data scripts to install required software and configure WordPress with the database details.
5. Key Pair Configuration (key.tf):
Generates an RSA private key and stores the public key in AWS as a key pair.
Uploads the private key to an S3 bucket for access.
6. Networking (routtable.tf and vpc.tf):
Sets up a VPC with CIDR block 10.0.0.0/16.
Creates an Internet Gateway and route table to allow internet access to the public subnets.
Associates the route table with public subnets and creates security groups to control traffic.
7. Load Balancer Configuration (sg-alb.tf):
Configures an Application Load Balancer (ALB) with associated security groups.
Creates a Target Group and Listener to route HTTP traffic to the web instances (web1 and web2).
8. Security Groups:
Configures security groups to control access to web servers and the RDS database. Security rules include:
Allowing HTTP, HTTPS, and SSH traffic for public instances.
Limiting database access to internal IP ranges.
This Terraform configuration automates the deployment of a scalable, two-tier architecture consisting of a web tier with load-balanced EC2 instances and a database tier using Amazon RDS, all within a secured and network-configured environment.
Create the infra
```
cd ~ && wget https://github.com/Mehar-Nafis/Terraform-Two-Tier-Architecture/archive/refs/heads/main.zip -O Terraform-Two-Tier-Architecture.zip
```
```
unzip Terraform-Two-Tier-Architecture.zip
```
```
cd Terraform-Two-Tier-Architecture-main
```
Make sure to replace all occurrences of `mehar` with your `name` to avoid any conflicts
```
terraform init
```
```
terraform plan
```
```
terraform apply -auto-approve
```
Cross verify by accessing your infra and finally destroy and clean up
```
terraform destroy -auto-approve
```
```
cd ~ && rm -rf Terraform-Two-Tier-Architecture-main
```
