# Configuring-On-premises-Active-Directory-within-Azure-VMs

Configure Active Directory

Active Directory stores data objects on your local network environment.

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/aef2518e-ee4c-44a0-8f3b-7c2d3d1b48be)


The structure of the data makes it possible to find the details of resources connected to the network from one location. Active Directory acts like a phonebook for your network so you can look up and manage devices easily.


![image](https://github.com/Christopherdek/configure-ad/assets/148359456/25bb5ba6-c107-41fd-b1a4-d7875b1b18ee)


<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

Here Creating thousands of accounts, passwords, and permissions in a single place. A good place to manage a ton of devices. This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

1. Domain Controller VM (Windows Server 2022) named “DC-1”

2. Domain Controller’s NIC Private IP address to be static

3. ICMPv4 (ping) was allowed on the Domain Controller

4. Create an Admin and Normal User Account in Active Directory

5. Join Client to domain

6. Attempt to login Client-1 with one of the users


<h2>Deployment and Configuration Steps</h2>

Before beginning, create a Resource group so that the Virtual Machines you create can be stored. Your VMs that will be created are for the Domain Controller(DC-1) and Virtual Machine(Client-1). The Domain Controller VM will be running Image: Windows Server 2022 Datacenter-x64 Gen2.

![Screenshot 2023-08-24 173900](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/842ba903-dc28-4425-bc2c-10ee3a0c8b38)
For the next create a VM for the Client(Windows 10) name "Client-1" with the same Resource Group and same Vnet. The RG and Vnet must match DC-1

![Screenshot 2023-08-24 173523](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/fa109a2b-de01-499e-befe-5b1dd4793de2)

![Screenshot 2023-08-24 173650](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/7cf26ea9-1108-4615-a6ad-b50e5e2b4aa2)
After both VMs are done creating go to DC-1, Private IP address, and set to static. This will allow the IP to stay the same.

![Screenshot 2023-08-10 175951](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/9f4c7a19-751e-460f-9b04-f3a372236d24)

To check for a connection between the client and domain controller by logging in with your username and PW to Client-1 with Remote Desktop Connection(RDP) and ping the DC-1 Private IP address using "-t"(Perpetual ping). The ICMPv4 (ping) was showing it was allowing on Domain Controller's(DC-1) Firewall in Windows Firewall (Core Networking Diagnostics). Then log back into Client-1 to see if the ping is successful.

![Annotation 2023-08-10 220707](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/ba9a1848-86bc-4c9e-81e3-f7f2e0ab63be)

Here in this example that shows that the ICMP rule has been allowed on the Windows firewall for inbound traffic:

![Screenshot 2023-08-10 181434](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/15c5a83d-d806-4a5b-af7c-88e238db3330)

Back in DC-1, go and select add roles and features to enable the "Active Directory Domain Services". This promotes the Domain Controller(DC) a new forest as mydomain.com setup. Your Remote Desktop will restart and then you can log back into DC-1 as 
user: mydomain.com/username*

![Screenshot 2023-08-10 182444](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/72dc078a-e50b-4374-a967-48a13afcb351)

Next, we can create and configure the Organizational units for the admins and employees in Active Directory(AD) while in DC-1. So in Active Directory Users and computers, right-click mydomain.com tab--->New---->Organizational Unit. Name it _Admin and repeat
and make _EMPLOYEES. 

![Screenshot 2023-08-29 185637](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/e119795e-c694-421b-9401-8841d5cd49a1)

Right-click on _ADMIN and create a new employee, use the same password with "user_admin" Once the admin created, add "user_admin" to the "domain admins" security group".

![Screenshot 2023-08-12 005937](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/81778209-f54c-4ae8-a268-0d1fdc6bde98)

Log out and (mydomain.com\user) reconnect to dc-1 with your "mydomain.com\user_admin. My example mydomain.com\ayrin_admin

![Screenshot 2023-08-11 202559](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/96fe6091-04b6-41a0-85ed-6d1dcc773155)

The next step is to go into VM Client-1 portal in Azure. We are going to join Client-1 to the domain(mydomain.com) We have to change the DNS on Client-1 Private IP address the same as the Domain. First, settings--->Network----> Network Interface the "NIC" click on DNS server and change the Private IP address to the Domain. Select "custom" enter the the IP address and click save.


![Screenshot 2023-08-11 205050](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/da5c542f-f47b-4b3e-b3d7-37c142aeb654)

Now with the DNS servers successfully change to the private IP address of DC-1, we can add the client-1 to the domain without any issue. You will get a message about the client being successfully added to the domain. To login to the client is by "System--->Rename This PC---> enter domain name--->select ok----> select apply. Then restart the systems.

![Screenshot 2023-08-12 005937](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/7e824df6-40e8-4956-a957-2fb59a634394)

This the message you'll be shown.

![Screenshot 2023-08-12 010137](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/3e6b2174-85a4-4839-a288-5d6cc46abed0)

With the Client-1 connected to the the domain, we now can create the clients and load them into "_employment OU" in the domain controller(DC-1). We'll need "Powershell_ISE" as the administrator. Click on file and cope and paste into the new file(White area) the pre-configured script. While the script is running, Employees will be made, Randomly.

![Screenshot 2023-08-30 162829](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/d5dd9532-2cfb-42c5-8800-596bd927ab63)

The randomly generated employees are reflected into the Active Directory on the Domain Conttroller.

![Capture](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/3fb49df4-cfcc-4b75-9a2e-61e8bf4bb850)

With a random user you can now log into that users account.

![Screenshot 2023-08-12 012159](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/6a0e46d5-828c-4821-87f4-379b50c9cd9a)

"To achieve your biggest dreams, work on the small things".
