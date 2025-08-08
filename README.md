<h1>Microsoft Azure & Sentinel (SIEM) Honeypot Home Lab</h1>

<h2>Description</h2>
Configured and deployed multiple Azure components, including Virtual Machines, Log Analytics Workspaces, and Azure Sentinel, to simulate a cloud-based security operations environment. Gained hands-on experience with Azure Sentinel as a SIEM platform, integrating third-party API calls, and using Kusto Query Language (KQL) to analyze security event logs. Built interactive workbooks, including a world map visualization, to display real-time attack statistics and enhance threat analysis capabilities. This project strengthened my skills in cloud security monitoring, log analysis, and incident detection using Microsoft Azure.
<br />


<h2>Tools Used:</h2>

✅ Microsoft Azure (Virtual Machines, Log Analytics Workspace, Azure Sentinel)

✅ PowerShell 

✅ Remote Desktop Protocol (RDP)

✅ Kusto Query Language (KQL)

✅ Third-party API: ipgeolocation.io

✅ Windows Security Event Logs

<h2>Project walk-through:</h2>

<p align="center">
<h3>Step 1: Create a Microsoft Azure Account</h3> 
<img src="https://i.imgur.com/5UDnjKr.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<h3>Step 2: Setup honeypot VM</h3> 
<img src="https://i.imgur.com/T2kVuLu.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
  
 - Go to Azure portal and create an Azure VM
  
 - Create a new resource group and give it a name (honeypot-lab)
   
 - Create a username and password for the VM
   
 - Allow inbound port: RDP (3389)

<img src="https://i.imgur.com/Nhza3P8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

 - Network Security Group: Add inbound security rule = Destination port ranges: * (wildcard for anything), Protocol: Any, Action: Allow
Priority: 100 (low), Name: Anything (allow-any-inbound), Select Review + Create
<img src="https://i.imgur.com/lGbUPpj.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<h3>Step 3: Create a Log analytics Workspace</h3>

- Search for "Log analystics workspace"

- Select Create Log Analytics workspace

- Place it in the identical resource group as the VM (give it a name and add to the same region)

- Select Review + Create
<img src="https://i.imgur.com/f6bUruF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<h3>Step 4: Setup Microsoft Defender for Cloud</h3> 

- Search for "Microsoft Defender for Cloud"

- Under Management, click on "Environment settings" > Subscription Name > Log Analytics Workspace Name
<img src="https://i.imgur.com/eDRTVuB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

- Settings (Defender Plans): Turn Foundational CSPM ON, Servers ON, SQL servers on machines OFF, Click Save

- Settings (Data Collection): Select "All Events" then Click Save
<img src="https://i.imgur.com/hNZT9Pj.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<h3>Step 5: Link VM to the Log Analytics Workspace</h3> 

- Look for "Log Analytics workspaces"

- Select workspace name > Virtual Machines > virtual machine name

- Connect
<img src="https://i.imgur.com/JL945Ga.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<h3>Step 6: Setup Microsoft Sentinel</h3> 

- Look for "Microsoft Sentinel"

- Click Create Microsoft Sentinel

- Choose Log Analytics Workspace name

- Click Add
<img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<h3>Step 7: Turn OFF the VM's firewall</h3> 

- For Windows: Open the Command Prompt: Press the Windows key, type "cmd," and press Enter to open the Command Prompt. Type the Ping Command: In the Command Prompt window, type the following command and press Enter: ping (ip-adress). Replace with the actual IP address you want to ping.

- Observe the Results: the ping command will start sending packets to the specified IP address, and you'll see the results with round-trip times and other statistics. To stop the ping process on Windows, you can press Ctrl + C or Ctrl + Z

- Locate the honeypot VM under Virtual Machines

- Copy the IP address from the VM

- Using the credentials from step 2, access the VM through Remote Desktop Protocol (RDP)

- Accept Certificate warning

- Select NO for all Choose privacy settings for your device

- Hit Start and search for "wf.msc" (Windows Defender Firewall)

- Click "Windows Defender Firewall Properties"

- Turn Firewall State OFF for Domain Profile | Private Profile | and Public Profile

- Click Apply and Ok

- To check if VM is reachable, ping it using the command line of the host ping -t (ip-adress)
<img src="https://i.imgur.com/AeZkvFQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<h3>Step 8: Automating the Security Log Exporter</h3> 

- In your VM launch Powershell ISE

- Configure Edge browser without logging in

- Copy Powershell Script and insert into Virtual Machine's Powershell (authored by Josh Madakor)

- Choose New Script in Powershell ISE and paste script

- Give it a name and save it to the desktop (log_exporter)

- Create a profile with ipgeolocation.io

- Once logged in, copy the API Key and pste it into line 2 of the script

- Click save

- To generate log data continually, run the PowerShell ISE script (green play button) in the virtual machine
<img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<h3>Step 9: Log Analytics Workspace: Make a Custom Log</h3> 

- To add the extra information from the IP Geolocation service to Azure Sentinel, create a custom log

- Search "Run" in VM and type "C:\ProgramData"

- Open file named "failed_rdp" hit CTRL + A to select all and CTRL + C to copy selection

- On the host PC, open notepad and paste the information

- Save to desktop as "failed_rdp.log" Note: make sure it's saved as a (.txt) text file. I had issues with formatting when saving in (.rtf) rich text format.

- In Azure go to Log Analytics Workspaces > Log Analytics workspace name > Custom logs > Add custom log

- Sample: Select Sample log saved to Desktop (failed_rdp.log) and click Next

- Record Delimiter: Look over sample logs > Click Next

- Collection Paths: Type: Window, Path: "C:\ProgramData\failed_rdp.log

- Details: Name and describe the custom log (FAILED_RDP_WITH_GEO) before pressing the Next button

- Click Create
<img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<h3>Step 10: Query + Extract Fields from Custom Logs</h3> 

- Navigate to the newly established workspace (honeypot-law) in Log Analytics Workspaces > Logs

- We then can run a query and extract the different data filtering by different fields such as latitude, longitude, destinationhost, etc.

- Copy/Paste the following query into the query window and Run Query
<img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<h3>Step 11: Create World Attack Map in Microsoft Sentinel</h3> 

- Access Microsoft Sentinel to view the Overview page and available events

- Click on Workbooks and Add workbook then click Edit

- Delete default widgets (three dots > remove)

- Click Add > Add query

- You can Copy/Paste the previous query or this one into the query window and Run Query

- We then can run a query and extract the different data filtering by different fields such as latitude, longitude, destinationhost, etc.

- When results appear, select Map from the Visualization drop-down box.

- Choose Map Settings to make additional adjustments

- Layout Settings: Location Info Using = Latitude/Longitude, Latitude = Latitude, Longitude: Longitude, Size By = event_count

- Color Settings: Coloring Type = Heatmap, Color by = event_count, Aggregation for color = Sum of Values, Color palette = Green to Red

- Metric Settings: Metric Label = label, Metric Value = event_count, Click Apply button and Save and Close, Save as "Failed RDP International Map" in the same region and under the resource group (honeypot-lab), Keep refreshing the map to show more inbound failed RDP attacks
<img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
