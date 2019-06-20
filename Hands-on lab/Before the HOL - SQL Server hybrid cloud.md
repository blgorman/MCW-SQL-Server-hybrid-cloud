![Microsoft Cloud Workshop](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "Microsoft Cloud Workshop")

<div class="MCWHeader1">
SQL Server hybrid cloud
</div>

<div class="MCWHeader2">
Before the hands-on lab setup guide
</div>

<div class="MCWHeader3">
June 2019
</div>


Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2019 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at <https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx> are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

**Contents**

- [SQL Server hybrid cloud before the hands-on lab setup guide](#sql-server-hybrid-cloud-before-the-hands-on-lab-setup-guide)
  - [Requirements](#requirements)
  - [Before the hands-on lab](#before-the-hands-on-lab)
    - [Task 1: Deploy the on-premises environment](#task-1-deploy-the-on-premises-environment)
    - [Task 2: Verify virtual machines domain join status](#task-2-verify-virtual-machines-domain-join-status)
    - [Task 3: Verify the website is operational](#task-3-verify-the-website-is-operational)
  - [Summary](#summary)

# SQL Server hybrid cloud before the hands-on lab setup guide 

## Requirements

1.  Microsoft Azure Subscription

2.  Virtual Machine Built during this hands-on lab or local machine with the following:

    - Latest Azure PowerShell cmdlets:
        - <https://azure.microsoft.com/en-us/downloads/>
        - <https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps>
        - Ensure you reboot after installing the SDK or Azure PowerShell may not work correctly.

## Before the hands-on lab

Duration: 60 minutes

In this exercise, you deploy an on-premises environment and the Azure infrastructure necessary to support the disaster recovery site in Azure. The on-premises environment will be hosted in an Azure Resource Group.

### Task 1: Deploy the on-premises environment

1. Browse to the Azure portal at <https://portal.azure.com> and verify that you are logged in with the subscription that you wish to use for this lab.

    >**Note**: You may need to launch an \"in-private\" session in your browser if you have multiple Microsoft Accounts.

2. Before we deploy the environment, we must pre-create the resource group that will host our disaster recovery site. Throughout the lab this will be called **CloudShop2**

3. Click the **+ Create a resource** button, type **Resource group** into the search bar, and choose **Resource group** from the search results.

4. On the resource group blade, click **Create**.

5. This lab will use Azure Site Recovery to replicate VMs to another region. It is not supported to replicate virtual machines to the same region. Choose a region pair to use for this lab. You can see a list of the region pairs at https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions 

6. On the create a resource group blade, type **CloudShop2** for the name of the resource group and choose a region one of your region pairs as the location. Click **Review + Create**, then **Create**.

    ![Resource group creation page of the Azure portal with the resource group name set to CloudShop2.](images/before-the-hands-on-lab/2019-03-24-14-40-03.png "Create a resource group")

    > **Note**: ***Do not use any other name for this resource group***. Using any other resource group name will cause your deployment to fail.

7. Click the **Deploy to Azure** button below.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fopsgility%2Fcw-sql-hybrid-cloud%2Fmaster%2Fazure-deploy.json" rel="nofollow">
    <img src="https://camo.githubusercontent.com/9285dd3998997a0835869065bb15e5d500475034/687474703a2f2f617a7572656465706c6f792e6e65742f6465706c6f79627574746f6e2e706e67" data-canonical-src="http://azuredeploy.net/deploybutton.png" style="max-width:100%;"></a>

8. On the **Custom deployment** blade, select **Create new** under the **Resource group**. Name the resource group ***CloudShop1***. Choose the region pair of your CloudShop2 resource group location. In our lab document we have chosen the **East US 2/Central US** region pair. Accept the terms and conditions and click **Purchase**.

    ![The Azure custom deployments blade is shown. The setting for resource group has been changed to CloudShop1 and the I agree to the terms and conditions stated above checkbox has been checked. All other values have been left at the defaults.](images/hands-on-lab/2019-03-25-21-15-05.png "Custom deployment blade")

    > **Note**: Do not use a different resource group and do not change any of the values listed here or your lab will not deploy correctly.

9. Wait for the deployment to complete. This may take up to 60 minutes.

### Task 2: Verify virtual machines domain join status

From the Azure portal, navigate to the CloudShop1 resource group.

1. Open your CloudShopWeb virtual machine and click **Connect** and login with account **demouser** and password **demo@pass123**.

2. After a minute or so, the Server Manager application should launch on its own. 

3. Navigate to **Local Server** on the left side menu. Verify that the domain name is **contoso.com**

    ![The Windows Server Manager app is shown with Local Server selected and the domain highlighted. The domain name is contoso.com.](images/before-the-hands-on-lab/2019-03-25-22-22-06.png "Server Manager")

4. If the machine has not joined the domain, click **WORKGROUP** and then joining the contoso.com domain. When joining the domain, use the credentials **CONTOSO\demouser** with a password of **demo@pass123**.

5. Repeat the above steps for the remaining servers.

### Task 3: Verify the website is operational

1. In the Azure portal, navigate to the **CloudShop1** resource group.

2. Open the **CloudShopWeb** virtual machine resource.

3. Copy the **public IP address**.

    ![The CloudShopWeb virtual machine with the public IP address highlighted.](images/before-the-hands-on-lab/2019-03-24-17-22-38.png "Copy the public IP address of the web server")

4. Paste the public IP address into a web browser. You should see the Cloud Shop web page.

    ![The CloudShop web page.](images/before-the-hands-on-lab/2019-03-24-17-25-37.png "Cloud Shop")

## Summary

In this exercise, you deployed and validated your "On-Premises" environment and your Azure disaster recovery site. You also verified that all three servers have successfully joined the domain. In the lab you will reconfigure your on-premises environment for high availability.

You should follow all steps provided *before* attending the Hands-on lab.
