`![](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
SQL Server Hybrid Cloud
</div>

<div class="MCWHeader2">
Hands-on lab step-by-step
</div>

<div class="MCWHeader3">
June 2019
</div>

Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2019 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

**Contents**

<!-- TOC -->

- [SQL Server Hybrid Cloud hands-on lab step-by-step](#SQL-Server-Hybrid-Cloud-hands-on-lab-step-by-step)
  - [Abstract and learning objectives](#Abstract-and-learning-objectives)
  - [Overview](#Overview)
  - [Solution architecture](#Solution-architecture)
  - [Requirements](#Requirements)
    - [Help references](#Help-references)
  - [Exercise 1: SQL Backup solution](#Exercise-1-SQL-Backup-solution)
    - [Task 1: Create an Azure Storage Account](#Task-1-Create-an-Azure-Storage-Account)
    - [Task 2: Configure managed backup in SQL Server](#Task-2-Configure-managed-backup-in-SQL-Server)
  - [Exercise 2: Implement a Data Archive Strategy with SQL Server Stretch Database](#Exercise-2-Implement-a-Data-Archive-Strategy-with-SQL-Server-Stretch-Database)
    - [Task 1: Crete a logical SQL Server to host Stretch DB](#Task-1-Crete-a-logical-SQL-Server-to-host-Stretch-DB)
    - [Task 2: Identify tables that may benefit from Stretch DB](#Task-2-Identify-tables-that-may-benefit-from-Stretch-DB)
    - [Task 3: Implement Stretch DB based on date key](#Task-3-Implement-Stretch-DB-based-on-date-key)
    - [Summary](#Summary)
  - [Exercise 3: Build SQL Availability Group for Database Disaster Recovery](#Exercise-3-Build-SQL-Availability-Group-for-Database-Disaster-Recovery)
    - [Task 1: Create the cluster](#Task-1-Create-the-cluster)
    - [Task 2: Create the SQL Server Availability Group](#Task-2-Create-the-SQL-Server-Availability-Group)
    - [Task 3: Create the Internal Load Balancer](#Task-3-Create-the-Internal-Load-Balancer)
    - [Task 4: Validate the Availability Group](#Task-4-Validate-the-Availability-Group)
    - [Task 5: Update the Web Application to Connect to the Listener](#Task-5-Update-the-Web-Application-to-Connect-to-the-Listener)
    - [Summary](#Summary-1)
  - [Exercise 4: Configure Azure Site Recovery for Web Tier DR](#Exercise-4-Configure-Azure-Site-Recovery-for-Web-Tier-DR)
    - [Task 1: Create a Recovery Services Vault](#Task-1-Create-a-Recovery-Services-Vault)
    - [Task 2: Configure Azure Site Recovery](#Task-2-Configure-Azure-Site-Recovery)
    - [Task 3: Creating the Recovery Plan](#Task-3-Creating-the-Recovery-Plan)
  - [Exercise 5: Failing Over to the Disaster Recovery Site](#Exercise-5-Failing-Over-to-the-Disaster-Recovery-Site)
    - [Task 1: Failover the Data Tier](#Task-1-Failover-the-Data-Tier)
    - [Task 2: Failover the Web Tier](#Task-2-Failover-the-Web-Tier)
    - [Task 3: Validate Failover of the CloudShop application](#Task-3-Validate-Failover-of-the-CloudShop-application)
  - [After the hands-on lab](#After-the-hands-on-lab)
    - [Task 1: Delete the resource groups created](#Task-1-Delete-the-resource-groups-created)

<!-- /TOC -->

# SQL Server Hybrid Cloud hands-on lab step-by-step 

## Abstract and learning objectives 

In this hands-on lab, you will build a disaster recovery site for an on-premises environment. You will enhance the existing database solution to support a hybrid cloud-based disaster recovery solution, implement an archiving strategy and a backup/restore strategy designed to protect data.

At the end of the lab, you will be better able to design and use availability sets, Managed Disks, SQL Server Always on Availability Groups, Stretch DB and SQL Server Managed Backup.

## Overview

Contoso has asked you to deploy their infrastructure in a resilient manner to ensure their infrastructure will be available for their users and gain an SLA from Microsoft.

## Solution architecture

Cloud based disaster recovery site.

![Enterprise disaster recovery architecture](images/hands-on-lab/disaster-recovery-diagram.png "Enterprise disaster recovery architecture")

## Requirements

1. Microsoft Azure Subscription

2. Virtual Machine Built during this hands-on lab or local machine with the following:

    - Visual Studio 2017 Community or Enterprise Edition
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

## Exercise 1: SQL Backup solution

Duration: 30 minutes

Backups must be maintained offsite from the on-premises environment. The backups must be online and accessible by the DBA team. To accomplish this, you will configure SQL Managed Backup.

### Task 1: Create an Azure Storage Account

In this task, you will create an Azure Storage Account for use with SQL Managed Backup.

1. Connect to your CloudShopSQL virtual machine by navigating to your **CloudShop1** resource group and then connecting to the **CloudShopSQL**  virtual machine.

2. Login with username **demouser** and password **demo@pass123**.

3. Launch **Server Manager**, select **Local Server** from the menu on the left and verify that **IE Enhanced Security Configuration** is set to **Off**.

    ![Server manager application with local server selected and IE Enhanced Security Configuration set to Off.](images/hands-on-lab/2019-03-24-18-38-33.png "Server Manager local server configuration")

4. From within your SQL Server guest virtual machine, install Azure PowerShell by launching an **administrative PowerShell ISE session** and running the following command. Accept any warnings or authorization to install the components.

    ```powershell
    Install-Module -Name AzureRM -AllowClobber
    ```

5. From the PowerShell ISE, type the following at the prompt and follow the prompts to login to your Azure subscription:

    ```powershell
    login-AzureRmAccount
    ```

6. Execute the following PowerShell commands in the PowerShell ISE to create a new storage account and generate the T-SQL needed to configure managed backup for the database. Before executing the script:

   - Change the **$storageAcctName** variable to a unique name.
   - Change the location to match the location you are deploying into for this lab.

    ```powershell
    $storageAcctName = "[unique storage account name]"

    $resourceGroupName = "CloudShop2"
    $containerName= "backups"
    $location = "[choose the same region you used to deploy the CloudShop2 resource group]"
    $storageSkuName = "Standard_LRS"

    "Creating Storage Account $storageAcctName"
    $sa = New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName  `
        -Name $storageAcctName `
        -Location $location `
        -SkuName $storageSkuName 

    $storageKey = (Get-AzureRmStorageAccountKey -Name $storageAcctName -ResourceGroupName $resourceGroupName )[0].Value
    $context = New-AzureStorageContext -StorageAccountName $storageAcctName -StorageAccountKey $storageKey

    Write-Host "Creating New Storage Container  $containerName" 
    New-AzureStorageContainer -name $containerName -permission container -context $context

    $fullSasToken = New-AzureStorageContainerSASToken -Name $containerName -Permission rwdl -FullUri -Context $context  
    $containerUrl = $fullSasToken.Substring(0,$fullSasToken.IndexOf("?"))
    $sasToken = $fullSasToken.Substring($fullSasToken.IndexOf("?")+1)

    $enableManagedBackupScript = @"
    --------------------
    ---BEGIN TSQL Script
    --------------------
    CREATE CREDENTIAL [$containerUrl]
    WITH IDENTITY = 'Shared Access Signature',
         SECRET = '$sasToken'

    GO

    EXEC msdb.managed_backup.sp_backup_config_basic
     @enable_backup = 1,
     @database_name = 'AdventureWorks',  
     @container_url = '$containerUrl',
     @retention_days = 30

     --------------------
     ---END TSQL Script
     --------------------
    "@

    Write-Host $enableManagedBackupScript
    ```

    >**Note**: Due to differences between markdown and PowerShell formatting requirements, you may need to remove the white space prior to the **\"\@** near the end of the file.

