#### Author: Eric Jarvinen

<span style="color:green"> **Estimated Lab Time - 2-3 hours** </span>

# Getting Started
This lab is for the purpose of training and Proof-of-Concept, It is recommended to follow steps in Azure Portal. The Template and Parameters file are not configured to deploy without modification. 

THIS SHOULD NOT BE DONE IN PRODUCTION OR ON A PERSONAL MACHINE WITH DATA YOU ARE NOT WILLING TO LOSE AND/OR BE COMPRIMSED. USE RESPONSIBILY, NOT LIABLE FOR MISUSE.

[![Example Template](template.json)]([https://labbox.azurewebsites.net/api/Labbox?url=https://supportability.visualstudio.com/AzureIaaSVM/_git/Labbox?path=/SME/Deployment/L200-Deploy-Lab-Oracle_Deploy.json](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/template.json))

After this lab you will be able to:

-Create a Windows and Linux Honeypot in Azure, Configure Azure Sentinel SEIM with Log Analytics, create custom alert rules, and create T-POT Honeypot(Debian).

<span style ="color:red">**The Template and Parameter files won't work as is, recommendation is to follow steps in Azure Portal. Otherwise, modifications need to be made to template such as SubscriptionID.. etc** </span>

## <span class="mw-customtoggle-myDivision">Deploy the Windows Honeypot</span>

<details closed>
<summary>1. Windows Guide </summary>
<pre>
<code>
  
-Create Windows 10 VM with Advanced NSG.

-Created Log Analytics Workspace for log retention.

-Enable Microsoft Defender for Cloud. 

![Picture1.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture1.png)

--Enabled on the Log Analytics Workspace for Servers only.

![Picture2.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture2.png)

-Under Data Collection, under Log Analytics Defender Plans. Select “Capture All Events”. Save.

-Go to log analytics workspace > Virtual Machines  add machine to log analytics (Note this feature will be deprecated in August of 2024 and Azure Monitor should be used for future log gathering.)

![Picture2.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture3.png)

-Go to Microsoft Sentinel > Create New > “add Microsoft Sentinel to a workspace” attach to the log analytics created.

-Next, we need to setup the log data source:

![Picture15.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture15.png)

-Creating Data collection Rule:

![Picture16.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture16.png)

-Add Resources:

![Picture17.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture17.png)

-Data Source:

![Picture18.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture18.png)

-Finish, then select Create.

-Now we will configure the Alert rule to detect the failed logins in Azure Sentinel.

-Azure Sentinel > Analytics > New Rule > Fillout the prompts:

![Picture20.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture20.png)

![Picture21.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture21.png)

-Copy the same query to view failed login attempts:

![Picture22.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture22.png)

![Picture23.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture23.png)

-My example already has failures but yours will once you open the ports on the NSG.

![Picture24.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture24.png)

-Next, Open all ports on your NSG to the internet with a * source and destination. RDP into your server and you can watch the logs in real time using event viewer.

-Event Viewer > Security > Audit Logs > Filter Audit log for Audit Failures or EventID 4625.
Example:

![Picture4.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture4.png)

![Picture5.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture5.png)

It may take up to 15 minutes but with all ports open password spraying usually occurs pretty quickly. When IP's have been identified you can utilize the website below to find the source of attacks:
https://www.ipgeolocation.io 
example:

![Picture6.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture6.png)

![Picture7.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture7.png)

I also recommend using netstat to confirm if any IP's are succesfull at establishing a connection to your VM and over which Port.
Example:

![Picture8.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture8.png)

![Picture9.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture9.png)

Once completed we can view the log from Log Analytics Query, Security Event's in Sentinel, or the Sentinel Dashboards.

LOG ANALYTICS:
![Picture10.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture10.png)

SecurityEvent 
| where EventID ==4625

![Picture11.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture11.png)

SENTINEL INCIDENTS:

![Picture24.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture24.png)

![Picture25.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture25.png)

SENTINEL DASHBOARD: (Overview)

![Picture26.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture26.png)

![Picture27.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture27.png)

-From here you can setup your own runbooks, automation, SEIM management, or maps. 

</code>
</pre>
</details>

## <span class="mw-customtoggle-myDivision">Deploy the Linux Honeypot</span>

<details closed>
<summary>1. Linux Guide </summary>
<pre>
<code>

Linux Honeypot (T-POT): 

IMPORTANT NOTE: While 2 vCPU's and 8GB of RAM may suffice, expect frequent crashes in GUI. Recommendation is to use atleast 4 vCPU and 16 GB of RAM for lab to run smoothly.

-Create Debian 11 Azure VM.

-Create SSH (Port 22 rule) and TPOT (Port 64297) open for access to TPOT browser GUI.

-telekom-security/tpotce: 🍯 T-Pot - The All In One Honeypot Platform 🐝 ([github.com Honeypot Source](https://github.com/telekom-security/tpotce?tab=readme-ov-file#running-in-a-cloud))

Commands to install:

git clone https://github.com/telekom-security/tpotce

cd tpotce/iso/installer/

./install.sh --type=user

-Follow installer creating your GUI login creds (new, different from SSH user)

-Reboot then login with web browser. (NOTE: SSH may fail following install. Interaction should be through GUI.
Example: 

https://<ServerIP>:64297

Once connected open all ports to the internet.

NOTE: This can take up to 15-20 minutes for data to begin populating.

-Go to Attack Map to view real time connections

![Picture29.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture29.png)

-Go to Kibana and pick a dashboard to start viewing data. You can select an existing Board or create your own. 

![Picture30.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture30.png)

![Picture31.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture31.png)

![Picture32.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture32.png)

![Picture33.png](https://github.com/EricJarvinen-MS/AzureHoneypot-PoC/blob/main/Picture33.png)

</code>
</pre>
</details>

I hope you learned something, stay safe!
