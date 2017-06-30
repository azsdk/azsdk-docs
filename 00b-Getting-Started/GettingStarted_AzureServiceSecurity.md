## Scan your cloud application (Azure resources) for security vulnerabilities
###### :clock10: 30 minutes to complete
The AzSDK contains scripts that can empower a developer to regularly keep their cloud applications
secure during the multiple sprints involving prototyping, core solution development and integration.
These scripts are called Security Verification Tests (SVT) and cover all prominent features in 
Azure (e.g., Web Apps, Storage, SQL DB, Key Vault, etc.). Each SVT corresponds to a particular
Azure feature or service (e.g., Data Lake Store or Logic Apps) and encapsulates the core secure
configuration and security best practices for that service in Azure.


> Note: Ensure you already have the latest version of AzSDK installed on your machine. If not, please follow instruction [here.](../00a-Setup/Readme.md)  

**Step 1**: Run the command below after replacing `<Subscriptionid`> with your Azure SubscriptionId 
and `<RG1, RG2, ..`> with a comma-separated list of resource groups where your resources are hosted.
```PowerShell
   Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId> -ResourceGroupNames <RG1, RG2,...etc.>
```
You can also make use of the 'tags' parameter to scan only resources with a specific tag value. More details can be found [here.](../02-Secure-Development/Secure_Development_userguide.md#how-do-i-check-for-specific-resource-groups-or-tagged-resources-only)  

You will start seeing output such as the following in the PowerShell console. 
Time required for execution will depend on the number of resources that you getting scanned.    

<!--#TODO# RG-based JPG-->
![00_AzSDK_ServicesSecurity_Status](../Images/00_AzSDK_ServicesSecurity_Status.png)  

**So what's happening?** 

Basically, the AzSDK command is scanning all the Azure resources in the given subscription (e.g. in the 
above case 64 resources were found in the subscription) using a set of security rules implemented in the PS cmdlet 'Get-AzSDKAzureServicesSecurityStatus'. 

As each rule is processed, the command prints out information about the specific security check being 
performed. 

<!--#TODO# Use #Include for this part..except for the pic (can the img names be parameterized?)-->

**Understanding the ouputs** 

Once the execution is complete, the output folder is (auto) opened for you. 
It has a single CSV file 
which provides the consolidated security report of all the resources that were evaluated. It also has 
a detailed LOG file for every resource that was evaluated. To make these logs more convenient to use, 
they are grouped under separate folders as per the resource groups under which the resources 
themselves are organized in your subscription. 

![00_AzSDK_ServiceSecurity_OP_Folder](../Images/00_AzSDK_ServiceSecurity_OP_Folder.png)  

We can now examine the CSV file to see the control summary. (It opens by default in XLS and you can 
use "Format as Table", "Hide Columns", "Filter" etc. to quickly look at controls that have "Failed" 
or ones that are marked "Verify" (in that they need manual verification).  

![00_AzSDK_Service_Status_OP_CSV](../Images/00_AzSDK_Service_Status_OP_CSV.PNG)  

For controls that are marked 'Failed' or 'Verify', there is usually additional information present in 
the LOG file to help understand why a control was marked as 'Failed' or what needs verification if it 
was marked as 'Verify'. 
For instance, in the image below, AzSDK has assessed that dignostics settings are either 
disabled or data retention is not configured for a minimum 365 days for the Key Vault resource that was scanned.

![00_AzSDK_ServicesSecurity_Status__OP_Log](../Images/00_AzSDK_ServicesSecurity_Status_OP_Log.png)  

> **Note:** Timestamps are used to disambiguate multiple invocations of the cmdlets.  

You have completed this section of the Getting Started guide successfully. 

**Next steps** 
To get more details and understand SVTs and other secure development features of the AzSDK further, 
please refer [here.](../02-Secure-Development/Secure_Development_userguide.md)