![image](https://github.com/amanuelararso/AWS--Three-Tier-Architecture/assets/26092925/ee046e6d-b128-4f2b-91f8-9b7d68b6c2f4)# AWS--Three-Tier-Architecture
Design and configure a high available 3-tier Architecture on AWS     
Tier 1 - User/Presentation Tier     
Tier 2 - Application Tier     
Tier 3 - Data Tier


To make it highly available I have used two Availability Zones. Bastion server
and Web server are place inside the public subnet in the vpc to make them 
accessible from anywhere. 

The app server which operates on the database is on separate private subnet,
and RDS database server is also on separate private subnet. The subnet group
for the database server is the last two private subnets, which are on different 
availability zones; so that making the database server multi-zone and highly available.

Internet gateway is used for resources to communicate with the internet as per
the configured security group inbound rules.

NAT gateway is used to allow private instances translate to webpage and connect to 
internet. Elastic IP (public) is attached to it.

SECURITY GROUPS (SG)

Bastion host (SG): ALLOW http, https, ssh FROM anywhere
Webserver (SG): ALLOW http, https, ssh FROM anywhere, ALLOW all icmp-ipv4 FROM app-server-sg
App server (SG): ALLOW mysql/Arora FROM database-sg, http, https, ssh FROM everywhere
Database server (SG): Allow mysql/Arora FROM baston-sg, ALLOW mysql/Arora FROM app-server-sg


All instances were running Amazon Linux 2 image.

Web server instance is launched with user data of the following:
  #!/bin/bash
  sudo yum update -y
  sudo amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2
  sudo yum install -y httpd
  sudo systemctl start httpd
  sudo systemctl enable httpd

  The App server was initially started with the following user data:
    #!/bin/bash
    sudo yum install -y mariadb-server
    sudo service mariadb start

and then the database server can be accessed with the command: mysql --user=user --password='password' host=database-server-endpoint
