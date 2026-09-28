---
title: "Week 2 Worklog"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:

* Practice launching, connecting, and managing both EC2 Windows Server and Amazon Linux instances.
* Master core EC2 administration tasks: modify instance configuration, create/manage EBS Snapshots, create Custom AMI, recover access when losing key pair, share AMI across accounts.
* Successfully deploy a full-stack web application (Node.js CRUD) on both Linux (LAMP) and Windows (XAMPP) platforms.
* Practice Cost & Usage Governance with IAM: restrict by Region, Instance Family, Instance Type, EBS Volume Type, and delete permissions by IP address and time period.
* Clean up all lab resources to avoid unexpected charges.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |s
| --- | --- | --- | --- | --- |
| Tue | - Review Launch & Connect Windows Server 2025 Instance (3.1–3.2) <br> - Launch & Connect Amazon Linux Instance (4.1–4.2): resolved "Connection timed out" by adding SSH inbound rule to Security Group <br> - Create and Manage EBS Snapshots (5.2): Snapshot by Instance vs Volume <br> - Create Custom AMI (5.3): Stop instance before creating AMI to ensure Sysprep completes <br> - Launch an Instance from a Custom AMI (5.4): created new key pair `kp-windows2`, verified Sysprep worked correctly <br> - Recovering Access to Windows Instances via SSM (5.5): created IAM Role (AmazonSSMManagedInstanceCore + AmazonSSMFullAccess), enabled Default Host Management Configuration, installed AWSPowerShell module, ran EC2Rescue tool, recovered Administrator password via Parameter Store and RDP successfully | 09/22/2026 | 09/22/2026 | <https://000004.awsstudygroup.com/5-amazonec2basic/> |
| Wed | - Recovering Access to Linux Instances (5.6): created new key pair, used cloud-init User data to inject new public key into `authorized_keys`, connected successfully with PuTTY <br> - Remote Desktop to EC2-Ubuntu (5.7): installed Xfce4 desktop + xRDP, configured RDP inbound rule (My IP), connected via Remote Desktop Connection <br> - Amazon EBS Snapshots Archive – Optional (5.8): deregistered AMI, archived snapshot (~75% cost savings), restored with Permanent mode <br> - Share AMI – Optional (5.9): shared Custom AMI with another AWS Account via Account ID (Private mode) | 09/23/2026 | 09/23/2026 | <https://000004.awsstudygroup.com/5-amazonec2basic/> |
| Thu | - Summarize content and write Week 2 worklog report (sections 3–5) | 09/24/2026 | 09/24/2026 | — |
| Fri | - Install LAMP Web Server on Amazon Linux 2023 (6.1): adapted `dnf` commands for AL2023 (instead of `amazon-linux-extras`), secured MariaDB with `mysql_secure_installation`, installed phpMyAdmin, created `awsuser` database and `user` table <br> - Install Node.js on Amazon Linux 2023 (6.2): installed via nvm, verified Security Group ports 22/80/443/5000 <br> - Deploy AWS FCJ Management application on Linux (6.3): cloned repo, installed Express stack dependencies, configured `.env`, added 18 sample users via phpMyAdmin, fully tested CRUD operations | 09/25/2026 | 09/25/2026 | <https://000004.awsstudygroup.com/6-awsfcjmanagement-linux/> |
| Sat | - Install XAMPP on Windows Server 2025 (7.1): enabled Apache + MySQL, created identical `awsuser` database and `user` table <br> - Install Node.js + Git + VS Code on Windows (7.2) <br> - Deploy AWS User Management application on Windows (7.3): resolved npm high-severity vulnerabilities by updating nodemon, added 18 sample users, fully tested Add/View/Edit/Delete via web UI | 09/26/2026 | 09/26/2026 | <https://000004.awsstudygroup.com/7-awsfcjmanagement-windows/> |
| Sun | - **Cost & Usage Governance with IAM** (8.1–8.6): <br>&emsp; + 8.1 Restrict by Region: policy `RegionRestrict` (only `ap-southeast-1`), created group `CostTest` and user `TestUser` <br>&emsp; + 8.2 Restrict by Instance Family: policy `EC2_FamilyRestrict` (only `t3.*`, `t4g.*`, `m5.*`) <br>&emsp; + 8.3 Restrict by Instance Type: policy `EC2_InstanceTypeRestrict` (only `t3.small`, `t3.large`) <br>&emsp; + 8.4 Restrict EBS Volume Type: only `gp3` allowed <br>&emsp; + 8.5 Restrict Terminate by Source IP: policy `IP_Restrict` <br>&emsp; + 8.6 Restrict Terminate by Time Period: policy `EC2_TimeRestrict` (DateGreaterThan / DateLessThan) <br> - **Clean up resources** (9): terminated all EC2 instances, deregistered AMIs, deleted Snapshots / Security Groups / Key Pairs / VPC, deleted IAM User / Group / Policies / Roles | 09/27/2026 | 09/27/2026 | <https://000004.awsstudygroup.com/8-costusagegovernance/> <br> <https://000004.awsstudygroup.com/9-cleanup/> |

