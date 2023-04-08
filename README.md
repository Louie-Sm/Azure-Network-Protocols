<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

## Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines

This tutorial demonstrates inspecting various network traffic to and from Azure Virtual Machines with Wireshark, as well as experimenting with Network Security Groups.

### Environments and Technologies Used

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, DNS, DHCP, ICMP)
- Wireshark (Protocol Analyzer)

### Operating Systems Used

- Windows 10 (21H2)
- Ubuntu Server 20.04

### Steps

1. Create VMs (Windows 10, Linux (Ubuntu Server))
2. Install/Run Wireshark on VM with Windows 10
3. Observing by Filtering Protocols

### Actions and Observations

**Step 1:** Creating Virtual Machines

Using Azure, create two virtual machines to use for this. One being Windows 10 and the other Ubuntu Server. You may name them whatever you'd like and use whatever username and password (for ubuntu server uncheck SSH and check password so that you may create a username and password for that VM.)

*VM-1 Windows 10*

![Creating Virtual Machine Windows 10](https://i.imgur.com/TC2WeYr.jpg)

*VM-2 Ubuntu Server*

![Creating Virtual Machine Ubuntu Server](https://i.imgur.com/z8L3NWy.jpg)

**Step 2:** Install/Run Wireshark

On the virtual machine with Windows 10, download Wireshark (Windows Installer 64-bit) and continue with all the default options. Npcap will pop up to install, go ahead and install that with defaults, and Wireshark will continue to install after. Open Wireshark on the VM, click Ethernet, and then click the blue fin at the top left under 'File' to begin capturing packets. Notice all the traffic already happening that happens in the background.

![Wireshark capturing packets](https://i.imgur.com/hRYo3PE.jpg)

**Step 3:** Observing Various Protocols

**Step 3.1:** ICMP (Internet Control Messaging Protocol)

In Wireshark, type in icmp to filter only icmp traffic (nothing should show at the moment). Back in Azure Portal, go to the Ubuntu Server VM and obtain the private IP address. Back in the VM with Windows 10, open Windows Powershell and in the command line type ping (Private IP address from Ubuntu Server VM). Observe what the traffic in Wireshark, you'll notice requests and replies, and in Powershell, it will tell you how many packets sent, and how many made it or were lost.

*Grabbing Ubuntu Server VM's private IP from Azure*

![Ubuntu Server VM's Private IP Address](https://i.imgur.com/YxeS3EG.jpg)

*Pinging Ubuntu Server VM's private IP with Powershell in Windows 10 VM*

![Pinging Ubuntu Server VM's Private IP Address from Windows 10 VM](https://i.imgur.com/WaTEtVt.jpg)

**Step 3.1.1:** Blocking ICMP in Ubuntu's Network Security Group

In Windows 10 VM, in Powershell, send a continuous ping by typing in the command line ping (Private IP address from Ubuntu Server VM) -t. In Azure Portal search for Network Security Group and click on the VM that has Ubuntu Server. From there click Inbound security rules, and click Add. Look for ICMP at the radio buttons and make sure it is ticked. Under Action check Deny. For priority set it before 300 just so we can have this rule take place before any other rule. Once this rule is created, go back to Powershell and notice it will say Request timed out, and observe in Wireshark how only requests are being shown. After observing the ping request timing out go ahead and delete the rule to allow pings to come through again.

*Pinging Ubuntu Server VM's private IP with Powershell in Windows 10 VM non-stop*

![Pinging Ubuntu Server VM's Private IP Address from Windows 10 VM non-stop](https://i.imgur.com/likADaX.jpg)

*Creating rule to deny ICMP in Ubuntu's NSG*

![NSG Inbound Rule to deny ICMP](https://i.imgur.com/lfxfsvu.jpg)

*Observing Ping request timing out*

![Ping request timing out because of new rule](https://i.imgur.com/46cwHyk.jpg)

**Step 3.2:** SSH (Secure Shell) port 22

In Wireshark, change the filter to SSH or tcp.port == 22, then in Powershell type ssh (Ubuntu's VM username that was created)@(Ubuntu's private IP address). Then type yes and it will ask for the password. Take note that as you are typing the password, it will not show up. Now you are connected to Ubuntu VM's command prompt and can use some Linux commands to mess around. Once you finish type exit to get back to VM-1 command prompt and close the SSH connection.

*SSH Protocol*

![SSH Protocol](https://i.imgur.com/OsrUIjq.jpg)

*Using some Linux Commands*

![SSH Protocol with some Linux commands](https://i.imgur.com/VGwpgXv.jpg)

**Step 3.3:** DHCP Traffic

In Wireshark filter type in DHCP to show DHCP traffic. Nothing should show. In Powershell type in ipconfig /renew and you will see some traffic happen in Wireshark.

*Renewing IP address in Powershell to show DHCP traffic*

![Renewing IP address](https://i.imgur.com/5AMAZDf.jpg)

**Step 3.4:** DNS Traffic UDP Port 53

In Wireshark filter to DNS traffic and click refresh to clear any traffic. In Powershell type in nslookup www.google.com (this is basically asking what google's IP address is).

*Using nslookup to see what is Google's IP address*

![nslookup www.google.com to get IP address for google](https://i.imgur.com/OL4TPR7.jpg)

## Finished

Here we observed various Network Protocols using two Virtual Machines and Wireshark. We also utilized inbound rules in Network Security Groups to block ICMP traffic from being received from one VM to the other. Now that both are set up, you can observe more traffic and create more rules just to see how it all works.

<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>
