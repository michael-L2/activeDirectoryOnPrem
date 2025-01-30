<p align="center">
<img src="https://www.startpage.com/av/proxy-image?piurl=https%3A%2F%2Flogodix.com%2Flogo%2F353645.png&sp=1738272373T2fc24d6140fcb13041f0d490a526db6b890ab3883e6549e4c43edad3e07ddd60" alt="Azure AD Logo" />
</p>

<h1 align="center">Microsoft Azure / Entra ID: Configuring On-premises Active Directory within Azure VMs</h1>

The goal of this exercise is to create a resource group with a virtual network consisting of a domain controller and a client, with thousands of users... And deploying Active Directory! 
---

## **Environments and Technologies Used**

- **Microsoft Azure** (Virtual Machines/Compute)
- **Remote Desktop Protocol (RDP)**
- **Server Manager**
- **Active Directory Users and Computers (ADUC)**
- **PowerShell ISE**

---

## **Operating System Used**

- **Windows 10** (21H2)
- **Windows Server 2022 Datacenter: Azure Edition**

---

## **Prerequisites**

Before starting, make sure you've set up an Azure account.
---

## **Setting Up Azure/Entra ID**

---

### **1. Create A Resource Group**
1. Create a **Resource Group** with a unique name.
2. **Ensure everything you create is in the same Region.**
![RSG](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/1RSG.png?raw=true)

---

### **2. Create A Virtual Network**
1. Create a **Virtual Network** with an appropriate name within your **Resource Group**.
2. **Ensure everything you create is in the same Region.**
![VNET](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/2VNET.png?raw=true)

---

### **3. Create A Domain Controller Virtual Machine**
1. Create a **Virtual Machine** with a name like "dc-1", or something to note it's purpose as a **Domain Controller** within your **Resource Group**.
2. **Ensure everything you create is in the same Region.**
3. Make sure you select an appropriate **Size**, as this machine will do a lot of work.
![VM1](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/3VM.png?raw=true)

---

### **4. Create A Client Virtual Machine**
1. Create a **Virtual Machine** with a name like "client-1", or something to note it's purpose as the **Client** within your **Resource Group**.
2. **Ensure everything you create is in the same Region.**
3. Make sure you select an appropriate **Size**, as this machine is just a client VM.
![VM2](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/5VM2.png?raw=true)

---

### **5. Configuring the Virtual Network**
1. Within your **Domain Controller VM** in the **Azure Virtual Machines Dashboard**, click **Networking**, and open **Network Settings**.
2. Click on **Network interface / IP configuration**.
![VM3](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/6VM1ConfigNIC.png?raw=true)
3. Inside of the **IP configurations** tab, under **Settings**, set the IP address to **Static**, as it will be acting as a **Domain Controller**.
![VM4](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/8VM1ConfigNICStatic.png?raw=true)
4.Save/Apply.

---

### **6. Connecting to the Domain Controller**
1. Return to the **Virtual Machines** Dashboard in Azure.
2. Find your **Domain Controller's Public IP Address** by looking to the right of your machine's name.
3. Connect via **RDP.**
![VM5](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/9VM1ConnectRDPdc-1.png?raw=true)

---

### **7. Disabling Firewall**
1. **Inside of your Domain Controller VM**, right-click the **Start Button** and click **Run**.
2. Type **wf.msc**, and press OK.
3. Inside of Firewall, click **properties**
4. Set the Firewall state to **Off** in all profiles for the sake of this demonstration. (Domain, Private, Public)
![VM6](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/12VMFireWallOff.png?raw=true)

---

### **8. Point Client to Domain Server**
1. Return to the **Azure Virtual Machines Dashboard**, and click on your **client** machine.
2. click **Networking** and open **Network Settings**, then click on **Network interface / IP configuration**.
3. Inside of **DNS Servers**, under **Settings**, set the DNS server to **Custom**, and type your **Domain Controller's Static IP** within the network.
![VM7](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/14VMClientDNS.png?raw=true)

---

### **9. Connecting to Client VM**
1. Open **Remote Desktop** on your actual machine, and connect to your **Client Machine**, using the public IP address, and log in with your credentials you set earlier.
2. Inside of the Virtual Machine, open **Powershell**, and ping 10.0.0.X (your static IP for your Domain Controller VM) to confirm.
![VM8](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/18Confirmation.png?raw=true)

---

