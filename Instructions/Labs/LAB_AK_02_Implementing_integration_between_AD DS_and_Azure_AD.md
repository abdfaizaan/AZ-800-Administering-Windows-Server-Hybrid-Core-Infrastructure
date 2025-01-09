# Lab 2: Implementing integration between AD DS and Microsoft Entra ID

## Lab scenario

To address concerns regarding management and monitoring overhead resulting from using Microsoft Entra ID to authenticate and authorize access to Azure resources, you decide to test integration between on-premises Active Directory Domain Services (AD DS) and Entra ID to verify that this will address business concerns about managing multiple user accounts by using a mix of on-premises and cloud resources.

Additionally, you want to make sure that your approach addresses the Information Security team's concerns and preserves existing controls applied to Active Directory users, such as sign-in hours and password policies. Finally, you want to identify Azure AD integration features that allow you to further enhance on-premises Active Directory security and minimize its management overhead, including Microsoft Entra ID Password Protection for Windows Server Active Directory and Self-Service Password Reset (SSPR) with password writeback.

Your goal is to implement pass-through authentication between on-premises AD DS and Entra ID.

**Note:** An **[interactive lab simulation](https://mslabs.cloudguides.com/guides/AZ-800%20Lab%20Simulation%20-%20Implementing%20integration%20between%20AD%20DS%20and%20Azure%20AD)** is available that allows you to click through this lab at your own pace. You may find slight differences between the interactive simulation and the hosted lab, but the core concepts and ideas being demonstrated are the same. 

## Lab objectives

In this lab, you will perform:

- Exercise 1: Prepare Entra ID for integration with on-premises AD DS, including adding and verifying a custom domain.
- Exercise 2: Prepare on-premises AD DS for integration with Entra ID, including running IdFix DirSync Error Remediation Tool.
- Exercise 3: Install and configure Microsoft Entra Connect.
- Exercise 4: Verify integration between AD DS and Entra ID by testing the synchronization process.
- Exercise 5: Implementing Entra ID integration features in Active Directory, including Entra ID Password Protection for Windows Server Active Directory and SSPR with password writeback.

## Estimated time: 60 minutes

## Architecture Diagram

   ![](media/mod2art.png)  

## Lab setup

Virtual machines: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1**, and **AZ-800T00A-ADM1** must be running. Other VMs can be running, but they aren't required for this lab. 

> **Note**: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1**, and **AZ-800T00A-SEA-ADM1** virtual machines are hosting the installation of **SEA-DC1**, **SEA-SVR1**, and **SEA-ADM1**

1. Select **SEA-ADM1**.

1. Sign in using the following credentials:

   - Username: **Administrator**
   - Password: **Pa55w.rd**
   - Domain: **CONTOSO**

## Exercise 1: Preparing Microsoft Entra ID for AD DS integration

### Task 1: Create a custom domain in Azure

1. Connect to **SEA-ADM1** and, if needed, sign in as **CONTOSO\Administrator** with a password of **Pa55w.rd**.

1. On **SEA-ADM1**, double-click on the Azure portal, and authenticate with your Azure credentials.

   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>
 
     ![Enter Your Username](./media/signin.png)
 
3. Next, provide your password:
 
   - **Password:** <inject key="AzureAdUserPassword"></inject>
 
     ![Enter Your Password](./media/signin1.png)   

1. If you see the pop-up **Action Required**, click **Ask Later**.

   ![](media/action.png) 
 
1. If prompted to **Stay signed in**, you can click **No**.

1. If a **Welcome to Microsoft Azure** pop-up window appears, simply click **Cancel** to skip the tour.

1. On the Azure portal, from the **Search resources, Services, and docs(G+/)** blade, search for **Microsoft Entra ID (1)** and select **Microsoft Entra ID (2)** from the services.

   ![](media/az--2.png) 

1. On the **Microsoft Entra ID** page, from the left-hand navigation pane, under **Manage** select **Custom domain names (1)** and then select **+ Add custom domain (2)**.

   ![](media/az-2.png) 

1. In the **Custom domain name** pane, in the **Custom domain name** text box, enter **contoso.com (1)**, and then select **Add domain (2)**.

   ![](media/az-3.png)

1. On the `contoso.com` custom domain name page, review the Domain Name System (DNS) record types that you would use to verify the domain.

1. Close the pane without **verifying** the domain name.

1. Here you can see the created Custom domain.   

   ![](media/azz5.png)

   > **Note**: While, in general, you would use DNS records to verify a domain, this lab doesn't require the use of a verified domain.

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
> - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
> - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
> - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help

<validation step="b65ef28d-a185-42fd-a25f-5a18bb1b2903" /> 

### Task 2: Create a user with the Global Administrator role

1. On **SEA-ADM1**, navigate back to the **Microsoft Entra ID** page in the Azure portal, from the left-hand navigation pane select **Users**.

    ![](media/azz22.png)

1. On the **Users** page, select **+ New User (1)** and in drop down select **Create new user (2)**

    ![](media/az-4.png)

1. On the **Create new User** page, under **Basics** tab, provide the below details and then click on **Next: Properties> (4)**.

    - In the **User principal name** and **Display Name** text boxes, enter **admin1 (1)(2)**.

      >**Note**: Ensure the domain name drop-down menu for the **User name** lists the default domain name ending with `onmicrosoft.com`.

    - Under **Password**, select the **Auto generate (2)** checkbox. Record the user name and password as you'll use it later in this lab.

      ![](media/az-5.png)

1. Under **settings** in the **Usage location** drop-down list, select **United States (1)** and then click on **Next: Assignments> (2)**.

    ![](media/az-6.png)

1. Under **Assignments** tab , select **+ Add role** and on **Directory roles** page, from the list of roles, select **Global administrator (1)**, and then select **Select (2)**.

    ![](media/az-7.png)

1. On the **Create new user** page, select **Next: Review + Create** and **Create**.

    ![](media/azz11.png)

1. Once created you can see the created user in the **Users** page.

    ![](media/azz2.png)

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
> - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
> - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
> - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help

<validation step="4ea1ff7e-4b7b-4fc0-bb83-fec04f01bdf9" />

### Task 3: Change the password for the user with the Global Administrator role

1. On the Azure portal, select your user account, and then select **Sign out**.

   ![](media/signout.png) 

1. On the **Pick an account** page, select **+ Use another account**.

1. On the **Sign in** page, enter the fully-qualified username of the user account you previously created , and then select **Next**.

   >**Note**: It looks similar to **admin1@otuwamocXXXX.onmicrosoft.com**

1. For the current password, use the password that you copied in the previous step.

1. It asks you to change current password, enter a complex password twice, and then select **Sign in**.

   >**Note**: Record the complex password you used as you'll use it later in this lab.

1. If you see the pop-up **Action Required**, click **Ask Later**.
 
4. If prompted to stay signed in, you can click **No**.

5. If a **Welcome to Microsoft Azure** pop-up window appears, simply click **Cancel** to skip the tour.

## Exercise 2: Preparing on-premises AD DS for Microsoft Entra ID integration

### Task 1: Install IdFix

1. On **SEA-ADM1**, open a new tab in Microsoft Edge, and then browse to **https://github.com/microsoft/idfix**.

1. On the **Github** page, you have to scroll down, and under **ClickOnce Launch**, select **launch**.

   ![](media/azz12.png)

1. On the status bar, select **Open file**.

1. In the **Application Install - Security Warning** dialog box, select **Install**.

   ![](media/azz13.png)

1. In the **IdFix Privacy Statement** dialog box, review the disclaimer, and then select **OK**.

### Task 2: Run IdFix

1. In the **IdFix** window, select **Query**.

1. If presented with the **Schema Warning** dialog box, select **Yes**.

   ![](media/az-8.png) 

1. Review the list of objects from Microsoft Entra ID, and observe the **ERROR** and **ATTRIBUTE** columns. In this scenario, the value of **displayName** for **ContosoAdmin** is blank, and the tool's recommended new value appears in the **UPDATE** column.

   ![](media/az-9.png) 

1. In the **IdFix** window, from the **ACTION** drop-down menu **(1)**, select **Edit(2)**, and then select **Apply(3)** to automatically implement the recommended changes.

   ![](media/azz24.png)

1. In the **Apply Pending** dialog box, select **Yes**.

   ![](media/azz15.png)

1. Close the IdFix tool.

## Exercise 3: Downloading, installing, and configuring Microsoft Entra Connect

### Task 1: Install and configure Microsoft Entra Connect

1. On **SEA-ADM1**, in the Microsoft Edge window displaying the Azure portal, browse to **Microsoft Entra ID**.

1. On the **Microsoft Entra ID** page, from the left-hand navigation pane, under the **Manage** select **Microsoft Entra Connect**.

   ![](media/azz16.png)

1. On the  **Microsoft Entra Connect | Get started** Get Started page, select **Manage** tab and under **Manage from on-premises: Connect Sync**, select **Download Connect Sync Agent**.

   ![](media/azz3.png)

1. On the **Microsoft Entra Connect Agent** page, select **Accept terms & download**

1. On the status bar, select **Open file**.

1. On the **Welcome to Microsoft Microsoft Entra Connect Sync** page, select the **I agree to the license terms and privacy notice (1)** checkbox, and then select **Continue (2)**.

   ![](media/za1.png)

1. On the **Express Settings** page, select **Use express settings**.

1. On the **Connect to Microsoft Entra ID** page, enter the username of the Microsoft Entra ID Global Administrator user account you created in exercise 1 **(1)**, and then select **Next (2)**.

   ![](media/az-10.png)

   >**Note**: If a Microsoft Sign-in pop appears, please sign-in using admin1 user account credentials that you created in exercise 1.

1. On the **Connect to AD DS** page, enter the following credentials, and then select **Next (3)**:

   - Username: **CONTOSO\Administrator (1)**
   - Password: **Pa55w.rd (2)**
     
     ![](media/za2.png)

1. On the **Microsoft Entra sign-in configuration** page, note that the new domain you added is in the list of Active Directory UPN Suffixes, but its status is listed as **Not verified**.

   > **Note**: The domain name provided does not have to be a verified domain. While you typically would verify a domain prior to installing Microsoft Entra Connect, this lab doesn't require that verification step.

1. Select the **Continue without matching all UPN suffixes to verified domains (1)** checkbox, and then select **Next (2)**.

   ![](media/za3.png)

1. On the **Ready to configure** page, review the list of actions, and then select **Install**.

   ![](media/za4.png)

   >**Note:** Wait for the installation to get completed.

1. On the **Configuration complete** page, select **Exit**.

   ![](media/az-11.png)

    >**Note:** If you encounter the message **Directory synchronization is enabled for this directory, but has not taken effect. Please wait untill directory synchronization is ready** in the configuration window, allow up to 30 minutes for the synchronization process to complete.      
 
## Exercise 4: Verifying integration between AD DS and Microsoft Entra ID

### Task 1: Verify synchronization in the Azure portal

1. On **SEA-ADM1**, switch to the Microsoft Edge window displaying the Azure portal. 

1. On the **Microsoft Entra ID** page, select **Users**.

1. Note that the user list includes users synced from Microsoft Entra ID.

   ![](media/azz26.png)

   >**Note**: After the directory synchronization starts, it can take 15 minutes for Microsoft Entra ID objects to appear in the Microsoft Entra ID portal.

1. In Microsoft Edge, go back to the **Microsoft Entra ID** page.

1. On the **Microsoft Entra ID** page, select **Groups**.

   ![](media/az-12.png)

1. Navigate to **All groups**, note the list of groups synced from Microsoft Entra ID.

   ![](media/az-13.png)

### Task 2: Verify synchronization in the Synchronization Service Manager

1. On **SEA-ADM1**, on the **Start** menu, expand **Azure AD Connect**, and then select **Synchronization Service**.

   ![](media/azz27.png)
            
1. In the **Synchronization Service Manager** window, under the **Operations** tab, observe the tasks that were performed to sync the Microsoft Entra ID objects.

   ![](media/azz28.png)

1. Select the **Connectors** tab and note the two connectors.

   >**Note**: One connector is for AD DS and the other is for the Microsoft Entra ID tenant.

   ![](media/azz29.png)

1. Close the **Synchronization Service Manager** window.

### Task 3: Update a user account in Active Directory

1. On **SEA-ADM1**, search and select **Server Manager**.

   ![](media/az-14.png)

1. Click on the **Tools** menu from top right, select **Active Directory Users and Computers**.

   ![](media/azz30.png) 

1. In **Active Directory Users and Computers**, expand the **Sales (1)**, and then open the properties **(3)** for **Sumesh Rajan** by right clicking on the user **(2)**.

   ![](media/az-15.png)

1. In the properties of the user, select the **Organization (1)** tab. In the **Job Title** text box, enter **Manager (2)**, and then select **OK (3)**.

   ![](media/az-16.png)

### Task 4: Create a user account in Active Directory

1. In **Active Directory Users and Computers**, right-click or access the context menu for the **Sales (1)**, select **New (2)**, and then select **User (3)**.

   ![](media/az-17.png)

1. In the **New Object - User** window, enter the following user details for each field, and then select **Next > (4)**:

   - First name: **Jordan (1)**
   - Last name: **Mitchell (2)**
   - User logon name: **Jordan (3)**

     ![](media/az-18.png)

1. In the **Password** and **Confirm password** fields, enter `Pa55w.rd`, and then select **Next >**.

   ![](media/azz33.png)

1. Select **Finish**.

### Task 5: Sync changes to Microsoft Entra ID

1. On **SEA-ADM1**, on the **Start** menu, select **Windows PowerShell**.

   ![](media/az--19.png)

1. In the **Windows PowerShell** console, enter the following command, and then press Enter to trigger synchronization:

   ```powershell
   Start-ADSyncSyncCycle
   ```

   ![](media/az-20.png)   

   > **Note**: Once the synchronization cycle starts, it can take 15 minutes for Microsoft Entra ID objects to appear in the Microsoft Entra ID portal.

### Task 6: Verify changes in Microsoft Entra ID

1. On **SEA-ADM1**, switch to the Microsoft Edge window displaying the Azure portal and go back to the **Microsoft Entra ID** page.

1. On the **Microsoft Entra ID** page, select **Users**.

1. On the **All Users** page, search for the user **Sumesh** and select it.

   ![](media/azz34.png)

1. Select the **Edit properties**.

   ![](media/az-21.png)   

1. Select **All (1)**, tab and then verify that the **Job title (2)** attribute has been synced from Microsoft Entra ID.

   ![](media/az-22.png)   

1. In Microsoft Edge, go back to the **All Users** page.

1. On the **All Users** page, search for the user **Jordan** and select it.

   ![](media/az-23.png)   

1. Select **Edit properties**, select **All (1)** tab, and review the attributes of the user account that was synced from Microsoft Entra ID **(2)**.

   ![](media/az-24.png)   

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
> - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
> - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
> - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help

<validation step="8e646bae-8cc0-4e44-8b11-fb2af82ffd89" />

## Exercise 5: Implementing Microsoft Entra ID integration features in AD DS 

### Task 1: Enable password writeback in Microsoft Entra Connect

1. On **SEA-ADM1**, on the **Start** menu, expand **Azure AD Connect (1)**, and then select **Azure AD Connect (2)**.

   ![](media/az-25.png)   

1. In the **Welcome to Microsoft Entra Connect Sync** window, select **Configure**.

   ![](media/az-26.png)   

1. On the **Additional tasks** page, select **Customize synchronization options (1)**, and then select **Next (2)**.

   ![](media/az--27.png)

1. On the **Connect to Microsoft Entra ID** page, enter the **Email/Username:** given below, and then select **Next**.
   
   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>

     ![](media/az-27.png)      
   
   - **Password:** <inject key="AzureAdUserPassword"></inject>

     >**Note**: If a Microsoft Sign-in pop appears, please click on **+ Use another account** and sign-in using your credentials.

1. On the **Connect your directories** page, select **Next**.

   ![](media/az-29.png)

1. On the **Domain and OU filtering** page, select **Next**.

   ![](media/az-30.png)

1. On the **Optional features** page, select **Password writeback (1)**, and then select **Next (2)**.

   ![](media/az-31.png)

   > **Note**: Password writeback is required for self-service password reset of Active Directory users. This allows passwords changed by users in Microsoft Entra ID to sync to the Active Directory.

1. On the **Ready to configure** page, review the list of actions to be performed, and then select **Configure**.

   ![](media/az-32.png)

   >**Note:** Wait for the configurations to get completed.

1. On the **Configuration complete** page, select **Exit**.

   ![](media/az-33.png)

    >**Note:** If you encounter the message **Directory synchronization is enabled for this directory, but has not taken effect. Please wait untill directory synchronization is ready** in the configuration window, allow up to 30 minutes for the synchronization process to complete. 

   >**Note:** If you still encounter the message "Directory synchronization is enabled for this directory, but has not taken effect. Please wait until directory synchronization is ready" in the configuration window, even after waiting 30 minutes, **do not proceed further** with the below tasks. This is a known issue with Microsoft, and synchronization may take up to 72 hours to complete.
   
### Task 2: Enable pass-through authentication in Microsoft Entra Connect

1. On **SEA-ADM1**, on the **Start** menu, expand **Azure AD Connect**, and then select **Azure AD Connect**.

1. In the **Welcome to Microsoft Entra Connect Sync** window, select **Configure**.

1. On the **Additional tasks** page, select **Change user sign-in (1)**, then select **Next (2)**.

   ![](media/az-34.png)

1. On the **Connect to Microsoft Entra ID** page, enter the following credentials, and then select **Next**.

   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>
   
   - **Password:** <inject key="AzureAdUserPassword"></inject>

     >**Note**: If a Microsoft Sign-in pop appears, please select the above account and sign-in using your credentials.

1. On the **User sign-in** page, select **Pass-through authentication (1)**. Verify that the **Enable single sign-on (2)** checkbox is selected, and then select **Next (3)**.

   ![](media/az-35.png)

1. On the **Enable single sign-on** page, select **Enter credentials**.

   ![](media/az-36.png)

1. In the **Forest credentials** dialog box, enter the following credentials, and then select **OK (3)**:

   - Username: **Administrator (1)**
   - Password: **Pa55w.rd (2)**

     ![](media/az-37.png)   

1. On the **Enable single sign-on** page, verify that there's a green check mark next to **Enter credentials (1)**, and then select **Next (2)**.

   ![](media/az-38.png)

1. On the **Ready to configure** page, review the list of actions to be performed, and then select **Configure**.

   >**Note:** Wait for the configurations to get completed.

1. On the **Configuration complete** page, select **Exit**.

   ![](media/az-39.png)

### Task 3: Verify pass-through authentication in Azure

1. On **SEA-ADM1**, switch to the Microsoft Edge window displaying the Azure portal and go back to the **Microsoft Entra ID** page.

1. On the **Microsoft Entra ID** page in the Azure portal, select **Microsoft Entra Connect**.

1. On the **Microsoft Entra Connect** page, in left navigation pane select **Connect Sync (1)** and review the information under **User Sign-In**.
Under **User Sign-In**, select **Seamless single sign-on (2)**.

   ![](media/az-40.png)

1. On the **Seamless single sign-on** page, note the on-premises domain name.

   ![](media/az-41.png)

1. In Microsoft Edge, go back to the **Microsoft Entra Connect** page.

1. On the **Microsoft Entra Connect** page, select **Connect sync**, under **User Sign-In**, select **Pass-through authentication**.

   ![](media/az-42.png)

1. On the **Passthrough Authentication** page, note the **SEA-ADM1** server name under **Authentication Agent**.

   ![](media/az-43.png)

   > **Note**: If you're not able see on-premises domain name and **SEA-ADM1** server name, kindly sign-in to azure portal in private window and perform above steps.
   > **Note**: To install the Azure AD Authentication Agent on multiple servers in your environment, you can download its binaries from the **Pass-through authentication** page in the Azure portal. 

### Task 4: Install and register the Microsoft Entra ID Password Protection proxy service and DC agent

1. On **SEA-ADM1**, start Microsoft Edge, browse to the **[Azure AD Password Protection for Windows Server Active Directory](https://www.microsoft.com/en-us/download/details.aspx?id=57071)** page where you can download installers, and then select **Download**.

   ![](media/az-44.png)

1. On the **Choose the download you want** page, select the **AzureADPasswordProtectionProxySetup.exe (1)** and the **AzureADPasswordProtectionDCAgentSetup.msi (2)** files, and then select **Download (3)**.

   ![](media/az-45.png)

1. In the **Download multiple files** dialog box, select **Allow**.

   > **Note**: We recommend installing the proxy service on a server that isn't a domain controller. In addition, the proxy service should not be installed on the same server as the Microsoft Entra Connect agent. You will install the proxy service on **SEA-SVR1** and the Password Protection DC Agent on **SEA-DC1**.

1. On **SEA-ADM1**, switch to the **Windows PowerShell** console window.

   >**Note:** Run all these commands only in **SEA-ADM1**'s Windows PowerShell, do not switch to other VM's.

1. In the **Windows PowerShell** console, enter the following command, and then press Enter to remove the Zone.Identifier alternate data stream indicating that files have been downloaded from internet:

   ```powershell
   Get-ChildItem -Path "$env:USERPROFILE\Downloads" -File | Unblock-File
   ```
1. Run the following commands to create the **C:\Temp** directory on **SEA-SVR1**, copy the **AzureADPasswordProtectionProxySetup.exe** installer to that directory, and then invoke the installation:

   ```powershell
   New-Item -Type Directory -Path '\\SEA-SVR1.contoso.com\C$\Temp' -Force
   Copy-Item -Path "$env:USERPROFILE\Downloads\AzureADPasswordProtectionProxySetup.exe" -Destination '\\SEA-SVR1.contoso.com\C$\Temp\'
   Invoke-Command -ComputerName SEA-SVR1.contoso.com -ScriptBlock { Start-Process -FilePath C:\Temp\AzureADPasswordProtectionProxySetup.exe -ArgumentList '/quiet /log C:\Temp\AzureADPPProxyInstall.log' -Wait }
   ```
   
   ![](media/az-46.png)   

   >**Note:** If it shows errors then wait for sometime and again perform the step-6.

1. Run the following commands to create the **C:\Temp** directory on **SEA-DC1**, copy the **AzureADPasswordProtectionDCAgentSetup.msi** installer to that directory, invoke the installation, and restart the domain controller after the installation completes:

   ```powershell
   New-Item -Type Directory -Path '\\SEA-DC1.contoso.com\C$\Temp' -Force
   Copy-Item -Path "$env:USERPROFILE\Downloads\AzureADPasswordProtectionDCAgentSetup.msi" -Destination '\\SEA-DC1.contoso.com\C$\Temp\'
   Invoke-Command -ComputerName SEA-DC1.contoso.com -ScriptBlock { Start-Process msiexec.exe -ArgumentList '/i C:\Temp\AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart /log C:\Temp\AzureADPPInstall.log' -Wait }
   Restart-Computer -ComputerName SEA-DC1.contoso.com -Force
   ```

   ![](media/az-47.png)

1. Run the following commands to validate that the installations resulted in the creation of services necessary to implement Azure AD Password Protection:

   ```powershell
   Get-Service -Computer SEA-SVR1 -Name AzureADPasswordProtectionProxy | fl
   Get-Service -Computer SEA-DC1 -Name AzureADPasswordProtectionDCAgent | fl
   ```

   ![](media/az-48.png)   

   > **Note**: Verify that each service has the **Running** status.

   >**Note:** If you encounter any error, please re run the command.

1. In the **Windows PowerShell** console, enter the following command and press Enter to start a PowerShell Remoting session to **SEA-SVR1**:

   ```powershell
   Enter-PSSession -ComputerName SEA-SVR1
   ```

   ![](media/az-49.png)   

1. From the **[SEA-SVR1]** prompt, enter the following command and press Enter to register the proxy service with Active Directory (replace the `<Azure_AD_Global_Admin>` placeholder with the following 
   credential):

   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>

   ```powershell
   Register-AzureADPasswordProtectionProxy -AccountUpn <Azure_AD_Global_Admin> -AuthenticateUsingDeviceCode
   ```

   ![](media/az-50.png)   

   >**Note:** If you encounter any error, please re run the command.

1. As instructed, open another Microsoft Edge window, browse to **https://microsoft.com/devicelogin** and when prompted, enter the code included in the message displayed in the PowerShell Remoting session. 

   ![](media/az-52.png)

1. When prompted, authenticate by using following credentials, and then select **Continue**.

   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>

   - **Password:** <inject key="AzureAdUserPassword"></inject>

1. When prompted **Are you trying to sign in to Microsoft Azure Powershell**, click on **Continue**.   

1. Switch back to the PowerShell Remoting session, enter the following command and press Enter to exit the PowerShell Remoting session to **SEA-SVR1**:

   ```powershell
   Exit-PSsession
   ```

1. In the **Windows PowerShell** console, enter the following command and press Enter to start a PowerShell Remoting session to **SEA-DC1**:

   ```powershell
   Enter-PSSession -ComputerName SEA-DC1
   ```

1. From the **[SEA-DC1]** prompt, enter the following command and press Enter to register the proxy service with Active Directory (replace the `<Azure_AD_Global_Admin>` placeholder with the following 
   credential):

   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>
   

   ```powershell
   Register-AzureADPasswordProtectionForest -AccountUpn <Azure_AD_Global_Admin> -AuthenticateUsingDeviceCode
   ```

1. As instructed, open another Microsoft Edge window, browse to **https://microsoft.com/devicelogin** and when prompted, enter the code included in the message displayed in the PowerShell Remoting session. 

1. When prompted, authenticate by using the following credentials, and then select **Continue**.

   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>

   - **Password:** <inject key="AzureAdUserPassword"></inject>

1. When prompted **Are you trying to sign in to Microsoft Azure Powershell**, click on **Continue**.   

1. Switch back to the PowerShell Remoting session, enter the following command, and then press Enter to exit the PowerShell Remoting session to **SEA-DC1**:

   ```powershell
   Exit-PSsession
   ```

### Task 5: Enable password protection in Azure

1. On **SEA-ADM1**, switch to the Microsoft Edge window displaying the Azure portal, go back to the **Microsoft Entra ID** page, and then, on the **Microsoft Entra ID** page, under **Manage** section, select **Security**.

   ![](media/az-53.png)

1. On the **Security** page, under **Manage** section, select **Authentication methods**.

   ![](media/az-54.png)

1. On the **Authentication methods** page, under **Manage** section, select **Password protection**.

   ![](media/az-55.png)

1. On the **Password protection** page, 

   - Change the slider for **Enforce custom list** to **Yes (1)**.

   - In the **Custom banned password list** text box, enter the following words (one per line): **(2)**
 
      - **Contoso**
      - **London**

        > **Note**: The list of banned passwords should be words that are relevant to your organization.

   - Verify that the slider for **Enable password protection on Windows Server Active Directory** is set to **Yes (3)**.

   - Verify that the slider for **Mode** is set to **Audit (4)**, and then select **Save (5)**.

     ![](media/az-56.png)


### Review
In this lab, you have completed:
- Preparing Microsoft Entra ID for AD DS integration
- Preparing on-premises AD DS for Microsoft Entra ID integration
- Downloading, installing, and configuring Microsoft Entra Connect
- Verifying integration between AD DS and Microsoft Entra ID
- Implementing Azure AD integration features in AD DS

## You have successfully completed this lab.
