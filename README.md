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

## 🛠️ Setup Walkthrough

I started by spinning up a Windows Server 2025 instance on AWS EC2 to serve as my monitored endpoint. After installing the Elastic Agent, I configured it to send security event logs to **Elastic SIEM**, where I had set up detection rules to catch suspicious activities.  

Once the SIEM was generating alerts, I connected **Tines** to automate the triage process. Tines would check the alert context, decide if it was a false positive or a genuine incident, and then either close it or escalate it to me for review. This allowed me to test both the detection and response phases without manual intervention for every alert.

## 🚀 Deployment Steps
1. Launched a **Windows Server 2025** EC2 instance in AWS
2. Installed the **Elastic Agent** and configured log forwarding
3. Created **Elastic SIEM** detection and correlation rules
4. Integrated **Tines** with Elastic for automated alert workflows
5. Simulated security events to test detection and automation
