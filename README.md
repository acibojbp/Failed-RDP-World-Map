# Failed RDP World Map

## Overview

In this project, we'll be diving into the intricacies of setting up a virtual machine honeypot. We'll walk through creating the virtual environment, fine-tuning the security settings on a Windows 10 VM, and integrating with Log Analytics Workspaces and Microsoft Defender for Cloud. Additionally, we'll deploy Microsoft Sentinel for enhanced threat detection and configure visualization tools within the Sentinel workbook. To wrap things up, we'll explore remediation steps aimed at boosting overall security.

![Failed RDP World Map Diagram](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/c54d3b60-84e4-4ea9-a2ce-c541743a3677)

### Objectives

- Deploy a virtual machine honeypot on Azure to lure potential attackers.
- Integrate the honeypot with Log Analytics Workspaces and Microsoft Defender for Cloud for data collection and threat detection.
- Utilize Microsoft Sentinel for data visualization and monitoring to analyze geolocation data of unauthorized access attempts.
- Implement remediation steps to enhance the security posture of the environment.

### Skills Learned

- Virtual machine deployment and management on Microsoft Azure.
- Configuration of networking and security groups.
- Setup and configuration of Log Analytics Workspaces and Microsoft Defender for Cloud.
- PowerShell scripting for log data collection and analysis.
- Data visualization and monitoring using Microsoft Sentinel.
- Enhanced understanding of RDP security and best practices for remediation.

### Tools Used

- Microsoft Azure for virtual machine deployment and management.
- Log Analytics Workspaces for data collection and analysis.
- Microsoft Defender for Cloud for threat detection and management.
- Microsoft Sentinel for data visualization and monitoring.
- Remmina (RDP client) for remote connection to the virtual machine.
- Windows PowerShell ISE for script creation and execution.

