# \# AWS Cloud Support Lab

# 

# \## Project status

# 

# \*\*In progress.\*\* The website is working after troubleshooting Systems Manager connectivity and the EC2 security group. Additional monitoring, a simulated Linux service outage, and cleanup are planned.

# 

# \## Project overview

# 

# This hands-on lab simulates supporting a website hosted on an Amazon EC2 instance. The goal is to build the underlying network, connect to a Linux server, deploy a simple web page, and investigate issues using a structured cloud support approach.

# 

# \## Skills practiced

# 

# \- Amazon VPC, subnets, route tables, and internet gateways

# \- EC2, Amazon Linux, and Elastic IP addresses

# \- IAM roles and AWS Systems Manager Session Manager

# \- Security groups and network ACLs

# \- Linux service administration and command-line troubleshooting

# \- Incident documentation and customer communication

# 

# \*\*Planned:\*\* CloudWatch alarms and Amazon SNS notifications.

# 

# \## Architecture

# 

# ```text

# Browser

# &#x20;  |

# &#x20;  | HTTP (port 80)

# &#x20;  v

# Internet gateway

# &#x20;  |

# &#x20;  v

# Public subnet in custom VPC

# &#x20;  |  Subnet network ACL

# &#x20;  v

# EC2 instance and security group

# &#x20;  |

# &#x20;  +-- Amazon Linux and Nginx

# &#x20;  +-- Systems Manager Session Manager for administration

# ```

# 

# The instance uses an Elastic IP address for public IPv4 connectivity. The subnet route table directs internet-bound traffic to the internet gateway. The security group controls access to the instance, and the network ACL applies at the subnet level. Session Manager provides terminal access without an inbound SSH rule.

# 

# \## Environment and setup

# 

# 1\. Created a custom VPC, public subnet, internet gateway, and route table.

# 2\. Configured a security group for the web server; the subnet also has a network ACL.

# 3\. Created an EC2 IAM role for Systems Manager access and launched an Amazon Linux instance.

# 4\. Associated an Elastic IP after finding that the instance had no public IPv4 address.

# 5\. Connected through Session Manager.

# 6\. Installed and started Nginx and created a basic test page.

# 7\. Verified that the page loaded in a browser after correcting the security group.

# 

# \### Commands used

# 

# ```bash

# sudo dnf install -y nginx

# sudo systemctl enable --now nginx

# ```

# 

# Created a simple test page:

# 

# ```bash

# echo '<h1>Cloud Support Lab</h1><p>The web server is working.</p>' | sudo tee /usr/share/nginx/html/index.html

# ```

# 

# Checked the service and its local response:

# 

# ```bash

# sudo systemctl status nginx --no-pager

# curl -I http://localhost

# ```

# 

# \## Troubleshooting ticket 1: Session Manager connection

# 

# \*\*Issue:\*\* Systems Manager reported that the EC2 instance was not connected.

# 

# \*\*Investigation:\*\* Checked the instance details and found that it did not have a public IPv4 address.

# 

# \*\*Resolution:\*\* Allocated and associated an Elastic IP address with the instance.

# 

# \*\*Verification:\*\* Successfully connected to the instance using Session Manager.

# 

# \*\*Lesson learned:\*\* An instance using a public subnet and internet gateway still needs an appropriate public IP address and network configuration to reach public AWS service endpoints through that path.

# 

# \## Troubleshooting ticket 2: Website not loading

# 

# \*\*Issue:\*\* The test website did not load in a browser.

# 

# \*\*Investigation:\*\* Checked the Nginx service status and used `curl -I http://localhost` to confirm that the web server responded locally on the instance. This narrowed the investigation to the path between the browser and the instance.

# 

# \*\*Cause:\*\* The EC2 security group was blocking browser access to the web server.

# 

# \*\*Resolution:\*\* Corrected the security group rule for web access.

# 

# \*\*Verification:\*\* Confirmed that the Cloud Support Lab page loaded in the browser.

# 

# \*\*Example customer update:\*\* The website is available again. I confirmed the web service was running, corrected the network access rule, and verified that the page loaded successfully.

# 

# \## Monitoring

# 

# Created a CloudWatch alarm to monitor the EC2 instance's average

# CPU utilization over a five-minute period. The alarm sends an email

# notification through an Amazon SNS topic when its threshold is met.

# 

# To test the notification, I temporarily changed the threshold to

# greater than or equal to 0%, confirmed that the alarm entered the

# ALARM state, and received an email. I then restored the threshold

# to greater than 70%.

# 

# A CPU alarm does not reliably detect an Nginx service outage.

# A separate website availability check would be a future improvement.





# \## Troubleshooting ticket 3: Simulated Nginx service outage

# 

# \*\*Scenario:\*\* A customer reports that the website is unavailable.

# 

# \*\*Issue:\*\* The website stopped loading after I intentionally stopped

# the Nginx service to simulate an outage.

# 

# \*\*Investigation:\*\*

# 

# \- Checked the Nginx service status using:

# 

# &#x20; ```bash

# &#x20; sudo systemctl status nginx --no-pager

# &#x20; ```

# 

# \- Reviewed recent service logs using:

# 

# &#x20; ```bash

# &#x20; sudo journalctl -u nginx --no-pager -n 30

# &#x20; ```

# 

# \- Confirmed that Nginx was inactive.

# 

# \*\*Cause:\*\* The Nginx web service had been intentionally stopped

# for this exercise.

# 

# \*\*Resolution:\*\* Restarted the service using:

# 

# ```bash

# sudo systemctl start nginx

# ```

# 

# \*\*Verification:\*\*

# 

# \- Checked the service status and confirmed it was active.

# \- Refreshed the website in a browser and confirmed it loaded.

# 

# \*\*Customer update:\*\* The website is available again. I found that

# the web service had stopped, restarted it, and verified that the

# page was loading.

# 

# \*\*Lesson learned:\*\* Checking the service status and logs helps

# identify whether a website issue is caused by the web server.



# \## Evidence

# 

# The `screenshots` folder documents the VPC, subnet, internet gateway,

# route table, network ACL, security groups, EC2 instance, and Session

# Manager connection. It also shows the connection issues I investigated

# and the steps I took to restore access to the website.

# 

# The screenshots include the simulated Nginx service outage, the

# restarted service, and the website working after recovery.

# 

# Sensitive information, including AWS account details and public IP

# addresses, has been redacted from the screenshots.





# \## Lessons learned

# 

# \- A public subnet alone does not assign a public IP address to an instance.

# \- When an application works locally but not from a browser, network access rules are a useful next place to investigate.

# \- Security groups are stateful and apply to instance network interfaces; network ACLs are stateless and apply at the subnet level.

# \- Documenting symptoms, checks, resolution, and verification makes troubleshooting easier to explain and repeat.

# 

# \## Cleanup and cost

# 

# \*\*Pending:\*\* When the lab is finished, terminate the EC2 instance, release the Elastic IP, remove any alarm and SNS topic created for the project, and review remaining storage and networking resources. Record the resources removed and observed costs here. AWS charges and eligibility depend on the account, Region, resource configuration, and usage.

# 