### **10. Configuring Domain Controller**
1. Connect to your **Domain Controller VM**, and open **Server Manager**.
2. Inside Server Manager, click **Add roles and features**, and enable **Active Directory Domain Services**, then click Next, and install.
![VM9](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/20Configdc-1.png?raw=true)
3. After installation, click the **Flag Icon**, and click **Promote this server to a domain controller**.
![VM10](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/21Promotion.png?raw=true)
4. Inside of the configuration wizard, click **Add a new forest**, and set your domain name as you wish, then Next.
5. **Disable** DNS delegation, then Next.
6. In the prerequisites check, click **Install**, and wait for it to finish, then next.
![VM11](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/25Post-Setup.png?raw=true)
7. Sign out, then connect again via **RDP**.
![VM12](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/26DomainControllerLoginMethod?raw=true)

---

### **11. Adding Organizational Units**
1. In your **Domain Controller Virtual Machine**, press/click the **Start Button**, open **Windows Administrative Tools**, and run **Active Directory Users and Computers**.
2. Inside **ADUC**, find your **Domain Name**, and right-click **New > Organizational Unit**.
3. Make one for **_EMPLOYEES**, and **_ADMINS**, then refresh.
![VM13](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/28AddingOU.png?raw=true)

---

### **12. Creating Admin Account**
1. In **ADUC**, select your **_ADMINS** OU from earlier.
2. Right-click **New > User**, and name it appropriately, with a memorable password that **never expires**.
![VM14](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/35UserFinal.png?raw=true)

---

### **13. Adding To Secutiry Group**
1. In **ADUC**, right-click your user, and select **Properties**.
2. Inside of **Properties**, click **Member Of**, and click **Add**.
3. Add the user to the group **Domain Admins**.
4. Save/Apply.
![VM15](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/39AddingToSecurityG.png?raw=true)

---

### **14. Join Domain as Client**
1. Sign into your **Client VM** as your **admin user**.
2. Open **Settings**, go to **About**, then click **Rename this PC (advanced)**.
3. Inside of **System Properties**, click **Change...**, and set the Domain to **your previously created domain name**.
![VM16](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/41JoiningTheDomainAsLabuser.png?raw=true)
4. Add your newly created **Admin Account** by typing in the **Domain Name\adminuser**, and press **OK**, and restart.
![VM17](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/42ChangingDomain.png?raw=true)

---

### **15. Confirming Successful Join & New OU**
1. Log back into your **Domain Controller Virtual Machine** using your newly administered **Admin Credentials** via **RDP**.
![VM18](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/45Login.png?raw=true)
2. Press **Start**, and find **ADUC**.
3. Inside **ADUC**, click your domain, and then click **Computers**. You should see your **Client Machine's Name**.
4. Right-click your domain, and select **New > Organizational Unit**, and name it **_CLIENTS**.
![VM19](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/49AddingToClients.png?raw=true)

---

### **16. Adding Domain Users To Client**
1. Log back into **Client** with your **admin credentials from before** via **RDP**.
![VM20](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/51Login.png?raw=true)
2. Once logged in, open **Settings**, and click **Remote Desktop**, and click **Advanced settings**.
3. Inside of **Remote Desktop Users**, click **Add...**, then add **Domain Users**, and press **OK**.
![VM21](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/53AddingDomainUsersRDP.png?raw=true)

---

### **17. Creating Fake Users**
1. Log back into your **Domain Controller** as your **admin user from before** via **RDP**, and open **Windows PowerShell ISE as admin**.
![VM22](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/56PowershellISE.png?raw=true)
2. Inside **Powershell**, click **New Script**, and save it as **createUsers**. Type the following:
![VM23](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/59Code.png?raw=true)
3. Wait for it to finish.
![VM24](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/61Creation.png?raw=true)

---

### **18. Check Added Users In ADUC & Log in**
1. Open **ADUC**, and click **_EMPLOYEES**.
![VM25](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/62CheckingInActiveDirectory.png?raw=true)
2. Select a random user, and sign in via **RDP** to your **Client Virtual Machine** using **"Password1"** as the default password from our script, and the user as the username.
![VM26](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/64RandomLogin.png?raw=true)
3. After signing in, open **Command Prompt**. You should now clearly see your username.
![VM27](https://github.com/michael-L2/activeDirectoryOnPrem/blob/main/7ActiveDirectory/66Confirmation.png?raw=true)

---
### **Congratulations!**
You've created a Virtual Network with a Domain Controller, and thousands of users! In the real world, many more security protocols would be followed... Like a [Group Policy or an NSG!](https://github.com/michael-L2/activeDirectoryGP)🎉
