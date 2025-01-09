# Lab 3: Managing Windows Server

## Lab scenario

Contoso, Ltd. wants to implement several new servers in their environment, and they have decided to use Server Core. They also want to implement Windows Admin Center for remote management of both these servers and other servers in the organization.

**Note:** An **[interactive lab simulation](https://mslabs.cloudguides.com/guides/AZ-800%20Lab%20Simulation%20-%20Managing%20Windows%20Server)** is available that allows you to click through this lab at your own pace. You may find slight differences between the interactive simulation and the hosted lab, but the core concepts and ideas being demonstrated are the same. 

## Lab objective
In this lab, you will perform:

  - Task 1: Install Windows Admin Center
  - Task 2: Add servers for remote administration
  - Task 3: Configure Windows Admin Center extensions
  - Task 4: Verify remote administration
  - Task 5:Administer servers with Remote PowerShell

## Estimated time: 45 minutes

## Architecture Diagram

   ![](media/mod3art.png)  

## Lab setup

Virtual machines: **AZ-800T00A-SEA-DC1** and **AZ-800T00A-ADM1** must be running. Other VMs can be running, but they aren't required for this lab.

> **Note**: **AZ-800T00A-SEA-DC1** and **AZ-800T00A-SEA-ADM1** virtual machines are hosting the installation of **SEA-DC1** and **SEA-ADM1**.

1. Select **SEA-ADM1**.

1. Sign in using the following credentials:

   - Username: **Administrator**
   - Password: **Pa55w.rd**
   - Domain: **CONTOSO**

For this lab, you'll use the available VM environment and an Azure AD tenant. 

## Exercise 1: Implementing and using remote server administration

#### Task 1: Install Windows Admin Center

1. Connect to **SEA-ADM1**, and if needed, sign in as **CONTOSO\Administrator** with a password of **Pa55w.rd**.

1. On **SEA-ADM1**, select **Start**, and then right click on **Windows PowerShell** and select **Run as Administrator**.

1. In the **Windows PowerShell** console, enter the following command, and then press Enter to download the latest version of Windows Admin Center:
	
   ```powershell
   Start-BitsTransfer -Source https://aka.ms/WACDownload -Destination "$env:USERPROFILE\Downloads\WindowsAdminCenter.msi"
   ```
1. Enter the following command, and then press Enter to install Windows Admin Center:
	
   ```powershell
   Start-Process msiexec.exe -Wait -ArgumentList "/i $env:USERPROFILE\Downloads\WindowsAdminCenter.msi /qn /L*v log.txt REGISTRY_REDIRECT_PORT_80=1 SME_PORT=443 SSL_CERTIFICATE_OPTION=generate"
   ```

   > **Note**: Wait until the installation completes. This should take about 2 minutes.
   
   > **Note**: After installation completes, you may encounter the error message 'ERR_Connection_Refused'. If this occurs, restart SEA-ADM1 to correct the issue.

#### Task 2: Add servers for remote administration

1. On **SEA-ADM1**, start Microsoft Edge, and then go to `https://SEA-ADM1.contoso.com`.

   >**Note**: If the link does not work, on **SEA-ADM1**, open File Explorer, select Downloads folder, in the Downloads folder select **WindowsAdminCenter.msi** file and install manually. After the install completes, refresh Microsoft Edge.

   >**Note**: If you get **NET::ERR_CERT_DATE_INVALID** error, select **Advanced** on the Edge browser page, at the bottom of page select **Continue to sea-adm1-contoso.com (unsafe)**.

2. When prompted, in the **Windows Security** dialog box, enter the following credentials, and then select **OK**:

   - Username: **CONTOSO\Administrator**
   - Password: **Pa55w.rd**

3. Review the **New in this release** pop-up window and select **Close** in its upper-right corner.

4. Review the **All connections** page and note that it includes the **sea-adm1.contoso.com** entry. 

5. On the **All connections** page, select **+ Add**. 

6. In the Add or create resources pane, on the **Servers** tile, select **Add**.

7. In the **Server name** text box, enter **sea-dc1.contoso.com**.

8. Ensure that the **Use another account for this connection** option is selected, enter the following credentials, and then select **Add with credentials**:

   - Username: **CONTOSO\Administrator**
   - Password: **Pa55w.rd**

   > **Note**: After performing step 7, if an error message that says **You can add this server to your list of connections, but we can't confirm it's available.** appears, select **Add**. In the All Connections pane,  select **sea-svr1.contoso.com**, and then select **Manage as**. In the **Specify your credentials** dialog box, ensure that the **Use another account for this connection** option is selected, enter the Administrator credentials, and then select **Continue**.

   > **Note**: To perform single sign-on, you would need to set up Kerberos constrained delegation.

#### Task 3: Configure Windows Admin Center extensions

1. On **SEA-ADM1**, in the upper-right corner of the Microsoft Edge window displaying Windows Admin Center, select the **Settings** icon (the cog wheel).

1. In the left pane, under **Gateway** section, select **Extensions**. Review the available extensions.

   ![](media/lab3-t3-1upd.png) 

1. Select the **Security (Preview)** extension, then click on **Install**. The extension will install and Windows Admin Center will refresh.

   ![](media/lab3-t3-2upd.png) 

   > **Note**: If the **Security (Preview)** extension is not available, choose another Microsoft extension.

1. In the details pane, select **Installed extensions** and verify that the list includes the **Security (Preview)** extension you just installed, and another extension named **DNS** as shown below:

   ![](media/sec-preextenupd.png)

   ![](media/dns-extensionupd.png) 

1. On the top menu, next to **Settings**, select the drop-down arrow, and then select **Server Manager**.

   ![](media/lab3-t3-4upd.png)

1. On the **Server connections** page, select the **sea-dc1.contoso.com** link.

1. Ensure that **Use another account for this connection** option is selected, select the **Use these credentials for all connections**, enter the following credentials, and then select **Continue**:

   - Username: **CONTOSO\Administrator**
   - Password: **Pa55w.rd**

1. To install the DNS PowerShell tools, in the left pane, in the list of **Tools**, select **DNS**, and then select **Install**. The tools will take less than a minute to install.

1. Select the **Contoso.com** zone and review the list of its DNS records.

   ![](media/lab3-t3-7upd.png)

#### Task 4: Verify remote administration

1. On **SEA-ADM1**, in Windows Admin Center, in the left pane, in the list of **Tools**, select **Overview**. Note that the details pane of Windows Admin Center displays basic server information and performance monitoring.

1. In the left pane, in the list of **Tools**, scroll down and review the basic administration tools available. Select **Roles & features** and note which roles and features are listed as installed and which ones are available to install. Scroll down, select the **Telnet Client** checkbox, and then select **+ Install** at the top of the pane.

1. In the **Install Roles and Features** pane, select **Yes** and wait for the message confirming that Telnet Client was installed successfully.

1. On the left pane, below the  **Overview**, select **Settings**.

1. In the **Settings** section , select **Remote Desktop**.

1. In the **Remote Desktop** section, select the option **Allow remote connections to this computer** checkbox, and then select **Save**.

1. In the left pane, in the list of **Tools**, select **Remote Desktop**.

1. In the Remote Desktop pane, select the **Automatically connect with the certificate presented by this machine** checkbox, and then select **Connect**.

1. When prompted, select **Confirm**, and enter Password: **Pa55w.rd** then select **Connect**.

1. Verify that you successfully connected via Remote Desktop to **SEA-DC1** within the Windows Admin Center interface.

1. Select **Disconnect**.

1. Close the Microsoft Edge window.

#### Task 5: Administer servers with Remote PowerShell

1. On **SEA-ADM1**, switch to the **PowerShell** console session. 

1. In the **Windows PowerShell** console, enter the following command, and then press Enter to start a PowerShell Remoting session to **SEA-DC1**:

   ```powershell
   Enter-PSSession -ComputerName SEA-DC1
   ```

1. From the **[SEA-DC1]** prompt, enter the following command and press Enter to display the status of the Application Identity service (AppIDSvc):

   ```powershell
   Get-Service -Name AppIDSvc
   ```

   > **Note**: Verify that the service is currently stopped.

1. From the **[SEA-DC1]** prompt, enter the following command and press Enter to start the Application Identity service:

   ```powershell
   Start-Service -Name AppIDSvc
   ```
1. From the **[SEA-DC1]** prompt, enter the following command and press Enter to display the status of the Application Identity service (AppIDSvc):

   ```powershell
   Get-Service -Name AppIDSvc
   ```

   > **Note**: Verify that the service is currently running.

### Results

After completing this exercise, you will have installed Windows Admin Center and connected it to the servers in your lab environment. You performed a number of remote management tasks including installing a feature as well as enabling and testing Remote Desktop connectivity. Finally, you used PowerShell Remoting to check the status of a service and then to start it.

### Review
In this lab, you have completed:
- Install Windows Admin Center
- Add servers for remote administration
- Configure Windows Admin Center extensions
- Verify remote administration
- Administer servers with Remote PowerShell

## You have successfully completed this lab.
