Guided Lab: Creating a VPC
 

Lab overview and objectives
Traditional networking is difficult. It involves equipment, cabling, complex configurations, and specialist skills. Amazon Virtual Private Cloud (Amazon VPC) hides the complexity and facilitates the deployment of secure private networks.

This lab shows you how to build your own virtual private cloud (VPC) and deploy resources.

After completing this lab, you should be able to do the following:

Deploy a VPC.

Create a public subnet.

Create a private subnet.

Create an internet gateway and attach it to the VPC.

Create an application server to test the VPC. 

Duration
This lab requires approximately 30 minutes to complete.

AWS service restrictions
In this lab environment, access to AWS services and service actions might be restricted to the ones that are needed to complete the lab instructions. You might encounter errors if you attempt to access other services or perform actions beyond the ones that are described in this lab.

At the end of this lab, your architecture will look like the following example:

Following image shows the final Architecture layout with networking components such as VPC, Public subnets and the Application server on the EC2 instance

Accessing the AWS Management Console
At the top of these instructions, choose Start Lab to launch your lab.

Tip: If you need more time to complete the lab, choose Start Lab again to restart the timer for the environment.

At the top of these instructions, choose the green circle next to AWS .

This option opens the AWS Management Console in a new browser tab. The system automatically signs you in.

Tip: If a new browser tab does not open, a banner or icon at the top of your browser will indicate that your browser is preventing the site from opening pop-up windows. Choose the banner or icon, and choose Allow pop-ups.

Arrange the AWS Management Console tab so that it displays along side these instructions. Ideally, you should be able to see both browser tabs at the same time so that you can follow the lab steps.

 Do not change the lab Region unless specifically instructed to do so.

Task 1: Creating a VPC
You begin by using Amazon VPC to create a new VPC.

A VPC is a virtual network that is dedicated to your Amazon Web Services (AWS) account. It is logically isolated from other virtual networks in the AWS Cloud. You can launch AWS resources, such as Amazon Elastic Compute Cloud (Amazon EC2) instances, into the VPC. You can configure the VPC by modifying its IP address range and create subnets. You can also configure route tables, network gateways, and security settings.

On the AWS Management Console, in the search box, enter and choose VPC to open the Amazon VPC console.

The VPC console provides a wizard that can automatically create several VPC architectures. However, in this lab, you create the VPC components manually.

In the left navigation pane, choose Your VPCs.

A default VPC is provided so that you can launch resources as soon as you start using AWS. However, you now create your own VPC.

Your VPC will have a Classless Inter-Domain Routing (CIDR) range of 10.0.0.0/16, which includes all IP address that start with 10.0.x.x. It contains more than 65,000 addresses. You later divide the addresses into separate subnets.

Choose Create VPC. 

On the Create VPC page, configure the following options:

Name tag - optional: Enter Lab VPC.

IPv4 CIDR: Enter 10.0.0.0/16.

Choose Create VPC.

  A message that you successfully created the VPC appears.

Choose the Tags tab.

Tags are useful for identifying resources. For example, you can use a tag to identify cost centers or different environments (such as development, test, or production).

Choose Actions, and choose Edit VPC settings.

This option assigns a friendly DNS name, such as the following, to EC2 instances in the VPC:

ec2-52-42-133-255.us-west-2.compute.amazonaws.com

For DNS settings, select Enable DNS hostnames.

Choose Save.

Any EC2 instances that are launched into the VPC now automatically receive a DNS hostname. You can also add a more meaningful DNS name (such as app.example.com) later by using Amazon Route 53.

 

Task 2: Creating subnets
A subnet is a subrange of IP addresses in the VPC. AWS resources can be launched into a specified subnet. Use a public subnet for resources that must be connected to the internet, and use a private subnet for resources that must remain isolated from the internet.

In this task, you create a public subnet and a private subnet:

Following image shows VPC with Public and Private subnet in one Availability zone 

 

Task 2.1: Creating a public subnet
In this task, you create a public subnet. The public subnet will be used for internet-facing resources.

In the left navigation pane, choose Subnets.

Choose Create subnet.

On the Create subnet page, configure the following options:

VPC ID: Choose Lab VPC.

Subnet name: Enter Public Subnet.

Availability Zone: Choose the first Availability Zone in the list. Do not keep No preference as the default.

IPv4 subnet CIDR block: Enter 10.0.0.0/24.

Choose Create subnet.

 The VPC has a CIDR block of 10.0.0.0/16, which includes all 10.0.x.x IP addresses. The subnet that you just created has a CIDR block of 10.0.0.0/24, which includes all 10.0.0.x IP addresses. They might look similar, but the subnet is smaller than the VPC because of the /24 in the CIDR range.

  You now configure the subnet to automatically assign a public IP address for all instances that are launched in it.