7. Save the T-SQL code generated between the **Begin TSQL Script** and **End TSQL Script** in your PowerShell ISE output after execution into a notepad file. This code creates an identity using a Shared Access Signature (SAS) to a container in the storage account and configures managed backup when executed.

### Task 2: Configure managed backup in SQL Server

1. From within your SQL Server virtual machine, launch SQL Server Management Studio and connect to the local database instance, expand databases, and select the **AdventureWorks** database.

2. From within SQL Server Management Studio, click **New Query**.

    ![A screen showing how to launch the new query pane in SQL Server Management Studio.](images/hands-on-lab/2019-03-20-11-08-08.png "Launching the new query pane")

3. Refresh SQL Server Management Studio and if SQL Server Agent is stopped right click on it and click Start.

    ![SQL Server Management Studio indicating the location of where to see the current status of the SQL Server Agent](images/hands-on-lab/2019-03-24-19-13-52.png "Management Studio")

4. Paste in the following code and click **Execute** to enable SQL Server Agent extended stored procedures.

    ```sql
    EXEC sp_configure 'show advanced options', 1
    GO
    RECONFIGURE
    GO
    EXEC sp_configure 'Agent XPs', 1
    GO
    RECONFIGURE
    GO
    ```

5. Paste the T-SQL code you copied at the end of the previous task into the query window replacing the existing code and click **Execute**. An example of the code is below. This code creates the new SQL identity with a Shared Access Signature for your storage account. 

    ```sql
    #sample only do not execute
    CREATE CREDENTIAL [https://<your_storage_account>.blob.core.windows.net/backups]
    WITH IDENTITY = 'Shared Access Signature',
    SECRET = '<your_sas_token>'
    GO

    EXEC msdb.managed_backup.sp_backup_config_basic
        enable_backup = 1,
        @database_name = 'AdventureWorks',
        @container_url = 'https://<your_storage_account>.blob.core.windows.net/backups',
        @retention_days = 30
    ```

6. Paste the code below into the query window replacing the existing code and click **Execute** to create a custom backup schedule.

    ```sql
    USE msdb;  
    GO  
    EXEC managed_backup.sp_backup_config_schedule   
         @database_name =  'AdventureWorks'  
        ,@scheduling_option = 'Custom'  
        ,@full_backup_freq_type = 'Weekly'  
        ,@days_of_week = 'Monday'  
        ,@backup_begin_time =  '17:30'  
        ,@backup_duration = '02:00'  
        ,@log_backup_freq = '00:05'  
    GO
    ```

7. Execute the following tSQL in the query window to generate a backup on-demand. You can also specify Log for \@type.

    ```sql
    EXEC msdb.managed_backup.sp_backup_on_demand
    @database_name  = 'AdventureWorks',
    @type ='Database'
    ```

8. To verify that your backups are working, go to the Azure portal, navigate to your **CloudShop2** resource group. Open the storage account you just created, select the **Blobs** tile, then the **backups** container. You should see your backups here.

## Exercise 2: Implement a Data Archive Strategy with SQL Server Stretch Database

