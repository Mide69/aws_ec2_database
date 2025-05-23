# aws_ec2_database
Instructions: Write on your process using a GitHub README.md file and submit. Include screenshots where necessary. 
 
Objective:
Design and set up a Virtual Private Cloud (VPC) with both public and private subnets. Implement routing, security groups, and network access control lists (NACLs) to ensure proper communication and security within the VPC. Work in the AWS EU-West-1 (Ireland) region.

Requirements:
Create a VPC:
Name: KCVPC
IPv4 CIDR block: 10.0.0.0/16
![Screenshot (3)](https://github.com/user-attachments/assets/4cdc57af-bc89-4a28-9f32-d66b6070bdd0)


Create Subnets:
Public Subnet:
Name: PublicSubnet
IPv4 CIDR block: 10.0.1.0/24
Availability Zone: Select any one from your region
![Screenshot (5)](https://github.com/user-attachments/assets/0d02d477-b6ee-409d-9708-8736f000d53a)

Private Subnet:
Name: PrivateSubnet
IPv4 CIDR block: 10.0.2.0/24
Availability Zone: Select any one from your region (preferably the same as the Public Subnet for simplicity)
![Screenshot (4)](https://github.com/user-attachments/assets/00ff40da-4f6b-4d79-be91-39cab5d6a386)

Configure an Internet Gateway (IGW):
Create and attach an IGW to KCVPC.

![Screenshot (9)](https://github.com/user-attachments/assets/564e96f3-fcfc-49a0-ad12-a9d5e6bff763)

![Screenshot (10)](https://github.com/user-attachments/assets/8e407a90-e236-4423-a386-821cbc463fd1)

Configure Route Tables:
Public Route Table:
Name: PublicRouteTable
Associate PublicSubnet with this route table.
![Screenshot (7)](https://github.com/user-attachments/assets/b2888716-4633-446b-8421-dbc44df29dd9)


Add a route to the IGW (0.0.0.0/0 -> IGW).
Private Route Table:
Name: PrivateRouteTable
Associate PrivateSubnet with this route table.
Ensure no direct route to the internet.
![Screenshot (11)](https://github.com/user-attachments/assets/006f1130-6184-4013-bf9e-77457025bb74)

Configure NAT Gateway:
Create a NAT Gateway in the PublicSubnet.
Allocate an Elastic IP for the NAT Gateway.
![Screenshot (6)](https://github.com/user-attachments/assets/293eb28a-ebea-4fc2-92c3-6dd19b15527b)

Update the PrivateRouteTable to route internet traffic (0.0.0.0/0) to the NAT Gateway.

Set Up Security Groups:
Create a Security Group for public instances (e.g., web servers):
Allow inbound HTTP (port 80) and HTTPS (port 443) traffic from anywhere (0.0.0.0/0).
Allow inbound SSH (port 22) traffic from a specific IP (e.g., your local IP). (https://www.whatismyip.com/)
Allow all outbound traffic.

![Screenshot (12)](https://github.com/user-attachments/assets/b337b9e4-47bb-4861-b7e9-cf3fec7c4be0)

Create a Security Group for private instances (e.g., database servers):
Allow inbound traffic from the PublicSubnet on required ports (e.g., MySQL port 3306).
Allow all outbound traffic.

![Screenshot (13)](https://github.com/user-attachments/assets/a93cfbf6-3dd6-4b49-bfbc-fb1b5aae04d4)

Network ACLs:
Configure NACLs for additional security on both subnets.
Public Subnet NACL: Allow inbound HTTP, HTTPS, and SSH traffic. Allow outbound traffic.
![Screenshot (15)](https://github.com/user-attachments/assets/ea8e44dd-7ba3-4e93-a8bb-8ef7851b69a9)

![Screenshot (20)](https://github.com/user-attachments/assets/d095528d-fad5-4c00-8cb1-140c530e1507)

Private Subnet NACL: Allow inbound traffic from the public subnet. Allow outbound traffic to the public subnet and internet.
![Screenshot (14)](https://github.com/user-attachments/assets/03b0f773-5f20-490b-9b35-0298186ea1d5)

Deploy Instances:
Launch an EC2 instance in the PublicSubnet:
Use the public security group.
Verify that the instance can be accessed via the internet.
![Screenshot (17)](https://github.com/user-attachments/assets/84d2cce1-3aa2-4ddc-9a9f-43b4bcea7019)
![Screenshot (23)](https://github.com/user-attachments/assets/4eddf786-7673-4228-93c8-b16d09f98030)

Launch an EC2 instance in the PrivateSubnet:
Use the private security group.
Verify that the instance can access the internet through the NAT Gateway and can communicate with the public instance.
![Screenshot (18)](https://github.com/user-attachments/assets/83ce35b6-f525-47d1-837a-8ad3d063a839)


Deliverables:
A detailed report with screenshots of each step.
A diagram of the VPC architecture, showing the VPC, subnets, route tables, and security configurations. (https://excalidraw.com/)
A brief explanation of the purpose and function of each component created (VPC, subnets, IGW, NAT Gateway, route tables, security groups, NACLs).


 Here’s an explanation of the purpose and function of each AWS component used in the VPC architecture setup:

1. VPC (Virtual Private Cloud)
A VPC is a logically isolated section of the AWS cloud where you can define and control a virtual network. You specify its IP address range, create subnets, and manage route tables, gateways, and security.
In this setup: KCVPC (CIDR: 10.0.0.0/16) serves as your isolated network environment.


2. Subnets (Public & Private)
Subnets divide the VPC into smaller network sections, enabling you to isolate resources.
PublicSubnet (10.0.1.0/24): Connected to the internet via an Internet Gateway; for public-facing resources like web servers.
PrivateSubnet (10.0.2.0/24): No direct access to the internet; for backend services like databases.

3. Internet Gateway (IGW)
An IGW enables internet access for resources in public subnets. It connects your VPC to the internet.

In this setup: KCVPC-IGW is attached to the VPC and linked to the Public Route Table, allowing the public subnet to send/receive traffic from the internet.


4. NAT Gateway
A NAT Gateway allows instances in a private subnet to access the internet for updates or downloads, but prevents inbound connections from the internet.

In this setup: Deployed in the Public Subnet, allowing the PrivateSubnet instances to reach the internet without exposing them to inbound traffic.


5. Route Tables
Route tables determine how traffic is directed within your VPC.

PublicRouteTable: Directs internet-bound traffic (0.0.0.0/0) from the PublicSubnet through the IGW.


PrivateRouteTable: Routes internet traffic from the PrivateSubnet through the NAT Gateway.


6. Security Groups
Security groups are virtual firewalls that control inbound and outbound traffic for your EC2 instances.
Public-SG: Allows HTTP (80), HTTPS (443) from anywhere, and SSH (22) from your IP.


Private-SG: Allows specific traffic (like MySQL on port 3306) from the PublicSubnet or Public-SG.


7. Network ACLs (Access Control Lists)
NACLs are optional stateless firewalls at the subnet level. They allow or deny traffic to and from subnets.

Public NACL: Allows HTTP, HTTPS, SSH inbound from the internet.

Private NACL: Allows traffic to/from the public subnet and NAT Gateway.


8. EC2 Instances
Elastic Compute Cloud (EC2) provides virtual servers.

Public EC2: A web server accessible via internet.

Private EC2: A backend service (like a database) only accessible internally, via the NAT Gateway for internet access or directly from the Public EC2 instance.
