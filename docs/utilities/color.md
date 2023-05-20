---
layout: default
title: AWS

parent: Utilities
---

# Color Utilities
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---
Introduction to Amazon Elastic Compute Cloud (EC2)

Lab Details
This lab walks you through the steps to launch and configure a virtual machine in the Amazon cloud.

You will practice using Amazon Machine Images to launch Amazon EC2 Instances and use key pairs for SSH authentication to log into your instance. You will create a web page and publish it.

Duration: 30 minutes

AWS Region: US East (N. Virginia) us-east-1

Task Details
Log into AWS Management Console.
Create an Amazon Linux Instance from an Amazon Linux AMI
Find your instance in the AWS Management Console.
SSH into your instance.
Configure our EC2 Instance to be a web server.
Create and publish a sample test.html file.
Test the page with the public IP address of EC2 Instance created.
Architecture Diagram


EC2 Configuration
Services -> EC2 -> Instances
Click on Launch instances



Launching EC2 Instance
Step 1: Choose an Amazon Machine Image(AMI)
Select Amazon Linux 2 AMI (HVM), SSD Volume Type


Step 2: Choose an Instance Type
Select t2.micro
Click on Next: Configure Instance Details


Step 3: Configure Instance Details
Leave it default settings
Click on Next: Add Storage


Step 4: Add Storage
Leave it default settings
Click on Next: Add Tags


Step 5: Add Tags
Click on Add Tag

Key: Name
Value: MyEC2Server
Click on Next: Configure Security Group



Step 6: Configure Security Group

Select Create a new security group

Security group name: EC2WebServerSG
Description : Security Group for EC2 Web Server
For SSH:

Source: Custom (Allow specific IP address) or Anywhere (From ALL IP addresses accessible).
Click on Add Rule
For HTTP:

Source: Anywhere
For HTTPS:

Source: Anywhere
Click on Review and Launch





Step 7: Review Instance Launch
Click on Launch


Select Create a new key pair


Set up the key pair name ec2
Click on Download Key Pair
See the left bottom of the screenshot, we have the file named ec2.pem
Click on Launch Instances


Click on View Instances


Waiting until the Instance state switch to Running


You can Click on Instance ID like i-095a9b2992ba8bf7d to see the instance detail.


Copy the public IP address


SSH into EC2 Instance
Open application Terminal on your MacBook


Go to your ec2.pem directory
e.g, my directory is ~/Downloads/
cd ~/Downloads


Search for ec2.pem
ls | grep ec2.pem


SSH into your instance
the syntax should be ssh -i yourKeyPairName ec2-user@yourInstancePublicIpAddress
Press Enter


Type yes and press Enter


Access must be denied since the permission of ec2.pem is too open.


Now we need to lower the permission of ec2.pem
The current permission is -rw-r--r-- which means 0644.
ls -l | grep ec2.pem

r: read - 4
w: write - 2
x: execute - 1 (not show on this file now)
-: None - 0


Lower it permission to 0400 which means -r--------
chmod 0400 ec2.pem


See its permission again.
ls -l | grep ec2.pem


Try to SSH into your EC2 instance again
ssh -i yourKeyPairName ec2-user@yourInstancePublicIpAddress
Now we log into the the server.


Install an Apache Server
Switch to root user: sudo -s
Notice the difference


Now run the updates using the following command:
yum -y update


Once completed, lets install and run an apache server
Install the Apache web server:
yum install httpd


When prompted, Press “Y” to confirm.


Start the web server
systemctl start httpd


Now enable httpd:
systemctl enable httpd


Check the webserver status
systemctl status httpd
You can see Active status is running.


You can test that your web server is properly installed and started by entering the public IP address of your EC2 instance in the address bar of a web browser. If your web server is running, then you see the Apache test page. If you don’t see the Apache test page, then verify whether you followed the above steps properly and check your inbound rules for the security group that you created.

Create and publish page
Navigate to the html folder where we will create a HTML page to test.
cd /var/www/html/


Create a sample test.html file using nano editor:
vime test.html


<HTML> Hi Whizlabs, I am a public page </HTML>



Restart the web server by using the following command:
systemctl restart httpd


Now enter the file name after the public IP which you got when you created ec2 instance in the browser, and you can see your HTML content.

Validation Test
Input your instance public IP address with the path test.html on your browser
for example, 3.82.192.250/text.html



Completion and Conclusion
You have successfully created and launched an EC2 Instance.
You have logged into EC2 instance by SSH, installed Apache server and published a page.
You have allocated an Elastic IP address and associated it to the running instance.
Launch a Spot Instance with Amazon EC2
https://play.whizlabs.com/site/task_details?lab_type=1&task_id=164&quest_id=35