Select Public Subnet.

Choose Actions, and then choose Edit subnet settings.

On the Edit subnet settings page, for Auto-assign IP settings, select Enable auto-assign public IPv4 address.

Choose Save.

  Although this subnet is named Public Subnet, it is not yet public. A public subnet must have an internet gateway, which you attach in the next task.

Task 2.2: Creating a private subnet
In this task, you create a private subnet. The private subnet will be used for resources that must remain isolated from the internet.

Choose Create subnet.

On the Create subnet page, configure the following options:

VPC ID: Choose Lab VPC.    

Subnet name: Enter Private Subnet.

Availability Zone: Choose the first Availability Zone in the list. Do not keep No preference as the default.

IPv4 subnet CIDR block: Enter 10.0.2.0/23.

 The 10.0.2.0/23 CIDR block includes all IP addresses that start with 10.0.2.x and 10.0.3.x. This is larger than the public subnet because most resources should be kept private unless they specifically must be accessible from the internet.

Choose Create subnet.

  Your VPC now has two subnets. However, the public subnet is totally isolated and cannot communicate with resources outside the VPC. Next, you configure the public subnet to connect to the internet through an internet gateway.

 

Task 3: Creating an internet gateway
An internet gateway is a horizontally scaled, redundant, and highly available VPC component. It allows communication between the instances in a VPC and the internet. It imposes no availability risks or bandwidth constraints on network traffic.

An internet gateway serves two purposes:

To provide a target in route tables that connects to the internet

To perform network address translation (NAT) for instances that were assigned public IPv4 addresses

In this task, you create an internet gateway so that internet traffic can access the public subnet.

In the left navigation pane, choose Internet gateways.

Choose Create internet gateway.

For Name tag, enter Lab IGW.

Choose Create internet gateway.

You can now attach the internet gateway to your Lab VPC.

Choose Actions, and then choose Attach to VPC.

For Available VPCs, choose Lab VPC.

Choose Attach internet gateway.

  This action attaches the internet gateway to your Lab VPC. Although you created an internet gateway and attached it to your VPC, you must also configure the public subnet route table so that it uses the internet gateway.

Task 4: Configuring route tables
A route table contains a set of rules, called routes, that are used to determine where network traffic is directed. Each subnet in a VPC must be associated with a route table because the table controls the routing for the subnet. A subnet can be associated with only one route table at a time, but you can associate multiple subnets with the same route table.

To use an internet gateway, a subnet's route table must contain a route that directs internet-bound traffic to the internet gateway. If a subnet is associated with a route table that has a route to an internet gateway, it is known as a public subnet.

In this task, you do the following:

Create a public route table for internet-bound traffic.

Add a route to the route table to direct internet-bound traffic to the internet gateway.

Associate the public subnet with the new route table.

In the left navigation pane, choose Route tables.

Several route tables are displayed, but there is only one route table associated with Lab VPC. This route table routes traffic locally, so it is called a private route table.

Expand the VPC column so that you can see which one is used by Lab VPC.

Select the route table that shows Lab VPC.

In the Name column, choose the edit icon (), and for Edit Name, enter Private Route Table.

Choose Save.

Choose the Routes tab.

There is only one route. It shows that all traffic that is destined for 10.0.0.0/16 (which is the range of the Lab VPC) will be routed locally. This route allows all subnets in a VPC to communicate with each other.

You now create a new public route table to send public traffic to the internet gateway.

Choose Create route table, and configure these settings:

Name - optional: Enter Public Route Table.

VPC: Choose Lab VPC.

Choose Create route table.

In the Routes tab, choose Edit routes.

You now add a route to direct internet-bound traffic (0.0.0.0/0) to the internet gateway.

Choose Add route, then configure these settings:

Destination: Enter 0.0.0.0/0.

Target: Choose Internet Gateway and then, from the list, choose Lab IGW.

Choose Save changes.

Next, you associate this new route table with the public subnet.

Choose the Subnet associations tab.

Choose Edit subnet associations.

Select the row with Public Subnet.

Choose Save associations.

The public subnet is now public because it has a route table entry that sends traffic to the internet through the internet gateway.

To summarize, you can create a public subnet by following these steps:

Create an internet gateway.

Create a route table.

Add a route to the route table that directs 0.0.0.0/0 traffic to the internet gateway.

Associate the route table with a subnet, which becomes a public subnet.

 