Duration: 30 minutes

In this exercise, you will implement SQL Server Stretch Database to stretch data from a table into Azure.

### Task 1: Crete a logical SQL Server to host Stretch DB

1. From your **CloudShopSQL** virtual machine, launch a browser, navigate to the Azure portal and login with your Azure credentials.

2. Click **+Create a resource** at the top of the left side menu.

3. Type **SQL Server logical server** into the search box, hit enter and choose **SQL server (logical server)** from the search results.

    ![Azure Marketplace search for SQL Server logical server](images/hands-on-lab/2019-03-20-11-38-42.png "Search for SQL Server logical server")

4. Click **Create** on the SQL Server (logical server) information blade.

5. On the SQL Server (logical server) blade, use the following configurations and click **Create**.

    - Server name: ***Choose a unique server name***.
    - Server admin login: **demouser**
    - Password: **demo@pass123**
    - Subscription: ***Your subscription***.
    - Resource group: **CloudShop2**
    - Location: ***Use the same location you used for CloudShop2***.
    - Allow Azure Services: ***Checked***

        ![The SQL Server logical server creation blade with the configurations listed previously set to the correct values.](images/hands-on-lab/2019-03-24-19-31-07.png "Create a SQL Server logical server")

6. Make note of the server name you chose above. It will be used in a later task.

7. Click **Create**.

8. After the SQL Server logical server has deployed, navigate to it and select **Firewalls and virtual networks** from the security menu on the left.

    ![Security menu with Firewalls and virtual networks highlighted.](images/hands-on-lab/2019-03-20-11-56-21.png "Security menu")

9. Add a new firewall rule by clicking **+Add client IP** and then clicking **Save**.

    ![The firewall configuration dialogue with the save and add client IP buttons highlighted.](images/hands-on-lab/2019-03-20-12-01-55.png "Firewall configuration")

### Task 2: Identify tables that may benefit from Stretch DB

1. From your **CloudShopSQL** virtual machine, launch SQL Server Management Studio if it is not already open.

2. In the SQL Server Management Studio Object Explorer, connect to your local SQL Server instance and expand the Databases folder.

3. Right-click the AdventureWorks database, select Tasks, select Stretch, then choose Enable.

    ![SQL Server Management Studio Object Explorer with the Smart Hotel right-click menu open, tasks is highlighted then stretch is highlighted and Enable is selected.](images/hands-on-lab/2019-03-24-19-38-01.png "Enable Stretch Database")

4. The Enable Database for Stretch wizard should open automatically. Click **Next** on the Introduction screen.

5. On the Select tables window all of your tables will be listed. Notice that some tables have warnings, and some may be greyed out. Click on the warning icon next to one of the tables. This indicates that the table does not meet the StretchDB eligibility requirements.

    ![Stretch Database Wizard showing error for a table.](images/hands-on-lab/2019-03-24-19-40-15.png "Stretch Database wizard")

6. Scroll to the right until you see the Migrate column. Clicking Entire Table on an eligible table allows you to define which rows will be migrated to Azure. In this lab, we are going to configure Stretch Database through TSQL. Click Cancel to break out of the wizard.

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

5. Refresh the AdventureWorks database in Object Explorer by right clicking it and choosing Refresh.

6. Right-click the AdventureWorks database, choose Tasks, Stretch, then select Monitor.

7. View the stretch database report. Note that over time the Eligible Rows, Local Rows, and Rows In Azure numbers will change as data is migrated to your Stretch Database in Azure.

    ![The stretch database report is shown with ResellerSales highlighted.](images/hands-on-lab/2019-03-20-12-21-30.png "Stretch Configured Tables")

8. Click the dropdown under Migration State. Notice that you have the option to pause the outbound migration.

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

## Exercise 3: Build SQL Availability Group for Database Disaster Recovery

Duration: 60 minutes

In this exercise, you will build a SQL Always-On Cluster for resiliency of the data tier.

### Task 1: Create the cluster

In this task, you will create the underlying Windows Failover Cluster which is the base of your SQL Server Availability Group. You will begin by logging in with an account with administrative privileges on all of the prospective nodes of the cluster and running a Failover Cluster Validation test. This test verifies that all the nodes of your cluster are properly configured to support clustering and will flag any best practices that you may not be adhering to in your configuration. Finally, you will create the cluster across the three nodes of you cluster.

1. Launch a browser and navigate to <https://portal.azure.com>. Login
    with your Azure credentials if you haven't already.

    > **Note**: You may need to launch an \"in-private\" session in your browser if you have multiple Microsoft Accounts.

2. Navigate to your **CloudShop1** resource group and open your **CloudShopSQL** virtual machine and connect to it via Remote Desktop.

3. Login with the **CONTOSO\demouser** domain account with password **demo@pass123**. Note that this is NOT the same account that you have been using, this is a domain account.

4. Launch Server Manager if it does not start automatically.

5. Select **Local Server** from the menu on the left and scroll down to **Roles and Features**, click the **Tasks** dropdown and select **Add Roles and Features** from the list.

    ![Server Administrator Application with local server selected, and add roles and features selected in the tasks dropdown of the Roles and Features section.](images/hands-on-lab/2019-03-20-17-33-21.png "Adding Roles and Features")

6. On the **Before you begin** page, click **Next**.

7. On the **Select installation type** page, select **Role-based or feature-based installation** and click **Next**.

8. On the **Select destination server** page, select the current server and click **Next**.

9. On the **Select server roles** page, accept the defaults and click **Next**.