Lab Details
This lab walks you through the steps to launch an EC2 Spot Instance using the AWS Management Console. You will also learn about Saving Summary and pricing history.
You will practice using Amazon Machine Image (AMI) to launch Amazon EC2 Spot Instance and use key pairs for SSH authentication to log into your instance.
You will create a web page and publish it.
Introduction
What is EC2 Spot Instance
Spot Instances are an unused part of Amazon EC2, using which you can save up to 90% on cost as compared to On-Demand cost, but AWS can interrupt your spot instances if the Current Price is higher than the Maximum Price you specified.
Spot uses the same EC2 instances (AMI and instance type) what On-Demand and Reserved Instances use. It is the best to fit for use cases where data is reproducible and can sustain the interruption at any point in time.
You can use Spot Instance as additional compute capacity to your On-Demand or Reserved Instances, where fault-tolerant is acceptable.
EC2 Spot Instances can be launched the same way you launch EC2 Instance, like using Spot Fleet. Auto Scaling Groups or AWS Management Console.
If AWS terminates or stops your Amazon EC2 Spot Instance within an hour then you will not be charged.
However, if you choose to stop or terminate your newly launched Spot Instances by yourself, you will have to pay for the total number of seconds you have used.
Task Details
Log into AWS Management Console.
Select an Amazon Linux Spot Instance from an Amazon Linux AMI 2.
Setting the price of a spot instance to Higher and lower values compared to a given value.
Launch the Spot Instance, to understand the difference between higher and lower prices.
Explore the Spot request, Saving Summary, and Pricing history options.
Test HTML page is launched or not using public IP.
Architecture Diagram



EC2 Configuration
Services -> EC2 -> Instances
Click on Launch instances



Launching EC2 Instance
Step 1: Choose an Amazon Machine Image(AMI)
Select Amazon Linux 2 AMI (HVM), SSD Volume Type


Step 2: Choose an Instance Type
Select t2.micro
Click on Next: Configure Instance Details


Step 3: Configure Instance Details

Number of instances: 1
Purchasing option: Check the Request Spot instance option
Now you will see the current price of the instance in each of the Availability Zone. In the Maximum price, enter the price lower than shown there, If it’s 0.004, enter 0.003 or less.

Persistent request: you will not check that option, but you should know what that means. Below is the explanation:

Ensures that your request will be submitted every time your Spot Instance is terminated. For information about Persistent requests and other customization options, see Customizing Your Spot Requests.
Request valid to: Any time

Auto-assign Public IP: Enable



Click on Advanced Details

Under the User data section, enter the following script, (which creates an HTML page served by Apache):

#!/bin/bash
sudo su
yum update -y
yum install httpd -y
systemctl start httpd
systemctl enable httpd
echo "<html><h1> Welcome to AWS EC2 Web Server</h1><html>" >> /var/www/html/index.html
Click on Next: Add Storage


Step 4: Add Storage
Leave it default settings
Click on Next: Add Tags


Step 5: Add Tags
Click on Add Tag

Key: Name
Value: MySpotInstance
Click on Next: Configure Security Group


Step 6: Configure Security Group

Select Create a new security group

Security group name: EC2WebServerSG
Description : Security Group for EC2 Web Server
For SSH:

Source: Custom (Allow specific IP address) or Anywhere (From ALL IP addresses accessible).
Click on Add Rule
For HTTP:

Source: Anywhere
For HTTPS:

Source: Anywhere
Click on Review and Launch





Step 7: Review Instance Launch
Click on Launch


Select Create a new key pair


Set up the key pair name ec2
Click on Download Key Pair
See the left bottom of the screenshot, we have the file named ec2.pem
Click on Launch Instances


You will get an error message like this if your Maximum price was less than the Current price shown in the Availability zone and Current Price table.



Now Click on Back to Review Screen, then click on 3. Configure Instance on the top of the screen and Edit the maximum Price, set to 0.01, and then click on Review and Launch.
Note: If by mistake you clicked on the Cancel button, you have to repeat from Step 4.



Click on View Instances


Waiting until the Instance state switch to active.


You can Click on Instance ID like sir-f791h5bj to see the instance detail.


Click on the Instance ID like i-0dbf9d48f0d443116 under the Capacity.


Select MySpotInstance.
Copy the public IP address


Validation Test
Paste your instance public IP address on a tab of your browser.


View the Spot Request
Click on Spot Requests on the navigation panel.
Select the Spot Instance.


Click on the Request ID like sir-f791h5bj, to see more details about your spot request.

