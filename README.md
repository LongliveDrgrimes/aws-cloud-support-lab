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

# \*\*Planned:\*\* Create a CloudWatch CPU utilization alarm and configure an Amazon SNS email notification. Record the threshold, test method, and actual result here after completing the exercise.

# 

# A CPU alarm does not reliably detect an Nginx service outage. A separate service-availability check would be a useful future improvement.

# 

# \## Next exercise: Simulated service outage

# 

# \*\*Planned:\*\* Intentionally stop Nginx, inspect the service status and logs, restart it, and verify the website. Document the commands, observations, and outcome after completing the exercise.

# 

# \## Evidence

# 

# Screenshots can be added to the `screenshots` folder to show the VPC, route table, security group, Session Manager connection, working website, and troubleshooting results. Remove or redact account numbers, public IP addresses, email addresses, credentials, and other sensitive information before publishing.

# 

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

