# ☁️ Securing AWS Endpoint with Elastic SIEM & AI Automation 🤖

![AWS](https://img.shields.io/badge/Cloud-AWS-orange)
![Elastic](https://img.shields.io/badge/SIEM-Elastic-blue)
![Tines](https://img.shields.io/badge/Automation-Tines-purple)
![Windows Server](https://img.shields.io/badge/OS-Windows%20Server%202025-lightgrey)
![AI](https://img.shields.io/badge/AI-Integrated-green)

This lab replicates a real SOC workflow from collecting endpoint logs to correlating events in Elastic SIEM and using AI automation in Tines for alert summarization.

## 🎯 Objective

I built this lab to explore how cloud-hosted endpoints can be monitored and protected using **Elastic SIEM** and **AI-driven automation** with Tines. My goal was to simulate a complete detection and response cycle, from collecting endpoint logs to automating alerts in a way that mirrors actual SOC workflows.

## 🧠 Skills Demonstrated
- Deployment and configuration of **Windows Server 2025** on AWS EC2
- Installation and setup of **Elastic Agent** with **Elastic Defender (EDR)** for endpoint monitoring
- Log forwarding from the endpoint to **Elastic SIEM**
- Creation of a **custom Elastic SIEM detection rule** for admin sign-ins
- Integration of **Elastic SIEM** with **Tines** via webhook for automated alert handling
- Design of a **Tines automation workflow** using AI summarization and email notifications
- Simulation of an admin login to trigger detection and validate automation

# 🛠️ Setup Walkthrough

I started by spinning up a Windows Server 2025 instance on AWS EC2 to serve as my monitored endpoint. After installing the Elastic Agent, I configured it to send security event logs to **Elastic SIEM**, where I had set up detection rules to catch suspicious activities.  

> 💡 If you don’t already have an AWS account, take a look at this guide before starting: [AWS Account Setup](https://learn.nextwork.org/projects/aws-account-setup)

Once the SIEM was generating alerts, I connected Tines to automatically process them. Tines received the alert data, generated a summary, and emailed it to me for review, allowing me to test the full detection-to-notification flow.

## 1️⃣ **Launch AWS EC2 Instance**  

I started by creating a Windows Server 2025 instance in AWS EC2 to serve as the monitored endpoint for this lab.  

I named the instance **Windows-Srv** and selected the **Microsoft Windows Server 2025 Base** AMI from the Quick Start tab. 

This AMI is free tier eligible and provided directly by Amazon.

![AWS EC2 - Name and AMI Selection](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/Name_and_machine_type.png)

I chose the **m7i-flex.large** instance type (2 vCPU, 8 GB RAM) and created a new key pair named **Windows-Srv-key** for RDP access. 

The `.pem` file was downloaded and saved localy.  

![AWS EC2 - Instance Type and Key Pair](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/instance_and_key.png)  

In the network settings, I allowed inbound **RDP traffic** only from my IP address and configured **30 GB gp3 SSD storage**. 

Encryption was left disabled.  

![AWS EC2 - Network Settings and Storage](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/network_and_storage.png)  

After reviewing all settings, I clicked **Launch instance** to deploy the server.  

![AWS EC2 - Launch Instance](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/launch_instance.png)

Once the instance was running, I selected it from the EC2 dashboard, clicked **Actions → Security → Get Windows password**.  

![AWS EC2 - Get Windows Password](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/get_windows_password.png)  

On the next screen, I uploaded my previously downloaded **Windows-Srv-key.pem** file and clicked **Decrypt password** to reveal the Administrator login credentials.  

![AWS EC2 - Decrypt Password](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/decrypt_password.png)  

## 2️⃣ **Install Elastic Agent & Integrate Elastic Defender**  

With the AWS instance running, I proceeded to install the Elastic Agent so that logs from the Windows Server could be sent to Elastic SIEM for monitoring and analysis.  

First, I logged into [Elastic Cloud](https://cloud.elastic.co/) and created a new account. 

I clicked **Start Free Trial** and made sure to select **Elastic SIEM** during the setup process so the deployment would include all necessary SIEM features by default.  

Once the deployment was ready, I navigated to **Assets → Fleet → Agents → Add Agent**.   

![Elastic Cloud - Add Agent Menu](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/add_agent_elastic.png)  

I clicked Add agent, created a new agent policy named Windows-Srv-AWS, and confirmed that the System integration was included.

![Elastic Cloud - Add Agent Menu](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/create-agent-policy.png)  

I selected **Windows** as the platform and copied the provided PowerShell installation command.

![Elastic Cloud - Add Agent Windows](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/powershell.png)  

On the Windows Server, I opened **PowerShell as Administrator** and pasted the installation command. 

![Elastic Cloud - AWS-powershell](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/aws-powershell.png)  

This command downloaded the Elastic Agent MSI, installed it, and enrolled it into the Fleet using the enrollment token from Elastic Cloud.  

![Elastic Cloud - AWS_Powershell_Success](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/aws-powershell-success.png)  

Once the installation was complete, I returned to the Elastic Cloud Fleet dashboard to confirm the agent’s status. 

The agent showed as **Healthy**, indicating it was successfully connected and sending data.  

![Elastic Cloud - agent_done](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/agent-installed.png)  

Once the agent was enrolled, I added the **Elastic Defend** integration to my `Windows-Srv-AWS` agent policy.  

![Elastic Cloud - integration](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/add_integration.png)

I named the integration `Elastic-Defend-WinSrv-AWS` and selected **Complete EDR (Endpoint Detection & Response)** to enable full telemetry.

![Elastic Cloud - integration](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/edit_integration.png)

> 💡 By doing this, Elastic Defend was **automatically deployed** to my AWS Windows Server endpoint via the Elastic Agent, providing real-time monitoring and protection.  

## 3️⃣ Create Workflow Automation in Tines

I moved on to building an automated workflow in **Tines** that will process the alert, generate an AI summary, and email it directly to me.

I logged into my **Tines** team-workspace and clicked **New Story**, naming it `Admin_Sign-in_Detection` so I could easily identify it later.

![Elastic Cloud - initialize](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/init_workflow.png)

From the left-hand menu, I dragged the **Webhook** action onto the canvas. This is the **entry point** for the alert that Elastic SIEM sends.

![Elastic Cloud - webhook](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/workflow_webhook.png)

Next, I dragged an **AI** action from the menu and connected it to my **Webhook Action**. I renamed it **Summarize webhook data** and set the **Prompt** to:

```
Summarize the following data in one sentence.
Provide some next action steps for the analyst to take when retrieving this alert.
Format this into bullet points to make it easy to read for the email.
```

I made sure to reference the webhook data by adding: `{{ webhook_action }}`

![Elastic Cloud - sumarize](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/workflow_sumarize.png)

Finally, I dragged a **Send Email** action onto the canvas and connected it to my **AI Action**.

I configured it as seen in the image below.

![Elastic Cloud - email](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/workflow_email.png)

At this point, my workflow looked like this:

1️⃣ **Webhook Action** → receives the alert from Elastic  
2️⃣ **Summarize webhook data** (AI Action) → processes and formats the alert details  
3️⃣ **Send Email Action** → delivers the summary to my inbox

![Elastic Cloud - flow](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/Automation_flow_init.png)


## 4️⃣ Create Elastic SIEM Alerts & Connect it to Tines

With my Tines workflow ready, I needed to connect **Elastic SIEM** so that it could send alerts directly into it.

I started in Elastic by navigating to **Management → Stack Management → Connectors** and clicking **Create connector**.

![Elastic Cloud - connector](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/Stack_mgmt_connectors.png)

I chose **Webhook** as the type and named it `Tines_Webhook_Admin_Sign-in`. For the URL, I pasted my webhook URL from Tines Webhook. 

I kept authentication set to **None**, selected **POST** as the method, and saved the connector.

![Elastic Cloud - webhook_elastic](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/weebhook_elastic.png)

Next, I went to **Security → Rules** and clicked **Create new rule**.

![Elastic Cloud - create_rule](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/create_siem_detection_rule.png)

I chose a **Custom Query Rule** and left the **Index Patterns** as they where. For the query, I used: `event.code : "4672"`

I named the rule `Admin_Log-in_Detection`, set the severity to **High**, and moved to the **Actions** section.

![Elastic Cloud - admin_rule](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/admin_detection_rule.png)

In **Rule Actions**, I selected my `Tines_Webhook_Admin_Sign-in` connector. I set it to run **Per rule run** and added a JSON payload to pass the rule name and description to Tines:

```json
{
  "rule_name" : "{{context.rule.name}}",
  "description" : "{{context.rule.description}}"
}
```

![Elastic Cloud - admin_rule_webhook](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/admin_rule_webhook.png)

## 5️⃣ **Simulate and Validate the Workflow**  

With my Elastic SIEM detection rule and Tines automation workflow fully configured, it was time to test the entire pipeline, from detection to automated triage and email alerting.

To simulate a real scenario, I initiated an admin login to my Windows Server 2025 instance hosted in AWS EC2. 

Using the AWS console, I navigated to the RDP client tab for my instance, decrypted the password, and downloaded the .rdp file to connect.

![Elastic Cloud - rdp](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/ec2_rdp.png)

Once I logged in, the Elastic Agent on the server captured the event and forwarded it to Elastic SIEM. The alert immediately appeared in the **Elastic Security → Alerts** panel.

![Elastic Cloud - alerts_panel](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/elastic_ec2_alerts.png)

Because my detection rule was connected to the **Tines_Webhook_Admin_Sign-in** webhook, the alert payload was automatically sent to my Tines workflow, containing data such as the rule name, description, and relevant metadata.

![Elastic Cloud - webhook_json](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/webhook_json.png)

A few moments later, I received the email with the subject **"New Alert!"**.  

It contained the summary and recommendations, although they weren’t formatted in bullet points as intended, not sure why, something to troubleshoot later.  

The email included:  

- A concise summary of the detection  
- Investigation guidance, such as checking the source IP, validating the login time, and correlating with other events  
- Recommendations for further monitoring and possible mitigation steps  

![Elastic Cloud - email-alert](https://github.com/gkopacz/Cloud-SIEM-AI-Automation/blob/main/images/email_alert.png)

This test confirmed that the entire chain worked exactly as designed:  
**log ingestion → detection → webhook → AI analysis → email notification**

## 📌 Closing Notes

This project showed how a single detection can flow from **endpoint logs** to an **AI-generated alert email** in minutes.  
Next, I’ll explore expanding automation with more detection rules, threat intelligence enrichment, and real-time dashboards to make this lab even more capable.