Max price: The highest price you are willing to pay for this EC2 Instance.
Instance Id: Your current EC2 Instance, associated with this Spot request.
Interruption behavior: Terminate, if the current price of this Spot Instance goes higher than the original price, then it will be terminated automatically.
Availability Zone: Current Price of the Spot instance varies by Availability Zone, There might be a possibility that in other AZ you can get the same instance at a lower price.


Explore the Saving Summary and Pricing history
Go back to the Spot Requests page.


Click on the Saving summary on the right and top window, below you can see, you will save a total of 70% as compared to on-demand instances. Details are also available.


Click on the Close button, and let’s explore the Pricing history option.


Here you will see the pricing history, but wait a minute, it’s showing for some other Instance size, let’s modify the options.

Graph: Availability Zones
Instance type: t2.micro
Platform: Linux/UNIX
Date Range: 3 months


Clean up section, terminate EC2 Spot Instance
On the Spot Requests page.


Click on Actions -> Cancel request


Click on Confirm


Refresh the page.
Now you will see the request, State as Cancelled and Status as instance-terminated-by-user. Which now means that clean up is done.

You can reverify the Termination state of Instance by clicking on Instances in the left sidebar, it will show the Instance state as Terminated.


Completion and Conclusion
You have successfully created and launched Amazon EC2 Spot Instance.
You have successfully created a webpage and published it.
Allocating Elastic IP address and Associating it to EC2 Instance
https://play.whizlabs.com/site/task_details?lab_type=1&task_id=78&quest_id=35

Lab Details
This lab walks you through the steps to launch and configure a virtual machine in the Amazon cloud.

You will practice using Amazon Machine Images to launch Amazon EC2 Instances and use key pairs for SSH authentication to log into your instance. You will create a web page and publish it.

You will allocate and associate an Elastic IP.

Tasks
Log into AWS Management Console.
Create an Amazon Linux Instance from an Amazon Linux AMI
Find your instance in the AWS Management Console.
SSH into your instance and configure your server as a web server.
Create and publish a sample test.html file.
Test the page with the public IP address of EC2 Instance created.
Allocate an Elastic IP and associate it to the EC2 Instance.
Test the page with Elastic IP address of EC2 Instance.
Architecture Diagram:


EC2 Configuration
Services -> EC2 -> Instances
Click on Launch instances



Step 1: Choose an Amazon Machine Image(AMI)
Select Amazon Linux 2 AMI (HVM), SSD Volume Type


Step 2: Choose an Instance Type
Select t2.micro
Click on Next: Configure Instance Details


Step 3: Configure Instance Details
Leave it default settings
Click on Next: Add Storage


Step 4: Add Storage
Leave it default settings
Click on Next: Add Tags


Step 5: Add Tags
Click on Add Tag

Key: Name
Value: MyEC2Server
Click on Next: Configure Security Group



Step 6: Configure Security Group

Select Create a new security group

Security group name: EC2WebServerSG
Description : Security Group for EC2 Web Server
For SSH:

Source: Custom (Allow specific IP address) or Anywhere (From ALL IP addresses accessible).
Click on Add Rule
For HTTP:

Source: Anywhere
For HTTPS:

Source: Anywhere
Click on Review and Launch





Step 7: Review Instance Launch
Click on Launch


Select Create a new key pair


Set up the key pair name ec2
Click on Download Key Pair
See the left bottom of the screenshot, we have the file named ec2.pem
Click on Launch Instances


Click on View Instances


Waiting until the Instance state switch to Running


You can Click on Instance ID like i-095a9b2992ba8bf7d to see the instance detail.


Copy the public IP address


SSH into EC2 Instance
Open application Terminal on your MacBook


Go to your ec2.pem directory
e.g, my directory is ~/Downloads/
cd ~/Downloads


Search for ec2.pem
ls | grep ec2.pem


SSH into your instance
the syntax should be ssh -i yourKeyPairName ec2-user@yourInstancePublicIpAddress
Press Enter


Type yes and press Enter


Access must be denied since the permission of ec2.pem is too open.


Now we need to lower the permission of ec2.pem
The current permission is -rw-r--r-- which means 0644.
ls -l | grep ec2.pem

r: read - 4
w: write - 2
x: execute - 1 (not show on this file now)
-: None - 0


Lower it permission to 0400 which means -r--------
chmod 0400 ec2.pem


See its permission again.
ls -l | grep ec2.pem


Try to SSH into your EC2 instance again
ssh -i yourKeyPairName ec2-user@yourInstancePublicIpAddress
Now we log into the the server.


Install an Apache Server
Switch to root user: sudo -s
Notice the difference


Now run the updates using the following command:
yum -y update


Once completed, lets install and run an apache server
Install the Apache web server:
yum install httpd


When prompted, Press “Y” to confirm.


