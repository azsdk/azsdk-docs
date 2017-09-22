## Scan your cloud application (Azure resources) for security vulnerabilities
###### :clock10: 30 minutes to complete
The AzSDK contains cmdlets that dev ops teams can use to regularly keep their cloud applications
secure during the multiple sprints involving prototyping, core solution development, integration
and deployment.
These scripts are called Security Verification Tests (SVT) and cover all prominent features in 
Azure (e.g., Web Apps, Storage, SQL DB, Key Vault, etc.). Each SVT corresponds to a particular
Azure feature or service and automates checks for secure configuration and security best practices 
for that Azure service.


> Note: Ensure that you already have the latest version of AzSDK installed on your machine. 
> If not, please follow instruction [here.](../00a-Setup/Readme.md)  

**Step 1**: Run the command below after replacing `<Subscriptionid>` with your Azure SubscriptionId 
and `<RG1, RG2, ..`> with a comma-separated list of resource groups where your resources are hosted.
```PowerShell
   Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId> -ResourceGroupNames <RG1, RG2,...etc.>
```

In the command above, an application is represented via a set of resource groups that hold the key 
cloud resources that constitute the application.
 
The AzSDK also supports other ways of representing an application.
For instance, you can also make use of the 'tags' parameter to scan only resources with 
a specific tag value. More details can be found [here.](../02-Secure-Development/Secure_Development_userguide.md#how-do-i-check-for-specific-resource-groups-or-tagged-resources-only)  

When the command runs, you will start seeing output such as the following in the PowerShell console. 
Time required for execution will depend on the number of resources that are being scanned.    

<!--#TODO# RG-based JPG-->
![00_AzSDK_ServicesSecurity_Status](../Images/00_AzSDK_ServicesSecurity_Status.png)  

**So what's happening?** 

Basically, the AzSDK command is scanning all the Azure resources in the given subscription (e.g. in the 
above case 64 resources were found in the subscription) using a set of security rules 
implemented in the PS cmdlet 'Get-AzSDKAzureServicesSecurityStatus'. Depending upon the type 
of the Azure resource (e.g., App Service or Data Lake Store), a set of security controls
relevant to the resource are evaluated. 

As each control is processed, the command prints out information about the specific security check being 
performed. 

<!--#TODO# Use #Include for this part..except for the pic (can the img names be parameterized?)-->

**Understanding the ouputs** 

Once the execution is complete, the output folder is (auto) opened for you. 
It has a single CSV file which provides the consolidated security report for all the resources 
that were evaluated. Moreover, for each resource group, there is also a detailed LOG file for 
every resource type that was evaluated from that resource group. 
To make these logs more convenient to use, they are grouped under separate folders as per 
the resource groups under which the resources themselves are organized in your subscription. 

![00_AzSDK_ServiceSecurity_OP_Folder](../Images/00_AzSDK_ServiceSecurity_OP_Folder.png)  

We can now examine the CSV file to see the control summary. (It opens by default in XLS and you can 
use "Format as Table", "Hide Columns", "Filter", etc., to quickly look at controls that have "Failed" 
or ones that are marked "Verify" (latter represents controls that need manual confirmations).  

![00_AzSDK_Service_Status_OP_CSV](../Images/00_AzSDK_Service_Status_OP_CSV.PNG)  

For controls that are marked 'Failed' (or 'Verify'), there is usually additional information present in 
the LOG file to help understand why a control was marked as 'Failed' (or what needs verification if it 
was marked as 'Verify'). 
For instance, in the image below, we can see from the LOG file that AzSDK has assessed that 
diagnostics settings are either disabled or data retention is not configured for a minimum 365 days 
for the Key Vault resource that was scanned.

> **Note**: Timestamps are used to disambiguate multiple invocations of the cmdlets.  

![00_AzSDK_ServicesSecurity_Status__OP_Log](../Images/00_AzSDK_ServicesSecurity_Status_OP_Log.png)  

Congratulations! You have completed this section of the Getting Started guide successfully!!

**Next steps** 
To get more details and understand SVTs and other secure development features of the AzSDK further, 
please refer [here.](../02-Secure-Development/Readme.md)
