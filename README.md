
# <h1> SIEM Setup | Azure Sentinel Map and Built a VM on Azure </h1>

<h2>Description</h2>
In this lab, I set up Azure Sentinel (SIEM) and connected it to a live virtual machine acting as a honey pot. We will observe live attacks (RDP Brute Force) from all around the world. We will use a custom PowerShell script to look up the attackers' Geolocation information and plot it on the Azure Sentinel Map!
<br />


<h2>Languages and Utilities Used</h2>

- <b> Azure Subscription: <b> (https://azure.microsoft.com/en-us/free/)
- <b> PowerShell Script for the Lab: </b> (https://github.com/joshmadakor1/Sentinel-Lab/edit/main/Custom_Security_Log_Exporter.ps1)

<h2>Environments Used </h2>

- <b> Azure VM </b>
 
<h2>Walk-through:</h2>

<p align="center">
First, we're going to create our Azure subscription; it's free; you'll get $200 worth of free credits: 
<br />
<img src="https://i.imgur.com/am0KAqo.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<br />
<br />
<p>Once your subscription is created, just go to (https://portal.azure.com/). Next, we're going to create a virtual machine in Azure. In the search bar, type virtual machine, go to the virtual machine:<p>  
<br/>
<img src="https://i.imgur.com/aJO7xRZ.png" height="80%" width="80%" alt=Azure Sentinel Tutorial"Azure Sentinel Tutorial"/>
<p>The machine that's going to be sitting out on the internet exposed to the internet essentially where all the different people from the different countries are going to attack the VM, they're going to start trying to log into it once they discover it's online. So this is going to be our honeypot.<p>
<br />
<img src="https://i.imgur.com/8gayTDx.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<br />
<br />
Create a virtual machine, click "Create" drop-down arrow click "Virtual Machine": <br/>
<br />
<img src="https://i.imgur.com/ZXw6u09.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<br />
<br />
Create a new resource group: A resource group is just a logical grouping of resources in Azure that usually share the same lifespan, so everything in this project we're going to put in the same resource group, so we'll call it honeypotlab; click "Okay" and then for the name of our virtual machine, we'll name it honeypot-VM:  <br/>
<br />
<img src="https://i.imgur.com/9iegBk3.png" height="80%" width="80%" alt=""/>
<br />
<br />
Put everything in the West US 2 region. This is the geographic region where they exist and the data center where they will exist. The image and size on default are good too. The size might be different by the time you do this but leave the default it will be good to go:  <br/>
<br />
<img src="https://i.imgur.com/cDzTKYS.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<br />
<br />
Create a username and password; make sure you remember what it is because we're going to use this to log into the virtual machine:  <br/>
<img src="https://i.imgur.com/cDzTKYS.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<br />
<br />
Under the Disk tab, leave everything as is and click Next:  <br/>
<img src="https://i.imgur.com/JYlesP1.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<br />
<br />
Under the Networking tab, the NIC nik network security group, click on "Advance"; under the Configure network security group, click on "Create new." You can see this as a firewall for the VM; this firewall is gonna be essentially open for everything and everyone public to the internet:  <br/>
<img src="https://i.imgur.com/4rzpkeF.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<br />
<br />
The default rule is here, so we're just going to remove it. Now, create our own inbound rule that allows everything into the VM. Click on "Add inbound rule." <br />
<img src="https://i.imgur.com/hKnCUdh.png" height="40%" width="80%" alt="Azure Sentinel Tutorial"/> 
<br/>
<br/>
For the Destination port, we're going to put a * star for anything; for "Protocol," leave it at "Any," for Action, click "Allow," Priority set it low like 100, and then the "Name" I put DANGER_ANY_IN. Essentially, this setup will allow all traffic from the internet into our virtual machine. Click Add and OK:  <br/>
<img src="https://i.imgur.com/kZvvQI4.png" height="40%" width="80%" alt="Azure Sentinel Tutorial"/>
<br />
<br />
Click Review + create. Then click Create:  <br/>
<img src="https://i.imgur.com/SBBywzD.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/> <br />
<br />
The creation of this VM will take a few; the point of this lab is to make the virtual machine very discoverable by any means necessary; whether people are doing TCP pings or an ICMP ping, we want the virtual machine not to drop any traffic essentially. We want it to be discoverable quickly so people can discover that it's online and then subsequently start attacking it. Usually, you wouldn't want to do this, but for the lab, we want to make it enticing and discoverable for people.
<br />
<br />
<img src="https://i.imgur.com/uvpdlVE.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/> <br />
<br />
Now, create a log analytics workspace, type in the search box log analytics, then click "Create":  <br/>
<img src="https://i.imgur.com/dLeBbiS.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<br />
<br />
The purpose of this is to ingest logs from the virtual machine. We're going to ingest windows event logs and create our own custom log containing geographic information so we can discover where the attackers are coming from. So, to do that, we're going to create a log analytics workspace; this is where those logs will be stored, and then our sim or Azure Sentinel will connect to this workspace to be able to display that geodata on the map for us to view:  <br/>
<br />
<br />
For the Resource Group, we will enter the VM we created, so select honeypotlab. In the Details Name, we can name it law-honeypot1 (law for Log Analytics Workspace) and for the region again, just put in put it in West US 2, and then we will click Review and Create:  <br/>
<img src="https://i.imgur.com/38Ol3B3.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<br />
<br />
Let's go to Security Center and enable the ability to gather logs from the virtual machine into the log analytics workspace; go to the left and click on Pricing & Settings: <br /> 
<img src="https://i.imgur.com/UnSvW3d.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/> 
<br />
And click the log analytics workspace that was just made (law-honeypot1): <br />
<img src="https://i.imgur.com/7GCMS2p.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<br />
Turn Azure Defender on, and then we're going to turn SQL servers Off because we don't need actually to do anything with the SQL server, then we're going to click Save: <br />
<img src="https://i.imgur.com/tzE3uEV.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/> 
<br />
On then on the left, you'll see Data collection, click it and then we're going to Click on All Events and then click Save:  <br/>
<img src="https://i.imgur.com/UnSvW3d.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<img src="https://i.imgur.com/7GCMS2p_d.jpg?maxwidth=520&shape=thumb&fidelity=high" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<img src="https://i.imgur.com/UnSvW3d.png" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<img src="" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<img src="" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
 
<br />
<br />
Create a username and password; make sure you remember what it is because we're going to use this to log into the virtual machine:  <br/>
<img src="" height="80%" width="80%" alt="Azure Sentinel Tutorial"/>
<br />
<br />
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
