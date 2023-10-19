# Configuring-On-premises-Active-Directory-within-Azure-VMs

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/aef2518e-ee4c-44a0-8f3b-7c2d3d1b48be)


The structure of the data makes it possible to find the details of resources connected to the network from one location. Active Directory acts like a phonebook for your network so you can look up and manage devices easily.


![image](https://github.com/Christopherdek/configure-ad/assets/148359456/25bb5ba6-c107-41fd-b1a4-d7875b1b18ee)


<p align="center">


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

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/2871a342-3530-44d1-ab20-dd77f05b3d9c)


![image](https://github.com/Christopherdek/configure-ad/assets/148359456/e80bd273-c22d-4da9-9d5a-c47299094811)

For the next create a VM for the Client(Windows 10) name "Client-1" with the same Resource Group and same Vnet. The RG and Vnet must match DC-1

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/e8f6696a-7a14-44e8-be98-54c29675160e)


![image](https://github.com/Christopherdek/configure-ad/assets/148359456/bd47a3d3-7d17-45f4-b8c7-dae8693a329d)


After both VMs are done creating go to DC-1, Private IP address, and set to static. This will allow the IP to stay the same.

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/9a452faa-e609-43ec-aa0a-12ef4df0a8ca)

To check for a connection between the client and domain controller by logging in with your username and PW to Client-1 with Remote Desktop Connection(RDP) and ping the DC-1 Private IP address using "-t"(Perpetual ping). The ICMPv4 (ping) was showing it was allowing on Domain Controller's(DC-1) Firewall in Windows Firewall (Core Networking Diagnostics). Then log back into Client-1 to see if the ping is successful.

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/13bc4e71-4499-4e36-833e-f06dabe061ec)


Here in this example that shows that the ICMP rule has been allowed on the Windows firewall for inbound traffic:

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/ed1cfa6c-92a2-4a2f-9d1d-be192c63741a)

Back in DC-1, go and select add roles and features to enable the "Active Directory Domain Services". This promotes the Domain Controller(DC) a new forest as mydomain.com setup. Your Remote Desktop will restart and then you can log back into DC-1 as 
user: mydomain.com/username*

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/4cfa5d72-e505-460f-912a-a507251914e0)

Next, we can create and configure the Organizational units for the admins and employees in Active Directory(AD) while in DC-1. So in Active Directory Users and computers, right-click mydomain.com tab--->New---->Organizational Unit. Name it _Admin and repeat
and make _EMPLOYEES. 

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/3b513d26-2107-4877-8ea3-9a00ad2fa77e)

Right-click on _ADMIN and create a new employee, use the same password with "user_admin" Once the admin created, add "user_admin" to the "domain admins" security group".

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/2af14024-18ef-42c0-813b-a893efad671c)

Log out and (mydomain.com\user) reconnect to dc-1 with your "mydomain.com\user_admin. My example mydomain.com\ayrin_admin

![Screenshot 2023-08-11 202559](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/96fe6091-04b6-41a0-85ed-6d1dcc773155)

The next step is to go into VM Client-1 portal in Azure. We are going to join Client-1 to the domain(mydomain.com) We have to change the DNS on Client-1 Private IP address the same as the Domain. First, settings--->Network----> Network Interface the "NIC" click on DNS server and change the Private IP address to the Domain. Select "custom" enter the the IP address and click save.


![Screenshot 2023-08-11 205050](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/da5c542f-f47b-4b3e-b3d7-37c142aeb654)

Now with the DNS servers successfully change to the private IP address of DC-1, we can add the client-1 to the domain without any issue. You will get a message about the client being successfully added to the domain. To login to the client is by "System--->Rename This PC---> enter domain name--->select ok----> select apply. Then restart the systems.

![Screenshot 2023-08-12 005937](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/7e824df6-40e8-4956-a957-2fb59a634394)

![Screenshot 2023-08-12 010137](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/3e6b2174-85a4-4839-a288-5d6cc46abed0)

With the Client-1 connected to the domain, we now can create the clients and load them into "_employment OU" in the domain controller(DC-1). We'll need "Powershell_ISE" as the administrator. Click on the file and copy and paste into the new file(White area) the pre-configured script. While the script is running, Employees will be made, Randomly.

![Screenshot 2023-08-30 162829](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/d5dd9532-2cfb-42c5-8800-596bd927ab63)

The randomly generated employees are reflected in the Active Directory on the Domain Controller.

![Capture](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/3fb49df4-cfcc-4b75-9a2e-61e8bf4bb850)

With a random user, you can now log into that user's account.

![Screenshot 2023-08-12 012159](https://github.com/Leibwatcher/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/137578446/6a0e46d5-828c-4821-87f4-379b50c9cd9a)


