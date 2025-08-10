# ☁️ Securing AWS Endpoint with Elastic SIEM & AI Automation 🤖

![AWS](https://img.shields.io/badge/Cloud-AWS-orange)
![Elastic](https://img.shields.io/badge/SIEM-Elastic-blue)
![Tines](https://img.shields.io/badge/Automation-Tines-purple)
![Windows Server](https://img.shields.io/badge/OS-Windows%20Server%202025-lightgrey)
![AI](https://img.shields.io/badge/AI-Integrated-green)

This lab replicates a real SOC workflow from collecting endpoint logs to correlating events in Elastic SIEM and using AI automation in Tines for alert triage and incident escalation.

## 🎯 Objective

I built this lab to explore how cloud-hosted endpoints can be monitored and protected using **Elastic SIEM** and **AI-driven automation** with Tines. My goal was to simulate a complete detection and response cycle, from collecting endpoint logs to automating incident triage in a way that mirrors actual SOC workflows.


## 🧠 Skills Demonstrated
- Deployment and configuration of **Windows Server 2025** on AWS EC2
- Installation and setup of **Elastic Agent** for log forwarding
- Creation and tuning of **Elastic SIEM correlation rules**
- Integration of **Tines** for automated alert triage and escalation
- Endpoint security configuration (firewall, antivirus, logging)
- Workflow automation for SOC tasks using AI-powered orchestration
- Simulation of detection and response scenarios

# 🛠️ Setup Walkthrough

I started by spinning up a Windows Server 2025 instance on AWS EC2 to serve as my monitored endpoint. After installing the Elastic Agent, I configured it to send security event logs to **Elastic SIEM**, where I had set up detection rules to catch suspicious activities.  

> 💡 If you don’t already have an AWS account, take a look at this guide before starting: [AWS Account Setup](https://learn.nextwork.org/projects/aws-account-setup)

Once the SIEM was generating alerts, I connected **Tines** to automate the triage process. Tines would check the alert context, decide if it was a false positive or a genuine incident, and then either close it or escalate it to me for review. This allowed me to test both the detection and response phases without manual intervention for every alert.

## 1️⃣ **Launch AWS EC2 Instance**  

I started by creating a Windows Server 2025 instance in AWS EC2 to serve as the monitored endpoint for this lab.  

I named the instance **Windows-Srv** and selected the **Microsoft Windows Server 2025 Base** AMI from the Quick Start tab. 
This AMI is free tier eligible and provided directly by Amazon.

![AWS EC2 - Name and AMI Selection](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/Name_and_machine_type.png)

I chose the **m7i-flex.large** instance type (2 vCPU, 8 GB RAM) and created a new key pair named **Windows-Srv-key** for RDP access. 
The `.pem` file was downloaded and saved localy.  

![AWS EC2 - Instance Type and Key Pair](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/instance_and_key.png)  

In the network settings, I allowed inbound **RDP traffic** only from my IP address and configured **30 GB gp3 SSD storage**. Encryption was left disabled.  

![AWS EC2 - Network Settings and Storage](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/network_and_storage.png)  

After reviewing all settings, I clicked **Launch instance** to deploy the server.  

![AWS EC2 - Launch Instance](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/launch_instance.png)

After launching, I retrieved the admin password from the AWS console, connected via RDP, and completed initial Windows updates.

### 2️⃣ **Install Elastic Agent**  

In Elastic Cloud, I navigated to **Fleet → Add Agent**, selected **Windows**, and copied the provided PowerShell install command.  
On the EC2 instance, I ran the command in an elevated PowerShell session, which downloaded, installed, and enrolled the Elastic Agent into Fleet.  
After a few minutes, the agent showed as **Healthy** in the Elastic dashboard.

### 3️⃣ **Enable and Configure SIEM Rules**  

Inside the Elastic Security app, I enabled a set of built-in Windows rules to detect:  
- Suspicious PowerShell execution  
- RDP brute force attempts  
- Service installation events  
I also set rules to generate alerts directly in the Elastic Security console for faster testing.

### 4️⃣ **Integrate with Tines**  

I created a new **Story** in Tines to receive alerts from Elastic via webhook.  

The workflow:  
- Receive alert JSON payload from Elastic  
- Parse event details (host, user, rule name, severity)  
- If low severity or known test activity → close  
- Else → send escalation to my email for review  
This allowed me to automate triage and reduce manual alert handling.

### 5️⃣ **Simulate and Validate the Workflow**  

To test end-to-end functionality, I generated benign triggers such as multiple failed RDP logins and harmless PowerShell commands.  
The events were ingested by Elastic, matched to active rules, and sent to Tines.  
Tines correctly closed low-priority events and escalated high-priority detections, confirming the lab workflow was operational.