10. On the **Select features** page, select **Failover Clustering**, then click **Add Features** on the popup and then click **Next**.

    ![The add roles and features wizard with Failover Clustering selected on the select features page.](images/hands-on-lab/2019-03-24-20-03-47.png "Add Failover Clustering")

11. On the **Confirm installation selections** page, click **Install**.

12. Repeat the above steps to install Failover Clustering on your remaining SQL Servers (CloudShopSQL2 and CloudShopSQL3).

    >**Note**: CloudShopSQL2 and CloudShopSQL3 were deployed to the disaster recovery site which is hosted in the **CloudShopSQL2** resource group.

13. From CloudShopSQL, open **Administrative Tools** and then launch **Failover Cluster Manager**.

14. From the Actions pane on the right, choose **Validate Configuration**.

    ![The Failover Cluster Manager with Validate Configuration highlighted.](images/hands-on-lab/2019-03-24-20-17-48.png "Failover Cluster Manager")

15. Click **Next** on the Before You Begin window.

16. On the Select Servers or a Cluster window, enter **CloudShopSQL** and then click the **Add** button. Repeat for **CloudShopSQL2** and **CloudShopSQL3** then click **Next**.

    ![The select servers or a cluster window with CloudShopSQL, CloudShopSQL2 and CloudShopSQL3 added to the selected servers.](images/hands-on-lab/2019-03-24-20-23-55.png "Validate a Configuration Wizard")

17. On the Testing Options window, select the **Run only tests I select** radio button and click **Next**.

    ![The testing options window with run only tests I select selected](images/hands-on-lab/2019-03-24-20-31-41.png "Validate a Configuration Wizard")

18. On the Test selection window, uncheck storage. The storage tests have no relevance for our case and will only throw additional warnings.

    ![The test selection window with the storage tests unchecked.](images/hands-on-lab/2019-03-24-20-34-43.png "Validate a Configuration Wizard")

19. On the Confirmation window, click **Next** to begin the validation tests.

20. Review the output of the tests by clicking the **View Report** button. This validation test aids you in determining if your server and network configuration is correctly configured for Windows Failover Clustering. You should review any warnings or errors. In our case, the environment was preconfigured to support clustering so there should be no errors. There may be warnings regarding best practices such as multiple network cards etc. In a production environment these warnings must be taken seriously as they may adversely affect the performance and reliability of the cluster. In our lab they will have a negligible impact.

21. Click **Finish** on the Validate a Configuration Wizard.

22. On CloudShopSQL, open the PowerShell ISE and execute the following code:

    ```
    New-Cluster -Name CLUST01 -Node CloudShopSQL, CloudShopSQL2, CloudShopSQl3 -StaticAddress 10.0.1.7, 172.16.1.7  -NoStorage
    ```
    > **Note**: This command requires that you be logged in with an account that is an administrator on all nodes specified. For this lab you should use **CONTOSO\demouser**.

    This will create a three-node cluster with two static IP addresses. We need two static IP addresses because the cluster may be running from our on-premises environment (CloudShop1) or our cloud disaster recovery environment (CloudShop2).  
    
    It is also possible to use a wizard for this task, but the resulting cluster will require additional configuration to set the static IP addresses to be viable in Azure. This is due to the manner in which Azure distributes IP addresses causing the cluster to receive the same IP address as the node it is executing on resulting in a duplicate IP address and failure of the cluster service.

23. Open the Failover Cluster Manager on CloudShopSQL from the start menu under Windows Administrative Tools. In the right navigation right-click **Failover Cluster Manager** and click **Connect to Cluster**. Click OK in the pop-up. Expand the **CLUST01** cluster, select **Nodes**, validate that all nodes are online, and that Assigned Vote and Current Vote are 1 for all nodes of the cluster.

    ![Failover Cluster Manager with CLUST01 selected, Nodes selected, and all three nodes of the cluster showing online with 1 assigned and 1 current vote.](images/hands-on-lab/2019-03-24-21-22-18.png "Nodes view of Failover Cluster Manager")

### Task 2: Create the SQL Server Availability Group

1. On CloudShopSQL, launch **SQL Server 2017 Configuration Manager**.

2. Select **SQL Server Services**, then right-click **SQL Server (MSSQLSERVER)** and select **Properties**.

    ![SQL Server Configuration Manager with SQL Server Services selected and SQL Server service highlighted.](images/hands-on-lab/2019-03-24-21-31-48.png "SQL Server Configuration Manager")

3. Click on the **Log On** tab. To setup a SQL Server Availability     Group, the SQL Server service account needs to have access to all of the SQL Servers that will participate in the Availability Group. Change the service account to the **CONTOSO\\demouser** domain account using **demo@pass123** for the password.

    ![SQL Server Properties is shown with the account set to the CONTOSO\demouser domain account.](images/hands-on-lab/2019-03-24-21-35-08.png "SQL Server Login Properties")

4. Select the **AlwaysOn High Availability** tab and check the box for **Enable AlwaysOn Availability Groups**, then click **OK**, then click **Yes** to confirm the account change.

    ![SQL Server Properties is shown with Enable AlwaysOn High Availability Groups selected.](images/hands-on-lab/2019-03-24-21-37-08.png "SQL Server AlwaysOn High Availability Properties")

5. Restart the SQL Server service by right-clicking the service and choosing **Restart**.

    ![SQL Server Configuration Manager with SQL Server Services selected and the SQL Server service being restarted.](images/hands-on-lab/2019-03-24-21-40-59.png "Restart the SQL Server service")