Start the web server
systemctl start httpd


Now enable httpd:
systemctl enable httpd


Check the webserver status
systemctl status httpd
You can see Active status is running.


You can test that your web server is properly installed and started by entering the public IP address of your EC2 instance in the address bar of a web browser. If your web server is running, then you see the Apache test page. If you don’t see the Apache test page, then verify whether you followed the above steps properly and check your inbound rules for the security group that you created.

Create and publish page
Navigate to the html folder where we will create a HTML page to test.
cd /var/www/html/


Create a sample test.html file using nano editor:
vime test.html


<HTML> Hi Whizlabs, I am a public page </HTML>



Restart the web server by using the following command:
systemctl restart httpd


Now enter the file name after the public IP which you got when you created ec2 instance in the browser, and you can see your HTML content.

Input your instance public IP address with the path test.html on your browser
for example, 3.82.192.250/text.html


Allocating Elastic IP Address
Service -> EC2 -> Elastic IPs
To use an Elastic IP address, you need to allocate one to your account and then associate it with your instance or a network interface.



Click on Associate Elastic IP address


Click on Allocate




Associating an Elastic IP Address with a Running Instance
Service -> EC2 -> Elastic IPs

Select the Elastic IP address created and click on Actions. Click on Associate Elastic IP address.



Associate Elastic IP address

Resource Type: Click on instance
Choose your instance in the drop down below as shown below.
Leave default values for the remaining fields and click on Associate.


Click on Associate


Now you can see that the instance is associated with the Elastic IP address.


Go to the EC2 Instance and check the IPv4 Public IP and it should be the same as Elastic IP.



Validation Test
Input your instance public IP address with the path test.html on your browser
for example, 3.82.192.250/text.html

Check the website

Old IP address allocated by system when you creating a new EC2 instance, which we test before associating new website


New IP address allocated by yourself and associated by you to your EC2 instance


Completion and Conclusion
You have successfully created and launched an EC2 Instance.
You have logged into EC2 instance by SSH, installed Apache server and published a page.
You have allocated an Elastic IP address and associated it to the running instance.
You have checked the web page with Elastic IP address to make sure it works correctly.
Create EC2 Instance and Connect to a Windows machine using RDC
https://play.whizlabs.com/site/task_details?lab_type=1&task_id=80&quest_id=35

Lab Details
This lab walks you through the steps to launch and configure a virtual machine in the Amazon cloud.
You will practice using Amazon Machine Images to launch Windows EC2 Instance and connect it using Remote Desktop Connection(RDC)
Tasks
Log into AWS Management Console.
Create an Amazon Windows Instance from Microsoft Windows Server
Find your instance in the AWS Management Console.
Connect your EC2 Instance using Remote Desktop Connection.
Architecture Diagram


EC2 Configuration
Services -> EC2 -> Instances
Click on Launch instances



Step 1: Choose an Amazon Machine Image (AMI)
Select Microsoft Windows Server 2019 Base
Click on Next: Configure Instance Details



Step 2: Choose an Instance Type
Select and then click on t2.micro



Step 3: Configure Instance Details
No need to change anything in this step.
Click on Next: Add Storage



Step 4: Add Storage
No need to change anything in this step.
Click on Next: Add Tags



Step 5: Add Tags
Click on Add Tag

Key: Name
Value: MyEC2Server


Click on Next: Configure Security Group

Step 6: Configure Security Group

Select Create a new security group

Security group name: EC2WindowsServerSG
Description : Security Group for EC2 Windows Server
For Type RDP:

Source: Anywhere
Click on Add Rule
Select Type HTTP:

Source: Anywhere
Click on Add Rule
Select Type HTTPS:

Source: Anywhere


After that, click on Review Instance Launch

Step 7: Review Instance Launch



Click on Launch

Select Create a new key pair
Set up the Key pair name


Click on Download Key Pair
Click on Launch Instances


Click on View Instances


Waiting for the Instance State to switch to Running


And copy the Public IPv4 address


Connecting EC2 Instance with Remote Desktop Connection
Select your EC2 Instance and click on Connect


Click on RDP Client


Click on Get password


Click on Browse your key pair, in my lab, it is ec2-windows.pem


Click on Decrypt Password


Copy the User name and Password

User name: Administrator
Password: yLn4)=tGn$9K*mU%H9I=2wFAuwEDqxe?
For Mac User, you may need to install Microsoft Remote Desktop application first, https://apps.apple.com/us/app/microsoft-remote-desktop/id1295203466?mt=12

Click on Download Remote Desktop file


Run your Remote Desktop file
Input your password
Click on Continue


Click on Continue



Or you could click on Add PC on Microsoft Remote Desktop
