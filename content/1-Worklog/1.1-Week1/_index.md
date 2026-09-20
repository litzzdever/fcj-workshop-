---
title: "Week 1 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Week 1 Objectives:

* Get familiar with the AWS Management Console, create and configure an AWS account.
* Understand Amazon EC2 and related services, and how to use the AWS Console to manage resources.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| Wed | - Create an AWS Management Console account (Root user) <br> - Create an AWS Builder ID <br> - Learn the difference between Root user, IAM user, and AWS Builder ID <br> - Sign in to the AWS Management Console, get familiar with the Console home page <br> - Learn about MFA (Multi-Factor Authentication) for the root user: <br>&emsp; + Passkey / Security key <br>&emsp; + Authenticator app <br> - Switch to a Region closer to Vietnam (Singapore / Jakarta) | 09/16/2026 | 09/16/2026 | <https://docs.aws.amazon.com/> |
| Thu | - Learn about Amazon EC2: <br>&emsp; + Instance and instance type concepts <br>&emsp; + AMI (Amazon Machine Image) <br>&emsp; + Core features: EBS volume, Key pair, Security group <br> - Learn about Amazon EC2 Auto Scaling: <br>&emsp; + Auto Scaling group, scaling policies <br>&emsp; + Real-world applications (e-commerce, ride-hailing apps, batch jobs...) <br> - Learn about AWS Backup: <br>&emsp; + Centralized backup, backup plans <br>&emsp; + Lifecycle policy, Backup Vault Lock <br> - Learn about Amazon CloudWatch: <br>&emsp; + Metrics, Alarms, Dashboards <br>&emsp; + Application Performance Monitoring (APM), log management <br> - Learn about EC2-related services (Load Balancing, GuardDuty, Systems Manager...) <br> - Learn about EC2 pricing models (Free Tier, On-Demand, Savings Plans, Reserved Instances, Spot Instances, Dedicated Host...) | 09/17/2026 | 09/17/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| Sat | - **Practice Tutorial** "Launch my first EC2 instance" (Task 1-4): <br>&emsp; + Task 1: Launch instance <br>&emsp; + Task 2: Find instance <br>&emsp; + Task 3: View instance configuration <br>&emsp; + Task 4: Terminate instance <br> - Read Reference for Amazon EC2 instance configuration parameters <br> - Read Launch an EC2 instance using the launch instance wizard (Quick Launch vs Defined Parameters) <br> - Learn about Launch EC2 instances using a launch template | 09/19/2026 | 09/19/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| Sun | - **Practice Tutorial** "Launch, Connect, and Stop your Instance" (Task 1-9): <br>&emsp; + Task 1-2: Learn EC2 components (AMI, Instance type, Key pair, Network, Security group, EBS) and the technical diagram <br>&emsp; + Task 3: Create a Key pair (ED25519, .pem format) <br>&emsp; + Task 4: Launch a test instance with the Key pair attached <br>&emsp; + Task 5-6: Find and view detailed instance configuration <br>&emsp; + Task 7-8: Connect via SSH from Windows PowerShell (resolved a private key permission error using `icacls`), practiced basic Linux commands (`whoami`, `pwd`, `cat`) <br>&emsp; + Task 9: Stop the instance <br> - Learn and practice creating a custom VPC (Multi-AZ, Public/Private subnets) for both Linux and Windows instances <br> - Create the `Linux-SG` Security Group (7 inbound rules) and learn about the `Windows-SG` Security Group (8 inbound rules) <br> - Write the Week 1 worklog report | 09/20/2026 | 09/20/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> <br> <https://000004.awsstudygroup.com/2-prerequiste/> |

### Week 1 Achievements:

* Understood the concept of Amazon EC2 and its role within the AWS Compute ecosystem.

* Learned the core components that make up an EC2 instance:
  * AMI (Amazon Machine Image)
  * Instance type
  * Key pair
  * VPC / Subnet
  * Security group
  * EBS volume

* Successfully created and configured an AWS account (Root user) and an AWS Builder ID, and understood the intended use of each.

* Practiced the full lifecycle of an EC2 instance: Launch → Find → View configuration → Connect (SSH) → Stop / Terminate.

* Created and managed a Key pair (ED25519, .pem format) for SSH authentication.

* Successfully connected via SSH to an EC2 Linux instance from Windows PowerShell; resolved a private key (.pem) permission error using the `icacls` command.

* Became familiar with basic Linux commands on the server (`whoami`, `pwd`, `cat`...).

* Learned and practiced creating a custom VPC with a Multi-AZ structure (2 Availability Zones, 2 Public subnets, 2 Private subnets) for both Linux and Windows instances.

* Created custom Security Groups with multiple inbound rules (SSH, ICMP, HTTP, HTTPS, MySQL, Custom TCP); understood and correctly handled the difference between IPv4 and IPv6 sources.

* Learned about EC2 Auto Scaling, AWS Backup, and Amazon CloudWatch, and their role in operating EC2 in practice.

* Learned about services related to EC2 (Elastic Load Balancing, GuardDuty, Systems Manager...) and pricing models (On-Demand, Spot, Reserved Instances, Savings Plans...).

* Understood the concept of Launch Templates and their use in Auto Scaling Groups / EC2 Fleets.

* ...