6. Repeat this configuration for **CloudShopSQL2** and **CloudShopSQL3**.

7. The database you will be replicating has its data and log files in the **C:\\Data** folder and the **C:\\Logs** folder on CloudShopSQL. We need to verify that the same folder structure exists on our secondary servers, CloudShopSQL2 and CloudShopSQL3. Login to **CloudShopSQL2** and **CloudShopSQL3** and verify that these folders exist. If they do not exist, create them.

8. Open a remote desktop connection to the CloudShopSQL virtual machine and login using the **CONTOSO\\demouser** account. Note that this demouser account is the domain administrator account. You must type the domain name in, otherwise you will be logged in with the local administrator account.

9. Your SQL Server Availability Group will require a file share to be used for the initial synchronization. Open up File Explorer and
    create a new folder in the root of the C: drive called **AG**.

10. Right-click the new folder and select **Share with \> Specific people...**

    ![File Explorer is shown with the right-click menu of the AG folder with Share with specific people highlighted.](images/hands-on-lab/2019-03-24-21-57-15.png "File Explorer")

11. On the File Sharing window, select **Administrators** and click **Share**, then click **Done**.

12. Launch **SQL Server Management Studio** and connect to **CloudShopSQL**.

13. Expand the databases folder, right-click the **AdventureWorks** database and select **Properties**.

14. On the Database Properties window, select **Options** then set the recovery model to **Full** and click the **OK** button. The full recovery model is required for databases that participate in SQL Server Always On Availability Groups.

    ![Database properties with the recovery model option set to full.](images/hands-on-lab/2019-03-24-22-31-59.png "Set the database recovery model to full")

15. Right-click the **AdventureWorks** database, select **Tasks** and choose **Back Up...**.

    ![The AdventureWorks database is selected, then tasks is selected and backup is selected.](images/hands-on-lab/2019-03-24-22-39-23.png "Backup the database")

16. On the Back Up Database - AdventureWorks window, choose to backup to disk, click **Add**, and set the destination to **C:\\Data\\AdventureWorks.bak**, then click **OK** and click **OK** again to initiate the backup.

    ![Backup destination is selected from the backup wizard.](images/hands-on-lab/2019-03-24-22-46-39.png "Backup the database")

17. From the SQL Server Management Studio CloudShopSQL connection, right-click **AlwaysOn High Availability** then select **New Availability Group Wizard...**
    
    ![SQL Server Management Studio with the CloudShopSQL instance shown, the Always On High Availability folder highlighted with the New Availability Group Wizard selected.](images/hands-on-lab/2019-03-24-22-21-28.png "Launch the New Availability Group Wizard")

18. Click **Next** on the Introduction screen.

19. Specify **AdventureWorks** for the name of the availability group and click **Next**.

    ![Specify the availability group options is shown with the availability group name set to AdventureWorks.](images/hands-on-lab/2019-03-24-22-24-47.png "Specify the name of the availability group")

20. Double-click the password field next to the **AdventureWorks** database and enter the password **demo@pass123** and click **Refresh**. The status of the database should state that it \"Meets prerequisites\" meaning that the database is in Full recovery mode and that you have supplied the correct password for the database master key. Click **Next**.

    ![AdventureWorks database is selected, the status shows that it meets prerequisites and the password has been filled in.](images/hands-on-lab/2019-03-24-23-02-02.png "Select database")

21. On the Specify Replicas tab, click the **Add Replica...** button and connect to CloudShopSQL2 and CloudShopSQL3.

    ![The Specify Replicas window is shown, the add replica button has been clicked and the connection shows a new connection to CloudShopSQL being established.](images/hands-on-lab/2019-03-24-23-05-41.png "Specify Replicas")

22. Click on the **Listener** tab, select the **Create an availability group listener** radio button, set the following values for the
    listener, then click **Add**.

    - Listener DNS Name: **AdventureWorks**

    - Port: **1433**

    - Network Mode: **Static IP**

        ![The specify replicas listener tab is selected, create an availability group listener is selected with the correct values entered.](images/hands-on-lab/2019-03-24-23-09-42.png "Specify replicas listener tab")

23. You will need to add two IP addresses for the listener, for the first use **10.0.1.8** and for the other use **172.16.1.8**, then click **Next**.

    ![The listener ip addresses are now shown in the specify replicas window.](images/hands-on-lab/2019-03-24-23-15-53.png "Specifying listener ip addresses")

24. Ensure that **Full database and log backup** is selected and use **\\\\CloudShopSQL\\ag** for the network share. Click **Next** to continue.

    ![Select the full database and log backup is chosen with the AG fileshare used as the shared backup location.](images/hands-on-lab/2019-03-24-23-19-12.png "Select initial data synchronization")

25. The validation tests will run automatically. They should all show success. Click **Next**.

    ![The validations window is shown with all validation marked as successful.](images/hands-on-lab/2019-03-24-23-21-45.png "Validation")

26. Verify your configuration then click **Finish** to build the Availability Group. Click **Close** after the wizard completes.

### Task 3: Create the Internal Load Balancer

1. In the Azure Portal, navigate to the **CloudShop2** resource group and click **Add**.

2. Type **Load Balancer** into the search bar then choose the **Load Balancer** and click **Create**.

    ![Create the load balancer resource.](images/hands-on-lab/2019-03-24-23-37-54.png "Add the load balancer")