## Contents
- [Create Virtual Machine Honeypot](#create-virtual-machine-honeypot)
- [Setting Up Log Analytics Workspaces and Microsoft Defender for Cloud](#setting-up-log-analytics-workspaces-and-microsoft-defender-for-cloud)
- [Deploy Microsoft Sentinel](#deploy-microsoft-sentinel)
- [Connecting to the Virtual Machine](#connecting-to-the-virtual-machine)
- [Configuring Windows 10 Virtual Machine Security Settings](#configuring-windows-10-virtual-machine-security-settings)
- [Configuring Custom Log in Log Analytics Workspaces](#configuring-custom-log-in-log-analytics-workspaces)
- [Setting Up Visualization in Sentinel Workbook](#setting-up-visualization-in-sentinel-workbook)
- [Remediation Steps for Enhanced Security](#remediation-steps-for-enhanced-security)

## Create Virtual Machine Honeypot
  
First, we'll set up a virtual machine to act as our honeypot, designed to lure hackers targeting our open RDP port.

Since I'm already familiar with creating a virtual machine in Azure, I'll streamline the process with a few modifications:

1. Create a new resource group named "Sentinel."
2. Name the virtual machine "HoneypotRDP," choosing the region closest to my location.
3. Adjust the security type to "Standard" to maximize vulnerability.
4. Use the Windows 10 Pro image and keep all other settings at their defaults.

Additionally, create an admin account with the username "HoneyRDP" and a secure password for later VM access.

Next, under the "Networking" section:

1. Change the NIC network security group to "Advanced."
2. Click on "Create new" to set up a new rule.
3. Define a rule to open all inbound ports for this virtual machine.

Finally, proceed with "Review + Create" to deploy the virtual machine. Once deployed, navigate to the resource and note down the public IP address; we'll need this to connect to our VM

## Setting Up Log Analytics Workspaces and Microsoft Defender for Cloud

1. **Log Analytics Workspace Creation**:
	- Navigate to the search bar and search for "Log Analytics Workspace".
	- Click on "Create".
	- Place this within the same resource group.
	- Name it "LAW-HoneypotRDP".
	- Click on "Review + Create".
2. **Microsoft Defender for Cloud**:
	- Once the deployment of the Log Analytics Workspace is complete, proceed to "Microsoft Defender for Cloud".
  
Microsoft Defender for Cloud gathers information from virtual machines and various Azure resources to detect possible security risks and vulnerabilities. This data is subsequently combined with Azure Monitor and Azure Log Analytics for storage and further analysis.

1. **Accessing Microsoft Defender for Cloud**:
    - Navigate to Microsoft Defender for Cloud.
2. **Adjusting Management Settings**:
    - On the left-hand side, locate the "Management" section.
    - Proceed to "Environment Settings" and locate your instance.
    - Enable "Servers" and disable "SQL servers" as it won't be required. Save the changes.
3. **Data Collection Configuration**:
    - Go to "Data Collection".
    - Select "All Events".

Return to the Log Analytics Workspaces and select your instance.

1. **Configure Virtual Machines**:
    - On the left-hand side, click on "Virtual Machines".
    - Choose the virtual machine you previously created.
2. **Initiate Connection**:
    - Click on "Connect".
    - Wait for the connection to be established.

## Deploy Microsoft Sentinel

Type "Microsoft Sentinel" in the search bar.

1. **Navigate to Sentinel**:
    - Head over to Sentinel and click on "Create".

2. **Add Log Analytics Workspace**:
    - Select the Log Analytics Workspace option and click "Add".

Wait for the deployment to complete.

As I am utilizing the free version of Azure, this activates my free trial for Microsoft Sentinel.

During this trial period, both Microsoft Sentinel and Log Analytics provide up to 10 GB/day for free. Any data consumption exceeding this 10 GB/day limit will incur charges. Nevertheless, this allocation should be ample for our lab's requirements.

## Connecting to the Virtual Machine

#### Connecting to Windows VM from Ubuntu using Remmina

If you are using Ubuntu, you'll be utilizing Remmina to connect to the Windows VM. Remmina comes pre-installed on Ubuntu and is the default RDP client.

**Note:** Remmina is the default RDP client on Ubuntu 20.04 and 22.04. To verify if the Remmina client is installed, access the command line and execute the following command:

```bash
dpkg -l | grep remmina
```

If Remmina is not installed on your Ubuntu system, update your packages with:

```bash
sudo apt update && sudo apt upgrade -y
```

Then, install Remmina using:

```bash
sudo apt install remmina
```

You can launch Remmina by typing:

```bash
sudo remmina
```

Alternatively, navigate to the applications folder and search for "Remmina" to run the application.

[How to Connect to a Windows PC from Ubuntu using RDP](https://phoenixnap.com/kb/ubuntu-rdp-to-windows)  
[Access a remote desktop | Ubuntu](https://ubuntu.com/tutorials/access-remote-desktop#1-overview)

If you encounter issues setting up Remmina on Ubuntu, refer to the following resources for assistance:

#### Connecting to Virtual Machine using Windows Built-in RDP

If you are using Windows, follow these steps to connect to the virtual machine using the built-in Remote Desktop Protocol (RDP):

1. **Open Remote Desktop Connection**:
    
    - Press `Win + R`, type `mstsc`, and hit `Enter` to open the Remote Desktop Connection app.
2. **Enter VM Details**:
    
    - In the "Computer" field, enter the public IP address of your virtual machine.
    - Click on "Connect".
3. **Provide Credentials**:
    
    - Enter the username and password of the virtual machine when prompted.
4. **Connect**:
    
    - Click "OK" or "Connect" to establish the RDP connection to your virtual machine.

## Configuring Windows 10 Virtual Machine Security Settings

After connecting to the VM, follow these steps to navigate to the Event Viewer and disable the firewall:

1. **Open Event Viewer**:
    - Search for "Event Viewer" in the taskbar.
    - Navigate to `Windows Logs > Security`.

1. **Disable Firewall**:
    - In the taskbar search, type `wf.msc` and hit `Enter`.
    - Click on "Windows Defender Firewall Properties".
    - Turn off the Firewall state for each of the profiles: Domain, Private, and Public.

### Setup ipgeolocation.io 

Visit [ipgeolocation.io](https://ipgeolocation.io/) to sign up and obtain an API key. 
Note: Free accounts are limited to 1k API requests/day.

### Powershell Script

We'll use a PowerShell script that leverages a third-party API key to fetch geolocation data. This data will be saved to a log file, which our Log Analytics Workspace will subsequently feed into Sentinel.

The script is saved in my repository, or you can check out Josh Madakor's repository. All credits for this script go to him. Here is the link to his repository: [joshmadakor1/Sentinel-Lab](https://github.com/joshmadakor1/Sentinel-Lab/tree/main)

Open Windows PowerShell ISE on the VM via the search bar. Create a new script, and paste the provided script.

To ensure the script runs correctly, replace the placeholder API key with your own. Save the script with a name of your choice; for example, `Custom_Security_Log_Exporter.ps1`.

After running the script, the logs will be saved in `C:\ProgramData\failed_rdp.log`.

You'll need to open this file, copy its contents, and save them in a text file. We will upload this text file to the Log Analytics Workspace as a sample log in the next step.

Note: The folder `C:\ProgramData\` is hidden by default. You'll need to enable the option to view hidden items to access and locate `failed_rdp.log`.
  
## Configuring Custom Log in Log Analytics Workspaces

Back at the Log Analytics Workspaces, you will navigate to Custom Logs and select "Add Custom Logs".

To proceed:

- Go to LAW, then select your workspace.
- On the left-hand side, click on "Tables" > "Create" > "Custom Log".

Upload the text file containing the sample log copied earlier. Move to "Collection paths". Paste the location on the virtual machine where the logs are stored, which is `C:\ProgramData\failed_rdp.log`.

Under "Details":

- Name the Custom log as: `FAILED_RDP_WITH_GEO`.
- Click "Next" > "Create".

Return to Log Analytics Workspaces > "Logs" and execute the query: `FAILED_RDP_WITH_GEO_CL`. It might take some time for the query to sync up and display results. Once you've confirmed that the results are being pulled up correctly, you can proceed to create a workbook to visualize this data.

## Setting Up Visualization in Sentinel Workbook

Navigate to Sentinel > New Workbook > Create New Query and input the provided query. Set the visualization to "Map", then run the query. Enable auto-refresh every 5 minutes and wait for attacks to populate on the map.

In Map settings:
- Set the metric label to "Country".
- Set the metric value to "event_count".

```KQL
FAILED_RDP_WITH_GEO_CL
| extend username = extract(@"username:([^,]+)", 1, RawData),
    timestamp = extract(@"timestamp:([^,]+)", 1, RawData),
    latitude = extract(@"latitude:([^,]+)", 1, RawData),
    longitude = extract(@"longitude:([^,]+)", 1, RawData),
    sourcehost = extract(@"sourcehost:([^,]+)", 1, RawData),
    state = extract(@"state:([^,]+)", 1, RawData),
    label = extract(@"label:([^,]+)", 1, RawData),
    destination = extract(@"destinationhost:([^,]+)", 1, RawData),
    country = extract(@"country:([^,]+)", 1, RawData)
| where destination != "samplehost"
| where sourcehost != ""
| summarize event_count=count() by latitude, longitude, sourcehost, label, destination, country
```

## Remediation Steps for Enhanced Security

**Secure Windows**

- Limit the number of user accounts that can establish an RDP connection.
- Create dedicated accounts for RDP to restrict access to critical systems or data.
- Enforce strong password policies for RDP user accounts.
- Implement [two-factor authentication](https://phoenixnap.com/glossary/two-factor-authentication) for RDP sessions.
- Activate Network Level Authentication (NLA) to require authentication before establishing a session.
- Temporarily lock out user accounts after multiple unsuccessful login attempts.

**Secure the Network**

- Use a firewall to restrict RDP access to specific IP addresses.
- Change the default RDP port (3389) to a non-standard port number to mitigate [brute-force attacks](https://phoenixnap.com/blog/brute-force-attack).
- Use a VPN to encrypt the data transfer between client and host machines.
- Alternatively, use [SSH port forwarding](https://phoenixnap.com/kb/ssh-port-forwarding) to create an encrypted tunnel for the RDP session if a VPN is not an option.

**Secure Linux**

- Regularly update the RDP Client to the latest version to benefit from security patches and improvements.
- Disconnect from an RDP session when it is not being used and set timeouts for idle sessions.
- Limit the number of simultaneous active sessions.
