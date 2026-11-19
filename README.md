# \# AWS Cloud Support Lab

# 

# \## Project status

# 

# \*\*In progress:\*\* The AWS environment, troubleshooting exercises,

# and monitoring alarm are complete. Resource cleanup and final cost

# verification are pending.

# 

# \## Project overview

# 

# This hands-on project demonstrates how to build and support a

# website hosted on an Amazon EC2 instance. I created a custom VPC,

# deployed a Linux web server, investigated connection issues,

# performed a simulated service recovery, and configured monitoring.

# 

# \## Skills practiced

# 

# \- Amazon VPC, subnets, route tables, and internet gateways

# \- Amazon EC2, Amazon Linux, and Elastic IP addresses

# \- IAM roles and AWS Systems Manager Session Manager

# \- Security groups and network ACLs

# \- Linux service administration and troubleshooting

# \- Amazon CloudWatch and Amazon SNS

# \- Incident documentation and customer communication

# 

# \## Architecture

# 

# ```text

# Browser

# &#x20;  |

# &#x20;  | HTTP on port 80

# &#x20;  v

# Internet gateway

# &#x20;  |

# &#x20;  v

# Public subnet in a custom VPC

# &#x20;  |  Subnet network ACL

# &#x20;  v

# EC2 instance and security group

# &#x20;  |

# &#x20;  +-- Amazon Linux and Nginx

# &#x20;  +-- Systems Manager Session Manager for administration

# &#x20;  +-- CloudWatch CPU alarm and SNS email notification

# ```

# 

# The subnet's route table directs internet traffic to an internet

# gateway. The EC2 instance used an Elastic IP address for public

# IPv4 connectivity. A security group controlled access to the

# instance, and a network ACL applied at the subnet level.

# 

# I used Session Manager to administer the instance without opening

# an inbound SSH port.

# 

# \## Environment and setup

# 

# 1\. Created a custom VPC and public subnet.

# 2\. Attached an internet gateway and configured a route table.

# 3\. Created a web security group and reviewed the subnet's default

# &#x20;  network ACL.

# 4\. Created an IAM role for Systems Manager and launched an Amazon

# &#x20;  Linux EC2 instance.

# 5\. Associated an Elastic IP after discovering that the instance

# &#x20;  had no public IPv4 address.

# 6\. Connected to the instance using Session Manager.

# 7\. Installed and started Nginx and created a test web page.

# 8\. Corrected the attached security group's HTTP rule and verified

# &#x20;  that the page loaded in a browser.

# 9\. Practiced a simulated Nginx outage and recovery.

# 10\. Configured a CloudWatch CPU alarm and SNS notification.

# 

# \## Troubleshooting ticket 1: Session Manager connection

# 

# \*\*Issue:\*\* Systems Manager reported that the EC2 instance was not

# connected.

# 

# \*\*Investigation:\*\* I checked the instance details and found that

# it did not have a public IPv4 address.

# 

# \*\*Resolution:\*\* I allocated and associated an Elastic IP address

# with the instance.

# 

# \*\*Verification:\*\* I successfully connected to the instance through

# Session Manager.

# 

# \*\*Lesson learned:\*\* A public subnet alone does not give an

# instance a public IP address. The instance also needs a suitable

# network path and permissions to reach Systems Manager.

# 

# \## Troubleshooting ticket 2: Website not loading

# 

# \*\*Issue:\*\* The test website did not load in my browser.

# 

# \*\*Investigation:\*\* I checked the Nginx service status and used

# `curl -I http://localhost` to confirm that the web server

# responded locally on the instance. This helped narrow the issue

# to the network path between the browser and the instance.

# 

# \*\*Cause:\*\* The security group attached to the instance did not

# allow the browser's HTTP request.

# 

# \*\*Resolution:\*\* I corrected the HTTP inbound rule on the attached

# security group.

# 

# \*\*Verification:\*\* I refreshed the browser and confirmed that the

# Cloud Support Lab page loaded.

# 

# \*\*Lesson learned:\*\* Creating a security group is not enough: it

# must also be attached to the intended instance. In this lab, I

# created a separate web security group, but the instance initially

# used the VPC's default security group.

# 

# \## Troubleshooting ticket 3: Simulated Nginx service outage

# 

# \*\*Scenario:\*\* A customer reports that the website is unavailable.

# 

# \*\*Issue:\*\* I intentionally stopped Nginx to simulate a web

# service outage. The website stopped loading.

# 

# \*\*Investigation:\*\* I checked the service status and reviewed

# recent logs:

# 

# ```bash

# sudo systemctl status nginx --no-pager

# sudo journalctl -u nginx --no-pager -n 30

# ```

# 

# The service status showed that Nginx was inactive.

# 

# \*\*Cause:\*\* Nginx had been intentionally stopped for the exercise.

# 

# \*\*Resolution:\*\* I restarted the service:

# 

# ```bash

# sudo systemctl start nginx

# ```

# 

# \*\*Verification:\*\* I confirmed that Nginx was active and refreshed

# the browser to verify that the website loaded.

# 

# \*\*Example customer update:\*\* The website is available again. I

# found that the web service had stopped, restarted it, and

# confirmed that the page was loading.

# 

# \*\*Lesson learned:\*\* Checking a service's status and logs helps

# identify whether the web server is contributing to an outage.

# 

# \## Monitoring

# 

# I created a CloudWatch alarm to monitor the EC2 instance's average

# CPU utilization over a five-minute period. The alarm used an

# Amazon SNS topic to send an email notification.

# 

# To test the notification, I temporarily changed the threshold to

# greater than or equal to 0%, confirmed that the alarm entered the

# ALARM state, and received an email. I then restored the threshold

# to greater than 70%.

# 

# A CPU alarm does not reliably detect an Nginx service outage.

# A separate website availability check would be a future

# improvement.

# 

# \## Evidence

# 

# The `screenshots` folder documents the VPC, subnet, internet

# gateway, route table, network ACL, security groups, EC2 instance,

# and Session Manager connection.

# 

# It also shows the connection issues I investigated, the simulated

# Nginx outage and recovery, the working website, and the monitoring

# exercise.

# 

# Sensitive information, including AWS account details and public IP

# addresses, should be redacted before screenshots are published.

# 

# \## Lessons learned

# 

# \- A public subnet alone does not assign a public IP address to an

# &#x20; instance.

# \- When an application responds locally but not in a browser,

# &#x20; check the network path and the security group attached to the

# &#x20; instance.

# \- Security groups are stateful and apply to network interfaces.

# &#x20; Network ACLs are stateless and apply at the subnet level.

# \- Service status and logs are useful starting points for Linux

# &#x20; troubleshooting.

# \- A CPU alarm and a website availability check answer different

# &#x20; monitoring questions.

# \- Documenting symptoms, investigation, resolution, and verification

# &#x20; makes support work easier to explain and repeat.

# 

# \## Cleanup and cost

# 

# \- Terminated the `cloud-support-lab` EC2 instance.

# \- Released the Elastic IP address used by the lab.

# \- Deleted `cloud-support-lab-vpc`. AWS confirmed that four

# &#x20; associated resources were also removed.

# \- Storage, the CloudWatch alarm, and the SNS topic are being

# &#x20; checked separately.

# 

# \*\*Lab resource cost:\*\* Pending. The lab services were not yet

# listed in the AWS bill when I checked.