3. On the Create load balancer blade, configure the following options, then click **Review + Create**:

    - Subscription: ***Your subscription***.
    - Resource group: **CloudShop2**
    - Name: **sqlag**
    - Region: ***Choose the region you have used for this lab***.
    - Type: **Internal**
    - SKU: **Basic**
    - Virtual network: **VNET2**
    - Subnet: **Data**
    - IP address assignment: **Static**
    - Private IP address: **172.16.1.8**

        ![Load balancer configuration screen with the configuration options set.](images/hands-on-lab/2019-03-25-17-41-47.png "Creating the load balancer")

4. Navigate back to your resource group and select the **sqlag** load balancer you just created.

5. On the sqlag load balancer blade, select **Backend pools**, then click the **+Add** button.

    ![The Load Balancer blade with backend pools selected.](images/hands-on-lab/2019-03-24-23-49-52.png "Add a backend pool")

6. On the Add backend pool blade, use the following configurations:

    - Name: **sqlpool**
    - IP version: **IPv4** 
    - Associated to: **Availability set**
    - Availability set: **SQLAvSet2**

        ![Adding the backend pool.](images/hands-on-lab/2019-03-24-23-52-15.png "Add backend pool")

7.  Click the **+ Add a target network IP configuration** button.

    ![The add a target network IP configuration button.](images/hands-on-lab/2019-03-24-23-54-54.png "Add a target network IP configuration")

8.  Select **CloudShopSQL2** for the Target virtual machine, and select **ipconfig1 (172.16.1.5)** for the Network IP configuration. 
   
    >**Note**: Your IP address may be different than our example.

    ![Selecting CloudShopSQL2 ipconfig as a backend target.](images/hands-on-lab/2019-03-24-23-59-23.png "Select the target ip configuration")

9.  Repeat steps 7-8 to add **CloudShopSQL3**. DO NOT ADD other machines to the backend pool. Click **OK** then wait for the load balancer to complete the update.

10. On the sqlag load balancer blade, select **Health Probes** and click the **+Add** button.

11. On the Add health probe blade, use the following configuration values then click **OK** and wait for the load balancer to complete
    the update.

    >**Note**: If you receive an error your load balancer may still be updating itself from the prior step. Wait a minute and try again.

    - Name: **sqlprobe** 
    - Protocol: **TCP** 
    - Port: **59999** 
    - Interval: **5** 
    - Unhealthy threshold: **2** 

        ![Health probe configuration showing port 59999 as the probe port.](images/hands-on-lab/2019-03-25-00-09-54.png "Health probe configuration")

12. On the sqlag load balancer blade, select **Load balancing rules** and then click the **+Add** button.

13. On the Add load balancing rule blade, use the following configuration values then click **OK** and wait for the load balancer to complete the update.

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

14. Next we need to verify our cluster's network names. Open the **Failover Cluster Manager**, expand **CLUST01**, select **Networks** then double-click **Cluster Network 2** to open the properties. Verify that Cluster Network 2's subnet is set to **172.16.1.0/24**. In some cases, the cluster deployment may reverse the names it assign to the cluster networks. If your subnet does not match here, then you will simply change the name of the cluster network in the script below to reflect this.
    
    ![The Failover Cluster Manager with Cluster Network 2 properties page open. The subnet is highlighted](images/hands-on-lab/2019-06-19-12-44-26.png "Cluster Network 2 properties")

15. Open an **Administrative** PowerShell ISE session on **CloudShopSQL2**.

16. Copy the following PowerShell script to the script window. If your Cluster Network 2 did not match the 172.16.1.0/24 subnet, then change the $ClusterNetworkName variable to \"Cluster Network 1\". If your subnet did match then you do not need to make this change. Execute the script to configure your cluster for the probe port:

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

4. On the properties window, change the availability mode to **Synchronous commit** and then click **OK**.

    Switching to synchronous commit allows us to failover without data loss. If we leave it in asynchronous mode, the failover would be "forced" which also implies some loss of data and invalidates the old primary replica. Since we are only testing functionality of our listener we DO NOT want to break the Availability Group replication.  

    ![The replica properties window with the availability mode set to synchronous commit.](images/hands-on-lab/2019-03-25-18-59-46.png "Replica properties")

5. Repeat the above for **CloudShopSQL**. This will enable synchronous commits between the two replicas allowing us to failover without breaking the Availability Group replication.

6. Right-click the **AdventureWorks** Availability Group, and choose **Show Dashboard** from the menu.

    ![The AdventureWorks availability group is highlighted with show dashboard selected in the right-click menu.](images/hands-on-lab/2019-03-25-19-16-22.png "Show Dashboard")

7. The dashboard should show both **CloudShopSQL** and **CloudShopSQL2** with a status of **Synchronized**.

    ![The availability group dashboard is shown with the synchronization state of CloudShopSQL and CloudShopSQL2 set to synchronized.](images/hands-on-lab/2019-03-25-19-19-34.png "Dashboard")

8. Right-click the **AdventureWorks** Availability Group again and choose **Failover...** from the menu.

    ![The Adventure Works availability group is highlighted with failover selected in the right-click menu.](images/hands-on-lab/2019-03-25-19-22-06.png "Failover")

9. In the Failover Wizard, click **Next** on the first screen.

10. On the Select New Primary Replica window, select **CloudShopSQL2** and then click **Next**.

    ![The select new primary replica window is shown with CloudShopSQL2.](images/hands-on-lab/2019-03-25-19-25-13.png "Select new primary replica")