### Week 2 Achievements:

* Practiced the complete advanced EC2 instance management lifecycle: Launch → Snapshot → Create Custom AMI → Launch from AMI → Recover access when losing Key pair.

* Mastered the difference between EBS Snapshot by Volume (backup a specific volume) and by Instance (backup all volumes attached to the instance simultaneously).

* Understood the correct Windows Custom AMI creation process: instance must be fully stopped before creating the AMI so that Sysprep completes properly.

* Successfully debugged a real error chain when recovering Windows access via Systems Manager:
  * Missing / insufficient IAM Role permissions (AmazonSSMManagedInstanceCore → AmazonSSMFullAccess)
  * SSM Agent not registered (checked via Fleet Manager, enabled Default Host Management Configuration)
  * AWSPowerShell module installation failed due to missing NuGet provider — fixed with `Install-PackageProvider`
  * Missing `ssm:PutParameter` permission when running EC2Rescue tool

* Understood and practiced recovering Linux instance access by editing User data with cloud-init to inject a new public key into `authorized_keys` without prior direct access to the instance.

* Successfully installed Desktop environment (Xfce4) and xRDP on Ubuntu, enabling Remote Desktop access to an EC2 Linux instance instead of terminal-only access.

* Understood EBS Snapshots Archive: long-term storage with ~75% lower cost, mandatory AMI deregistration before archiving, and minimum 90-day retention period.

* Learned how to share a Custom AMI across multiple AWS Accounts via Account ID for synchronized infrastructure deployment.

* Successfully deployed a complete LAMP stack (Apache, MariaDB, PHP) on Amazon Linux 2023 and adapted installation commands from Amazon Linux 2 (`yum` / `amazon-linux-extras`) to Amazon Linux 2023 (`dnf`).

* Successfully deployed the same full-stack web application (Node.js + Express + Express-Handlebars + MySQL) on **both** Linux (LAMP) and Windows (XAMPP) platforms, comparing the tooling differences:
  * Linux: `dnf` / `systemctl`, edit files with `vi`, install Node.js via nvm
  * Windows: XAMPP GUI installer, services via XAMPP Control Panel, edit code with Visual Studio Code, install Node.js/Git via installer

* Configured basic MariaDB security and managed the database visually via phpMyAdmin on both platforms.

* Handled npm audit high-severity vulnerability warnings by updating packages to the latest versions.

* Connected Node.js / full-stack knowledge from school coursework directly with real AWS infrastructure, gaining the ability to perform cross-platform application deployment.

* Strengthened the skill of reading and debugging technical English error logs (IAM permission errors, PowerShell verbose logs, npm audit) to independently identify root causes and remediation steps.

* **Cost & Usage Governance with IAM:**
  * Mastered IAM Policy Condition Keys for cost control: `aws:RequestedRegion`, `ec2:InstanceType`, `ec2:VolumeType`, `aws:SourceIp`, `aws:CurrentTime`.
  * Practiced the least-privilege principle: started with a broad policy (Region) → narrowed by Family → specific Instance Type → combined with Volume Type.
  * Understood conditional Deny mechanisms (`NotIpAddress`, `DateGreaterThan` / `DateLessThan`) to protect resources from unauthorized deletion by IP or sensitive time windows.
  * Applied IAM Group + Customer managed policies for centralized permission management, easy attach/detach, and audit.
  * Performed full clean-up: terminated instances, deregistered AMIs, deleted Snapshots / Security Groups / Key pairs / VPC, and cleaned up IAM User / Group / Policy / Role — preventing unexpected post-lab charges.
