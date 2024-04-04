# RDP Security Monitoring and Geolocation Analysis Automation

## Overview

In this project, we'll be diving into the intricacies of setting up a virtual machine honeypot. We'll walk through creating the virtual environment, fine-tuning the security settings on a Windows 10 VM, and integrating with Log Analytics Workspaces and Microsoft Defender for Cloud. Additionally, we'll deploy Microsoft Sentinel for enhanced threat detection and configure visualization tools within the Sentinel workbook. To wrap things up, we'll explore remediation steps aimed at boosting overall security.

![Failed RDP World Map Diagram](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/19a1049a-7b54-458a-ae21-f1858f9a643e)

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
- [Deprovision Resource Groups](#deprovision-resource-groups)


## Create Virtual Machine Honeypot
  
First, we'll set up a virtual machine to act as our honeypot, designed to lure hackers targeting our open RDP port.

Since I'm already familiar with creating a virtual machine in Azure, I'll streamline the process with a few modifications:

1. Create a new resource group named "Sentinel."
2. Name the virtual machine "HoneypotRDP," choosing the region closest to my location.
3. Adjust the security type to "Standard" to maximize vulnerability.
4. Use the Windows 10 Pro image and keep all other settings at their defaults.
5. 
![VirtualBox_Ubuntu_31_03_2024_09_25_38](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/1085b565-9d15-4698-94f2-99fda692e3b1)

Additionally, create an admin account with the username "HoneyRDP" and a secure password for later VM access.

Next, under the "Networking" section:

![VirtualBox_Ubuntu_31_03_2024_09_28_24](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/2e2ba1b9-8d73-4cc7-ab32-170faefc042f)

1. Change the NIC network security group to "Advanced."
2. Click on "Create new" to set up a new rule.
3. Define a rule to open all inbound ports for this virtual machine.

![VirtualBox_Ubuntu_31_03_2024_09_28_43](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/c81bada4-4405-4420-8f5e-a0af7f2e9c0d)

![VirtualBox_Ubuntu_31_03_2024_09_29_13](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/56f27f1f-2986-45db-87ed-ace2c88d0890)

Finally, proceed with "Review + Create" to deploy the virtual machine. Once deployed, navigate to the resource and note down the public IP address; we'll need this to connect to our VM

![VirtualBox_Ubuntu_31_03_2024_09_31_39](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/c4c1285b-d736-4436-8f18-cb56fe71861d)

![VirtualBox_Ubuntu_31_03_2024_09_35_11](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/d59ac4d7-9193-4f6f-9894-036ddfd649e5)

![VirtualBox_Ubuntu_31_03_2024_09_35_40](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/ee393ada-a6a7-41cf-809f-0714cb6f0dc7)

## Setting Up Log Analytics Workspaces and Microsoft Defender for Cloud

![VirtualBox_Ubuntu_31_03_2024_14_13_03](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/c6ed3990-3291-4b12-94dc-7ddb77859822)

![VirtualBox_Ubuntu_31_03_2024_14_14_19](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/ac283a3a-442d-4b0e-8d15-b4291f86f67d)

1. **Log Analytics Workspace Creation**:
	- Navigate to the search bar and search for "Log Analytics Workspace".
	- Click on "Create".
	- Place this within the same resource group.
	- Name it "LAW-HoneypotRDP".
	- Click on "Review + Create".

![VirtualBox_Ubuntu_31_03_2024_14_15_04](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/cc452b7f-47ea-46bd-b767-6aea2d07d0da)

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

![VirtualBox_Ubuntu_31_03_2024_14_18_36](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/f38b1b67-ece3-48d1-9668-af0eade0e8c0)

![VirtualBox_Ubuntu_31_03_2024_14_20_57](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/47fb3e70-2e10-4350-ae81-348dae7c08eb)


Return to the Log Analytics Workspaces and select your instance.

1. **Configure Virtual Machines**:
    - On the left-hand side, click on "Virtual Machines".
    - Choose the virtual machine you previously created.

![VirtualBox_Ubuntu_31_03_2024_14_22_59](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/3ff22d03-5515-413d-97fe-5af74c2db9a4)

![VirtualBox_Ubuntu_31_03_2024_14_23_43](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/3f07ab37-f58d-4d45-89dd-6fab4293d6fd)

2. **Initiate Connection**:
    - Click on "Connect".
    - Wait for the connection to be established.
  
![VirtualBox_Ubuntu_31_03_2024_14_23_55](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/fb064abf-6947-49fc-a857-a130fb595800)


## Deploy Microsoft Sentinel

Type "Microsoft Sentinel" in the search bar.

1. **Navigate to Sentinel**:
    - Head over to Sentinel and click on "Create".

2. **Add Log Analytics Workspace**:
    - Select the Log Analytics Workspace option and click "Add".

![VirtualBox_Ubuntu_31_03_2024_14_26_07](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/7fbd2b21-b6de-4d58-b6da-e7200265e899)

![VirtualBox_Ubuntu_31_03_2024_14_42_49](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/e98fb95d-7238-4cb5-a876-7dd6b5bcacd9)

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

![VirtualBox_Vulnerable Windows 10_31_03_2024_14_32_23](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/6b39df24-a4d4-4ae8-ad68-64edf5b8ff6c)

## Configuring Windows 10 Virtual Machine Security Settings

After connecting to the VM, follow these steps to navigate to the Event Viewer and disable the firewall:

1. **Open Event Viewer**:
    - Search for "Event Viewer" in the taskbar.
    - Navigate to `Windows Logs > Security`.

![VirtualBox_Vulnerable Windows 10_31_03_2024_09_51_49](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/f09795e4-27e3-40e6-b7c0-ded79c6f77ef)

![VirtualBox_Vulnerable Windows 10_31_03_2024_09_52_07](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/2847f275-abcf-44bf-ad67-310b2004f2b3)

![VirtualBox_Vulnerable Windows 10_31_03_2024_14_33_55](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/288ac914-4f88-4d96-af36-36138419d421)


1. **Disable Firewall**:
    - In the taskbar search, type `wf.msc` and hit `Enter`.
    - Click on "Windows Defender Firewall Properties".
    - Turn off the Firewall state for each of the profiles: Domain, Private, and Public.

![VirtualBox_Vulnerable Windows 10_31_03_2024_14_35_27](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/708ea097-2895-4d66-a6b6-9db23c0b19c1)

![VirtualBox_Vulnerable Windows 10_31_03_2024_14_35_58](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/4c4235c4-9c6a-4682-8b83-1bf742e505c5)


### Setup ipgeolocation.io 

Visit [ipgeolocation.io](https://ipgeolocation.io/) to sign up and obtain an API key. 
Note: Free accounts are limited to 1k API requests/day.

### Powershell Script

We'll use a PowerShell script that leverages a third-party API key to fetch geolocation data. This data will be saved to a log file, which our Log Analytics Workspace will subsequently feed into Sentinel.

The script is saved in my repository, or you can check out Josh Madakor's repository. All credits for this script go to him. Here is the link to his repository: [joshmadakor1/Sentinel-Lab](https://github.com/joshmadakor1/Sentinel-Lab/tree/main)

![VirtualBox_Vulnerable Windows 10_31_03_2024_14_36_48](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/fef64deb-d102-4e4a-835b-64b9ac53e2da)

Open Windows PowerShell ISE on the VM via the search bar. Create a new script, and paste the provided script.

![VirtualBox_Vulnerable Windows 10_31_03_2024_14_37_31](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/793a0e20-c21c-43a9-9608-d7d4e3b4e22b)

To ensure the script runs correctly, replace the placeholder API key with your own. Save the script with a name of your choice; for example, `Custom_Security_Log_Exporter.ps1`.

![VirtualBox_Vulnerable Windows 10_31_03_2024_14_38_16](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/143fe9c8-f86e-4f6b-8617-2971a43032b0)

After running the script, the logs will be saved in `C:\ProgramData\failed_rdp.log`.

You'll need to open this file, copy its contents, and save them in a text file. We will upload this text file to the Log Analytics Workspace as a sample log in the next step.

![VirtualBox_Vulnerable Windows 10_31_03_2024_14_39_43](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/f3839cd4-0543-4528-b47a-834516d152b8)

Note: The folder `C:\ProgramData\` is hidden by default. You'll need to enable the option to view hidden items to access and locate `failed_rdp.log`.

![VirtualBox_Vulnerable Windows 10_31_03_2024_14_39_51](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/2d584746-51bf-4e32-a9b1-0a9b5b47a490)

## Configuring Custom Log in Log Analytics Workspaces

- Go to LAW, then select your workspace.
- On the left-hand side, click on "Tables" > "Create" > "Custom Log".

![VirtualBox_Ubuntu_31_03_2024_14_48_56](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/b69bb2bb-6908-44d2-91e9-8f1d69660dbe)

Upload the text file containing the sample log copied earlier. Move to "Collection paths". Paste the location on the virtual machine where the logs are stored, which is `C:\ProgramData\failed_rdp.log`.

![VirtualBox_Ubuntu_31_03_2024_14_51_29](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/dbbb15ec-ff2a-4733-83be-a91face8bead)

![VirtualBox_Ubuntu_31_03_2024_14_53_08](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/be1a6014-87dd-42e1-95c2-325d214dc6a1)

Under "Details":

- Name the Custom log as: `FAILED_RDP_WITH_GEO`.
- Click "Next" > "Create".

![VirtualBox_Ubuntu_31_03_2024_14_53_26](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/b8f97304-18e7-4825-91ea-9daa31ae41a4)

Return to Log Analytics Workspaces > "Logs" and execute the query: `FAILED_RDP_WITH_GEO_CL`. It might take some time for the query to sync up and display results. Once you've confirmed that the results are being pulled up correctly, you can proceed to create a workbook to visualize this data.

![VirtualBox_Ubuntu_31_03_2024_15_02_42](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/8daf1ec4-3881-40d5-9581-9d1e3d0956aa)

![VirtualBox_Ubuntu_31_03_2024_15_21_43](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/fd24c9fb-7b3c-45b3-9337-3a8fa0fa0c8c)

## Setting Up Visualization in Sentinel Workbook

![VirtualBox_Ubuntu_31_03_2024_14_55_23](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/84fe955e-8dff-4371-a96b-240e648b6f78)

Navigate to Sentinel > New Workbook > Create New Query and input the provided query. Set the visualization to "Map", then run the query. Enable auto-refresh every 5 minutes and wait for attacks to populate on the map.

![VirtualBox_Ubuntu_31_03_2024_14_56_07](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/b348acba-9e06-4c4d-a4b6-da5f55402a32)

![VirtualBox_Ubuntu_31_03_2024_14_56_24](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/b9436a70-2a63-42ae-a6c8-9f21b2cb3787)

![VirtualBox_Ubuntu_31_03_2024_14_58_42](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/7ce04958-3b07-4f1f-9cdd-58653e85ca19)

![VirtualBox_Ubuntu_31_03_2024_15_01_08](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/9dd5a024-1ce2-47f5-8d01-a3e93ca70b87)


In Map settings:
- Set the metric label to "Country".
- Set the metric value to "event_count".

![VirtualBox_Ubuntu_31_03_2024_15_24_56](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/1e5fd8df-3a73-4710-b52c-fc81804b1047)

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

![Screenshot 2024-04-01 132218](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/3bf0943b-b871-4465-b344-62add675a439)

During my testing, I noticed something intriguing. They managed to guess the virtual machine's name and used it as the username for the RDP login. Although I set a unique username for the administrator account—something not commonly done in lab settings—this finding really caught my attention.

![Screenshot 2024-04-01 122932](https://github.com/acibojbp/Failed-RDP-World-Map/assets/164168280/b60c35a7-4c8a-4de4-a483-a9f4349515c7)

I've also set a strong and unique password for the administrator account, enhancing the security further. I highly recommend you do the same.

Back in the Log Analytics workspace under "Logs," you can run this query to check for unique usernames used and their frequency. Please refrain from using these credentials, even though this is a lab demonstration.

```KQL
FAILED_RDP_WITH_GEO_CL 
| extend username = extract(@"username:([^,]+)", 1, RawData)
| summarize Count = count() by username
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

## Deprovision Resource Groups

To prevent unnecessary charges and optimize resources, it's essential to deprovision unused or redundant Resource Groups. This is especially important once you are done with the lab and no longer want to keep the machine, or if you have used up all your free credits. To deprovision Resource Groups, follow these steps:

1. **Navigate to the Azure Portal**: Open your web browser and go to the [Azure Portal](https://portal.azure.com/).
2. **Sign in to Your Account**: Enter your credentials to sign in to your Azure account.
3. **Access Resource Groups**: In the Azure Portal, click on "Resource groups" in the left-hand menu to view a list of all your Resource Groups.
4. **Select the Resource Group**: Click on the name of the Resource Group you want to deprovision.
5. **Review Resources**: Inside the selected Resource Group, review the list of resources to ensure you are deprovisioning the correct group.
6. **Deprovision Resources**: Click on "Delete resource group" at the top of the page.
7. **Confirm Deletion**: Follow the on-screen instructions to confirm the deletion of the Resource Group.

By following these steps, you can effectively deprovision unused Resource Groups, helping to avoid unnecessary charges and optimize your Azure resources.
