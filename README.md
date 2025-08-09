# ☁️ Securing AWS Endpoint with Elastic SIEM & AI Automation 🤖

![AWS](https://img.shields.io/badge/Cloud-AWS-orange)
![Elastic](https://img.shields.io/badge/SIEM-Elastic-blue)
![Tines](https://img.shields.io/badge/Automation-Tines-purple)
![Windows Server](https://img.shields.io/badge/OS-Windows%20Server%202025-lightgrey)
![AI](https://img.shields.io/badge/AI-Integrated-green)

This lab replicates real-world SOC workflows from log ingestion and correlation to automated alert triage and incident escalation driven by AI-powered orchestration.

## 🎯 Objective

Deploy a Windows Server 2025 endpoint on AWS EC2, integrate it with Elastic SIEM for log ingestion and detection, and connect it to Tines for AI-driven alert automation. The setup demonstrates end-to-end endpoint security monitoring with automated incident handling.

## 🧠 Skills Demonstrated
- Deployment and configuration of **Windows Server 2025** on AWS EC2
- Installation and setup of **Elastic Agent** for log forwarding
- Creation and tuning of **Elastic SIEM correlation rules**
- Integration of **Tines** for automated alert triage and escalation
- Endpoint security configuration (firewall, antivirus, logging)
- Workflow automation for SOC tasks using AI-powered orchestration
- Simulation of detection and response scenarios

# 🛠️ Setup Walkthrough

The AWS-hosted Windows Server 2025 instance serves as the monitored endpoint in this lab. Logs from the endpoint are forwarded to **Elastic SIEM** via the Elastic Agent, where detection rules identify suspicious activity. When an alert is triggered, **Tines** automatically evaluates it, dismissing false positives or escalating incidents to an analyst.

## 🚀 Deployment Steps
1. Provisioned a **Windows Server 2025** instance on AWS EC2
2. Installed the **Elastic Agent** to forward security logs
3. Configured **Elastic SIEM** detection and correlation rules
4. Integrated **Tines** to receive and process alerts from Elastic
5. Simulated attack scenarios to validate detection and automation workflows
