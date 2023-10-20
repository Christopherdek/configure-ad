# Configuring On-premises Active Directory within Azure VMs

![Azure VMs](https://github.com/Christopherdek/configure-ad/assets/148359456/aef2518e-ee4c-44a0-8f3b-7c2d3d1b48be)

Active Directory serves as a centralized repository, enabling the efficient management and lookup of network devices.

![Active Directory](https://github.com/Christopherdek/configure-ad/assets/148359456/25bb5ba6-c107-41fd-b1a4-d7875b1b18ee)

## Environments and Technologies Used

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

## Operating Systems Used

- Windows Server 2022
- Windows 10 (21H2)

## High-Level Deployment and Configuration Steps

1. **Domain Controller VM (Windows Server 2022):** Named "DC-1"

2. **Domain Controller’s NIC Private IP address:** Set to static

3. **ICMPv4 (ping):** Allowed on the Domain Controller

4. **Create Admin and Normal User Account in Active Directory**

5. **Join Client to domain**

6. **Attempt to login Client-1 with one of the users**

## Deployment and Configuration Steps

Before beginning, create a Resource group to store the Virtual Machines you create: Domain Controller (DC-1) and Virtual Machine (Client-1). The Domain Controller VM will run the Windows Server 2022 Datacenter-x64 Gen2 image.

![DC-1 VM](https://github.com/Christopherdek/configure-ad/assets/148359456/2871a342-3530-44d1-ab20-dd77f05b3d9c)

![DC-1 Firewall Settings](https://github.com/Christopherdek/configure-ad/assets/148359456/e80bd273-c22d-4da9-9d5a-c47299094811)

Create a VM for the Client (Windows 10) named "Client-1" in the same Resource Group and Vnet as DC-1.

![Client-1 VM](https://github.com/Christopherdek/configure-ad/assets/148359456/e8f6696a-7a14-44e8-be98-54c29675160e)

![Client-1 Firewall Settings](https://github.com/Christopherdek/configure-ad/assets/148359456/bd47a3d3-7d17-45f4-b8c7-dae8693a329d)

Once both VMs are created, set DC-1's Private IP address to static.

![Set DC-1 IP](https://github.com/Christopherdek/configure-ad/assets/148359456/9a452faa-e609-43ec-aa0a-12ef4df0a8ca)

Check the connection between the client and domain controller by logging in with your username and PW to Client-1 with Remote Desktop Connection(RDP) and ping the DC-1 Private IP address using "-t" (Perpetual ping). The ICMPv4 (ping) was showing it was allowing on Domain Controller's (DC-1) Firewall in Windows Firewall (Core Networking Diagnostics). Then log back into Client-1 to see if the ping is successful.

![Ping Test](https://github.com/Christopherdek/configure-ad/assets/148359456/13bc4e71-4499-4e36-833e-f06dabe061ec)

In this example, the ICMP rule has been allowed on the Windows firewall for inbound traffic.

![Firewall Rule](https://github.com/Christopherdek/configure-ad/assets/148359456/ed1cfa6c-92a2-4a2f-9d1d-be192c63741a)

Back in DC-1, go and select add roles and features to enable "Active Directory Domain Services". This promotes the Domain Controller (DC) a new forest as mydomain.com setup. Your Remote Desktop will restart, and then you can log back into DC-1 as user: mydomain.com/username*.

![Add Roles](https://github.com/Christopherdek/configure-ad/assets/148359456/4cfa5d72-e505-460f-912a-a507251914e0)

Next, create and configure the Organizational units for the admins and employees in Active Directory (AD) while in DC-1. So in Active Directory Users and Computers, right-click mydomain.com tab --&gt; New --&gt; Organizational Unit. Name it _Admin and repeat and make _EMPLOYEES.

![Organizational Units](https://github.com/Christopherdek/configure-ad/assets/148359456/3b513d26-2107-4877-8ea3-9a00ad2fa77e)

Right-click on _ADMIN and create a new employee, use the same password with "user_admin." Once the admin is created, add "user_admin" to the "domain admins" security group.

![Create Employee](https://github.com/Christopherdek/configure-ad/assets/148359456/2af14024-18ef-42c0-813b-a893efad671c)

Log out and (mydomain.com\user) reconnect to dc-1 with your "mydomain.com\user_admin. My example mydomain.com\ayrin_admin.

![Reconnect to DC-1](https://github.com/Christopherdek/configure-ad/assets/148359456/361fed99-8d02-4cb9-8d8d-9c49e50bf444)

The next step is to go into VM Client-1 portal in Azure. We are going to join Client-1 to the domain(mydomain.com) We have to change the DNS on Client-1 Private IP address the same as the Domain. First, settings --&gt; Network --&gt; Network Interface the "NIC" click on DNS server and change the Private IP address to the Domain. Select "custom," enter the IP address, and click save.

![Change DNS](https://github.com/Christopherdek/configure-ad/assets/148359456/2f0c74ef-9f2f-4c34-8306-ae53a6f131ad)

Now with the DNS servers successfully changed to the private IP address of DC-1, we can add the client-1 to the domain without any issue. You will get a message about the client being successfully added to the domain. To log in to the client is by "System --&gt; Rename This PC --&gt; enter domain name --&gt; select ok --&gt; select apply. Then restart the systems.

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/ea6f3327-6a9f-4260-a921-9c1d09744861)

A tab will show 'welcome to mydomain.com'

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/cf29f30c-b895-45da-8209-8116d04a54fc)


With the Client-1 connected to the domain, we now can create the clients and load them into "_employment OU" in the domain controller(DC-1). We'll need "Powershell_ISE" as the administrator. Click on the file and copy and paste into the new file(White area) the pre-configured script. While the script is running, Employees will be made, Randomly.

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/cd71fbc9-245a-42dd-98e6-ccf9cb6078c9)

The randomly generated employees are reflected in the Active Directory on the Domain Controller.

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/09c28289-4df4-42d3-aecc-8fff84f04d92)

With a random user, you can now log into that user's account.

![image](https://github.com/Christopherdek/configure-ad/assets/148359456/6c90d286-0108-47a4-ac0d-5f68832f7a53)
