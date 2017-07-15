# Secure Development

### Contents
### [Security Verification Tests (SVT)](Secure_Development_userguide.md#security-verification-tests-svt-1)

- [Overview](Secure_Development_userguide.md#overview)
- [Execute SVTs for all controls in all resources in a given subscription](Secure_Development_userguide.md#execute-svts-for-all-controls-in-all-resources-in-a-given-subscription)
- [Execute SVTs for specific resource groups (or tagged resources)](Secure_Development_userguide.md#execute-svts-for-specific-resource-groups-or-tagged-resources)
- [Execute SVTs for a specific resource](Secure_Development_userguide.md#execute-svts-for-a-specific-resource)
- [Execute SVTs for a specific resource type](Secure_Development_userguide.md#execute-svts-for-a-specific-resource-type)
- [Control Attestation](Secure_Development_userguide.md#control-attestation)
- [FAQs](Secure_Development_userguide.md#faqs)
### [Express Route-connected Virtual Networks (ER-vNet)](Secure_Development_userguide.md#express-route-connected-virtual-networks-er-vnet-1)

-----------------------------------------------------------------
## Security Verification Tests (SVT)
>  **Prerequisites:**
> For all commands in this feature it is assumed that you have:
> 1. Logged in to your Azure account using Login-AzureRmAccount from a PowerShell ISE.
> 2. Selected a subscription using Set-AzureRmContext.


### Overview
Security Verifications Tests (or SVTs) represent the core of security testing functionality of the 
AzSDK. For all the prominent features in Azure (e.g., Web Apps, Storage, SQL DB, Key Vault, etc.), 
the AzSDK ccan perform automated security checks against Azure resources of those types. 
These checks are based on security standards and best practices as applicable for sensitive corporate 
data at Microsoft. In general, these are likely to be applicable for most scenarios that involve 
processing sensitive data in other environments.

An SVT for a particular resource type basically examines a resource of that type within a specified 
resource group and runs a set of security control checks pertinent to that resource type. 
The outcome of the analysis is printed on the console during SVT execution and a CSV and a LOG file are 
also generated for subsequent use.

The CSV file and LOG file are generated under a subscription-specific sub-folder in the folder  
*%LOCALAPPDATA%\Microsoft\AzSDKLogs\Sub_[yourSubscriptionName]*  
E.g.  
C:\Users\UserName\AppData\Local\Microsoft\AzSDKLogs\Sub_[yourSubscriptionName]\20170331_142819

There are multiple ways that SVTs can be executed:
1. Scan all resources in a subscription. This is the simplest approach and simply enumerates 
all resources in a specific subscription and runs security checks against all the known resource 
types found.
2. Scan all resources in specific resource group(s). In this option, you can target resources
within one or more resource group. The AzSDK simply enumerates all resources in the resource group(s) 
and runs security checks..
3. Scan a specific resource. In this approach, you can target a specific (individual) resource.
4. Scan a specific resource type. In this approach, you can target a specific resource type (e.g., Storage)
by specifying the 'resource type' value.
	
These options are described with examples below.  

[Back to top…](Secure_Development_userguide.md#contents)

### Execute SVTs for all controls of all resources in a given subscription
The cmdlet below checks security control state and generates a status report of all Azure resources 
in a given subscription:  

```PowerShell
Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId>
```
		
The parameters required are:
- SubscriptionId – Subscription ID is the identifier of your Azure subscription.  

>**Note** Although this command is simple, it may take time proportionate to the number of 
>resources in your subscription and may not be ideal for 'shared' subscriptions. Typically, 
>you would want to scan a specific application (organized under one or more resource groups or 
>using tags). The subsequent options provide ability to narrow down the scope of the scan.
    
[Back to top…](Secure_Development_userguide.md#contents)  
### Execute SVTs for specific resource groups (or tagged resources) 
The cmdlet below scans all Azure resources in the specified resource groups within a subscription and 
generates a status report:
```PowerShell
Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId> -ResourceGroupNames <ResourceGroupNames>
```
	
The parameters required are:
- SubscriptionId – Subscription ID is the identifier of your Azure subscription. 
- ResourceGroupNames – Comma separated list of resource groups that hold related resources for an Azure subscription.
	
The cmdlet below scans all resources with specific tag names/values under a given subscription:  
1. Single Tag
```PowerShell
    Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId> -TagName <TagName> -TagValue <TagValue>
```
		
2. Multiple Tags
```PowerShell
    Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId> -Tag <TagHashset>
```
		
The parameters required are:
- SubscriptionId – Subscription ID is the identifier of your Azure subscription. 
- TagName – Key to identify the resources.
- TagValue – Value to identify the resources.
- Tag – The tag filter for Azure resource. The expected format is @{tagName1=$null} or @{tagName = 'tagValue'; tagName2='value1'}.  

[Back to top…](Secure_Development_userguide.md#contents)
### Execute SVTs for a specific resource
The cmdlet below scans a single Azure resource within a specific resource group in a subscription and generates a status report:
```PowerShell
Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId> -ResourceGroupNames <ResourceGroupName> -ResourceName <ResourceName>
```
	
The parameters required are:
- SubscriptionId – Subscription ID is the identifier of your Azure subscription. 
- ResourceGroupNames – Name of the resource group that holds the individual resource to be scanned.
- ResourceName – Name of the resource. 

> **Note**: In the command above, 'ResourceName' should be the short name as used in Azure 
> and shown in the portal (as opposed to the fully qualified domain name (FQDN) which may 
> apply to some resource types such as storage blobs or SQL DB).  

[Back to top…](Secure_Development_userguide.md#contents)
### Execute SVTs for a specific resource type
The cmdlet below scans all resources for a specific Azure resource type in a subscription (and a resource group [optional]):
1. 	Using Azure resource type
```PowerShell
 Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId> [-ResourceGroupNames <ResourceGroupNames>] -ResourceType <ResourceType>
```
		
The parameters required are:
- SubscriptionId – Subscription ID is the identifier of your Azure subscription. 
- [Optional] ResourceGroupNames  – Name of the container that holds related resource under an Azure subscription. Comma separated values are allowed.
- ResourceType – Resource type as defined by Azure. E.g.: Microsoft.KeyVault/vaults. Run command 'Get-AzSDKSupportedResourceTypes' to get the list of supported types.

2. Using a user-friendly resource type name
```PowerShell
 Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId> [-ResourceGroupNames <ResourceGroupName>] -ResourceTypeName <ResourceTypeName>
```
	
The parameters required are:
- SubscriptionId – Subscription ID is the identifier of your Azure subscription. 
- [Optional] ResourceGroupNames – Name of the container that holds related resource under an Azure subscription. Comma separated values are allowed.
- ResourceTypeName – Friendly name of resource type. E.g.: KeyVault. Run command 'Get-AzSDKSupportedResourceTypes' to get the list of supported values.  

[Back to top…](Secure_Development_userguide.md#contents)

### Control Attestation

#### Overview

The attestation feature empowers users to support scenarios where human input is required to augment or override the default control 
evaluation status from the AzSDK. These may be situations such as:

- the AzSDK has generated the list of 'Owners' or 'Contributors' for a resource but someone needs to have a look at the list and ratify that 
these are indeed the correct people, or
- the AzSDK has marked a control as failed. However, given the additional contextual knowledge, the application owner wants to ignore the control failure, or
- the AzSDK has marked a control as failed and the issue is a valid security concern. However, due to other constraints, 
the application owner wants to defer the fix for later and request a risk ack.

In all such situations, there is usually a control result that is based on the technical evaluation (e.g., Verify, Failed, etc.) that has to 
be combined with the user's input in order to determine the overall or effective control result. The user is said to have 'attested' such controls 
and, after the process is performed once, AzSDK remembers it and generates an effective control result for subsequent control scans _until_ there 
is a state change.

The attestation feature is implemented via a new switch called *AttestControls* which can be specified in any of the standard security scan cmdlets
of the AzSDK. When this switch is specified, the AzSDK first performs a scan of the target resource(s) like it is business as usual and, once
the scan is complete, it enters a special interactive 'attest mode' where it walks through each resource and relevant attestable controls
and captures inputs from the user and records them in the subscription (along with details about the person who attested, the time, etc.). 
After this, for all future scans on the resource(s), AzSDK will show the effective control evaluation results. Various options are provided to support
different attestation scenarios (e.g., expiry of attestations, edit/change/delete previous attestations, attest only a subset of controls, etc.). 
These are described below. Also, for 'stateful' controls (e.g., "are these the right IP addresses to permit on the firewall?"), the attestation
state is auto-reset if there is any change in 'state' (e.g., someone added a new IP to the list).

Lastly, due to the governance implications, the ability to attest controls is available to a subset of subscription users. This is described in
the permissions required section below. 

#### Starting attestation
      
The AzSDK subscription and services scan cmdlets now support a new switch called *AttestControls*. When this switch is specified, 
AzSDK enters 'attest' mode immediately after a scan is completed. This ensures that attestation is done on the basis of the most current
control statuses.

All controls that have a technical evaluation status of anything other than 'Passed' (i.e., 'Verify' or 'Failed' or 'Manual' or 'Error') are considered 
valid targets for attestation.

To manage attestation flow effectively, 4 options are provided for the *AttestControls* switch to specify which subset of controls to target for attestation. These are described below:

|Attestation Option|Description|
|------------------|-----------|
|NotAttested|Attest only those controls which have not been attested yet.|
|AlreadyAttested|Attest those controls which have past attestations. To re-attest or clear attestation.|
|All|Attest all controls which can be attested (including those that have past attestations).|
|None|N/A.|

For example, to attest controls corresponding to a subscription security scan, run the command below:
 ```PowerShell  
$subscriptionId = <Your SubscriptionId>
Get-AzSDKSubscriptionSecurityStatus -SubscriptionId $subscriptionId -AttestControls NotAttested -DoNotOpenOutputFolder  
 
 ``` 
As shown in the images, the command enters 'attest' mode after completing a scan and does the following:

a) For each resource that was scanned, if a control is a target for attestation, control details (such as description, severity, etc.) and 
the current evaluation result are displayed (to help the user)
b) The user gets to choose whether they want to attest the control
c) If the user chooses to attest, attestation details (attest status, justification, etc.) are captured
d) This is repeated for all attestable controls and each resource.

 Sample attestation workflow in progress:
 ![02_SVT_Attest_1](../Images/02_SVT_Attest_1.png) 
 
 Sample summary of attestation after workflow is completed:
 ![02_SVT_Attest_2](../Images/02_SVT_Attest_2.png) 


 Attestation details corresponding to each control (e.g., justification, user name, etc.) are also captured in the CSV file as shown below:
 ![02_SVT_Attest_3](../Images/02_SVT_Attest_3.png) 

 The attestation process for application resources is similar to that for subscriptions. For example, the command below shows how to 
trigger attestation for a specific resource in an RG:
 ```PowerShell  
$subscriptionId = <Your SubscriptionId>
$resourceGroupName = <ResourceGroup Name>
$resourceName = <ResourceName>
Get-AzSDKAzureServicesSecurityStatus -SubscriptionId $subscriptionId -ResourceGroupNames $resourceGroupName -ResourceName $resourceName -AttestControls NotAttested -DoNotOpenOutputFolder 
 ``` 


If, for any reason, the attestations of previously attested controls need to be revisited, it can be done by simply changing the 'NotAttested' flag in the commands above with 'AlreadyAttested'.


#### How AzSDK determines the effective control result

During the attestation workflow, the user gets to provide attestation (sub)status for each control attested. This basically represents the user's attestation preference w.r.t.
a specific control (i.e., whether the user wants to override/augment the AzSDK status and treat the control as passed or whether the user agrees with the AzSDK status but wants to defer
fixing the issue for the time being):

|Attestation Status | Description|
|-------------------|------------|
|None               | There is no attestation done for a given control. User can select this option duriung the workflow to skip the attestation|
|NotAnIssue         | User has verified the control data and attesting it as not a issue with proper justification|
|NotFixed           | User has verified the control data and attesting it as not fixed with proper justification stating the future fix plan|


The following table shows the complete 'state machine' that is used by AzSDK to support control attestation. 
The columns are described as under:
- 'Control Scan Result' represents the technical evaluation result 
- 'Attestation Status' represents the user choice from an attestation standpoint
- 'Effective Status' reflects the effective control status (combination of technical status and user input)
- 'Requires Justification' indicates whether the corresponding row requires a justification comment
- 'Comments' outlines an example scenario that would map to the row



|Control Scan Result  |Attestation Status |Effective Status|Requires Justification |Comments |
|---------------------------|-------------------|---------------|-----------------------|---------|
|Passed                     |None               |Passed         |No                     |No need for attestation. Control has passed outright!|
|Verify                     |None	            |Verify         |No                     |User has to ratify based on manual examination of AzSDK evaluation log. E.g., SQL DB firewall IPs list.|
|Verify                     |NotAnIssue         |Passed         |Yes                    |User has ratified in the past. E.g., SQL firewall IPs scenario, where all are IPs are legitimate.|
|Verify                     |NotFixed           |RiskAck        |Yes                    |Valid security issue but a fix cannot be implemented immediately. E.g., A 'deprecated' account was found in the subscription. However, the user wants to check any dependecies before removal.|
|Failed                     |None	            |Failed         |No                     |Control has failed but has not been attested. Perhaps a fix is in the works...|	 
|Failed                     |NotAnIssue         |Passed         |Yes                    |Control has failed but the issue is benign in a given context business. E.g., Failover instance for a non BC-DR critical service|
|Failed                     |NotFixed           |RiskAck        |Yes                    |Control has failed. The issue is not benign but the user wishes to defer fixing it for later. E.g., AAD is not enabled for Azure SQL DB.|
|Error                      |None	            |Error          |No                     |There was an error during evaluation. Manual verification is needed and is still pending.|
|Error                      |NotAnIssue         |Passed         |Yes                    |There was an error during evaluation. However, control has been manually verified by the user.|
|Error                      |NotFixed           |RiskAck        |Yes                    |There was an error during evaluation. Manually verification by the user indicates a valid security issue.|
|Manual                     |None	            |Manual         |No                     |The control is not automated and has to be manually verified. Verification is still pending.| 
|Manual                     |NotAnIssue         |Passed         |Yes                    |The control is not automated and has to be manually verified. User has verified that there's no security concern.|
|Manual                     |NotFixed           |RiskAck        |Yes                    |The control is not automated and has to be manually verified. User has reviewed and found a security issue to be fixed.|



<br/>

The following table describes the possible effective control evaluation results (taking attestation into consideration).

|Control Scan Result| Description|
|-------------------|------------|
|Passed             |Fully automated control. Azure resource/subscription configuration meeting the AzSDK control requirement|
|Verfiy             |Semi automated control. It would emit the required data in the log files which can be validated by the user/auditor.e.g. SQL DB IP ranges|
|Failed             |Fully automated contorl. Azure resource/subscription configuration not meeting the AzSDK control requirement|
|Error              |Automated control. Currently failing due to some exception. User needs to validate manually|
|Manual             |No automation as of now. User needs to validate manually|
|RiskAck            |Risk to be Acknowledged. It would trainsition to this control if the user is trying to attest a failed control|


#### Permissions required for attesting controls:
The attestation feature internally stores state in a storage account in a resource group called AzSDKRG. (This RG is also used by other features in the AzSDK for stateful scenarios.)
If this RG has already been created, then a user needs 'Owner' permission to it.
If this RG is not present (as is possible when none of the scenarios that internally create this RG have been run yet), then the user needs 'Owner' or 'Contributor' permission to the subscription.

> **Note**: The attestation data stored in the AzSDKRG is opaque from an end user standpoint. Any attempts to access/change it may impact correctness of security evaluation results.


### FAQs
#### What Azure resource types that can be checked?
Below resource types can be checked for validating the security controls 

| Resource Name |Resource Type Name	|Resource Type|
|-------| ------------- | ----------- |
|Analysis Services |AnalysisService |Microsoft.AnalysisServices/servers|
|App Services|AppService |Microsoft.Web/sites|
|Batch accounts |Batch |Microsoft.Batch/batchAccounts|
|CDN profiles |CDN |Microsoft.Cdn/profiles|
|Cloud Services|CloudService |Microsoft.ClassicCompute/domainNames|
|Data Factories |DataFactory |Microsoft.DataFactory/dataFactories|
|Data Lake Analytics|DataLakeAnalytics |Microsoft.DataLakeAnalytics/accounts|
|Data Lake Store|DataLakeStore |Microsoft.DataLakeStore/accounts|
|Express Route-connected Virtual Networks|ERvNet |Microsoft.Network/virtualNetworks|
|Event Hubs|EventHub |Microsoft.Eventhub/namespaces|
|Key Vaults|KeyVault |Microsoft.KeyVault/vaults|
|Logic Apps|LogicApps |Microsoft.Logic/Workflows|
|Notification Hubs|NotificationHub |Microsoft.NotificationHubs/namespaces/notificationHubs|
|On-premises Data Gateways |ODG |Microsoft.Web/connectionGateways|
|Redis Caches |RedisCache |Microsoft.Cache/Redis|
|Search services |Search |Microsoft.Search/searchServices|
|Service Bus |ServiceBus |Microsoft.ServiceBus/namespaces|
|Service Fabric clusters |ServiceFabric |Microsoft.ServiceFabric/clusters|
|SQL Databases|SQLDatabase |Microsoft.Sql/servers|
|Storage Accounts|StorageAccount |Microsoft.Storage/storageAccounts|
|Traffic Manager profiles |TrafficManager |Microsoft.Network/trafficmanagerprofiles|
|Virtual Machines|VirtualMachine |Microsoft.Compute/virtualMachines|
| Virtual Networks|VirtualNetwork |Microsoft.Network/virtualNetworks|

This list continues to grow so best way to confirm is to look at the output of the following command:  
 ```PowerShell  
Get-AzSDKSupportedResourceTypes  
 ```    
>  We regularly add SVT coverage for more Azure features. Please write to us (mailto:AzSDKSupExt@microsoft.com) if you are looking for SVTs for a service not listed here.  

#### What do the different columns in the status report mean?
Status report will be in CSV format and will contain below columns 
- ControlID - Unique ID for security control.
- Status – { Passed | Failed | Verify | Manual | Error} 
	-  Passed: The resource complies with the security control.
	-  Failed: The resource does not comply with the security control.
	-  Verify: The status needs to be verified using the data generated by the automated script. This is usually an indication that some level of human judgment is required in order to attest to the control status.
	-  Manual: The control is not automated (yet). It should be manually implemented and/or validated.
	-  Error: An error occurred due to which the control state could not be determined.
- FeatureName - Azure feature name e.g. AzureAppService, AzureSQLDB etc.
- ResourceName - Azure resource name. 
- ChildResourceName - Sub resource name of the Azure resource. E.g. for SQL Server, controls validating the databases will have the resource name as ‘SQLServerName’ and child resource name as 'SQLDBName' whereas features having no sub resources will have same value for resource name and child resource name.
- 	ResourceGroupName - Resource group name for resource name.
- 	ControlType - Control type can be ‘TCP’, ‘Best Practice’ or ‘Information’. 
- 	Automated - Yes or No	    
Note that not all security checks are automatable. The 'non-automated' checks (things that have to be manually validated/checked) are also listed in the reports from a completeness standpoint.
- 	Description - Security control description.
- 	Reference - Link to detailed document/explanation.
- 	Recommendation - Recommended steps to implement a fix for a failed control.  

#### How can I find out what to do for controls that are marked as 'manual'?
Refer [TBD] for details on the security controls defined by AzSDK for Azure features. You can also email to AzSDKSupExt@microsoft.com or reach out to your security point of contact for any queries.  

#### How can I implement fixes for the failed ones which have no auto-fix available?
Refer the recommendations provided in the output CSV file for the security controls defined by AzSDK. You can also email to AzSDKSupExt@microsoft.com or reach out to your security point of contact for any queries.  

#### Troubleshooting
|Error	|Comments|
| ----- |--------|
|Subscription xxxx was not found in tenant. Please verify that the subscription exists in this tenant…."|	Provide the valid Subscription Id to the 'Subscription' parameter while running the cmdlets that accept subscription id as the parameter.|
|File xxxx cannot be loaded because the execution of scripts is disabled on this system… "|	By default, PowerShell restricts execution of all scripts. Execute below cmdlet to fix this issue: **Set-ExecutionPolicy -ExecutionPolicy Unrestricted** |  

[Back to top…](Secure_Development_userguide.md#secure-development)

--------------------------------------
# Express Route-connected Virtual Networks (ER-vNet)


**Summary**  
The following cmdlet can be used to scan the secure configuration of an ExpressRoute-connected virtual network (referred to as ERVnet below):

```PowerShel
 Get-AzSDKExpressRouteNetworkSecurityStatus -SubscriptionId <SubscriptionId>
```

This cmdlet assumes that the vNet connected to ExpressRoute circuit is in a ResourceGroup named 'ERNetwork' or 'ERNetwork-DMZ'. 
If you have the vNet in a different resource group then the `-ResourceGroup` parameter can be used.

Just like other SVTs, this will create a summary ".CSV" file of the control evaluation and a detailed
".LOG" file that includes more information about each control and the outcome.

The following core checks are currently performed by this utility:
- There should not be any Public IPs (i.e., NICs with PublicIP) on ER-vNet VMs.
- There should not be multiple NICs on ER-vNet VMs.
- The 'EnableIPForwarding' flag cannot set to true for NICs in the ER-vNet.
- There should not be any NSGs on the GatewaySubnet of the ER-vNet.
- There should not be a UDR on *any* subnet in an ER-vNet
- There should not be another virtual network gateway (GatewayType = Vpn) in an ER-vNet.
- There should not be any virtual network peerings on an ER-vNet.
- Only internal load balancers (ILBs) may be used inside an ER-vNet.

Additionally the following other 'protective' checks are also done: 
- Ensuring that the resource lock that is setup to keep these resources from being deleted is intact.
- Ensuring that the RBAC permissions for the account used to track compliance are intact.
- Setting up alerts to fire for any of the above actions in the subscription.
	
[Back to top…](Secure_Development_userguide.md#contents)
