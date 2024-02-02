<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1 align = "center">Installing and Configuring Active Directory</h1>
This guide illustrates the process of setting up and configuring Active Directory through Azure. The procedure involves deploying two virtual machines within the same Azure virtual network. The first virtual machine serves as the Domain Controller, with Active Directory installed and configured, while the second functions as a Client. Subsequently, we will set up the Active Directory to enable the Client to join the domain, and create user accounts using a Powershell script.
</p>

<h3>Environments and Technologies</h3>

<li>Microsoft Azure (Virtual Machines/Compute)</li>
<li>Remote Desktop</li>
<li>Active Directory Domain Services</li>
<li>Powershell</li>
<li>Notepad (Optional): for writing down usernames and passwords for virtual machines</li>
</ul>


<h3>Operating Systems</h3>

<li>Windows Server 2022</li>
<li>Windows 10 Pro (21H2)</li>
</ul>


<h3>Installation Steps</h3>

<p>
<h3>Setup Resources in Azure</h3>

<p>
</p>
Create the Domain Controller VM (Windows Server 2022) named “DC-1”
</p>
<img src="https://i.imgur.com/CGxw4lg.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created with DC-1
<p>
<img src="https://i.imgur.com/YvIJiP0.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Go to DC-1 click on "Networking" under "Setttings", click on "Network Interface link". Go to "IP configurations" under Settings. Click on "ipconfigurations" and set Domain Controller’s NIC Private IP address to be "static"
<p>
<p>
<img src="https://i.imgur.com/5Cd55yS.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
</p>

<br />

<h3>Ensuring Connectivity</h3>

<p>
<p>
Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t <ip address> (perpetual ping) Connection should time out after the first ping due to the DC-1's Firewall Settings.
<p>
<img src="https://i.imgur.com/dT8ZTi9.jpg" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Login to the Domain Controller and enable ICMPv4 in on the local windows Firewall. In the firewall screen, sort the protocols and find icmpv4 “core networking destination unreachable” - enable the first two requests
<p>
<img src="https://i.imgur.com/XFdo6dy.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
<p>
Check back at Client-1 to see the ping succeed
<p>
<img src="https://i.imgur.com/MexbGsX.jpg" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
</p>

<br />

<h3>Installing Active Directory on the Domain Controller</h3>

<p>
In DC-1, go to the Server Manager Dashboard and click on "Add Roles and Features" Go through the installation process, at Server Roles check the box for Active Directory Domain Services.
<p>
<img src="https://i.imgur.com/YfIcUAb.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Once finished installing click the flag in the top right and hit “promote this server to a domain controller” Promote as a DC: Setup a new forest as mydomain.com - You will be signed out. Restart and then log back into DC-1 as user: mydomain.com\labuser
<p>
<img src="https://i.imgur.com/xxoVaj5.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
<img src="https://i.imgur.com/4fFhCjK.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
<p>
</p>

<br />

<h3>Configuration Steps</h3>

<h3>Creating Organizational Units and Users</h3>

<p>
Organizational Units function as containers that store information, privileges, and login access details for users within the directory. Navigate to the Tools tab within the Server Manager dashboard, then access the Active Directory Users and computers console. Right click on the domain (mydomain.com), and establish two Organizational Units (OUs) named _admins and _employees. In the _ADMINS OU create a new employee named “Jane Doe” with the username of “jane_admin” as well as a password.
<p>
<img src="https://i.imgur.com/kZ9qnLA.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Add jane_admin to the “Domain Admins” Security Group (Properties>member of>add.
<p>
<img src="https://i.imgur.com/nZUX40V.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Exit out of the vm, sign back in with username jane_admin
<p>
</p>

<br />

<h3>Join Client-1 to your domain</h3>

<p>
Open the Azure Portal and navigate to DC-1. Access the networking section and make note of the private IP address. Repeat the identical steps for Client-1 by clicking on the link adjacent to the network interface. Within the DNS servers under "Settings", configure the DNS server to "Custom". Enter the private IP address of DC-1 and save the changes. From the Azure Portal, restart Client-1
<p>
<img src="https://i.imgur.com/z4dzPD3.jpg" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Go to the Client Virtual Machine, go to "Settings" then System>About>Rename this PC (advanced)
<p>
Input the domain and credentials to facilitate the client's domain joining process. Use the format mydomain.com\jane_admin.
<p>
<img src="https://i.imgur.com/svDXBq4.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Log into Client-1 as mydomain.com\jane_admin and open system properties - Click “Remote Desktop” - Allow “domain users” access to remote desktop - You can now log into Client-1 as a normal, non-administrative user now
<p>
<img src="https://i.imgur.com/jlBSsZc.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
</p>

<br />

<h3>Creating Additional Users</h3>

<p>
Login to DC-1 as jane_admin and Open PowerShell_ise as an administrator. Create a new File and paste the contents of the script into it (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)
<p>
Run the script and observe the accounts being created
<p>
<img src="https://i.imgur.com/XAI9G55.jpg" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
When finished, open ADUC and observe the accounts in the appropriate OU
<p>
<img src="https://i.imgur.com/Fx61rby.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Attempt to log into Client-1 with one of the accounts (take note of the password in the script)
<p>
This is the end of the lab
