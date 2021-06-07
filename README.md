## Deployed a highly available and scalable secured Wordpress web application on AWS
This project adopts the AWS reference architecture below to deploy highly available and scalable secured Wordpress web application on AWS. A high level step instruction is detailed below on howto create this setup with snapshots of adopted configuration for each service and feature in each folder. Each Tier of the Architecture (Database, Application and File server) has been deployed to scale independently.

**AWS Services used includes:** Amazon Virtual Private Cloud (Amazon VPC), Amazon Elastic Compute Cloud (Amazon EC2), Auto Scaling,Elastic Load Balancing (Application Load Balancer), 
Amazon Relational Database Service (Amazon RDS), Amazon Elastic File System (Amazon EFS), Amazon Route 53,Amazon Certificate Manager (Amazon ACM).

## AWS reference architecture used
![Reference Architecture](https://user-images.githubusercontent.com/15005443/120980732-e8100680-c744-11eb-9241-645927ba848f.PNG)
                                                                                https://docs.aws.amazon.com/whitepapers/latest/best-practices-wordpress/reference-architecture.html
                                            
## High level Steps to setup this architecture

* Configure the network
* Configure A VPC with the CIDR range of 10.0.0.0/16
* Create 6 subnets for each tier of the architecture. Keep the application and database subnets Private
* Create Internet gateway and NAT gateway for internet access and outbound only internet access (for application tier) respectively
* Create 3 route tables leveraging the internet gateway And NAT gateways. Associate the appropriate subnets  
* To stay within the Free tier, this deployment uses the RDS database and deployed it in database Subnets
* Database Security group should allow access on 3306
* Create EFS cluster with mount points in each applciation subnet and allow security group acceess on 2049
* Create an internet facing application Load balancer deployed in each public subnets for resiliency. The Subnet group must allow all traffic on 443 for secure access (and or 80 for http)
* Create a launch template to be leveraged by the Auto Scaling Group.
* Launch template should have a user data which installs the required http packages (if a golden ami is not used), auto EFS mounts and necesary security groups defined to enable access for the ALB, EFS and RDS
* Create an ASG, specify launch template and select 2 AZs for new instance creation. Choose a scaling policy, this project adopt the simple scaling policy on the CPUUtilization metric.
* A new domain called awssonustac was created using route 53 and alias record to the ALB was used.
* For http only access, ACM was used to create a new certificate which was associated with the ALB.
* Once a desired number of instance is set at the ASG, a new instance will be created with a scaling up/down depending on CPU utilization.

## Snapshot of Wordpress and website is shown here
![Wordpress URL settings](https://user-images.githubusercontent.com/15005443/120981177-65d41200-c745-11eb-9904-28698e97ce0d.PNG)

![Wordpress Website](https://user-images.githubusercontent.com/15005443/120981232-74bac480-c745-11eb-81e9-f82b7b801f5c.PNG)

