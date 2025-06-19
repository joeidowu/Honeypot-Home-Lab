<h1>Honeypot Home Lab</h1>

<h2>Description</h2>
In this project, I set up a basic home SOC in Azure from scratch. Using a free Azure subscription, we walk through creating a virtual machine (VM), opening it to the internet as a honeypot, and forwarding logs to a central repository. We then integrate Microsoft Sentinel to analyze real-world attack data. Below, are the immediate results just minutes after the VM was created.<br />


<h2>Languages and Utilities Used</h2>

- <b>Microsoft Azure</b> 
- <b>KQL</b>

<h2>Environments Used </h2>

- <b>Windows 10</b> 

<h2>Program Steps:</h2>
<h2> Step 1. Create Free Azure Subscription </h2>
To begin, we register for a free Azure account, which provides $200 in credits for the first 30 days, as well as access to many services under the free tier for 12 months. This allows us to deploy and manage cloud infrastructure without incurring immediate costs. Azure also offers security tools and services that we will use later, such as Log Analytics, Monitor, and the Kusto Query Language (KQL) engine. This step involves setting up billing, selecting a region, and becoming familiar with the Azure portal.
<h2> Step 2. Create Virtual Machine </h2>
In this step, we spin up a virtual machine (VM) that will act as our honeypot. A honeypot is a server intentionally exposed to attract and log malicious traffic. We choose a lightweight Linux distro like Ubuntu Server or Debian, which offers easy access to log files and fast deployment. During setup, we configure the VM to use a public IP address and open several commonly targeted ports (e.g., 22 for SSH, 80 for HTTP, 3389 for RDP) to increase the likelihood of being scanned or attacked. Additionally, we disable unnecessary protections (while keeping basic control) to allow attackers to interact with the system, but in a sandboxed and non-critical environment.<p align="center">
 <br/>
<img src="https://i.imgur.com/9Ji1ME0.png"/>
<br />
<br />
  <br/>
<img src="https://i.imgur.com/a/LpMT7o4.png"/>
<br />
<br />
  <br/>
<h2> Step 3. Viewing raw logs on the Virtual Machine </h2>
Once the honeypot is live, we monitor the system logs in real time to capture attacker activity. On a Linux system, this involves accessing /var/log/auth.log for login attempts, /var/log/syslog for system events, and tools like journalctl. We use commands such as tail -f /var/log/auth.log to observe brute-force attempts, failed logins, and shell activity. This hands-on log inspection provides insight into the types of attacks being attempted, attacker IP addresses, and the frequency of intrusion attempts. We take note of patterns such as dictionary attacks or scanning behavior.
<img src="https://i.imgur.com/HTK9haf.png"/>
<br />
<br />
 <br/>
<img src="https://i.imgur.com/95PNvCq.png" />
<br />
<br />
  <br/>
<h2> Step 4. Creating our Log Repository </h2>
To scale our monitoring and enable advanced querying, we create a Log Analytics Workspace in Azure Monitor. This workspace is a cloud-based log repository that aggregates data from multiple sources, including VMs, networks, and other Azure services. We provision a new workspace by choosing a name, region, and pricing tier, then prepare it to receive logs from our honeypot VM. This centralized platform allows us to retain logs over time, apply filters, and use analytical tools to detect suspicious behavior with more granularity than is possible on the VM alone.
<h2> Step 5. Connecting our VM to Log Analytics Workspace </h2>
We install the Log Analytics Agent (also known as the Microsoft Monitoring Agent) on our VM, which acts as a bridge between the server and our workspace. This agent collects performance metrics, system events, and security logs from the VM and forwards them to Azure Monitor. On Linux, the installation involves downloading the OMS agent, linking it to the workspace using a workspace ID and key, and verifying connectivity. After installation, we configure the agent to monitor specific log files, such as authentication logs or SSH logs, ensuring that all critical events are sent to the workspace for further analysis.
<h2> Step 6. Querying our Log Repository with KQL </h2>
With logs now flowing into the workspace, we begin using Kusto Query Language (KQL) to analyze them. KQL is a powerful query language similar to SQL but optimized for time series and log data. We start by writing basic queries to count login attempts, filter by IP address, or search for failed authentications.
<img src="https://i.imgur.com/IIvAyTd.png" />
<br />
<br />
  <br/>
<img src="https://i.imgur.com/dNKHQLQ.png" />

<h2> Step 7. Uploading our Geolocation data to the SIEM </h2>
To make our log analysis more meaningful, we enrich the attacker IP addresses with geolocation data. This involves mapping each IP to a location (e.g., country, region, city) using external services or datasets like MaxMind GeoLite2 or IPinfo, but in this case we just used a Google document found online. We create a CSV file containing IP addresses and their respective geolocation information, then upload this data into Azure Log Analytics using a custom log table or via PowerShell/Azure CLI. This additional context allows us to understand the geographical distribution of attackers and identify regional targeting patterns.
<h2> Step 8. Inspecting Our Enriched Logs – We Can See Where the Attackers Are </h2>
With geolocation enrichment in place, we merge our log data and geolocation data within our workspace using KQL join operations. We write queries that correlate attacker IPs with countries or cities, showing us where threats originate. We also detect repeated attacks from the same region and analyze whether certain countries are attempting specific types of intrusions. This provides a clearer view of threat origins and attacker behavior patterns.
<h2> Step 9. Creating Our Attack Map </h2>
Finally, we visualize our enriched log data using a geographic attack map. This can be done using tools like Power BI, Python (with Folium, Plotly, or Matplotlib), or Kibana. We plot the source locations of IPs on a world map, using bubbles or heatmaps to represent the volume of attacks from each region. This attack map offers a visual summary of real-time or historical attack activity and can be updated dynamically as new logs arrive. It not only makes the data more engaging but also provides insights for potential threat intelligence reporting or further automation.
<br />
<br />
<br/>
<img src="https://i.imgur.com/K7uYbcm.png" />
<br />
<br />
</p>