11. On the Connect to Replica window, connect to **CloudShopSQL2** with the **CONTOSO\demouser** account and click **Next**.

    ![On the Specify the connection for the secondary replica window, CloudShopSQL2 is highlighted and CONTOSO\demouser is listed under connected as.](images/hands-on-lab/2019-03-25-19-27-50.png "Specify the connection")

12. On the summary window, click **Finish**, then click **Close** on successful completion of the failover.

13. From CloudShopSQL2 or CloudShopSQL3, connect to your **AdventureWorks** listener and open a new Query Window by clicking connect in SSMS and using AdventureWorks for the server name.

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

## Exercise 4: Configure Azure Site Recovery for Web Tier DR 

Duration: 15 minutes

In this exercise, you will configure Azure Site Recovery to protect your web application and allow it to fail to your disaster recovery site.

### Task 1: Create a Recovery Services Vault

1. From the Azure portal, select **+Create a resource** from the menu on the left.

2. In the search box, type **Backup and site recovery** and then select **Backup and site recovery (OMS)** from the results.

3. Click the **Create** button on the information blade.

4. On the Recovery Services vault creation blade, use the following configurations and click **Create**.

    - Name: **RSVault**
    - Subscription: ***Choose your subscription***.
    - Resource group: **CloudShop2**
    - Location: ***Use the same location assigned to CloudShop2***.

        ![The recovery services vault creation blade is show.](images/hands-on-lab/2019-03-25-23-30-11.png "Create the Recovery Services Vault")

### Task 2: Configure Azure Site Recovery

1. In the Azure Portal navigate to the Recovery Services vault you just created.
   
2. Select **Site recovery infrastructure** from the menu on the left and then choose **Network mapping**.

3. Click the **+Add mapping** button. Set your source network to VNET1 and your target network to VNET2.

    ![Network mapping is shown across VNET1 and VNET2.](images/hands-on-lab/2019-03-26-03-20-41.png "Add network mapping")

4. In the Recovery Services vault blade, click on **Site Recovery** under **Getting started**.

    ![The recovery services vault blade with site recovery highlighted.](images/hands-on-lab/2019-03-25-23-46-47.png "Setup Site Recovery")

5. Under **FOR ON-PREMISES MACHINES AND AZURE VMS** click **Step 1: Replicate Application**.

    ![An image that depicts Azure Site Recovery. Step 1: Replicate Application is highlighted.](images/hands-on-lab/2019-03-25-23-48-09.png "Replicate application Settings")

6. Choose the following configurations and click **OK**.

    - Source: **Azure**
    - Source location: ***The location of your CloudShop1 resource group***.
    - Azure virtual machine deployment model: **Resource Manager**
    - Source subscription: ***Your subscription***.
    - Source resource group: **CloudShop1**

        ![An image that depicts Azure Site Recovery settings.](images/hands-on-lab/2019-03-25-23-53-54.png "ASR replicate source settings")

7. On the select virtual machines blade, select the **CloudShopWeb** virtual machine for replication. We will only use Azure Site Recovery to protect the web servers. Click the **OK** button.

    ![CloudShopWeb is highlighted in the select virtual machines window.](images/hands-on-lab/2019-03-26-00-02-35.png "Select virtual machines")

8. On the Configure settings blade, set the Target location to the location of your CloudShop2 resource group. 

9. Click on the **Customize** link.

    ![The configure settings dialogue with the Customize button highlighted.](images/hands-on-lab/2019-03-26-03-26-07.png "Configure settings dialogue")

10. Change the target resource group to **CloudShop2**.

    ![The customize target resources dialogue with default settings.](images/hands-on-lab/2019-03-26-03-27-29.png "Customize target resources")

11. Click **Create target resources**.

    **Note**: Do not close the blade. It will close by itself after the target resources are created (2-3 minutes).

    ![The Configure settings dialogues with the create target resources button highlighted.](images/hands-on-lab/2019-03-26-00-06-44.png "Create target resources")

12. On the Enable Replication blade, click the **enable replication** button. 

13. Several Site Recovery jobs will be initiated which are creating the replication policy as well as the target resources to be used during a failover. Click on **Site recovery jobs** to view the job progress.

    ![The site recovery jobs dialogue showing the automatically generated jobs running.](images/hands-on-lab/2019-03-26-00-52-02.png "Site recovery jobs")

14. Once all the jobs are successful, click on **Replicated items** under **Protected Items** to view the status of the initial replication.

    ![THe replicated items dialogue showing the CloudShop2 replication health as healthy.](images/hands-on-lab/2019-03-26-00-53-16.png "Replicated items")

15. While waiting for the initial replication/synchronization, move on to the next task.

### Task 3: Creating the Recovery Plan

In this task, you will create the recovery plan that will be used to orchestrate failover action for the web tier.

1. Within the properties of the Recovery Services vault, click on **Recovery Plans (Site Recovery)** under Manage.

    ![The manage menu is shown with Recovery Plans (Site Recovery) highlighted.](images/hands-on-lab/2019-03-26-00-58-00.png "Recovery Plans")

2. Click the **+Recovery Plan** button.

3. On the Create recovery plan blade enter the name **CloudShopRP**. In the Source area select the region where your CloudShop1 resource group is deployed. The Target will be automatically selected. Under Allow items with deployment model, select **Resource Manager**. Click Select items and select the Virtual Machine. Click **OK** and then click **OK** on the Create recovery plan blade.

    ![Create recovery plan settings](images/hands-on-lab/2019-03-26-01-09-00.png "ASR Recovery Plan settings")

