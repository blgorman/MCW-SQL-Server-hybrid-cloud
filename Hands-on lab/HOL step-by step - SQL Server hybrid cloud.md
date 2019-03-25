![](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
SQL Server Hybrid Cloud
</div>

<div class="MCWHeader2">
Hands-on lab step-by-step
</div>

<div class="MCWHeader3">
March 2019
</div>


Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.
© 2018 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

**Contents**

<!-- TOC -->

- [SQL Server Hybrid Cloud hands-on lab step-by-step](#sql-server-hybrid-cloud-hands-on-lab-step-by-step)
  - [Abstract and learning objectives](#abstract-and-learning-objectives)
  - [Overview](#overview)
  - [Solution architecture](#solution-architecture)
  - [Requirements](#requirements)
    - [Help references](#help-references)
  - [Exercise 1: SQL Backup solution](#exercise-1-sql-backup-solution)
    - [Task 1: Create an Azure Storage Account](#task-1-create-an-azure-storage-account)
    - [Task 2: Configure managed backup in SQL Server](#task-2-configure-managed-backup-in-sql-server)
  - [Exercise 2: Implement a Data Archive Strategy with SQL Server Stretch Database](#exercise-2-implement-a-data-archive-strategy-with-sql-server-stretch-database)
    - [Task 1: Crete a logical SQL Server to host Stretch DB](#task-1-crete-a-logical-sql-server-to-host-stretch-db)
    - [Task 2: Identify tables that may benefit from Stretch DB](#task-2-identify-tables-that-may-benefit-from-stretch-db)
    - [Task 3: Implement Stretch DB based on date key](#task-3-implement-stretch-db-based-on-date-key)
  - [Summary](#summary)
  - [Exercise 3: Build SQL Availability Group for Database Disaster Recovery](#exercise-3-build-sql-availability-group-for-database-disaster-recovery)
    - [Task 1: Create the cluster](#task-1-create-the-cluster)
    - [Task 2: Create the SQL Server Availability Group](#task-2-create-the-sql-server-availability-group)
    - [Task 3: Create the Internal Load Balancer](#task-3-create-the-internal-load-balancer)
    - [Summary](#summary-1)
  - [Exercise 4: Configure Azure Site Recovery to Web Tier DR](#exercise-4-configure-azure-site-recovery-to-web-tier-dr)
    - [Task 1: Site Recovery Stuff](#task-1-site-recovery-stuff)
  - [Exercise 5: Validate resiliency](#exercise-5-validate-resiliency)
    - [Task 1: Validate resiliency for the CloudShop application](#task-1-validate-resiliency-for-the-cloudshop-application)
    - [Task 2: Validate SQL Always On](#task-2-validate-sql-always-on)
    - [Task 3: Validate backups are taken](#task-3-validate-backups-are-taken)
  - [Exercise 6: Implementing Azure Site Recovery](#exercise-6-implementing-azure-site-recovery)
    - [Task 1: Configure ASR Protection for Cloud Shop](#task-1-configure-asr-protection-for-cloud-shop)
    - [Task 2: Creating the Recovery Plan](#task-2-creating-the-recovery-plan)
    - [Task 3: Creating the Test Fail Over.](#task-3-creating-the-test-fail-over)
    - [Task 4: Cleaning the Test Fail Over.](#task-4-cleaning-the-test-fail-over)
  - [After the hands-on lab](#after-the-hands-on-lab)
    - [Task 1: Delete the resource groups created](#task-1-delete-the-resource-groups-created)

<!-- /TOC -->

# SQL Server Hybrid Cloud hands-on lab step-by-step 

## Abstract and learning objectives 

In this hands-on lab, you will build a disaster recovery site for an on-premises environment. You will enhance the existing database solution to support a hybrid cloud-based disaster recovery solution, implement an archiving strategy and a backup/restore strategy designed to protect data.

At the end of the lab, you will be better able to design and use availability sets, Managed Disks, SQL Server Always on Availability Groups, Stretch DB and SQL Server Managed Backup.

## Overview

Contoso has asked you to deploy their infrastructure in a resilient manner to insure their infrastructure will be available for their users and gain an SLA from Microsoft.

## Solution architecture

Cloud based disaster recovery site.

    need new diagram

## Requirements

1.  Microsoft Azure Subscription

2.  Virtual Machine Built during this hands-on lab or local machine with the following:

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

Backups must be maintained offsite from the on-premises environment. The backups must be online and accessible by the DBA team. To accomplish this you will configure SQL Managed Backup.

### Task 1: Create an Azure Storage Account

In this task, you will create an Azure Storage Account for use with SQL Managed Backup.

1.  Connect to your CloudShopSQL virtual machine by navigating to your **CloudShop1** resource group and then connecting to the **CloudShopSQL** virtual machine.

2.  Login with user name **demouser** and password **demo@pass123**.

3.  Launch **Server Manager**, select **Local Server** from the menu on the left and verify that **IE Enhanced Security Configuration** is set to **Off**.

    ![Server manager application with local server selected and IE Enhanced Security Configuration set to Off.](images/hands-on-lab/2019-03-24-18-38-33.png "Server Manager local server configuration")

4.  From within your SQL Server guest virtual machine, install Azure PowerShell by launching an **administrative PowerShell ISE session** and running the following command. Accept any warnings or authorization to install the components.

    ```
    Install-Module -Name AzureRM -AllowClobber
    ```

5.  From the PowerShell ISE, type the following at the prompt and follow the prompts to login to your Azure subscription.

    ```
    login-AzureRmAccount
    ```

6.  Execute the following PowerShell commands in the PowerShell ISE to create a new storage account and generate the T-SQL needed to configure managed backup for the database. Before executing the script, change the **$storageAcctName** variable to a unique name and change the location to match the location you are deploying into for this lab.

    ```powershell
    $storageAcctName = "[unique storage account name]"

    $resourceGroupName = "CloudShop2"
    $containerName= "backups"
    $location = "[choose the region you are using for this lab]"
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

7.  Execute the code using PowerShell ISE. Make sure you change the **\$storageAcctName = \"\[unique storage account name\]\"** field to a unique storage account name across Azure prior to execution. 

    >**Note**: Due to differences between markdown and PowerShell formatting requirements, you may need to remove the white space prior to the **\"\@** near the end of the file.

8.  Save the T-SQL code generated between the **Begin TSQL Script** and **End TSQL Script** in your PowerShell ISE output after execution into a notepad file. This code creates an identity using a Shared Access Signature (SAS) to a container in the storage account and configures managed backup when executed.

### Task 2: Configure managed backup in SQL Server

1.  From within your SQL Server virtual machine, launch SQL Server Management Studio and connect to the local database instance.

2.  From within SQL Server Management Studio, click **New Query**.

    ![A screen showing how to launch the new query pane in SQL Server Management Studio.](images/hands-on-lab/2019-03-20-11-08-08.png "Launching the new query pane")

3.  Refresh SQL Server Management Studio and if SQL Server Agent is stopped right click on it and click Start.

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

5.  Paste the T-SQL code you copied at the end of the previous task into the query window replacing the existing code and click **Execute**. This code creates the new SQL identity with a Shared Access Signature for your storage account. 

6.  Paste the code below into the query window replacing the existing code and click **Execute** to create a custom backup schedule.

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
7.  Execute the following tSQL in the query window to generate a backup on-demand. You can also specify Log for \@type.

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

1.  From your **CloudShopSQL** virtual machine, launch a browser, navigate to the Azure portal and login with your Azure credentials.

2.  Click **+Create a resource** at the top of the left side menu.

3.  Type **SQL Server logical server** into the search box, hit enter and choose **SQL server (logical server)** from the search results.
    ![Azure Marketplace search for SQL Server logical server](images/hands-on-lab/2019-03-20-11-38-42.png "Search for SQL Server logical server")

4. Click **Create** on the SQL Server (logical server) information blade.
5. On the SQL Server (logical server) blade, use the following configurations and click **Create**.

    - Server name: ***Choose a unique server name***
    - Server admin login: **demouser**
    - Password: **demo@pass123**
    - Subscription: ***Your subscription***
    - Resource group: **CloudShop2**
    - Location: ***Use the same location you have been using***
    - Allow Azure Services: ***Checked***

        ![The SQL Server logical server creation blade with the configurations listed previously set to the correct values.](images/hands-on-lab/2019-03-24-19-31-07.png "Create a SQL Server logical server")

6. Make note of the server name you chose above. It will be used in a later task.

7. After the SQL Server logical server has deployed, navigate to it and select **Firewalls and virtual networks** from the security menu on the left.

    ![Security menu with Firewalls and virtual networks highlighted.](images/hands-on-lab/2019-03-20-11-56-21.png "Security menu")

8. Add a new firewall rule by clicking **+Add client IP** and then clicking **Save**.

    ![](images/hands-on-lab/2019-03-20-12-01-55.png "Firewall configuration")

### Task 2: Identify tables that may benefit from Stretch DB 

1.  From your **CloudShopSQL** virtual machine, launch SQL Server Management Studio if it is not already open.

2.  In the SQL Server Management Studio Object Explorer, connect to your local SQL Server instance and expand the Databases folder.

3.  Right-click the AdventureWorks database, select Tasks, select Stretch, then choose Enable.

    ![SQL Server Management Studio Object Explorer with the Smart Hotel right-click menu open, tasks is highlighted then stretch is highlighted and Enable is selected.](images/hands-on-lab/2019-03-24-19-38-01.png "Enable Stretch Database")

4.  The Enable Database for Stretch wizard should open automatically. Click **Next** on the Introduction screen.

5.  On the Select tables window all of your tables will be listed. Notice that some tables have warnings and some may be greyed out. Click on the warning icon next to one of the tables. This indicates that the table does not meet the StretchDB eligibility requirements.

    ![Stretch Database Wizard showing error for a table](images/hands-on-lab/2019-03-24-19-40-15.png "Stretch Database wizard")

6.  Scroll to the right until you see the Migrate column. Clicking Entire Table on an eligible table allows you to define which rows will be migrated to Azure. In this lab, we are going to configure Stretch Database through TSQL. Click Cancel to break out of the wizard.

### Task 3: Implement Stretch DB based on date key  

1.  Launch a new Query tab and execute the following code to prepare the server and the database for Stretch Database. 

    ```
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

2.  Execute the following code replacing the server name with the name of the SQL Server you created in exercise 1. This code enables Stretch Database on your database. It may take a few minutes to complete.

    ```
    --Enable the local database for stretch
    --You must change your server name to match your environment
    ALTER DATABASE [AdventureWorks]
    SET REMOTE_DATA_ARCHIVE = ON (SERVER = N'<your server name>.database.windows.net', CREDENTIAL = StretchDB )
    ```

3.  Execute the following code to create the stretch predicate. The stretch predicate allows us to define the conditions under which a row will be stretched. In this case the function leverages a date key column to stretch any rows prior to January 1, 2002. 

    ```
    --Create the stretch predicate
    CREATE FUNCTION dbo.fn_stretch_datekey_predicate(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING 
    AS 
    RETURN	SELECT 1 AS is_eligible
		    WHERE @column1 < '20020101'
    GO
    ```

4. Execute the following to begin the migration of eligible data to your Azure SQL Database.

    ```
    --Enable stretch on the table using the stretch predicate with the OrderDateKey
    ALTER TABLE Sales.ResellerSales 
    SET ( REMOTE_DATA_ARCHIVE = ON (
	    FILTER_PREDICATE = dbo.fn_stretch_datekey_predicate([OrderDateKey]),
	    MIGRATION_STATE = OUTBOUND
    ) )
    ```

5. Refresh the AdventureWorks database in Object Explorer by right clicking it and choosing Refresh.

6. Right-click the AdventureWorks database, choose Tasks, Stretch, then select Monitor

7. View the stretch database report. Note that over time the Eligible Rows, Local Rows, and Rows In Azure numbers will change as data is migrated to your Stretch Database in Azure.

    ![](images/hands-on-lab/2019-03-20-12-21-30.png)

8.	Click the dropdown under Migration State. Notice that you have the option to pause the outbound migration.

9.	Launch a new Query tab and execute the following code to programatically monitor space used in Azure and locally.

    ```
    sp_spaceused 'Sales.ResellerSales', @mode = 'REMOTE_ONLY'
    GO
    sp_spaceused 'Sales.ResellerSales', @mode = 'LOCAL_ONLY'
    GO
    ```
10. You may also change the scope of a query.

    ```
    SELECT COUNT(*) FROM [Sales].[ResellerSales] WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = REMOTE_ONLY)
    ```

## Summary
In this exercise, you implemented an archive solution with Stretch Database. First, you reviewed the table compatibility by using the Enable Database for Stretch wizard. You then configured your database and table via T-SQL to archive data satisfied by a stretch predicate. Finally, you reviewed the progress, status and space used locally and in Azure via the built-in management tools

## Exercise 3: Build SQL Availability Group for Database Disaster Recovery

Duration: 60 minutes

In this exercise, you will build a hybrid-cloud SQL Always-On Cluster for resiliency of the data tier.

### Task 1: Create the cluster

In this task, you will create the underlying Windows Failover Cluster which is the base of your SQL Server Availability Group. You will begin by logging in with an account with administrative privileges on all of the prospective nodes of the cluster, and running a Failover Cluster Validation test. This test verifies that all of the nodes of your cluster are properly configured to support clustering and will flag any best practices that you may not be adhering to in your configuration. Finally, you will create the cluster across the three nodes of you cluster.

1.  Launch a browser and navigate to <https://portal.azure.com>. Login 
    with your Azure credentials if you haven't already.

    > Note: You may need to launch an \"in-private\" session in your
browser if you have multiple Microsoft Accounts.

2.  Navigate to your **CloudShop1** resource group and open your **CloudShopSQL** virtual machine and connect to it via Remote Desktop.

3.  Login with the **CONTOSO\demouser** domain account with password **demo@pass123**. Note that this is NOT the same account that you have been using, this is a domain account.

4.  Launch Server Manager if it does not start automatically. 

5.  Select **Local Server** from the menu on the left and scroll down to **Roles and Features**, click the **Tasks** dropdown and select **Add Roles and Features** from the list.

    ![Server Administrator Application with local server selected, and add roles and features selected in the tasks dropdown of the Roles and Features section.](images/hands-on-lab/2019-03-20-17-33-21.png "Adding Roles and Features")

6.  On the **Before you begin** page, click **Next**.

7.  On the **Select installation type** page, select **Role-based or feature-based installation** and click **Next**.

8.  On the **Select destination server** page, select the current server and click **Next**.

9.  On the **Select server roles** page, accept the defaults and click **Next**.

10. On the **Select features** page, select **Failover Clustering**, then click **Add Features** on the popup and then click **Next**.

    ![The add roles and features wizard with Failover Clustering selected on the select features page.](images/hands-on-lab/2019-03-24-20-03-47.png "Add Failover Clustering")

11. On the **Confirm installation selections** page, click **Install**.

12. Repeat the above steps to install Failover Clustering on your remainging SQL Servers (CloudShopSQL2 and CloudShopSQL3).

13. From CloudShopSQL, open **Administrative Tools** and then launch **Failover Cluster Manager**.

14. From the Actions pane on the right, choose **Validate Configuration**.

    ![The Failover Cluster Manager with Validate Configuration highlighted.](images/hands-on-lab/2019-03-24-20-17-48.png "Failover Cluster Manager")

15. Click **Next** on the Before You Begin window.

16. On the Select Servers or a Cluster window, enter **CloudShopSQL** and then click the **Add** button. Repeat for **CloudShopSQL2** and **CloudShopSQL3** then click **Next**.

    ![The select servers or a cluster window with CloudShopSQL, CloudShopSQL2 and CloudShopSQL3 added to the selected servers.](images/hands-on-lab/2019-03-24-20-23-55.png "Validate a Configuration Wizard")

16. On the Testing Options window, select the **Run only tests I select** radio button and click **Next**

    ![The testing options window with run only tests I select selected](images/hands-on-lab/2019-03-24-20-31-41.png "Validate a Configuration Wizard")

17. On the Test selection window, uncheck storage. The storage tests have no relevance for our case and will only throw additional warnings.

    ![The test selection window with the storage tests unchecked.](images/hands-on-lab/2019-03-24-20-34-43.png "Validate a Configuration Wizard")

18. On the Confirmation window, click **Next** to begin the validation tests.

19. Review the output of the tests by clicking the **View Report** button. This validation test aids you in determining if your server and network configuration is correctly configured for Windows Failover Clustering. You should review any warnings or errors. In our case, the environment was preconfigured to support clustering so there should be no errors. There may be warnings regarding best practices such as multiple network cards etc. In a production environment these warnings must be taken seriously as they may adversely effect the performance and reliability of the cluster. In our lab they will have a negligible impact.

20. Click **Finish** on the Validate a Configuration Wizard.

21. On CloudShopSQL, open the PowerShell ISE and execute the following code:

    ```
    New-Cluster -Name CLUST01 -Node CloudShopSQL, CloudShopSQL2, CloudShopSQl3 -StaticAddress 10.0.1.7, 172.16.1.7  -NoStorage
    ```
    > **Note**: This command requires that you are logged in with an account that is an administrator on all nodes specified. For this lab you should use **CONTOSO\demouser**.

    This will create a three node cluster with two static IP addresses. We need two static IP addresses because the cluster may be running from our on-premises environment (CloudShop1) or our cloud disaster recovery environment (CloudShop2).  
    
    It is also possible to use a wizard for this task but the resulting cluster will require additional configuration to set the static IP addresses to be viable in Azure. This is due to the manner in which Azure distributes IP addresses causing the cluster to receive the same IP address as the node it is executing on resulting in a duplicate IP address and failure of the cluster service.

22. Open the Failover Cluster Manager on CloudShopSQL from the start menu under Winows Administrative Tools. In the right navigation right-click **Failover Cluster Manager** and click **Connect to Cluster**. Click OK in the pop-up. Expand the **CLUST01** cluster, select **Nodes**, validate that all nodes are online and that Assigned Vote and Current Vote are 1 for all nodes of the cluster.

    ![Failover Cluster Manager with CLUST01 selected, Nodes selected, and all three nodes of the cluster showing online with 1 assigned and 1 current vote.](images/hands-on-lab/2019-03-24-21-22-18.png "Nodes view of Failover Cluster Manager")

### Task 2: Create the SQL Server Availability Group

1.  On CloudShopSQL, launch **SQL Server 2017 Configuration Manager**.

2.  Select **SQL Server Services**, then right-click **SQL Server
    (MSSQLSERVER)** and select **Properties**.

    ![SQL Server Configuration Manager with SQL Server Services selected and SQL Server service highlighted.](images/hands-on-lab/2019-03-24-21-31-48.png "SQL Server Configuration Manager")

3.  Click on the **Log On** tab. To setup a SQL Server Availability
    Group, the SQL Server service account needs to have access to all of
    the SQL Servers that will participate in the Availability Group.
    Change the service account to the **CONTOSO\\demouser** domain
    account using **demo@pass123** for the password.

    ![SQL Server Properties is shown with the account set to the CONTOSO\demouser domain account.](images/hands-on-lab/2019-03-24-21-35-08.png "SQL Server Login Properties")

4.  Select the **AlwaysOn High Availability** tab and check the box for
    **Enable AlwaysOn Availability Groups**, then click **OK**, then
    click **Yes** to confirm the account change.

    ![SQL Server Properties is shown with Enable AlwaysOn High Availability Groups selected.](images/hands-on-lab/2019-03-24-21-37-08.png "SQL Server AlwaysOn High Availability Properties")

5.  Restart the SQL Server service by right-clicking the service and choosing **Restart**.

    ![SQL Server Configuration Manager with SQL Server Services selected and the SQL Server service being restarted.](images/hands-on-lab/2019-03-24-21-40-59.png "Restart the SQL Server service")

6.  Repeat this configuration for **CloudShopSQL2** and **CloudShopSQL3**.

7.  The database you will be replicating has its data and log files in the
    C:\\Data folder and the C:\\Log folder on CloudShopSQL. We need to verify that the same folder
    structure exists on our secondary servers, CloudShopSQL2 and CloudShopSQL3. Login to **CloudShopSQL2** and **CloudShopSQL3**
    and verify that these folders exist. If they do not exist, create them.

8.  Open a remote desktop connection to the CloudShopSQL virtual machine and
    login using the **CONTOSO\\demouser** account. Note that this
    demouser account is the domain administrator account. You must type
    the domain name in, otherwise you will be logged in with the local
    administrator account.

9.   Your SQL Server Availability Group will require a file share to be
    used for the initial synchronization. Open up File Explorer and
    create a new folder in the root of the C: drive called **AG**.

10. Right-click the new folder and select **Share with \> Specific
    people...**
    
    ![File Explorer is shown with the right-click menu of the AG folder with Share with specific people highlighted.](images/hands-on-lab/2019-03-24-21-57-15.png "File Explorer")

11. On the File Sharing window, select **Administrators** and click
    **Share**, then click **Done**.

12. Launch **SQL Server Management Studio** and connect to
    **CloudShopSQL**.

13. Expand the databases folder, right-click the **AdventureWorks** database and select **Properties**.

14. On the Database Properties window, select **Options** then set the recovery model to **Full** and click the **OK** button. The full recovery model is required for databases that participate in SQL Server Always On Availability Groups.

    ![Database properties with the recovery model option set to full.](images/hands-on-lab/2019-03-24-22-31-59.png "Set the database recovery model to full")

15. Right-click the **AdventureWorks** database, select **Tasks** and choose **Back Up...**.

    ![The AdventureWorks database is selected, then tasks is selected and backup is selected.](images/hands-on-lab/2019-03-24-22-39-23.png "Backup the database")

16. On the Back Up Database - AdventureWorks window, choose to backup to disk, click **Add**, and set the destination to **C:\\Data\\AdventureWorks.bak**, then click **OK**.

    ![Backup destination is selected from the backup wizard.](images/hands-on-lab/2019-03-24-22-46-39.png "Backup the database")

17. From the SQL Server Management Studio CloudShopSQL connection, right-click **AlwaysOn High Availability** then select **New Availability Group Wizard...**
    
    ![SQL Server Management Studio with the CloudShopSQL instance shown, the Always On High Availability folder highlighted with the New Availability Group Wizard selected.](images/hands-on-lab/2019-03-24-22-21-28.png "Launch the New Availability Group Wizard")

18. Click **Next** on the Introduction screen.

19. Specify **AdventureWorks** for the name of the availability group
    and click **Next**.

    ![Specify the availability group options is shown with the availability group name set to AdventureWorks.](images/hands-on-lab/2019-03-24-22-24-47.png "Specify the name of the availability group")

20. Double-click the password field next to the **AdventureWorks** database and enter the password **demo@pass123** and click **Refresh**. The status of the
    database should state that it \"Meets prerequisites\" meaning that the database is in Full recovery mode and that you have supplied the correct password for the database master key. Click **Next**.

    ![AdventureWorks database is selected, the status shows that it meets prerequisites and the password has been filled in.](images/hands-on-lab/2019-03-24-23-02-02.png "Select database")

21. On the Specify Replicas tab, click the **Add Replica...** button and connect
    to CloudShopSQL2 and CloudShopSQL3.

    ![The Specify Replicas window is shown, the add replica button has been clicked and the connection shows a new connection to CloudShopSQL being established.](images/hands-on-lab/2019-03-24-23-05-41.png "Specify Replicas")

22. Click on the **Listener** tab, select the **Create an availability
    group listener** radio button, set the following values for the
    listener, then click **Add**.

    - Listener DNS Name: **AdventureWorks**

    - Port: **1433**

    - Network Mode: **Static IP**

        ![The specify replicas listener tab is selected, create an availability group listener is selected with the correct values entered.](images/hands-on-lab/2019-03-24-23-09-42.png "Specify replicas listener tab")

23. You will need to add two IP addresses for the listener, for the first use **10.0.1.8** and for the other use 172.16.1.8, then click
    **Next**.

    ![The listener ip addresses are now shown in the specify replicas window.](images/hands-on-lab/2019-03-24-23-15-53.png "Specifying listener ip addresses")

24. Ensure that **Full database and log backup** is selected and use **\\\\CloudShopSQL\\ag** for
    the network share. Click **Next** to continue.

    ![Select the full database and log backup is chosen with the AG fileshare used as the shared backup location.](images/hands-on-lab/2019-03-24-23-19-12.png "Select initial data synchronization")

25. The validation tests will run automatically. They should all show
    success. Click **Next**.

    ![The validations window is shown with all validation marked as successful.](images/hands-on-lab/2019-03-24-23-21-45.png "Validation")

26. Verify your configuration then click **Finish** to build the
    Availability Group. Click **Close** after the wizard completes.

### Task 3: Create the Internal Load Balancer

1.  In the Azure Portal, navigate to the **CloudShop2** resource group
    and click **Add**.

    ![](Exercise4images/media/image1.png)

2.  Type **Load Balancer** into the search bar then choose the **Load
    Balancer** and click the **Create** button.

    ![](Exercise4images/media/image2.png)

3.  On the Create load balancer blade, configure the following options,
    then click **Create**:

    - Name: **sqlag**
    - Type: **Internal**
    - Virtual network: **OpsTrainingVNET**
    - Subnet: **Data**
    - IP address assignment: **Static**
    - Private IP address: **10.0.0.8**
    - Subscription: ***\<the subscription you are using for this lab\>***
    - Resource group: **OpsVMRmRG**
    - Location: ***\<same location you have used for this lab\>***

4.  Navigate back to your resource group and select the **sqlag** load
    balancer you just created.

    ![](Exercise4images/media/image3.png)

5.  On the sqlag load balancer blade, select **Backend Pools**, then
    click the +Add button.

    ![](Exercise4images/media/image4.png)

6.  On the Add backend pool blade, use the following configurations:

    - Name: **sqlpool**
    - IP version: **IPv4** 
    - Associated to: **Availability set**
    - Availability set: **SQLAvSet**

        ![](Exercise4images/media/image5.png)

7.  Click the **+ Add a target network IP configuration** button.

    ![](Exercise4images/media/image6.png)

8.  Select **CloudShopSQL** for the Target virtual machine, and select
    **ipconfig1** (10.0.1.4) for the Network IP configuration.

    ![](Exercise4images/media/image7.png)

9.  Repeat steps 7-8 to add **CloudShopSQL2**. DO NOT ADD THE WITNESSVM to
    the backend pool. Click OK then wait for the load balancer to
    complete the update.

10. On the sqlag load balancer blade, select **Health** **Probes** and
    click the **+Add** button.

    ![](Exercise4images/media/image8.png)

11. On the Add health probe blade, use the following configuration
    values then click **OK** and wait for the load balancer to complete
    the update.

    - Name: **sqlprobe** 
    - Protocol: **TCP** 
    - Port: **59999** 
    - Interval: **5** 
    - Unhealthy threshold: **2** 

        ![](Exercise4images/media/image9.png)

12. On the sqlag load balancer blade, select **Load balancing rules**
    and then click the **+Add** button.

    ![](Exercise4images/media/image10.png)

13. On the Add load balancing rule blade, use the following
    configuration values then click **OK** and wait for the load
    balancer to complete the update.

    - Name: **sqlrules**
    - Frontend IP address: **10.0.0.8**
    - Protocol: **TCP**
    - Port: **1433**
    - Backend port: **1433**
    - Backend pool: **sqlpool**
    - Probe: **sqlprobe**
    - Session persistence: **None**
    - Idle timeout: **4**
    - Floating IP: **Enabled**

        ![](Exercise4images/media/image11.png)

14. Open an Administrative PowerShell ISE session on CloudShopSQL2.

    ![](Exercise4images/media/image12.png)

15. Copy the following PowerShell script to the script window and
    execute it to configure your cluster for the probe port.

    ```
    $ClusterNetworkName = "Cluster Network 1" 

    $IPResourceName = "AdventureWorks_10.0.0.8" 

    $ILBIP = "10.0.0.8" 

    Import-Module FailoverClusters

    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Stop-ClusterResource -Name "AdventureWorks_10.0.0.8"

    Start-ClusterResource -Name "AdventureWorks"
    ```

16. Connect to **CloudShopSQL** and launch SQL Server Management Studio.

17. Open a Server connection to the **adventureworks** listener endpoint to verify connectivity.

    ![](Exercise4images/media/image13.png)

### Summary

In this exercise, you deployed resilient web servers behind a load balancer, and a SQL Always-On Availability Group for database resiliency through an ARM template. Also you deployed resilient web tier with an external load balancer through an ARM template.

## Exercise 4: Configure Azure Site Recovery to Web Tier DR 

Duration: 15 minutes

In this exercise, you will configure SQL Server Managed Backup to back up to an Azure Storage Account.

### Task 1: Site Recovery Stuff
  
## Exercise 5: Validate resiliency

### Task 1: Validate resiliency for the CloudShop application 

1.  In the Azure portal, open the **CloudShopRG** resource group. Click the Load Balancer, **OPSLB**.

2.  Click the **Overview** tab and copy the public IP address to the clipboard, and navigate to it in a different browser tab.

3.  After the application is loaded, click the Spike CPU button to simulate an auto scale event.

    ![A screen that shows the web page that allows for spiking the CPU,.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image105.png "CPU Spike Demo")

4.  After 15-20 minutes, refresh the browser and you will see cloud shop site is running and switched from one web server to another one. 

### Task 2: Validate SQL Always On

1.  Within the Azure portal, click on Virtual Machines and open **SQL0** Click **Stop** at the top of the blade to shut the virtual machine off.

2.  After the VM is deallocated, refresh the CloudShop application in your browser. If the page loads with data in the dropdown list SQL has successfully failed over the primary node to the secondary. You can login to the secondary vm (SQL1) and connect via SQL Server Management Studio to confirm.

### Task 3: Validate backups are taken 

1.  In the Azure portal, click All Services and search for Recovery Vault. Click the link and you should see the two recovery vaults created as part of the deployment of the Active Directory domain controllers.

2.  Open each vault and validate that a backup of the VM has occurred.

    ![The screen shows 2 backup items from one of the vaults.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image109.png "Usage")

    >**Note**: Backup storage consumption may be 0 B if a backup has not occurred. The timing of backups is driven by the policy associated with the backup. Only one policy can be assigned to a virtual machine when using the Azure Backup Extension for Virtual Machines.

3.  To validate the SQL Server backup, open the Storage Account created earlier in the Azure portal and click **Blobs** -\> and then **backups**. If the backup has already completed, you will see the backup file in the container.

    ![An image that depicts SQL Server backup data in an Azure Storage Account.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image110.png "Backup files in storage")

## Exercise 6: Implementing Azure Site Recovery 

### Task 1: Configure ASR Protection for Cloud Shop

1. In the Azure Portal - Resource Group - BackupVault2RG and open the BackupVault2.
   
2. In the **BackupVault2** blade, click on **Site Recovery** under **GETTING STARTED**.
   
3. Under **FOR ON-PREMISES MACHINES AND AZURE VMS** click **Step 1: Replicate Application**.

    ![An image that depicts Azure Site Recovery. An arrow points to Step 1: Replicate Application.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image03.png "Replicate application Settings")

4. On Step 1 Source, under Source Location choose the azure region where your Cloud Shop deployment exists. Then under Source resource group select the resource group where your Cloud Shop deployment exists (e.g. "CloudShopRG"). Select **Resource Manager** as Azure VM Deployment Model. Click OK.

    ![An image that depicts Azure Site Recovery settings.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image04.png "ASR replicate source settings")

5. On Step 2, Select the Virtual Machines (Web and SQL Servers) for the replication.

6. On the Configure settings blade, notice that you can alter the target resource group and virtual network settings, along with the replication policy. Click **Create target resources**. 

    **Note**: Do not close the blade. It will close by itself after the target resources are created (2-3 minutes).

7. Several Site Recovery jobs will be initiated which are creating the replication policy as well as the target resources to be used during a failover. 

8. If you click on the Enable replication job, you can see additional details of what takes place when protecting a VM. It may take up to 30 minutes to complete the job. You can review it under Monitoring - Site Recovery Jobs at the Recovery Services Vault BackupVault2 blade. 
   
    ![An image that depicts Azure Site Recovery settings.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image05.png "ASR replicate source settings")

9. Once all the Enable replication jobs are successful, click on **Replicated items** under **Protected Items** to view the status of the initial replication.
    
10. While waiting for the initial replication/synchronization, move on to the next task.

### Task 2: Creating the Recovery Plan

In this task, you will create the recovery plan that will be used to orchestrate failover actions, such as the order in which failed-over VMs are powered on.

1. Within the properties of the Recovery Services vault, click on **Recovery Plans (Site Recovery)** under Manage. Click on Step 2: Manage Recovery Plans.

    ![An image that depicts Azure Recovery Plan for Site Recovery.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image06.png "ASR Recovery Plan")

2. On the Create recovery plan blade enter the name CloudShopRP. In the Source area select the region where you deployed Cloud Shop. The Target will be automatically selected. Under Allow items with deployment model, select Resource Manager. Click Select items and select the Virtual Machines. Click OK and, back on the Create recovery plan blade, click OK.

    ![An image that depicts Azure Recovery Plan Settings.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image07.png "ASR Recovery Plan settings")

3. After a minute or two, you should see the CloudShopRP on the Recovery plans blade. This recovery plan would bring up both servers at once during a failover. In an N-tier application, it is often best to have the data tier come up first. So, we will edit the recovery plan to accomplish this. Click the CloudShopRP recovery plan.
   
4. On the CloudShopRP blade click Customize. Within the Recovery plan blade, click + Group.

    ![An image that depicts Azure Recovery Plan Settings.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image08.png "ASR Recovery Plan settings")

5. Under Group 1: Start click on the ellipse beside WebVM-1 and and WebVM-2 and choose Delete machine. Leave only SQL Server in Group-1.


6. Click on the ellipse beside Group 2: Start and choose Add protected item and Add Both Web servers. Then save the changes.

    ![An image that depicts Azure Recovery Plan Group Settings.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image09.png "ASR Recovery Plan Group settings")

7. Now go back to the Recovery Services Vault BackupVault2 - Overview blade. Click on Site Recovery. Notice the two servers that make the Cloud Shop application are replicating. Take note of their status. They should be close to 100%. You will not be able to continue until they are finished replicating. 
   
   **Note**: This may take up to an hour.

### Task 3: Creating the Test Fail Over.

1. Within the Azure portal, click on Resource Groups and locate the resource group with -asr added to the end of it's name.
   
    ![An image that depicts Azure Resource Group.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image10.png "ASR Resource Group")

2. Click on this resource group and notice the resources created by ASR to support workload protection and failover.

3. Navigate back to the Overview section of your Recovery Services vault via the tile on your dashboard. Under Site Recovery, click on **Replicated items** and ENSURE both Cloud Shop VMs are fully protected before continuing.
   
4. Navigate back to the Overview section of the **Recovery Services Vault**. Under Site Recovery click on **Recovery Plans**.

    ![An image that depicts Azure ASR Recovery Plan.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image12.png "ASR Recovery plan")

5. Right-click the CloudShopRP and click **Test Failover**.

    ![An image that depicts Azure ASR Recovery Plan.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image13.png "ASR Test failover")

6. On the new Test failover blade, under Choose a recovery point, select Latest processed (low RTO) and under Azure virtual network choose **CloudShopVNET1-asr**. Click OK.

**Note**: In a 'real-world' recovery test, you should choose an isolated virtual network so as to not impact the production application. 

7. From the Recovery Services vault blade, click Jobs under MONITORING AND REPORTS. In the new blade, under General, select **Site recovery jobs**.
On the Site recovery jobs blade click on the running job (Test failover).

8. On the Test failover blade, monitor the progress of the failover. Notice each step is executing and you can track the status and execution time. Also notice that the data tier is being started first, then the app tier, as per our recovery plan.

9. Navigate back to the Overview section of the **Recovery Services Vault**. Under Site Recovery click back on Recovery plans. Notice the Recovery plan is waiting on your input.


10. Under Resource groups in the left-hand navigation bar, navigate to the resource group created for this protected workload, called CloudShopRG1-asr. Note the resources that have been created as a part of the failover action. The compute resources were not provisioned until the failover occurred.

### Task 4: Cleaning the Test Fail Over.

1. In the Azure portal, navigate back to the **Recovery Services Vault** via the dashboard tile. In the **Overview** section of the Recovery Services Vault, under the **Site Recovery tab**, click on **Recovery plans**.
   
2. Notice that the recovery plan has a pending job called **Cleanup test failover** pending. Right-click on the **CloudShopRP recovery plan** and select **Cleanup test failover**.

3. In the Test failover cleanup blade, enter notes indicating that the test was successful, and click the checkbox indicating the testing is complete. Then click OK.
   
4. Navigate back to the Overview section of the **Recovery Services Vault**. Under Site Recovery find the jobs tile and click on In-progress jobs. 
   
5. On the Site recovery jobs blade, click on the running job. Monitor the status until the environment is cleaned up (approximately 5 minutes).

6. In the Azure portal navigate to Resource Groups and click on the **CloudShopRG1-asr** resource group. Notice that the virtual machines and network interfaces have all been deleted, leaving only the resources ASR initial created to support protection and the manually-created public IP address.


## After the hands-on lab

### Task 1: Delete the resource groups created

1.  Within the Azure portal, click Resource Groups on the left navigation.

2.  Delete each of the resource groups created in this lab by clicking them followed by clicking the Delete Resource Group button. You will need to confirm the name of the resource group to delete.

You should follow all steps provided *after* attending the hands-on lab.