Task 5: Creating a security group for the application server
A security group acts as a virtual firewall for instances to control inbound and outbound traffic. Security groups operate at the level of the elastic network interface for the instance. Security groups do not operate at the subnet level. Thus, each instance can have its own firewall that controls traffic. If you do not specify a particular security group at launch time, the instance is automatically assigned to the default security group for the VPC.

In this task, you create a security group that gives users the ability to access your application server through HTTP.

In the left navigation pane, choose Security groups.

Choose Create security group.

On the Create security group page, configure the following options:

Security group name: Enter App-SG.

Description: Enter Allow HTTP traffic.

VPC: Choose Lab VPC.

In the Inbound rules section, choose Add rule, and then configure the following options:

Type: Choose HTTP.

Source: Choose Anywhere-IPv4.

Description - optional: Enter Allow web access.

The settings for inbound rules determine which traffic is permitted to reach the instance. You configure the inbound rules to permit HTTP (port 80) traffic that comes from anywhere on the internet (0.0.0.0/0).

Choose Create security group.

You use the App-SG security group in the next task.

Task 6: Launching an application server in the public subnet
To test that your VPC is correctly configured, you now launch an EC2 instance into the public subnet. You also confirm that you can access the EC2 instance from the internet.

On the AWS Management Console, in the search box, enter and choose EC2 to open the Amazon EC2 console.

Choose Launch instance.

On the Launch an instance page, configure the following options:

For Name, enter App Server.

In the Application and OS Images (Amazon Machine Image) section, configure the following options:

For  Quick Start, keep the default Amazon Linux option. 

For Amazon Machine Image (AMI), keep the default Amazon Linux 2023 AMI option.

In the Instance type section, keep the default t2.micro option.

For Key pair name - required, choose vockey.

In the Network settings section, choose Edit, and then configure the following options: 

For VPC - required, choose Lab VPC.

For Subnet, choose Public Subnet.

For Firewall (security groups), choose Select existing security group.

For Common security groups, choose App-SG.

In the Configure storage section, keep the default settings.

Expand the Advanced details panel, and for IAM instance profile, choose Inventory-App-Role.

This role, which was created when you provisioned the lab, is required for the application to access the database credentials securely stored in AWS Secrets Manager.

In the User data box, copy and paste the following code:

#!/bin/bash
# Install Apache Web Server and PHP
dnf install -y httpd wget php-fpm php-mysqli php-json php php-devel
dnf install -y mariadb105-server
# Download Lab files
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/06-lab-mod7-guided-VPC/s3/scripts/al2023-inventory-app.zip -O inventory-app.zip
unzip inventory-app.zip -d /var/www/html/
# Download and install the AWS SDK for PHP
wget https://docs.aws.amazon.com/aws-sdk-php/v3/download/aws.zip
unzip aws.zip -d /var/www/html
# Turn on web server
systemctl enable httpd
systemctl start httpd
In the Summary section, choose Launch instance.

You should see a Success message.

Choose the link to the new instance that you created. 

Wait until the App Server instance shows 2/2 checks passed in the Status check column.

 This might take a few minutes. Choose the refresh  icon at the top of the page every 30 seconds or so to check the status of the instance.

Select App Server.

From the Details tab, copy the Public IPv4 DNS value.

Open a new web browser tab, and enter this public IPv4 DNS value.

If you configured the VPC correctly, the Inventory application and this message should appear: Please configure Settings to connect to database. The appearance of the Inventory application demonstrates that the public subnet was correctly configured.

 If the Inventory application does not appear, wait 60 seconds and refresh  the page to try again. It can take a couple of minutes for the EC2 instance to boot and run the script that installs the software.

 

Submitting your work
At the top of these instructions, choose Submit to record your progress, and when prompted, choose Yes.

If the results don't display after a minute, return to the top of these instructions and choose Grades.

Tip: You can submit your work multiple times. After you change your work, choose Submit again. Your last submission is what will be recorded for this lab.

To find detailed feedback on your work, choose Submission Report.

 

Conclusion
Congratulations! You now have successfully done the following:

Deployed a VPC

Created a public subnet

Created a private subnet

Created an internet gateway and attached it to the VPC

Created an application server to test the VPC

Lab complete 
 Congratulations! You have completed the lab.

At the top of this page, choose End Lab, and then choose Yes to confirm that you want to end the lab. 

The message "Ended AWS Lab Successfully" is briefly displayed, indicating that the lab has ended.

© 2023, Amazon Web Services, Inc. or its affiliates. All rights reserved. This work may not be reproduced or redistributed, in whole or in part, without prior written permission from Amazon Web Services, Inc. Commercial copying, lending, or selling is prohibited.