4. After a minute or two, you should see the CloudShopRP on the Recovery plans blade. This recovery plan will bring up your web server during a failover. 

5. Now go back to the Recovery Services vault RSVault - Overview blade. Click on Site Recovery. Notice that the CloudShopWeb virtual machine is replicating. Take note of the status. It should be close to 100%. You will not be able to continue until it is finished replicating. 

   >**Note**: This may take up to an hour.

    ![The recovery services vault overview with site recovery selected.](images/hands-on-lab/2019-03-26-02-15-27.png "RSVault - Site Recovery")

## Exercise 5: Failing Over to the Disaster Recovery Site 

Duration: 30 minutes

In this exercise, you will fail over to your disaster recovery site.

### Task 1: Failover the Data Tier

1. Login to your CloudShopSQL virtual machine and launch SQL Server Management Studio.

2. Open a connection to your CloudShopSQL instance, expand Always On High Availability, Availability Replicas, then right-click **CloudShopSQL2** and choose **Properties**.

    ![The CloudShop2 availability replica is selected and the properties is highlighted.](images/hands-on-lab/2019-03-25-18-55-26.png "CloudShop2 properties")

3. On the properties window, change the availability mode to **Synchronous commit** and then click **OK**. Note that in a real outage scenario it may not be possible to use this method and may require a forced failover which may incur some data loss.  

    ![The properties of CloudShop2 are shown with the availability mode set to synchronous commit.](images/hands-on-lab/2019-03-25-18-59-46.png "Set CloudShop2 to synchronous commit")

4. Repeat the above for **CloudShopSQL**. This will enable synchronous commits between the two replicas allowing us to failover without breaking the Availability Group replication.

5. Right-click the **AdventureWorks** Availability Group and choose **Failover...** from the menu.

    ![The AdventureWorks availability group is highlighted and failover is selected from the right-click menu.](images/hands-on-lab/2019-03-25-19-22-06.png "Failover")

6. In the Failover Wizard, click **Next** on the first screen.

7. On the Select New Primary Replica window, select **CloudShopSQL2** and then click **Next**.

    ![The select new primary window is shown with CloudShopSQL2 highlighted.](images/hands-on-lab/2019-03-25-19-25-13.png "Select New Primary Replica")

8. On the Connect to Replica window, connect to **CloudShopSQL2** with the **CONTOSO\demouser** account and click **Next**.

    ![The specify the connection window is shown with CONTOSO\demouser set as the username.](images/hands-on-lab/2019-03-25-19-27-50.png "Connect to replica")

9. On the summary window, click **Finish**, then click **Close** on successful completion of the failover.

### Task 2: Failover the Web Tier

To fail over the entire environment requires the database server to be up, then the web server may come online. After successfully failing over the data tier in the previous step we will now failover the web tier via ASR. 

1. From the Azure portal, navigate to your **RSvault** resource.

2. Select **Recovery Plans (Site Recovery)** from the menu on the left.

    ![Recovery Plans Site Recovery is highted on the Manage menu.](images/hands-on-lab/2019-03-26-03-01-20.png "Recovery services vault")

3. Select your **CloudShopRP** recovery plan.

4. On the CloudShopRP recovery plan blade, click the **...More** button, and then select **Failover** from the dropdown menu.

    ![The CloudShopRP recovery plan blade is shown and the **...More** button is highlighted, and failover is highlighted from the dropdown menu](images/hands-on-lab/2019-03-26-03-04-07.png "CloudShopRP recovery plan")

5. Check the box next to **I understand the risk**.

6. Verify your failover settings and click **OK**.

    ![Failover window with recovery point set to latest processed, and the shutdown machines checkbox checked.](images/hands-on-lab/2019-03-26-04-06-02.png "Verify failover")

7. You can monitor the status of your failover by selecting **Site recovery jobs** from the monitoring section of the menu on the left.

8. After the failover completes, navigate to the **CloudShop2** resource group and open your **CloudShopWeb** virtual machine.

9. Notice that it does not have a Public IP address. Click on **Networking** on the menu to the left.

    ![CloudShopWeb virtual machine overview with missing public IP highlighted.](images/hands-on-lab/2019-03-26-04-24-41.png "Missing public IP")

10. Select the **CloudShopWebNetworkInterface**.

    ![The CloudShopWebNetworkInterface is shown.](images/hands-on-lab/2019-03-26-04-26-18.png "CloudShopWebNetworkInterface")

11. Select **IP Configurations** from the menu.

    ![The network interface overview with IP configurations selected from the menu on the left.](images/hands-on-lab/2019-03-26-04-28-08.png "Network Interface overview")

12. Change the **Public IP address settings** to **Enabled**, then create a new public IP address called **webpubip**. Click **OK** and **Save**.

    ![The CloudShopWeb network interface settings are shown with the public IP address being enabled.](images/hands-on-lab/2019-03-26-04-30-12.png "Update Public IP settings")

13. Open a command prompt and issue an **iisreset** to restart the web service.

### Task 3: Validate Failover of the CloudShop application 

1. From the Azure portal, navigate to the **CloudShopWeb** virtual machine.   

2. Copy the **Public IP address** and paste it into a browser to verify that your site is working.

## After the hands-on lab

### Task 1: Delete the resource groups created

1. Within the Azure portal, click Resource Groups on the left navigation.

2. Delete each of the resource groups created in this lab by clicking them followed by clicking the Delete Resource Group button. You will need to confirm the name of the resource group to delete.

You should follow all steps provided *after* attending the hands-on lab.
