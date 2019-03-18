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

- [Building a resilient IaaS architecture hands-on lab step-by-step](#building-a-resilient-iaas-architecture-hands-on-lab-step-by-step)
  - [Abstract and learning objectives](#abstract-and-learning-objectives)
  - [Overview](#overview)
  - [Solution architecture](#solution-architecture)
  - [Requirements](#requirements)
    - [Help references](#help-references)
  - [Exercise 1: SQL Backup solution](#exercise-1-sql-backup-solution)
    - [Task 1: Create an Azure Storage Account](#task-1-create-an-azure-storage-account)
    - [Task 2: Configure managed backup in SQL Server](#task-2-configure-managed-backup-in-sql-server)
  - [Exercise 2: Implement a Data Archive Strategy with SQL Server Stretch Database](#exercise-2-implement-a-data-archive-strategy-with-sql-server-stretch-database)
    - [Task 1: Identify tables that may benefit from Stretch DB](#task-1-identify-tables-that-may-benefit-from-stretch-db)
    - [Task 2: Implement Stretch DB on based on date key](#task-2-implement-stretch-db-on-based-on-date-key)
  - [Summary](#summary)
  - [Exercise 3: Build SQL Availability Group for Database HADR](#exercise-3-build-sql-availability-group-for-database-hadr)
    - [Task 1: Deploy SQL Always-On Cluster](#task-1-deploy-sql-always-on-cluster)
    - [Task 2: Build a scalable and resilient web tier](#task-2-build-a-scalable-and-resilient-web-tier)
    - [Summary](#summary-1)
  - [Exercise 4: Configure Azure Site Recovery to Web Tier DR](#exercise-4-configure-azure-site-recovery-to-web-tier-dr)
    - [Task 1: Create an Azure Storage Account](#task-1-create-an-azure-storage-account-1)
    - [Task 2: Configure managed backup in SQL Server](#task-2-configure-managed-backup-in-sql-server-1)
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

# Building a resilient IaaS architecture hands-on lab step-by-step 

## Abstract and learning objectives 

In this hands-on lab, you will build a disaster recovery site for an on-premises environment. You will enhance the existing database solution to support high availability on-premises, implement an archiving strategy and a backup/restore strategy.

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

1.  Connect to your Hyper-V host server by navigating to your **OnPremises** resource group and then connecting to the **sh360host** virtual machine.

2.  Launch the **Hyper-V Manager** application and connect to the SQL Server guest virtual machine.

3.  Launch **Server Manager**, select **Local Server** from the menu on the left and verify that **IE Enhanced Security Configuration** is set to **Off**.

    ![Server manager applciation with local server selected and IE Enhanced Security Configuration set to Off.](images/hands-on-lab/2019-03-16-14-00-37.png "Server Manager local server configuration")

4.  From within your SQL Server guest virtual machine, install Azure PowerShell by launching an **administrative PowerShell ISE session** and running the following command. Accept any warnings or authorization to install the components.

    ```
    Install-Module -Name AzureRM -AllowClobber
    ```

5.  From the PowerShell ISE, type the following at the prompt and follow the prompts to login to your Azure subscription.

    ```
    login-AzureRmAccount
    ```

6.  Execute the following PowerShell commands in the PowerShell ISE to create a new storage account and generate the T-SQL needed to configure managed backup for the database. Before executing the script, change the $storageAcctName to a unique name.

    ```powershell
    $storageAcctName = "[unique storage account name]"

    $resourceGroupName = "DRsite"
    $containerName= "backups"
    $location = "South Central US"
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
     @database_name = 'SmartHotel.Registration',  
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

    ![A screen showing how to launch the new query pane in SQL Server Management Studio.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image102.png "Launching the new query pane")


3.  Paste in the following code and click **Execute** to enable SQL Server Agent extended stored procedures. Refresh SQL Server Management Studio and if SQL Server Agent is stopped right click on it and click Start.

    ![SQL Server Management Studio indicating the location of where to see the current status of the SQL Server Agent](images/hands-on-lab/2019-03-16-14-19-27.png "Management Studio" )

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

4.  Paste the T-SQL code you copied in the previous task into the query window replacing the existing code and click **Execute**. This code creates the new SQL identity with a Shared Access Signature for your storage account. 

5.  Paste the code into the query window replacing the existing code and click **Execute** to create a custom backup schedule.

    ```sql
    USE msdb;  
    GO  
    EXEC managed_backup.sp_backup_config_schedule   
         @database_name =  'SmartHotel.Registration'  
        ,@scheduling_option = 'Custom'  
        ,@full_backup_freq_type = 'Weekly'  
        ,@days_of_week = 'Monday'  
        ,@backup_begin_time =  '17:30'  
        ,@backup_duration = '02:00'  
        ,@log_backup_freq = '00:05'  
    GO
    ```
6.  Execute the following tSQL in the query window to generate a backup on-demand. You can also specify Log for \@type.

    ```sql
    EXEC msdb.managed_backup.sp_backup_on_demand   
    @database_name  = 'SmartHotel.Registration',
    @type ='Database'
    ```

7. To verify that your backups are working, go to the Azure portal, navigate to your **DRsite** resource group. Open the storage account you just created, select the **Blobs** tile, then the **backups** container. You should see your backups here.

## Exercise 2: Implement a Data Archive Strategy with SQL Server Stretch Database

Duration: 30 minutes

In this exercise, you will implement SQL Server Stretch Database to stretch data from a table into Azure.

### Task 1: Identify tables that may benefit from Stretch DB 

1.  Connect to your Hyper-V host server by navigating to your **OnPremises** resource group and then connecting to the **sh360host** virtual machine.

2.  Launch the **Hyper-V Manager** application and connect to the SQL Server guest virtual machine.

3.  Select **Virtual machines** in the left menu pane of the Azure portal.

4.  In the SQL Server Management Studio Object Explorer, connect to your local SQL Server instance and expand the Databases folder.

5.  Right-click the AdventureWorksDW2016CTP3 database, select Tasks, select Stretch, then choose Enable.

6.  The Enable Database for Stretch wizard should open automatically. Click Next on the Introduction screen.

7.  On the Select tables window all of your tables will be listed. Notice that some tables have warning next to them. Click on the warning icon next to the FactResellerSalesXL_PageCompressed table. This warning simply indicates that the primary key cannot be enforced in the remote Azure table.

8.  Scroll to the right until you see the Migrate column. Clicking Entire Table allows you to choose rows that define which rows will be migrated to Azure. In this lab we are going to configure this through TSQL. Click Cancel to break out of the wizard.

### Task 2: Implement Stretch DB on based on date key  

1.  Launch a new Query tab and execute the following code to prepare the server and the database for Stretch Database. 

    ```
    --Enable the server for Stretch Database
    EXEC sp_configure 'remote data archive' , '1';
    GO
    RECONFIGURE;
    GO

    --Create a database master key to encrypt the data stored in Azure
    USE AdventureWorksDW2016CTP3
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD='demo@pass123'
    GO

    --Create the credential used to access an Azure SQL Database
    CREATE DATABASE SCOPED CREDENTIAL StretchDB 
    WITH IDENTITY = 'demouser' , SECRET = 'demo@pass123'
    GO
    ```

2.  Execute the following code replacing the server name with the name of the SQL Server you created in exercise 1. This code enables Stretch Database on your database

    ```
    --Enable the local database for stretch
    --You must change your server name to match your environment
    ALTER DATABASE [AdventureWorksDW2016CTP3]
    SET REMOTE_DATA_ARCHIVE = ON (SERVER = N'<your server name>.database.windows.net', CREDENTIAL = StretchDB )
    ```

3.  Execute the following code to create the stretch predicate. The stretch predicate allows us to define the conditions under which a row will be stretched. In this case the function leverages a date key column to stretch any rows prior to January 1, 2014. 

    ```
    --Create the stretch predicate
    CREATE FUNCTION dbo.fn_stretch_datekey_predicate(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING 
    AS 
    RETURN	SELECT 1 AS is_eligible
		    WHERE @column1 < '20140101'
    GO
    ```

4. Execute the following to begin the migration of eligible data to your Azure SQL Database.

    ```
    --Enable stretch on the table using the stretch predicate with the OrderDateKey
    ALTER TABLE FactResellerSalesXL_PageCompressed 
    SET ( REMOTE_DATA_ARCHIVE = ON (
	    FILTER_PREDICATE = dbo.fn_stretch_datekey_predicate([OrderDateKey]),
	    MIGRATION_STATE = OUTBOUND
    ) )

5. Refresh the AdventureWorksDW2016CTP3 database in Object Explorer by right clicking it and choosing Refresh.

6. Right-click the AdventureWorksDW2016CTP3 database, choose Tasks, Stretch, then select Monitor

7. View the stretch database report. Note that over time the Eligible Rows, Local Rows, and Rows In Azure numbers will change.

8.	Click the dropdown under Migration State. Notice that you have the option to pause the outbound migration.

9.	Launch a new Query tab and execute the following code to programatically monitor space used in Azure and locally.

    ```
    sp_spaceused 'FactResellerSalesXL_PageCompressed', @mode = 'REMOTE_ONLY'
    GO
    sp_spaceused 'FactResellerSalesXL_PageCompressed', @mode = 'LOCAL_ONLY'
    GO
    ```

## Summary
In this exercise, you implemented an archive solution with Stretch Database. First, you reviewed the table compatibility by using the Enable Database for Stretch wizard. You then configured your database and table via T-SQL to archive data satisfied by a stretch predicate. Finally, you reviewed the progress, status and space used locally and in Azure via the built-in management tools

## Exercise 3: Build SQL Availability Group for Database HADR

Duration: 60 minutes

In this exercise, you will deploy resilient web servers using VM scale sets and a SQL Always-On Cluster for resiliency at the data tier.

### Task 1: Deploy SQL Always-On Cluster 

In this task, you will deploy a SQL Always-On cluster using an ARM template that deploys to your existing Virtual Network and Active Directory infrastructure.

1.  Navigate to https://github.com/hansenms/iac/tree/master/sql-alwayson and click **Deploy to Azure** to deploy the template.
   
2.  Specify the following information
   
    Resource group:**CloudShopRG** 
    
    Location: **East US 2** 
    
    **Subnet Id**: Go to **resources.azure.com** then go to Subscription - Resource Group - ContosoRG - Providers - Microsoft.Network - VirtualNetworks - **Find subnet id for Data Subnet**. Copy the full subnet ID in between the **" "** quote.

    Admin username **demouser**
    Admin Password **demo@pass123**
    Domain Name: contoso.com

    Leave the rest of the template parameters with their default values.

    ![The custom deployment blade is displayed with CloudShopRG as the resource group and East US 2 as the location.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image126.png "Custom deployment")

3.  Check the **I agree to the terms and conditions state above** checkbox on the page and click **Purchase**.

    ![A screen with the checkbox for I agree to the terms and conditions checked and the purchase button highlighted.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/2018-08-27-20-21-18.png "Terms and Conditions")

4.  Wait until the template deployment is complete before continuing. 

    >**Note**: The deployment may take up to 30 minutes.

    ![Screenshot of the successful deployment of sql tier.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/sqltierdeployment.png "Template deployment")

5.  Open a remote desktop connection to the **SQL0** virtual machine you created in the previous task, and login using the **contoso\\demouser** account with the password **demo@pass123**.

    ![Screenshot of the Connect icon.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image52.png "Connect icon")

6.  Once connected, open the Windows Explorer, check to make sure the **F:\\** Drive is present.

7.  open the **Failover Cluster Manager**, click connect to the cluster and type **SQLClusterAG**. Cluster manager will connect to the newly deployed Always on availability group. expand the cluster, select Nodes, validate all nodes are online and Assigned Vote and Current Vote are listed as "1" for all nodes of the cluster.

    ![In Failover Cluster Manager, in the Nodes pane, two Nodes display: SQL0, SQL1. Their Assigned Votes and Current votes are all 1.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image76.png "Failover Cluster Manager")

8.  Launch **SQL Server 2017 Configuration Manager** on **SQL0**.

    ![SQL Server 2017 Configuration Manager is typed in the search field, and below, SQL Server 2016 Configuration Manager is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image77.png "Search field and results")

9. Click **SQL Server Services**, right-click **SQL Server (MSSQLSERVER)**, and select **Properties**.

    ![In SQL Server 2017 Configuration Manager, in the left pane, under SQL Server Configuration Manager (Local), SQL Server Services is selected. In the right pane, under Name, SQL Server (MSSQLSERVER) is selected, and Properties is selected from its right-click menu.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image78.png "SQL Server 2017 Configuration Manager")

10. Select the **AlwaysOn High Availability** tab, make sure the box for **Enable AlwaysOn Availability Groups** is selected.

    ![In the SQL Server (MSSQLSERVER) Properties dialog box, on the AlwaysOn High Availability tab, the Enable AlwaysOn Availability Groups checkbox is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image79.png "SQL Server (MSSQLSERVER) Properties dialog box")

11. On the **Log On** tab, change the service account to **contoso\\demouser** using **demo\@pass123** for the password. Click **OK** to accept the changes, and click **Yes** to confirm the restart of the server.

    ![In the SQL Server (MSSQLSERVER) Properties dialog box, on the Log On tab, in the Account Name field, contoso\\demouser is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image80.png "SQL Server (MSSQLSERVER) Properties dialog box")

    ![On the Confirm Account Change pop-up, the Yes button is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image81.png "Confirm Account Change pop-up")

12. Minimize the RDP Window for **SQL0**.

13. From the Azure portal, locate **SQL1**, and click **Connect.** Make sure to Sign On using the **contoso\\demouser** domain account.

    ![On the Windows Security login page, the contoso\\demouser credentials are called out.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image82.png "Windows Security login page")

14. From the RPD Session on **SQL1**, repeat steps to verify the configuration of **AlwaysOn High Availability** and **Log On** using SQL 2017 Configuration Manager.

15. Move back to RDP session with **SQL0**.

16. Launch **SQL Server Management Studio 17**, and connect to the local instance of SQL Server.

17. Click **Connect** to login to SQL Server.

    ![The Connect to Server dialog box displays.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/2018-08-28-19-36-49.png "Connect to Server dialog box")

    >**Note**: Availability Groups require that the databases be in full recovery mode.

18. Use the PowerShell or PowerShell ISE to deploy the cloudshop database by running the command below. Deploy-cloudshop-db.ps1 script is available in this github repository. The script will deploy the cloudshop database to the database servers. 
    
    >**Note**: You need to pass the parameters below with the PowerShell script. If you download the script from the url below in your local desktop or any drive, just copy and paste in your PowerShell window.
    
    https://github.com/opsgility/cw-building-resilient-iaas-architecture/tree/master/script-extensions 

    
    PS C:\Users\demouser.SQL0\Desktop> .\deploy-cloudshop-db.ps1  -user "demouser" -password "demo@pass123" -dbsource "https://cloudworkshop.blob.core.windows.net/building-resilient-iaas-architecture/AdventureWorks2012.bak" -sqlConfigUrl "https://raw.githubusercontent.com/opsgility/cw-building-resilient-iaas-architecture/master/script-extensions/configure-sql.ps1"


    >**Note**: You may need to wait few minutes to view the newly created adventureworks database in SSMS.

20. In SSMS - Right click AdventureWorks database - Task - Back Up. Make sure **Backup type** and **Recovery Type** is **Full** selected. Click Ok to initiate the backup. 

    ![New Database creation in Availability Group.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image02.png "Creating a new database")


21. Expand **AlwaysOn High Availability -\> Availability Groups**, right-click **Availability Databases** (Primary), - Add Database -  and select Adventure works and continue the prompt to add the database to the availability group. Once completed, Right click the **Availability Groups** - **Show Dashboard**. Your dashboard should look similar to this:

    ![On the Dashboard, a green Check mark displays next to AdventureWorksAG:  (Replica role: Primary). The Availability group state is Healthy, and Synchronization state for SQL0 SQL1, AdventureWorks SQL0 and AdventureWorks SQL1 is Synchronized.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image89.png "Dashboard")

### Task 2: Build a scalable and resilient web tier

In this task, you will deploy a highly available web servers. 

1. Navigate to the URL https://github.com/opsgility/cw-building-resilient-iaas-architecture and click to **Deploy to Azure** on the sample for building a resilient IaaS Architecture - Deploy Web Tier.
   
2.  Specify the existing resource group **CloudShopRG**. 

3.  **Subnet ID**: Go to **resources.azure.com** then go to Subscription - Resource Group - ContosoRG - Providers - Microsoft.Network - VirtualNetworks - **Find subnet id for App Subnet**. Copy the full subnet ID in between the **" "** quote.

4.  Check the **I agree to the terms and conditions state above** checkbox on the page and click **Purchase**.

    >**Note**: It could take up to 30 minutes to deploy the environment.

### Summary

In this exercise, you deployed resilient web servers behind a load balancer, and a SQL Always-On Availability Group for database resiliency through an ARM template. Also you deployed resilient web tier with an external load balancer through an ARM template.

## Exercise 4: Configure Azure Site Recovery to Web Tier DR 

Duration: 15 minutes

In this exercise, you will configure SQL Server Managed Backup to back up to an Azure Storage Account.

### Task 1: Create an Azure Storage Account

In this task, you will add a 3rd node to the SQL Always-On deployment in a second region that you can then failover with Azure Site Recovery in the event of a failure in the primary region.

1.  From **LABVM**, execute the following PowerShell commands in the PowerShell ISE to create a new storage account and generate the T-SQL needed to configure managed backup for the AdventureWorks database.

    ```powershell
    $storageAcctName = "[unique storage account name]"

    $resourceGroupName = "CloudShopRG"
    $containerName= "backups"
    $location = "East US 2"
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

2.  Execute the code using PowerShell ISE. Make sure you change the **\$storageAcctName = \"\[unique storage account name\]\"** field to a unique storage account name across Azure prior to execution. 

3.  Save the T-SQL code generated between the **Begin TSQL Script** and **End TSQL Script** in your PowerShell ISE output after execution into a notepad file. This code creates an identity using a Shared Access Signature (SAS) to a container in the storage account and configures managed backup when executed.

### Task 2: Configure managed backup in SQL Server

1.  Connect to **SQL0** using remote desktop and launch SQL Server Management Studio and connect to the database instance.

2.  Right click on **SQL0**, and click **New Query**.

    ![A screen showing how to launch the new query pane in SQL Server Management Studio.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image102.png "Launching the new query pane")

3.  Paste in the following code and click **Execute** to enable SQL Server Agent extended stored procedures. Refresh SQL Server Management Studio and if SQL Server Agent is stopped right click on it and click Start.

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

4.  Paste the T-SQL code you copied in the previous task into the query window replacing the existing code and click **Execute**. This code creates the new SQL identity with a Shared Access Signature for your storage account. 

5.  Paste the code into the query window replacing the existing code and click **Execute** to create a custom backup schedule.

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
6.  Execute the following tSQL in the query window to generate a backup on-demand. You can also specify Log for \@type.

    ```sql
    EXEC msdb.managed_backup.sp_backup_on_demand   
    @database_name  = 'AdventureWorks',
    @type ='Database'
    ```
    
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

