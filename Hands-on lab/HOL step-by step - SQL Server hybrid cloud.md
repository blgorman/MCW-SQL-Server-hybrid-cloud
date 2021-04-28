![Microsoft Cloud Workshop](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/main/Media/ms-cloud-workshop.png "Microsoft Cloud Workshop")

<div class="MCWHeader1">
SQL Server hybrid cloud
</div>

<div class="MCWHeader2">
Hands-on lab step-by-step
</div>

<div class="MCWHeader3">
June 2020
</div>

Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2020 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

**Contents**

<!-- TOC -->

- [SQL Server hybrid cloud hands-on lab step-by-step](#sql-server-hybrid-cloud-hands-on-lab-step-by-step)
  - [Abstract and learning objectives](#abstract-and-learning-objectives)
  - [Overview](#overview)
  - [Solution architecture](#solution-architecture)
  - [Requirements](#requirements)
    - [Help references](#help-references)
  - [Exercise 1: Configure Azure Key Vault integration with the SQL VM resource provider.](#exercise-1-configure-azure-key-vault-integration-with-the-sql-vm-resource-provider)
    - [Task 1: Install Azure PowerShell cmdlets](#task-1-install-azure-powershell-cmdlets)
    - [Task 2: Register the SQL VM resource provider](#task-2-register-the-sql-vm-resource-provider)
    - [Task 3: Register an application in Azure Active Directory](#task-3-register-an-application-in-azure-active-directory)
    - [Task 4: Create the Azure Key Vault](#task-4-create-the-azure-key-vault)
    - [Task 5: Configure the SQL Server resource provider integrated key vault](#task-5-configure-the-sql-server-resource-provider-integrated-key-vault)
    - [Task 6: Enable TDE](#task-6-enable-tde)
  - [Exercise 2: SQL Backup solution](#exercise-2-sql-backup-solution)
    - [Task 1: Create an Azure Storage account to hold backups](#task-1-create-an-azure-storage-account-to-hold-backups)
    - [Task 2: Configure automated backup](#task-2-configure-automated-backup)
  - [Exercise 3: Implement a Data Archive Strategy with SQL Server Stretch Database](#exercise-3-implement-a-data-archive-strategy-with-sql-server-stretch-database)
    - [Task 1: Create a logical SQL Server to host Stretch DB](#task-1-create-a-logical-sql-server-to-host-stretch-db)
    - [Task 2: Identify tables that may benefit from Stretch DB](#task-2-identify-tables-that-may-benefit-from-stretch-db)
    - [Task 3: Implement Stretch DB based on date key](#task-3-implement-stretch-db-based-on-date-key)
  - [Exercise 4: Create an archive solution using table partitioning](#exercise-4-create-an-archive-solution-using-table-partitioning)
    - [Task 1: Create the archive database](#task-1-create-the-archive-database)
    - [Task 2: Create the new partitioned table](#task-2-create-the-new-partitioned-table)
    - [Task 3: Move data from the partitioned table to the archive database](#task-3-move-data-from-the-partitioned-table-to-the-archive-database)
  - [Exercise 5: Build SQL Availability Group for Database Disaster Recovery](#exercise-5-build-sql-availability-group-for-database-disaster-recovery)
    - [Task 1: Create the cluster](#task-1-create-the-cluster)
    - [Task 2: Create the SQL Server Availability Group](#task-2-create-the-sql-server-availability-group)
    - [Task 3: Create the Internal Load Balancer](#task-3-create-the-internal-load-balancer)
    - [Task 4: Validate the Availability Group](#task-4-validate-the-availability-group)
    - [Task 5: Update the Web Application to Connect to the Listener](#task-5-update-the-web-application-to-connect-to-the-listener)
  - [Exercise 6: Configure Azure Site Recovery for Web Tier DR](#exercise-6-configure-azure-site-recovery-for-web-tier-dr)
    - [Task 1: Create a Recovery Services Vault](#task-1-create-a-recovery-services-vault)
    - [Task 2: Configure Azure Site Recovery](#task-2-configure-azure-site-recovery)
    - [Task 3: Creating the Recovery Plan](#task-3-creating-the-recovery-plan)
  - [Exercise 7: Failing Over to the Disaster Recovery Site](#exercise-7-failing-over-to-the-disaster-recovery-site)
    - [Task 1: Failover the Data Tier](#task-1-failover-the-data-tier)
    - [Task 2: Failover the Web Tier](#task-2-failover-the-web-tier)
    - [Task 3: Validate Failover of the CloudShop application](#task-3-validate-failover-of-the-cloudshop-application)
  - [After the hands-on lab](#after-the-hands-on-lab)
    - [Task 1: Delete the resource groups created](#task-1-delete-the-resource-groups-created)

<!-- /TOC -->

# SQL Server hybrid cloud hands-on lab step-by-step 

## Abstract and learning objectives 

In this hands-on lab, you will build a disaster recovery site for an on-premises environment. You will enhance the existing database solution to support a hybrid cloud-based disaster recovery solution, implement an archiving strategy and a backup/restore strategy designed to protect data.

At the end of the lab, you will be better able to design and use availability sets, Managed Disks, SQL Server Always on Availability Groups, Stretch DB and SQL Server Managed Backup.

## Overview

Contoso has asked you to deploy their infrastructure in a resilient manner to ensure their infrastructure will be available for their users and gain an SLA from Microsoft.

## Solution architecture

Cloud based disaster recovery site.

![Enterprise disaster recovery architecture.](images/hands-on-lab/disaster-recovery-diagram.png "Enterprise disaster recovery architecture")

## Requirements

1. Microsoft Azure Subscription

2. Virtual Machine Built during this hands-on lab or local machine with the following:

    - Latest Azure PowerShell cmdlets:
        - <https://azure.microsoft.com/en-us/downloads/>
        - <https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps>
        - Ensure you reboot after installing the SDK or Azure PowerShell may not work correctly.

### Help references

|    |            |
|----------|:-------------:|
| **Description** | **Links** |
| Authoring ARM Templates | <https://azure.microsoft.com/en-us/documentation/articles/resource-group-authoring-templates/> |
| Virtual Machine Scale Set Samples | <https://github.com/gbowerman/azure-myriad> |
| Azure Quick Start Templates | <https://github.com/Azure/azure-quickstart-templates> |
| Network Security Groups | <https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-nsg/> |
| Managed Disks | <https://azure.microsoft.com/en-us/services/managed-disks> |
| Always-On Availability Groups | <https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017> |
| SQL Server Managed Backup to Azure | <https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017> |
| Virtual Network Peering | <https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview> |
| Azure Backup |  <https://azure.microsoft.com/en-us/services/backup/> |

## Exercise 1: Configure Azure Key Vault integration with the SQL VM resource provider.

Duration: 60 minutes

To help protect and manage encryption keys, you need to leverage Azure Key Vault. To minimize the administrative overhead, you will use the SQL Server virtual machine resource provider to allow the SQL Servers to integrate with the Key Vault. You will then enable TDE on the database leveraging keys stored in Azure Key Vault.

### Task 1: Install Azure PowerShell cmdlets

In this task, you will install the Azure PowerShell cmdlets on your SQL Servers.

1. Connect to your **CloudShopSQL** virtual machine by navigating to your **CloudShop1** resource group and then connecting to the **CloudShopSQL**  virtual machine.

2. Login with username **demouser** and password **demo@pass123**.

3. Launch an administrative PowerShell ISE session.  

    ![The Windows Start menu with the PowerShell ISE tile highlighted, the More option highlighted from the menu and the Run as administrator option chosen.](images/2020-06-17-16-33-22.png "PowerShell ISE Run as administrator menu item.")

4. Execute the following code (approve any popups about NuGet or untrusted repositories):

    ```powershell
    Install-Module -Name Az -AllowClobber -Scope AllUsers
    ```

5. Repeat the above steps for **CloudShopSQL2** and **CloudShopSQL3**.

### Task 2: Register the SQL VM resource provider

In this task, you will register the SQL VM resource provider in full mode for all of your existing SQL Server virtual machines.

1. From a machine with Azure PowerShell cmdlets installed, launch the PowerShell ISE and login to Azure using the Az-LoginAccount command.

    ```powershell
    Login-AzAccount
    ```

2. If your account has access to multiple subscriptions, verify that you are in the correct subscription. If not, you can use **Select-AzureSubscription** to change the Azure subscription context of your PowerShell session.

3. Execute the following script to register the SQL VM resource provider in your subscriptions and register your VMs:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

    $vm = Get-AzVM -Name CloudShopSQL -ResourceGroupName CloudShop1
    New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full -LicenseType PAYG -Location $vm.Location 

    $vm = Get-AzVM -Name CloudShopSQL2 -ResourceGroupName CloudShop2
    New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full -LicenseType PAYG -Location $vm.Location

    $vm = Get-AzVM -Name CloudShopSQL3 -ResourceGroupName CloudShop2
    New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full -LicenseType PAYG -Location $vm.Location
    ```

    These commands will add the SQL virtual machine resource for each of your SQL Servers. They will also install the SQL IaaS extension and the SQL Connector for Azure Key Vault. The servers will need to be rebooted as part of the installation process.

    ![Listing of all of the resources in the CloudShop1 resource group with the new SQL virtual machine resource highlighted.](images/2020-06-17-18-32-11.png "The SQL virtual machine resource.")

4. Restart each of the SQL virtual machines to complete the installation.

### Task 3: Register an application in Azure Active Directory

In this task, you will register an application in Azure Active Directory so that you can leverage a Service Principal account to access your Key Vault.

1. From the Azure portal, navigate to **Azure Active Directory**.

2. From the Azure Active Directory blade, under the Manage menu, select **App registrations**.

    ![The Azure AD Manage menu with App registrations highlighted.](images/2020-06-17-16-55-39.png "The Azure AD Manage menu")

3. On the App registrations page, select the **+ New registration** button.

4. On the Register an application page, type **SQLKeyVaultIntegration** for the name, leave **Accounts in this organizational directory only** radio button selected, and type **https://sqlkeyvault** for the Redirect URI then select **Register**.

    ![The register an application window with the name and redirect URI fields filled in.](images/2020-06-17-17-05-39.png "Register an application")

5. On the SQLKeyVaultIntegration page, copy the **Application (client) ID** and save it in a text editor for later use.

    ![The SQLKeyVaultIntegration app registration page with the application ID highlighted.](images/2020-06-17-17-12-34.png "The application ID")

6. Select the **Certificates & secrets** from the menu and then choose the **+ New client secret** button.

    ![The certificates and secrets page with certificates and secrets and the new client secret button highlighted.](images/2020-06-17-17-18-47.png "Certificates and secrets page")

7. On the Add a client secret popup, type **SQL Server/Key Vault integration** for the description, leave the expiration set to **1 year** and select **Add**.

    ![The add a client secret page with the description filled in and 1 year is selected.](images/2020-06-17-17-21-57.png "Add a client secret")

8. The new client secret is now listed under **Client secrets**. Copy the **Value** and save it to Notepad, once you navigate away from this page you will no longer be able to retrieve this secret.

    ![The Certificates and secrets page, the new secret is listed under client secrets and the secret value is highlighted.](images/2020-06-17-17-28-15.png "Client secrets")

9. **Verify that your client secret does not contain hyphens**. The command that is used to parse and store this secret in SQL Server cannot currently parse hyphens. If the client secret that is generated does contain hyphens, you must generate a new client secret.

### Task 4: Create the Azure Key Vault

In this task, you will create the Azure Key Vault resource that will store and protect secrets used by your SQL Server virtual machines.

1. From the Azure portal, navigate to your **CloudShop2** resource group and select the **+ Add** button.

2. Type **Key Vault** into the search box, choose **Key Vault** from the list and then select **Create**.

3. On the Create key vault blade, use the following configurations and then choose **Next: Access policy**.

    - Subscription: *Your subscription*
    - Resource group: **CloudShop2**
    - Key vault name: *A unique name for your key vault*.
    - Region: *The region you used for the CloudShop2 resources*.
    - Pricing tier: **Standard**
    - Soft delete: **Disable**

    ![The basics tab of the create key vault blade with the parameters configured.](images/2020-06-17-17-45-05.png "Create key vault")

4. On the access policy tab, select the **+ Add Access Policy** button.

    ![The access policy tab with the + add access policy button highlighted.](images/2020-06-18-17-02-16.png "Access policy")

5. On the Add access policy screen, select the **Configure from template** dropdown and choose **SQL Server Connector** from the list.

    ![The add access policy screen with the SQL Server Connector option selected in the  configure from template dropdown.](images/2020-06-18-17-05-43.png "Configure from template")

6. Choose **Select principal**, then type **SQLKeyVaultIntegration**, select it from the list and then choose the **Select** button. 

    ![The select a principal blade with SQLKeyVaultIntegration chosen.](images/2020-06-18-17-10-19.png "Select a principal")

7. On the add access policy page, select **Add**, then select **Review + create**.

8. On the review and create page, select **Create** to deploy your key vault.

9.  After your Key Vault deployment completes, navigate to the resource, copy the **DNS Name** from the overview page and save it to Notepad for later use.

    ![The Azure Key Vault overview page with the DNS name highlighted.](images/2020-06-17-18-46-02.png "Key vault overview.")

10. Create a key by selecting **Keys** from the settings menu, then selecting the **+Generate/Import** button. Use the following options to create the key and select **Create**.

    ![The create a key window is shown with options set to generate, name set to CloudShopSQL, Key Type set to RSA, Key Size set to 2048 and enabled set to Yes.](images/2020-06-18-18-49-48.png "Create a key")

### Task 5: Configure the SQL Server resource provider integrated key vault

1. Navigate to the CloudShop resource group and open the **CloudShopSQL SQL virtual machine** resource.

    ![The CloudShopSQL Virtual machine and the CloudShopSQL SQL virtual machine resource are shown with the CloudShop SQL virtual machine resource highlighted.](images/2020-06-18-18-02-35.png "SQL virtual machine resource.")

2. Select **Security** from the settings menu.

    ![The CloudShopSQL virtual machine blade is shown with the Security menu item highlighted.](images/2020-06-18-18-05-23.png "The CloudShopSQL virtual machine menu")

3. On the Security page, use the following configurations and select **Apply**.

    >**Note**: The create credential command used in the next task cannot parse hyphens. If your client secret has hyphens you must go back to your App registration in Azure AD and generate a new client secret.

    - Azure Key Vault integration: **Enabled**
    - Principal name: *The application id for the application registration you created earlier*.
    - Principal secret: *The client secret you created in Azure AD*.
    - Credential name: **CloudShopSQL**

4. Wait for the VM to be updated before proceeding.

### Task 6: Enable TDE

In this task, you will enable TDE leveraging your integrated key vault to store the keys.

>**Note**: The create credential command used in the steps below cannot parse hyphens. If your client secret has hyphens you must go back to your App registration in Azure AD and generate a new client secret.

1. Login to your **CloudShopSQL** virtual machine with the **CONTOSO\demouser** account and launch **Microsoft SQL Server Management Studio**.

2. From a new query window, create the credential by executing the following script replacing the **IDENTITY** value with the name of your key vault (not the URL), and replacing the **SECRET** value with the Application ID without hyphens and the client secret to be passed together without a space between them. 

    ```sql
    USE master;  
    CREATE CREDENTIAL TDEcred   
        WITH IDENTITY = '<your key vault name>', 
        SECRET = '<app id and client secret that you copied in a previous task>'   
    FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;
    ```

    Your completed script should look similar to this:
    
    ![T-SQL query window with the script above filled in with example values for identity and secret.](images/2020-06-18-18-36-37.png "Create the credential.")

3. Associate the credential with your administrative login.

    ```
    ALTER LOGIN [CONTOSO\demouser]  
    ADD CREDENTIAL TDEcred;
    ```

4. Create a key in your Azure Key Vault and open the key by executing the following T-SQL code.

    ```sql
    CREATE ASYMMETRIC KEY TDEKey   
    FROM PROVIDER [AzureKeyVault_EKM]  
    WITH PROVIDER_KEY_NAME = 'CloudShopSQL',  
    CREATION_DISPOSITION = OPEN_EXISTING;
    ```

5. Create a SQL Server login and add the credential from above to it. This credential will be used by the database engine when it loads a database encrypted by TDE. This Transact-SQL example uses the same key that was imported earlier.

    ```sql
    CREATE LOGIN EKM_Login   
    FROM ASYMMETRIC KEY TDEKey;  
    GO   
  
    ALTER LOGIN EKM_Login   
    ADD CREDENTIAL CloudShopSQL;  
    GO
    ```

6. Create the database encryption key (DEK) by executing the following query. The DEK will encrypt your data and log files in the database instance, and in turn be encrypted by the Azure Key Vault asymmetric key.

    ```sql
    USE AdventureWorks;  
    GO  

    CREATE DATABASE ENCRYPTION KEY   
    WITH ALGORITHM = AES_128   
    ENCRYPTION BY SERVER ASYMMETRIC KEY TDEKey;  
    GO
    ```

7. Turn on TDE by executing the following query.

    ```sql 
    ALTER DATABASE AdventureWorks   
    SET ENCRYPTION ON;  
    GO
    ```

8. Using Management Studio, verify that TDE has been turned on by connecting to your database with Object Explorer. Right-click your database, point to **Tasks**, and then choose **Manage Database Encryption**.

    ![The right-click menu is shown with tasks sub-menu open and the Manage Database Encryption task highlighted.](images/2020-06-18-19-43-30.png "Database Tasks menu")

9.  In the Manage Database Encryption dialog box, confirm that TDE is on, and that your asymmetric key is encrypting the DEK.

    ![The Manage Database Encryption window is open with the asymmetric key and the set database encryption fields highlighted.](images/2020-06-18-19-44-38.png "Manage Database Encryption")

## Exercise 2: SQL Backup solution

Duration: 30 minutes

Backups must be maintained offsite from the on-premises environment. The backups must be online and accessible by the DBA team. To accomplish this, you will leverage the automated backup feature provided by the SQL Server resource provider. 

### Task 1: Create an Azure Storage account to hold backups

In this task, you will create an Azure storage account that will be used as a target for the automated backups.

1. From the Azure portal, select **+ Create a resource** and choose **Storage account** from the Azure Marketplace.

2. Set the resource group to **CloudShop1**, choose a unique storage account name, set the location to the same location you deployed CloudShop1 into and verify that replication is set to **Read-access geo-redundant storage (RA-GRS)**. Leave all other settings at the default and choose **Review + create** then select **Create** on the validation screen.

    ![The create storage account window with the resource group set to CloudShop1, a unique storage account name, the location set to the same region that CloudShop1 was deployed to and all other settings left at the default.](images/2020-06-18-20-57-17.png "Create storage account.")

### Task 2: Configure automated backup

In this task, you will configure automated backup using the SQL Server resource provider.

1. Connect to your CloudShopSQL virtual machine by navigating to your **CloudShop1** resource group and then connecting to the **CloudShopSQL**  virtual machine.

2. Login with username **demouser** and password **demo@pass123**.

3. Launch **SQL Server Management Studio** and connect to **CloudShopSQL**.

4.  Expand the databases folder, right-click the **AdventureWorks** database and select **Properties**.

5.  On the Database Properties window, select **Options** then set the recovery model to **Full** and select the **OK** button. The full recovery model is required for databases that participate in automated backup and SQL Server Always On Availability Groups.

    ![Database properties with the recovery model option set to full.](images/hands-on-lab/2019-03-24-22-31-59.png "Set the database recovery model to full")

6. From the Azure portal, navigate to your **CloudShop1** resource group and open the **CloudShopSQL SQL virtual machine** resource.

    ![The CloudShopSQL Virtual machine and the CloudShopSQL SQL virtual machine resource are shown with the CloudShop SQL virtual machine resource highlighted.](images/2020-06-18-18-02-35.png "SQL virtual machine resource.")

7. From the SQL virtual machine blade, select **Backups** then use the following configurations and select **Apply**.

    - Automated backup: **Enable**
    - Retention period (days): **1**
    - Storage account: ***Select the storage account you created in the previous task***
    - Leave the other settings at the default settings. Note that you have options for encryption, backup of system databases and choosing either manual or automated backups. 

    ![The SQL virtual machine backups configuration page with settings configured per the instructions.](images/2020-06-18-20-47-52.png "SQL virtual machine backup configuration.")

## Exercise 3: Implement a Data Archive Strategy with SQL Server Stretch Database

Duration: 30 minutes

There are numerous ways to implement an archive strategy in SQL Server. The easiest way to do this is through the SQL Server Stretch Database feature. A more common approach is to leverage table partitioning to separate historical data from current data. If you wish to implement table partitioning to archive data skip to **Exercise 4**.

In this exercise, you will implement SQL Server Stretch Database to stretch data from a table into Azure.

### Task 1: Create a logical SQL Server to host Stretch DB

1. From your **CloudShopSQL** virtual machine, launch a browser, navigate to the Azure portal and login with your Azure credentials.

2. Select **+Create a resource** at the top of the left side menu.

3. Type **SQL Server logical server** into the search box, hit enter and choose **SQL server (logical server)** from the search results.

    ![Azure Marketplace search for SQL Server logical server.](images/hands-on-lab/2019-03-20-11-38-42.png "Search for SQL Server logical server")

4. Select **Create** on the SQL Server (logical server) information blade.

5. On the Create SQL Database Server Basics tab, use the following configurations and select **Next:Networking**.

    - Subscription: ***Your subscription***.
    - Resource group: **CloudShop2**
    - Server name: ***Choose a unique server name***. *Make note of the server name you chose. It will be used in a later task.*
    - Location: ***Use the same location you used for CloudShop2***.
    - Server admin login: **demouser**
    - Password: **demo@pass123**

        ![The SQL Server logical server creation blade with the configurations listed previously set to the correct values.](images/2019-09-24-16-20-26.png "Create a SQL Server logical server")

6. On the Networking tab, enable the firewall rule to allow Azure services and resources to access this server then select **Review + Create**.

    ![The create SQL Database Server blade with the networking tab selected and the allow azure services and resources to access this server option set to yes.](images/2019-09-24-16-26-49.png "Create a SQL Server logical server")

7. Select **Create** on the Review + create tab.

8. After the SQL Server logical server has deployed, navigate to it and select **Firewalls and virtual networks** from the security menu on the left.

    ![Security menu with Firewalls and virtual networks highlighted.](images/hands-on-lab/2019-03-20-11-56-21.png "Security menu")

9. Add a new firewall rule by selecting **+Add client IP** and then selecting **Save**.

    ![The firewall configuration dialogue with the save and add client IP buttons highlighted.](images/hands-on-lab/2019-03-20-12-01-55.png "Firewall configuration")

### Task 2: Identify tables that may benefit from Stretch DB

1. From your **CloudShopSQL** virtual machine, launch SQL Server Management Studio if it is not already open.

2. In the SQL Server Management Studio Object Explorer, connect to your local SQL Server instance and expand the Databases folder.

3. Right-click the AdventureWorks database, select Tasks, select Stretch, then choose Enable.

    ![SQL Server Management Studio Object Explorer with right-click menu open, tasks is highlighted then stretch is highlighted and Enable is selected.](images/hands-on-lab/2019-03-24-19-38-01.png "Enable Stretch Database")

4. The Enable Database for Stretch wizard should open automatically. Select **Next** on the Introduction screen.

5. On the Select tables window all of your tables will be listed. Notice that some tables have warnings, and some may be greyed out. Select the warning icon next to one of the tables. This indicates that the table does not meet the StretchDB eligibility requirements.

    ![Stretch Database Wizard showing error for a table.](images/hands-on-lab/2019-03-24-19-40-15.png "Stretch Database wizard")

6. Scroll to the right until you see the Migrate column. Selecting Entire Table on an eligible table allows you to define which rows will be migrated to Azure. In this lab, we are going to configure Stretch Database through TSQL. Select Cancel to break out of the wizard.

### Task 3: Implement Stretch DB based on date key  

1. Launch a new Query tab and execute the following code to prepare the server and the database for Stretch Database:

    ```sql
    --Enable the server for Stretch Database
    EXEC sp_configure 'remote data archive' , '1';
    GO
    RECONFIGURE;
    GO

    --Create a database master key to encrypt the data stored in Azure
    USE [AdventureWorks]
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD='demo@pass123'
    GO

    --Create the credential used to access an Azure SQL Database
    CREATE DATABASE SCOPED CREDENTIAL StretchDB 
    WITH IDENTITY = 'demouser' , SECRET = 'demo@pass123'
    GO
    ```

2. Execute the following code replacing the server name with the name of the SQL Server you created in Exercise 1. This code enables Stretch Database on your database. It may take a few minutes to complete.

    ```sql
    --Enable the local database for stretch
    --You must change your server name to match your environment
    ALTER DATABASE [AdventureWorks]
    SET REMOTE_DATA_ARCHIVE = ON (SERVER = N'<your server name>.database.windows.net', CREDENTIAL = StretchDB )
    ```

3. Execute the following code to create the stretch predicate. The stretch predicate allows us to define the conditions under which a row will be stretched. In this case the function leverages a date key column to stretch any rows prior to January 1, 2002. 

    ```sql
    --Create the stretch predicate
    CREATE FUNCTION dbo.fn_stretch_datekey_predicate(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN SELECT 1 AS is_eligible
        WHERE @column1 < '20020101'
    GO
    ```

4. Execute the following to begin the migration of eligible data to your Azure SQL Database:

    ```sql
    --Enable stretch on the table using the stretch predicate with the OrderDateKey
    ALTER TABLE Sales.ResellerSales
    SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE =
        dbo.fn_stretch_datekey_predicate([OrderDateKey]),
    MIGRATION_STATE = OUTBOUND
    ) )
    ```

5. Refresh the AdventureWorks database in Object Explorer by right-clicking on it and choosing Refresh.

6. Right-click the AdventureWorks database, choose Tasks, Stretch, then select Monitor.

7. View the stretch database report. Note that over time the Eligible Rows, Local Rows, and Rows In Azure numbers will change as data is migrated to your Stretch Database in Azure.

    ![The stretch database report is shown with ResellerSales highlighted.](images/hands-on-lab/2019-03-20-12-21-30.png "Stretch Configured Tables")

8. Select the dropdown under Migration State. Notice that you have the option to pause the outbound migration.

9. Launch a new Query tab and execute the following code to programmatically monitor space used in Azure and locally.

    ```sql
    sp_spaceused 'Sales.ResellerSales', @mode = 'REMOTE_ONLY'
    GO
    sp_spaceused 'Sales.ResellerSales', @mode = 'LOCAL_ONLY'
    GO
    ```

10. You may also change the scope of a query.

    ```sql
    SELECT COUNT(*) FROM [Sales].[ResellerSales] WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = REMOTE_ONLY)
    ```

## Summary

In this exercise, you implemented an archive solution with Stretch Database. First, you reviewed the table compatibility by using the Enable Database for Stretch wizard. You then configured your database and table via T-SQL to archive data satisfied by a stretch predicate. Finally, you reviewed the progress, status and space used locally and in Azure via the built-in management tools

## Exercise 4: Create an archive solution using table partitioning

Duration: 60 minutes

In this exercise, you will create an archive solution using table partitioning. The goal of partitioning the table is to minimize the impact of the archival process on the production database and to minimize the size of the database overall to improve performance of the maintenance jobs. We will use monthly partitions to accomplish these goals.

> **Note**: This is an alternate archive solution. It is not necessary to complete this exercise if you have already completed Exercise 2.

### Task 1: Create the archive database

In this task, you will create the archive database to store historical data from large tables in the production database.

1.  From your **CloudShopSQL** virtual machine, launch SQL Server Management Studio and login to the local instance of SQL Server.

2.  Open a new query window, copy and paste the following code into the query window. This code creates the archive database where you will store historical data from the production database.

    ```sql
    -- Create archive database
    CREATE DATABASE AdventureWorksArchive
    ```

3.  Open a new query window, copy and paste the following code into the query window. This creates a table you will store historical data from the **AdventureWorks.Sales.ResellerSales** table. You will copy data into this table in a later step.

    ```sql
    USE AdventureWorksArchive
    GO
    CREATE TABLE ResellerSalesArchive (
  	    [ProductKey] [int] NOT NULL,
	    [OrderDateKey] [int] NOT NULL,
	    [DueDateKey] [int] NOT NULL,
	    [ShipDateKey] [int] NOT NULL,
	    [ResellerKey] [int] NOT NULL,
	    [EmployeeKey] [int] NOT NULL,
	    [PromotionKey] [int] NOT NULL,
	    [CurrencyKey] [int] NOT NULL,
	    [SalesTerritoryKey] [int] NOT NULL,
	    [SalesOrderNumber] [nvarchar](20) NOT NULL,
	    [SalesOrderLineNumber] [tinyint] NOT NULL,
	    [RevisionNumber] [tinyint] NULL,
    	[OrderQuantity] [smallint] NULL,
	    [UnitPrice] [money] NULL,
	    [ExtendedAmount] [money] NULL,
	    [UnitPriceDiscountPct] [float] NULL,
	    [DiscountAmount] [float] NULL,
	    [ProductStandardCost] [money] NULL,
	    [TotalProductCost] [money] NULL,
	    [SalesAmount] [money] NULL,
	    [TaxAmt] [money] NULL,
	    [Freight] [money] NULL,
	    [CarrierTrackingNumber] [nvarchar](25) NULL,
	    [CustomerPONumber] [nvarchar](25) NULL
    );
    ```

### Task 2: Create the new partitioned table

In this task, we will create a new partitioned table which will be used to hold the current data. Applications, stored procedures may require updates to be able to use the table.

1.  From SQL Server Management Studio on your **CloudShopSQL** virtual machine, open a new query window.

2.  Create a partition function for the new partitioned table that you will create. For now, we will create the table with enough partitions to accommodate all of the data that is currently in the Sales.ResellersSales table. Paste the following code into your query window and execute it:

    ```sql
    USE AdventureWorks
    GO
    CREATE PARTITION FUNCTION pfResellerSales (int)
    AS RANGE RIGHT FOR VALUES 
    ('20010801','20010901','20011001','20011101','20011201'
    ,'20020101','20020201','20020301','20020401','20020501','20020601','20020701','20020801','20020901','20021001','20021101','20021201'
    ,'20030101','20030201','20030301','20030401','20030501','20030601','20030701','20030801','20030901','20031001','20031101','20031201'
    ,'20040101','20040201','20040301','20040401','20040501','20040601');
    ```

3.  Create the partition scheme for your table. Copy, paste and execute the following code into a new query window:

    ```sql
    CREATE PARTITION SCHEME psResellerSales
    AS PARTITION pfResellerSales
    ALL TO ([Primary]);
    ```

4.  Create the new partitioned table using the partition scheme defined in the previous step. Copy, paste and execute the following code into a new query window:

    ```sql
    -- Create the new partitioned table on the Partition Scheme
    CREATE TABLE Sales.ResellerSalesCurrent (
  	    [ProductKey] [int] NOT NULL,
	    [OrderDateKey] [int] NOT NULL,
	    [DueDateKey] [int] NOT NULL,
	    [ShipDateKey] [int] NOT NULL,
	    [ResellerKey] [int] NOT NULL,
	    [EmployeeKey] [int] NOT NULL,
	    [PromotionKey] [int] NOT NULL,
	    [CurrencyKey] [int] NOT NULL,
	    [SalesTerritoryKey] [int] NOT NULL,
	    [SalesOrderNumber] [nvarchar](20) NOT NULL,
	    [SalesOrderLineNumber] [tinyint] NOT NULL,
	    [RevisionNumber] [tinyint] NULL,
	    [OrderQuantity] [smallint] NULL,
	    [UnitPrice] [money] NULL,
	    [ExtendedAmount] [money] NULL,
	    [UnitPriceDiscountPct] [float] NULL,
	    [DiscountAmount] [float] NULL,
	    [ProductStandardCost] [money] NULL,
	    [TotalProductCost] [money] NULL,
	    [SalesAmount] [money] NULL,
	    [TaxAmt] [money] NULL,
	    [Freight] [money] NULL,
	    [CarrierTrackingNumber] [nvarchar](25) NULL,
	    [CustomerPONumber] [nvarchar](25) NULL
    ) ON psResellerSales(OrderDateKey);
    ```

5.  Load the data from **Sales.ResellerSales** into your new table by executing the following statement in a new query window in SQL Server Management Studio:

    ```sql
    -- Load the new partitioned table
    INSERT INTO Sales.ResellerSalesCurrent
    SELECT * FROM Sales.ResellerSales
    ```
6.  Create a partition aligned clustered index on the new partitioned table.

    ```sql
    -- Create partition aligned clustered index
    CREATE CLUSTERED INDEX IX_ResellerSalesCurrent_OrderDateKey
    ON Sales.ResellerSalesCurrent (OrderDateKey)
    WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF,
         ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)
    ON psResellerSales(OrderDateKey)
    ```

### Task 3: Move data from the partitioned table to the archive database

In this task, you will move data from the new partitioned table to the table in your archive database. This will be done by switching out the oldest partition into a work table and copying the data from the work table to the archive table. We could just write a query to accomplish this without partitioning, however, this method minimizes the impact on the production table as the partition switch is very fast.

1.  From SQL Server Management Studio on your **CloudShopSQL** virtual machine, open a new query window.

2.  Copy and paste the following code into the query window and execute it. This code creates the work table that we will switch our oldest partition into. The table must be identical to the original table including indexing.

    ```sql
    USE AdventureWorks
    GO
    -- Create the work table
    -- This is the table we will switch out to
    CREATE TABLE Sales.ResellerSalesWork (
  	    [ProductKey] [int] NOT NULL,
	    [OrderDateKey] [int] NOT NULL,
	    [DueDateKey] [int] NOT NULL,
	    [ShipDateKey] [int] NOT NULL,
	    [ResellerKey] [int] NOT NULL,
	    [EmployeeKey] [int] NOT NULL,
	    [PromotionKey] [int] NOT NULL,
	    [CurrencyKey] [int] NOT NULL,
	    [SalesTerritoryKey] [int] NOT NULL,
	    [SalesOrderNumber] [nvarchar](20) NOT NULL,
	    [SalesOrderLineNumber] [tinyint] NOT NULL,
	    [RevisionNumber] [tinyint] NULL,
	    [OrderQuantity] [smallint] NULL,
	    [UnitPrice] [money] NULL,
	    [ExtendedAmount] [money] NULL,
	    [UnitPriceDiscountPct] [float] NULL,
	    [DiscountAmount] [float] NULL,
	    [ProductStandardCost] [money] NULL,
	    [TotalProductCost] [money] NULL,
	    [SalesAmount] [money] NULL,
	    [TaxAmt] [money] NULL,
	    [Freight] [money] NULL,
	    [CarrierTrackingNumber] [nvarchar](25) NULL,
	    [CustomerPONumber] [nvarchar](25) NULL
    );

    --Create the clustered index on the work table
    CREATE CLUSTERED INDEX IX_ResellerSalesWork_OrderDateKey
    ON Sales.ResellerSalesWork (OrderDateKey)
    WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF,
         ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)
    ```

3.  Before we move any data let's take a look at how our data is distributed across our existing partitions. Execute the following code in SQL Server Management Studio:

    ```sql
    -- View partition info
    SELECT o.name objectname,i.name indexname, partition_id, partition_number, [rows]
    FROM sys.partitions p 
    INNER JOIN sys.objects o ON o.object_id=p.object_id
    INNER JOIN sys.indexes i ON i.object_id=p.object_id and p.index_id=i.index_id
    WHERE o.name LIKE '%ResellerSales%'
    ```

4.  Notice that partition number 1 of the ResellerSalesCurrent table has 352 rows and that partition 1 of the ResellerSalesWork table has 0 rows. We will switch these two partitions.

    ![Output from the previous query showing there are 352 rows in partition 1.](images/2019-09-25-11-12-26.png "Query output from partion system tables")

    ![Continuation of the previous output showing that the work table partion contains 0 rows.](images/2019-09-25-11-13-10.png "Query output from partion system tables")

5.  To switch the oldest partition from the current table to the work table, execute the following code.

    ```sql
    -- Execute a switch
    ALTER TABLE Sales.ResellerSalesCurrent SWITCH PARTITION 1 TO Sales.ResellerSalesWork;
    ```

6.  Now run the following query again and notice that the work table now contains 352 rows while partition 1 of the ResellerSalesCurrent table now contains 0 rows. This indicates that our switch was successful. 

    ```sql
    -- View partition info
    SELECT o.name objectname,i.name indexname, partition_id, partition_number, [rows]
    FROM sys.partitions p 
    INNER JOIN sys.objects o ON o.object_id=p.object_id
    INNER JOIN sys.indexes i ON i.object_id=p.object_id and p.index_id=i.index_id
    WHERE o.name LIKE '%ResellerSales%'    
    ```

    ![Output showing that the work table partion now contains 352 rows.](images/2019-09-25-12-40-56.png "Query output from partion system tables")
    
    ![Output from the previous query showing there are 0 rows in partition 1.](images/2019-09-25-12-42-01.png "Query output from partion system tables")

7.  Now we need to create a new partition for the upcoming month. To do this we execute a split, specifying the new range for the next partition.

    ```sql
    -- Create new partition
    ALTER PARTITION FUNCTION pfResellerSales() SPLIT RANGE ('20040701');
    ```

8.  We need to clean up the now empty partition. To do this we execute a merge and specify the new lower boundary on our production table.

    ```sql
    -- Merge empty partition
    ALTER PARTITION FUNCTION pfResellerSales() MERGE RANGE ('20010801');
    ```

9.  Now let's copy the data to the archive database and create the necessary indexes by executing the following code. Since we are copying data from the worktable, there should be no adverse impact on the production table.

    ```sql
    INSERT INTO AdventureWorksArchive..ResellerSalesArchive
    SELECT * FROM AdventureWorks.Sales.ResellerSalesWork

    USE AdventureWorksArchive
    GO
    CREATE CLUSTERED INDEX IX_ResellerSalesWork_OrderDateKey
    ON ResellerSalesArchive (OrderDateKey)
    WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF,
         ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)
    ```

10. After the copy completes, you should cleanup the work table so that it can be used to move the other partitions in the future.

    ```sql
    USE AdventureWorks
    GO
    TRUNCATE TABLE Sales.ResellerSalesWork
    ```

11. To support our legacy reports, we will need to create a view that unions both the current table and the archive table using the same table name as the original table. To do this we must first drop the original table (or rename it). Execute the following code to drop the existing Sales.ResellerSales table and create a view to take its place.

    ```sql
    USE AdventureWorks
    GO

    DROP TABLE Sales.ResellerSales
    GO

    CREATE VIEW Sales.ResellerSales
    AS SELECT * FROM AdventureWorksArchive..ResellerSalesArchive 
       UNION ALL 
       SELECT * FROM Sales.ResellerSalesCurrent
    ```

12. Execute the following code to verify that you are returning 60855 rows.

    ```sql
    SELECT COUNT(*) FROM Sales.ResellerSales
    ```

## Summary

In this exercise, you implemented an archive solution with table partitioning. You created a partitioned table to replace the existing Sales.ResellerSales table. You allowed the legacy reports to maintain access to the current and historical data by creating a view across both the historical archive table and the new current data stored in the partitioned table. 

## Exercise 5: Build SQL Availability Group for Database Disaster Recovery

Duration: 60 minutes

In this exercise, you will build a SQL Always-On Cluster for resiliency of the data tier.

### Task 1: Create the cluster

In this task, you will create the underlying Windows Failover Cluster which is the base of your SQL Server Availability Group. You will begin by logging in with an account with administrative privileges on all of the prospective nodes of the cluster and running a Failover Cluster Validation test. This test verifies that all the nodes of your cluster are properly configured to support clustering and will flag any best practices that you may not be adhering to in your configuration. Finally, you will create the cluster across the three nodes of you cluster.

1. Launch a browser and navigate to <https://portal.azure.com>. Login with your Azure credentials if you haven't already.

    > **Note**: You may need to launch an \"in-private\" session in your browser if you have multiple Microsoft Accounts.

2. Navigate to your **CloudShop1** resource group and open your **CloudShopSQL** virtual machine and connect to it via Remote Desktop.

3. Login with the **CONTOSO\demouser** domain account with password **demo@pass123**. 
    > **Note**: This is NOT the same account that you have been using, this is a domain account. You must use a domain account that is an local administrator on all nodes of the cluster to properly configure Windows Failover Clustering.

4. Launch Server Manager if it does not start automatically.

5. Select **Local Server** from the menu on the left and scroll down to **Roles and Features**, select the **Tasks** dropdown and select **Add Roles and Features** from the list.

    ![Server Administrator Application with local server selected, and add roles and features selected in the tasks dropdown of the Roles and Features section.](images/hands-on-lab/2019-03-20-17-33-21.png "Adding Roles and Features")

6. On the **Before you begin** page, select **Next**.

7. On the **Select installation type** page, select **Role-based or feature-based installation** and select **Next**.

8. On the **Select destination server** page, select the current server and select **Next**.

9.  On the **Select server roles** page, accept the defaults and select **Next**.

10. On the **Select features** page, select **Failover Clustering**, then verify that **Include management tools** is checked and select **Add Features** on the popup and then select **Next**.

    ![The add roles and features wizard with Failover Clustering selected on the select features page.](images/hands-on-lab/2019-03-24-20-03-47.png "Add Failover Clustering")

11. On the **Confirm installation selections** page, select **Install**.

12. Repeat the above steps to install Failover Clustering on your remaining SQL Servers (CloudShopSQL2 and CloudShopSQL3).

    >**Note**: CloudShopSQL2 and CloudShopSQL3 were deployed to the disaster recovery site which is hosted in the **CloudShopSQL2** resource group.

13. From CloudShopSQL, open **Administrative Tools** and then launch **Failover Cluster Manager**.

14. From the Actions pane on the right, choose **Validate Configuration**.

    ![The Failover Cluster Manager with Validate Configuration highlighted.](images/hands-on-lab/2019-03-24-20-17-48.png "Failover Cluster Manager")

15. Select **Next** on the Before You Begin window.

16. On the Select Servers or a Cluster window, enter **CloudShopSQL** and then select the **Add** button. Repeat for **CloudShopSQL2** and **CloudShopSQL3** then select **Next**.

    ![The select servers or a cluster window with CloudShopSQL, CloudShopSQL2 and CloudShopSQL3 added to the selected servers.](images/hands-on-lab/2019-03-24-20-23-55.png "Validate a Configuration Wizard")

17. On the Testing Options window, select the **Run only tests I select** radio button and select **Next**.

    ![The testing options window with run only tests I select selected.](images/hands-on-lab/2019-03-24-20-31-41.png "Validate a Configuration Wizard")

18. On the Test selection window, uncheck storage. The storage tests have no relevance for our case and will only throw additional warnings.

    ![The test selection window with the storage tests unchecked.](images/hands-on-lab/2019-03-24-20-34-43.png "Validate a Configuration Wizard")

19. On the Confirmation window, select **Next** to begin the validation tests.

20. Review the output of the tests by selecting the **View Report** button. This validation test aids you in determining if your server and network configuration is correctly configured for Windows Failover Clustering. You should review any warnings or errors. In our case, the environment was preconfigured to support clustering so there should be no errors. There may be warnings regarding best practices such as multiple network cards etc. In a production environment these warnings must be taken seriously as they may adversely affect the performance and reliability of the cluster. In our lab they will have a negligible impact.

21. Select **Finish** on the Validate a Configuration Wizard.

22. On CloudShopSQL, open the PowerShell ISE and execute the following code:

    ```
    New-Cluster -Name CLUST01 -Node CloudShopSQL, CloudShopSQL2, CloudShopSQL3 -StaticAddress 10.0.1.7, 172.16.1.7 -NoStorage
    ```
    > **Note**: This command requires that you be logged in with an account that is an administrator on all nodes specified. For this lab you should use **CONTOSO\demouser**.

    This will create a three-node cluster with two static IP addresses. We need two static IP addresses because the cluster may be running from our on-premises environment (CloudShop1) or our cloud disaster recovery environment (CloudShop2).  
    
    It is also possible to use a wizard for this task, but the resulting cluster will require additional configuration to set the static IP addresses to be viable in Azure. This is due to the manner in which Azure distributes IP addresses causing the cluster to receive the same IP address as the node it is executing on resulting in a duplicate IP address and failure of the cluster service.

23. Open the Failover Cluster Manager on CloudShopSQL from the start menu under Windows Administrative Tools. In the right navigation right-click **Failover Cluster Manager** and select **Connect to Cluster**. Select OK in the pop-up. Expand the **CLUST01** cluster, select **Nodes**, validate that all nodes are online, and that Assigned Vote and Current Vote are 1 for all nodes of the cluster.

    ![Failover Cluster Manager with CLUST01 selected, Nodes selected, and all three nodes of the cluster showing online with 1 assigned and 1 current vote.](images/hands-on-lab/2019-03-24-21-22-18.png "Nodes view of Failover Cluster Manager")

### Task 2: Create the SQL Server Availability Group

1. On CloudShopSQL, launch **SQL Server 2019 Configuration Manager**.

2. Select **SQL Server Services**, then right-click **SQL Server (MSSQLSERVER)** and select **Properties**.

    ![SQL Server Configuration Manager with SQL Server Services selected and SQL Server service highlighted.](images/hands-on-lab/2019-03-24-21-31-48.png "SQL Server Configuration Manager")

3. Select the **Log On** tab. To setup a SQL Server Availability     Group, the SQL Server service account needs to have access to all of the SQL Servers that will participate in the Availability Group. Change the service account to the **CONTOSO\\demouser** domain account using **demo@pass123** for the password.

    ![SQL Server Properties is shown with the account set to the CONTOSO\demouser domain account.](images/hands-on-lab/2019-03-24-21-35-08.png "SQL Server Login Properties")

4. Select the **AlwaysOn High Availability** tab and check the box for **Enable AlwaysOn Availability Groups**, then select **OK**, then select **Yes** to confirm the account change.

    ![SQL Server Properties is shown with Enable AlwaysOn High Availability Groups selected.](images/hands-on-lab/2019-03-24-21-37-08.png "SQL Server AlwaysOn High Availability Properties")

5. Restart the SQL Server service by right-clicking the service and choosing **Restart**.

    ![SQL Server Configuration Manager with SQL Server Services selected and the SQL Server service being restarted.](images/hands-on-lab/2019-03-24-21-40-59.png "Restart the SQL Server service")

6. Repeat this configuration for **CloudShopSQL2** and **CloudShopSQL3**.

7. The database you will be replicating has its data and log files in the **C:\\Data** folder and the **C:\\Logs** folder on CloudShopSQL. We need to verify that the same folder structure exists on our secondary servers, CloudShopSQL2 and CloudShopSQL3. Login to **CloudShopSQL2** and **CloudShopSQL3** and verify that these folders exist. If they do not exist, create them.

8. Open a remote desktop connection to the CloudShopSQL virtual machine and login using the **CONTOSO\\demouser** account. Note that this demouser account is the domain administrator account. You must type the domain name in, otherwise you will be logged in with the local administrator account.

9.  From within SQL Server Management Studio, right-click **AlwaysOn High Availability** then select **New Availability Group Wizard...**
    
    ![SQL Server Management Studio with the CloudShopSQL instance shown, the Always On High Availability folder highlighted with the New Availability Group Wizard selected.](images/hands-on-lab/2019-03-24-22-21-28.png "Launch the New Availability Group Wizard")

10. Select **Next** on the Introduction screen.

11. Specify **AdventureWorks** for the name of the availability group and select **Next**.

    ![Specify the availability group options is shown with the availability group name set to AdventureWorks.](images/hands-on-lab/2019-03-24-22-24-47.png "Specify the name of the availability group")

12. Double-click the password field next to the **AdventureWorks** database and enter the password **demo@pass123** and select **Refresh**. The status of the database should state that it \"Meets prerequisites\" meaning that the database is in Full recovery mode and that you have supplied the correct password for the database master key. Select **Next**.

    ![AdventureWorks database is selected, the status shows that it meets prerequisites and the password has been filled in.](images/hands-on-lab/2019-03-24-23-02-02.png "Select database")

13. On the Specify Replicas tab, select the **Add Replica...** button and connect to CloudShopSQL2 and CloudShopSQL3.

    ![The Specify Replicas window is shown, the add replica button has been selected and the connection shows a new connection to CloudShopSQL being established.](images/hands-on-lab/2019-03-24-23-05-41.png "Specify Replicas")

14. Select the **Listener** tab, select the **Create an availability group listener** radio button, set the following values for the
    listener, then select **Add**.

    - Listener DNS Name: **AdventureWorks**

    - Port: **1433**

    - Network Mode: **Static IP**

        ![The specify replicas listener tab is selected, create an availability group listener is selected with the correct values entered.](images/hands-on-lab/2019-03-24-23-09-42.png "Specify replicas listener tab")

15. You will need to add two IP addresses for the listener, for the first use **10.0.1.8** and for the other use **172.16.1.8**, then select **Next**.

    ![The listener IP addresses are now shown in the specify replicas window.](images/hands-on-lab/2019-03-24-23-15-53.png "Specifying listener ip addresses")

16. Ensure that **Automatic seeding** is selected. Select **Next** to continue.

    ![Select Initial Data Synchronization window with autommatic seeding selected.](images/2020-06-17-14-42-14.png "Select initial data synchronization")

17. The validation tests will run automatically. They should all show success. Select **Next**.

    ![The validations window is shown with all validation marked as successful.](images/hands-on-lab/2019-03-24-23-21-45.png "Validation")

18. Verify your configuration then select **Finish** to build the Availability Group. Select **Close** after the wizard completes.

### Task 3: Create the Internal Load Balancer

1. In the Azure Portal, navigate to the **CloudShop2** resource group and select **Add**.

2. Type **Load Balancer** into the search bar then choose the **Load Balancer** and select **Create**.

    ![Create the load balancer resource.](images/hands-on-lab/2019-03-24-23-37-54.png "Add the load balancer")

3. On the Create load balancer blade, configure the following options, then select **Review + Create** and **Create** to deploy the load balancer:

    - Subscription: ***Your subscription***.
    - Resource group: **CloudShop2**
    - Name: **sqlag**
    - Region: ***Choose the region where you deployed CloudShopSQL2 and CloudShopSQL3***.
    - Type: **Internal**
    - SKU: **Basic**
    - Virtual network: **VNET2**
    - Subnet: **Data**
    - IP address assignment: **Static**
    - Private IP address: **172.16.1.8**

        ![Load balancer configuration screen with the configuration options set.](images/hands-on-lab/2019-03-25-17-41-47.png "Creating the load balancer")

4. Navigate back to your resource group and select the **sqlag** load balancer you just created.

    ![List of resources in the CloudShop2 resource group are shown with the sqlag load balancer resource highlighted.](images/2020-06-17-14-00-18.png "Load balancer")

5. On the sqlag load balancer blade, select **Backend pools**, then select the **+Add** button.

    ![The Load Balancer blade with backend pools selected.](images/hands-on-lab/2019-03-24-23-49-52.png "Add a backend pool")

6. On the Add backend pool blade, use the following configurations:

    - Name: **sqlpool**
    - IP version: **IPv4** 
    - Associated to: **Virtual machines**

        ![Adding the backend pool.](images/2020-06-17-14-03-33.png "Add backend pool")

7.  Under the **Virtual machines** section, select the **+ Add** button.

    ![The add a target network IP configuration button.](images/2020-06-17-14-05-53.png "Add a virtual machine")

8.  Select **CloudShopSQL2** and **CloudShopSQL3** virtual machines, and select **Add**. 
   
    >**Note**: Your IP address may be different than our example.

    ![Selecting CloudShopSQL2 ipconfig as a backend target.](images/2020-06-17-14-16-44.png "Select the target ip configuration")

9.  On the sqlag load balancer blade, select **Health Probes** and select the **+Add** button.

10. On the Add health probe blade, use the following configuration values then select **OK** and wait for the load balancer to complete
    the update.

    >**Note**: If you receive an error your load balancer may still be updating itself from the prior step. Wait a minute and try again.

    - Name: **sqlprobe** 
    - Protocol: **TCP** 
    - Port: **59999** 
    - Interval: **5** 
    - Unhealthy threshold: **2** 

        ![Health probe configuration showing port 59999 as the probe port.](images/hands-on-lab/2019-03-25-00-09-54.png "Health probe configuration")

11. On the sqlag load balancer blade, select **Load balancing rules** and then select the **+Add** button.

    >**Note**: If your add button is greyed out and you have a message that states *Updating* then your load balancer is still configuring the prior steps. If it does not complete in a few minutes you can refresh the browser until it is available.

12. On the Add load balancing rule blade, use the following configuration values then select **OK** and wait for the load balancer to complete the update.

    - Name: **sqlrules**
    - IP Version: **IPv4**
    - Frontend IP address: **172.16.1.8 (LoadBalancerFrontEnd)**
    - Protocol: **TCP**
    - Port: **1433**
    - Backend port: **1433**
    - Backend pool: **sqlpool**
    - Probe: **sqlprobe**
    - Session persistence: **None**
    - Idle timeout: **4**
    - Floating IP: **Enabled**

        ![Adding load balancer rule for floating IP with all configurations set.](images/hands-on-lab/2019-03-25-17-51-59.png "Load balancer rule configurations")

13. Next we need to verify our cluster's network names. Open the **Failover Cluster Manager**, expand **CLUST01**, select **Networks** then double-click **Cluster Network 2** to open the properties. Verify that Cluster Network 2's subnet is set to **172.16.1.0/24**. In some cases, the cluster deployment may reverse the names it assigns to the cluster networks. If your subnet does not match here, then you will simply change the name of the cluster network in the script below to reflect this.
    
    ![The Failover Cluster Manager with Cluster Network 2 properties page open. The subnet is highlighted.](images/hands-on-lab/2019-06-19-12-44-26.png "Cluster Network 2 properties")

14. Open an **Administrative** PowerShell ISE session on **CloudShopSQL2**.

15. Copy the following PowerShell script to the script window. If your Cluster Network 2 did not match the 172.16.1.0/24 subnet, then change the $ClusterNetworkName variable to \"Cluster Network 1\". If your subnet did match then you do not need to make this change. Execute the script to configure your cluster for the probe port:

    ```powershell
    $ClusterNetworkName = "Cluster Network 2"

    $IPResourceName = "AdventureWorks_172.16.1.8"

    $ILBIP = "172.16.1.8"

    Import-Module FailoverClusters

    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Stop-ClusterResource -Name "AdventureWorks_172.16.1.8"
    Stop-ClusterResource -Name "AdventureWorks"
    Start-ClusterResource -Name "AdventureWorks"
    ```

### Task 4: Validate the Availability Group 

1. Login to your CloudShopSQL virtual machine and launch SQL Server Management Studio.

2. From Object Explorer, launch a new connection and connect to the **AdventureWorks** listener. This is simply to check if your listener is actually working.

3. From your CloudShopSQL connection, expand Always On High Availability, Availability Groups, AdventureWorks, Availability Replicas, then right-click **CloudShopSQL2** and choose **Properties**.

    ![SQL Server Management Studio object explorer with Availability Groups and Availability Replicas expanded, the CloudShopSQL2 replica is highlighted and properties is highlighted on the right-click menu.](images/hands-on-lab/2019-03-25-18-55-26.png "Replica properties")

4. On the properties window, change the availability mode to **Synchronous commit** and then select **OK**.

    Switching to synchronous commit allows us to failover without data loss. If we leave it in asynchronous mode, the failover would be "forced" which also implies some loss of data and invalidates the old primary replica. Since we are only testing functionality of our listener we DO NOT want to break the Availability Group replication.  

    ![The replica properties window with the availability mode set to synchronous commit.](images/hands-on-lab/2019-03-25-18-59-46.png "Replica properties")

5. Repeat the above for **CloudShopSQL**. This will enable synchronous commits between the two replicas allowing us to failover without breaking the Availability Group replication.

6. Right-click the **AdventureWorks** Availability Group, and choose **Show Dashboard** from the menu.

    ![The AdventureWorks availability group is highlighted with show dashboard selected in the right-click menu.](images/hands-on-lab/2019-03-25-19-16-22.png "Show Dashboard")

7. The dashboard should show both **CloudShopSQL** and **CloudShopSQL2** with a status of **Synchronized**.

    ![The availability group dashboard is shown with the synchronization state of CloudShopSQL and CloudShopSQL2 set to synchronized.](images/hands-on-lab/2019-03-25-19-19-34.png "Dashboard")

8. Right-click the **AdventureWorks** Availability Group again and choose **Failover...** from the menu.

    ![The Adventure Works availability group is highlighted with failover selected in the right-click menu.](images/hands-on-lab/2019-03-25-19-22-06.png "Failover")

9. In the Failover Wizard, select **Next** on the first screen.

10. On the Select New Primary Replica window, select **CloudShopSQL2** and then select **Next**.

    ![The select new primary replica window is shown with CloudShopSQL2.](images/hands-on-lab/2019-03-25-19-25-13.png "Select new primary replica")

11. On the Connect to Replica window, connect to **CloudShopSQL2** with the **CONTOSO\demouser** account and select **Next**.

    ![On the Specify the connection for the secondary replica window, CloudShopSQL2 is highlighted and CONTOSO\demouser is listed under connected as.](images/hands-on-lab/2019-03-25-19-27-50.png "Specify the connection")

12. On the summary window, select **Finish**, then select **Close** on successful completion of the failover.

13. From CloudShopSQL2, connect to your **AdventureWorks** listener and open a new Query Window by selecting connect in SSMS and using AdventureWorks for the server name.

    >**Note**: We must test from the same virtual network as our Internal Load Balancer. Internal Load Balancers do not currently support connectivity across peered networks.

14. Run the following code to verify that you are running from CloudShopSQL2:

    ```sql
    SELECT @@SERVERNAME
    ```

    ![A query window is shown with select @@version as the query and CloudShopSQL2 as the result.](images/hands-on-lab/2019-03-25-19-41-47.png "Query window")

15. Use the Failover Wizard to fail **AdventureWorks** back to CloudShopSQL.

### Task 5: Update the Web Application to Connect to the Listener

1. Login to your CloudShopWeb virtual machine.

2. Launch File Explorer and navigate to **C:\inetpub\wwwroot**.

3. Open the **web.config** file in Notepad.

4. Edit the connection string to connect to the **AdventureWorks** listener.

    ![The web.config file is shown with the data source set to adventureworks.](images/hands-on-lab/2019-03-26-04-37-23.png "Edit the web.config")

5. Save the file.

6. Launch a command prompt and execute **iisreset**.

### Summary

In this exercise, you deployed a Windows Failover Cluster and a SQL Always-On Availability Group for database resiliency. Also, you deployed and configured an internal load balancer to support the Availability Group in Azure. Finally, you validated your configuration by failing over to one of your secondary nodes and connecting to the Availability Group Listener.

## Exercise 6: Configure Azure Site Recovery for Web Tier DR 

Duration: 15 minutes

In this exercise, you will configure Azure Site Recovery to protect your web application and allow it to fail to your disaster recovery site.

### Task 1: Create a Recovery Services Vault

1. From the Azure portal, select **+Create a resource** from the menu on the left.

2. In the search box, type **Backup and site recovery** and then select **Backup and Site Recovery** from the results.

3. Select the **Create** button on the information blade.

4. On the Recovery Services vault creation blade, use the following configurations and select **Review + create**.

    - Subscription: ***Choose your subscription***.
    - Resource group: **CloudShop2**
    - Vault name: **RSVault**
    - Region: ***Use the same region assigned to CloudShop2***.

        ![The recovery services vault creation blade is shown.](images/2019-09-25-13-10-47.png "Create the Recovery Services Vault")

5. On the Review and create tab select **Create**.

### Task 2: Configure Azure Site Recovery

1. In the Azure Portal navigate to the Recovery Services vault you just created.
   
2. Select **Site recovery infrastructure** from the menu on the left and then choose **Network mapping**.

3. Select the **+ Network mapping** button. Set your source network to VNET1 and your target network to VNET2.

    ![Network mapping is shown across VNET1 and VNET2.](images/hands-on-lab/2019-03-26-03-20-41.png "Add network mapping")

4. In the Recovery Services vault blade, select **Site Recovery** under **Getting started**.

    ![The recovery services vault blade with site recovery highlighted.](images/hands-on-lab/2019-03-25-23-46-47.png "Setup Site Recovery")

5. Under **For on-premises machines and Azure VMs** select **Step 1: Replicate Application**.

    ![The Azure Site Recovery blade. Step 1: Replicate Application is highlighted.](images/hands-on-lab/2019-03-25-23-48-09.png "Replicate application Settings")

6. Choose the following configurations and select **OK**.

    - Source: **Azure**
    - Source location: ***The location of your CloudShop1 resource group***.
    - Azure virtual machine deployment model: **Resource Manager**
    - Source subscription: ***Your subscription***.
    - Source resource group: **CloudShop1**

        ![An image that depicts Azure Site Recovery settings.](images/hands-on-lab/2019-03-25-23-53-54.png "ASR replicate source settings")

7. On the select virtual machines blade, select the **CloudShopWeb** virtual machine for replication. We will only use Azure Site Recovery to protect the web servers. Select the **OK** button.

    ![CloudShopWeb is highlighted in the select virtual machines window.](images/hands-on-lab/2019-03-26-00-02-35.png "Select virtual machines")

8. On the Configure settings blade, set the Target location to the location of your CloudShop2 resource group. 

9. Select the **Customize** link.

    ![The configure settings dialogue with the Customize button highlighted.](images/hands-on-lab/2019-03-26-03-26-07.png "Configure settings dialogue")

10. Change the target resource group to **CloudShop2** and select **OK**.

    ![The customize target resources dialogue with default settings.](images/hands-on-lab/2019-03-26-03-27-29.png "Customize target resources")

11. Select **Create target resources**.

    >**Note**: Do not close the blade. It will close by itself after the target resources are created (2-3 minutes).

    ![The Configure settings dialogues with the create target resources button highlighted.](images/hands-on-lab/2019-03-26-00-06-44.png "Create target resources")

12. On the Enable Replication blade, select the **enable replication** button. 

13. Several Site Recovery jobs will be initiated which are creating the replication policy as well as the target resources to be used during a failover. Select **Site recovery jobs** to view the job progress.

    ![The site recovery jobs dialogue showing the automatically generated jobs running.](images/hands-on-lab/2019-03-26-00-52-02.png "Site recovery jobs")

14. Once all the jobs are successful, select **Replicated items** under **Protected Items** to view the status of the initial replication.

    ![The replicated items dialogue showing the CloudShop2 replication health as healthy.](images/hands-on-lab/2019-03-26-00-53-16.png "Replicated items")

15. While waiting for the initial replication/synchronization, move on to the next task.

### Task 3: Creating the Recovery Plan

In this task, you will create the recovery plan that will be used to orchestrate failover action for the web tier.

1. Within the properties of the Recovery Services vault, select **Recovery Plans (Site Recovery)** under Manage.

    ![The manage menu is shown with Recovery Plans (Site Recovery) highlighted.](images/hands-on-lab/2019-03-26-00-58-00.png "Recovery Plans")

2. Select the **+Recovery Plan** button.

3. On the Create recovery plan blade enter the name **CloudShopRP**. In the Source area select the region where your CloudShop1 resource group is deployed. The Target will be automatically selected. Under Allow items with deployment model, select **Resource Manager**. Choose Select items and select the Virtual Machine. Select **OK** and then select **OK** on the Create recovery plan blade.

    ![Create recovery plan settings.](images/hands-on-lab/2019-03-26-01-09-00.png "ASR Recovery Plan settings")

4. After a minute or two, you should see the CloudShopRP on the Recovery plans blade. This recovery plan will bring up your web server during a failover. 

5. Now go back to the Recovery Services vault RSVault - Overview blade. Select **Site Recovery** and then select the **Virtual Machine** tile. Notice that the CloudShopWeb virtual machine is replicating. Take note of the status. You will not be able to continue until it is finished replicating. 

   >**Note**: This may take up to an hour.

    ![The recovery services vault overview with site recovery selected.](images/hands-on-lab/2019-03-26-02-15-27.png "RSVault - Site Recovery")

## Exercise 7: Failing Over to the Disaster Recovery Site 

Duration: 30 minutes

In this exercise, you will fail over to your disaster recovery site.

### Task 1: Failover the Data Tier

1. Login to your CloudShopSQL virtual machine and launch SQL Server Management Studio.

2. Open a connection to your CloudShopSQL instance, expand Always On High Availability, Availability Replicas, then right-click **CloudShopSQL2** and choose **Properties**.

    ![The CloudShop2 availability replica is selected and the properties is highlighted.](images/hands-on-lab/2019-03-25-18-55-26.png "CloudShop2 properties")

3. On the properties window, change the availability mode to **Synchronous commit** and then select **OK**. Note that in a real outage scenario it may not be possible to use this method and may require a forced failover which may incur some data loss.  

    ![The properties of CloudShop2 are shown with the availability mode set to synchronous commit.](images/hands-on-lab/2019-03-25-18-59-46.png "Set CloudShop2 to synchronous commit")

4. Repeat the above for **CloudShopSQL**. This will enable synchronous commits between the two replicas allowing us to failover without breaking the Availability Group replication.

5. Right-click the **AdventureWorks** Availability Group and choose **Failover...** from the menu.

    ![The AdventureWorks availability group is highlighted and failover is selected from the right-click menu.](images/hands-on-lab/2019-03-25-19-22-06.png "Failover")

6. In the Failover Wizard, select **Next** on the first screen.

7. On the Select New Primary Replica window, select **CloudShopSQL2** and then select **Next**.

    ![The select new primary window is shown with CloudShopSQL2 highlighted.](images/hands-on-lab/2019-03-25-19-25-13.png "Select New Primary Replica")

8. On the Connect to Replica window, connect to **CloudShopSQL2** with the **CONTOSO\demouser** account and select **Next**.

    ![The specify the connection window is shown with CONTOSO\demouser set as the username.](images/hands-on-lab/2019-03-25-19-27-50.png "Connect to replica")

9. On the summary window, select **Finish**, then select **Close** on successful completion of the failover.

### Task 2: Failover the Web Tier

To fail over the entire environment requires the database server to be up, then the web server may come online. After successfully failing over the data tier in the previous step we will now failover the web tier via ASR. 

1. From the Azure portal, navigate to your **RSvault** resource.

2. Select **Recovery Plans (Site Recovery)** from the menu on the left.

    ![Recovery Plans Site Recovery is highted on the Manage menu.](images/hands-on-lab/2019-03-26-03-01-20.png "Recovery services vault")

3. Select your **CloudShopRP** recovery plan.

4. On the CloudShopRP recovery plan blade, select the **...More** button, and then select **Failover** from the dropdown menu.

    ![The CloudShopRP recovery plan blade is shown and the **...More** button is highlighted, and failover is highlighted from the dropdown menu.](images/hands-on-lab/2019-03-26-03-04-07.png "CloudShopRP recovery plan")

5. Check the box next to **I understand the risk**.

6. Verify your failover settings and select **OK**.

    ![Failover window with recovery point set to latest processed, and the shutdown machines checkbox checked.](images/hands-on-lab/2019-03-26-04-06-02.png "Verify failover")

7. You can monitor the status of your failover by selecting **Site recovery jobs** from the monitoring section of the menu on the left.

8. After the failover completes, navigate to the **CloudShop2** resource group and open your **CloudShopWeb** virtual machine.

9. Notice that it does not have a Public IP address. Select **Networking** on the menu to the left.

    ![CloudShopWeb virtual machine overview with missing public IP highlighted.](images/hands-on-lab/2019-03-26-04-24-41.png "Missing public IP")

10. Select the **CloudShopWebNetworkInterface**.

    ![The CloudShopWebNetworkInterface is shown.](images/hands-on-lab/2019-03-26-04-26-18.png "CloudShopWebNetworkInterface")

11. Select **IP Configurations** from the menu.

    ![The network interface overview with IP configurations selected from the menu on the left.](images/hands-on-lab/2019-03-26-04-28-08.png "Network Interface overview")

12. Change the **Public IP address settings** to **Enabled**, then create a new public IP address called **webpubip**. Select **OK** and **Save**.

    ![The CloudShopWeb network interface settings are shown with the public IP address being enabled.](images/hands-on-lab/2019-03-26-04-30-12.png "Update Public IP settings")

13. Open a command prompt and issue an **iisreset** to restart the web service.

### Task 3: Validate Failover of the CloudShop application 

1. From the Azure portal, navigate to the **CloudShopWeb** virtual machine.   

2. Copy the **Public IP address** and paste it into a browser to verify that your site is working.

## After the hands-on lab

### Task 1: Delete the resource groups created

1. Within the Azure portal, select Resource Groups on the left navigation.

2. Delete each of the resource groups created in this lab by selecting them followed by selecting the Delete Resource Group button. You will need to confirm the name of the resource group to delete.

You should follow all steps provided *after* attending the hands-on lab.
