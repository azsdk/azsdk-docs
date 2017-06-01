# Secure Development

### Contents
### [Security Verification Tests (SVT)](Secure_Development_userguide.md#security-verification-tests-svt-1)

- [Overview](Secure_Development_userguide.md#overview)
- [Execute SVTs for all controls in all resources in a given subscription](Secure_Development_userguide.md#execute-svts-for-all-controls-in-all-resources-in-a-given-subscription)
- [Execute SVTs for specific resource groups (or tagged resources)](Secure_Development_userguide.md#execute-svts-for-specific-resource-groups-or-tagged-resources)
- [Execute SVTs for specific resource](Secure_Development_userguide.md#execute-svts-for-specific-resource)
- [Execute SVTs for specific resource type](Secure_Development_userguide.md#execute-svts-for-specific-resource-type)
- [Attest SVTs controls](Secure_Development_userguide.md#attest-svts-controls)
- [FAQs](Secure_Development_userguide.md#faqs)
### [Express Route-connected Virtual Networks (ER-vNet)](Secure_Development_userguide.md#express-route-connected-virtual-networks-er-vnet-1)

-----------------------------------------------------------------
## Security Verification Tests (SVT)
>  **Prerequisites:**
> For all commands in this feature it is assumed that you have:
> 1. Logged in to your Azure account.
> 2. Selected a subscription.
> 3. Imported the latest version of the AzSDK module using Import-Module as described in the Installation Guide.

### Overview
Security Verifications Tests (or SVTs) represent the core of security testing functionality of the AzSDK. For all the prominent features in Azure (e.g., Web Apps, Storage, SQL DB, Key Vault, etc.), the AzSDK contains cmdlets that can perform a set of automated checks against Azure resources of those types. These checks are based on security standards and best practices as applicable for HBI data at Microsoft. In general, these are likely to be applicable for most scenarios the involve processing sensitive data.

An SVT for a particular resource type basically examines a resource of that type within a specified resource group and runs the set of security checks. The outcome of the analysis is printed as a summary on the console and a CSV and a LOG file are also generated for subsequent use.

The CSV file and LOG file are generated under a subscription-specific sub-folder in the folder  
*%LOCALAPPDATA%\Microsoft\AzSDKLogs\Sub_[yourSubscriptionName]*  
E.g.  
C:\Users\UserName\AppData\Local\Microsoft\AzSDKLogs\Sub_[yourSubscriptionName]\20170331_142819

There are multiple ways that SVTs can be executed:
1. Run against all resources in a subscription. This is the simplest approach and simply enumerates all resources in a specific subscription and runs security checks against all the known resource types found.
2. Run against all resources in resource group(s). In this approach, you can target resource group(s) and simply enumerates all resources in resource group(s) and runs security checks against all the known resource types found.
3. Run against a specific resource. In this approach, you can target a specific resource.
4. Run against a specific resource type. In this approach, you can target a specific resource type and have to use the correct resource type value.
	
All the options are described below.  

[Back to top…](Secure_Development_userguide.md#contents)

### Execute SVTs for all controls in all resources in a given subscription
The below cmdlet checks security control state and generates a status report of all Azure resources in a given subscription:  

```PowerShell
Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId>
```
		
The parameters required are:
- SubscriptionId – Subscription ID is the identifier of your Azure subscription.  
    
[Back to top…](Secure_Development_userguide.md#contents)  
### Execute SVTs for specific resource groups (or tagged resources) 
 Below cmdlet generate the status report of all Azure resources under a specific subscription and resource group:
```PowerShell
Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId> -ResourceGroupNames <ResourceGroupNames>
```
	
The parameters required are:
- SubscriptionId – Subscription ID is the identifier of your Azure subscription. 
- ResourceGroupNames – Name of the container that holds related resources under an Azure subscription. Comma separated values are allowed.
	
 Below cmdlet generate the status report of all tagged resources under a specific subscription:  
1. For Single Tag
```PowerShell
    Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId> -TagName <TagName> -TagValue <TagValue>
```
		
2. For Multiple Tags
```PowerShell
    Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId> -Tag <TagHashset>
```
		
The parameters required are:
- SubscriptionId – Subscription ID is the identifier of your Azure subscription. 
- TagName – Key to identify the resources.
- TagValue – Value to identify the resources.
- Tag – The tag filter for Azure resource. The expected format is @{tagName1=$null} or @{tagName = 'tagValue'; tagName2='value1'}.  

[Back to top…](Secure_Development_userguide.md#contents)
### Execute SVTs for specific resource
 Below cmdlet generate the status report for an Azure resource under a specific subscription and resource group:
```PowerShell
Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId> -ResourceGroupNames <ResourceGroupName> -ResourceName <ResourceName>
```
	
The parameters required are:
- SubscriptionId – Subscription ID is the identifier of your Azure subscription. 
- ResourceGroupNames – Name of the container that holds related resource under an Azure subscription. 
- ResourceName – Name of the resource. 

> **Note**: In the command above, 'ResourceName' should be the short name as used in Azure and shown in the portal (as opposed to the fully qualified domain name (FQDN) which may apply to some resource types such as storage blobs or SQL DB).  

[Back to top…](Secure_Development_userguide.md#contents)
### Execute SVTs for specific resource type
Below cmdlet generate the status report for an Azure resource type under a specific subscription and resource group [optional]:
1. 	Using Azure resource type
```PowerShell
 Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId> [-ResourceGroupNames <ResourceGroupNames>] -ResourceType <ResourceType>
```
		
The parameters required are:
- SubscriptionId – Subscription ID is the identifier of your Azure subscription. 
- [Optional] ResourceGroupNames  – Name of the container that holds related resource under an Azure subscription. Comma separated values are allowed.
- ResourceType – Resource type as defined by Azure. E.g.: Microsoft.KeyVault/vaults. Run command 'Get-AzSDKSupportedResourceTypes' to get the list of supported types.

2. Using friendly resource type name
```PowerShell
 Get-AzSDKAzureServicesSecurityStatus -SubscriptionId <SubscriptionId> [-ResourceGroupNames <ResourceGroupName>] -ResourceTypeName <ResourceTypeName>
```
	
The parameters required are:
- SubscriptionId – Subscription ID is the identifier of your Azure subscription. 
- [Optional] ResourceGroupNames – Name of the container that holds related resource under an Azure subscription. Comma separated values are allowed.
- ResourceTypeName – Friendly name of resource type. E.g.: KeyVault. Run command 'Get-AzSDKSupportedResourceTypes' to get the list of supported values.  

[Back to top…](Secure_Development_userguide.md#contents)

### Attest SVTs controls

Helps address scenarios such as:
- Control result is **Verify** and attestation has to be performed and recorded
- Control result is **Failed** and one of the following scenarios applies:
  - Application team had other mitigations in place or the risk is not significant (NotAnIssue)
  - Application team understands the risk but is unable to fix it at present (Exception)

AzSDK helps with attestation by letting an application owner capture their response to the above situation via a 'record attestation' feature. The response is combined with the actual control status to derive an effective control status that is used for downstream consumption. A text justification is also recorded along with data about who performed the attestation. This information is saved in the subscription and used for subsequent AzSDK scans.

> **Note**: The attested data for controls would be persisted under the user subscription itself, inside a Resource Group called `AzSDKRG`.

1. Permissions required for Attestation
   + AzSDKRG is already present
     + User needs to have owner access on *AzSDKRG* ResourceGroup 
   + AzSDKRG is not present
     + User need to have owner access on the Subscription. The command used to Attest the controls, would create the required resources under *AzSDKRG* resource group
2. Start Attestation
      
      Subscription scan command and Azure services scan command now support a new switch called **AttestControls** with three possible options as described in the below table:

      |Attestation Option|Description|
      |------------------|-----------|
      |NotAttested|It only shows those controls which doesnt have any attestation associated with.|
      |AlreadyAttested|It only shows those controls which doesnt have any attestation associated with. You could re-attest or clear the current attestation.|
      |All|It shows all the controls which are attestable*.|

> **Note**: All the controls whose current execution status is not **Passed** are attestable.

To attest subscription scan controls, run the below command
 ```PowerShell  
$subscriptionId = <Your SubscriptionId>
Get-AzSDKSubscriptionSecurityStatus -SubscriptionId $subscriptionId -AttestControls NotAttested
 ``` 
 This command would start the attestation workflow immediately after completing the latest subscription scan. 
 It would show all the relevant information required during attestation like, control severity, description etc. as higlighted in the yellow boxes in the below picture.
 It would also show all previous attested details, if the current control already have an associated attestation.
 ![02_SVT_Attest_1](../Images/02_SVT_Attest_1.png) 
 
 After the whole attestation workflow is completed, it would show the overall summary at the end as shown below
 ![02_SVT_Attest_2](../Images/02_SVT_Attest_2.png) 

 The attestation details including justification are also captured in the CSV file as shown below:
 ![02_SVT_Attest_3](../Images/02_SVT_Attest_3.png) 

 Attestation of Azure services scan controls is also very much similar, you could run the below command to attest the failed controls for a specific resource under a resource group
 ```PowerShell  
$subscriptionId = <Your SubscriptionId>
$resourceGroupName = <ResourceGroup Name>
$resourceName = <ResourceName>
Get-AzSDKSubscriptionSecurityStatus -SubscriptionId $subscriptionId -ResourceGroupNames $resourceGroupName -ResourceName $resourceName -AttestControls NotAttested
 ``` 

 Rest of the workflow is very similar to subscription scan attestion workflow as shown above.

 3. Control Attestation table
    Below table details out of all the possible combinations of control attestations possible through this workflow in AzSDK


|Control Scan Result  |Attestation Status |Attested Status|Requires Justification |Comments |
|---------------------------|-------------------|---------------|-----------------------|---------|
|Passed                     |None               |Passed         |No                     |No need for attestation|
|Verify                     |None	            |Verify         |No                     |Data that manually attested from logs.e.g SQL DB firewall IP's|
|Verify                     |NotAnIssue         |Passed         |Yes                    |Data has been validated. No issue with the controls.e.g.SQL firewall IP's scenario, where all are legitimate IPs|
|Verify                     |NotFixed           |RiskAck        |Yes                    |Data has been validated and it has to be fixed in future. e.g. , Found deprecated account on your subscription, need to check the dependecies before removal|
|Failed                     |None	            |Failed         |No                     |Fixable control no need of attestation. e.g. enbaling auditing on your SQL DB|	 
|Failed                     |NotAnIssue         |Passed         |Yes                    |Control not relevant as per business needs.e.g turn on backup for your non critical AppService|
|Failed                     |NotFixed           |RiskAck        |Yes                    |e.g.Enabling AAD auth for your AppService|
|Error                      |None	            |Error          |No                     |Manually verification of the control is still pending|
|Error                      |NotAnIssue         |Passed         |Yes                    |Manually verified the control and passing as required by security guidelines|
|Error                      |NotFixed           |RiskAck        |Yes                    |Manually verified the control and issue has to be fixed|
|Manual                     |None	            |Manual         |No                     |Manually verification of the control is still pending| 
|Manual                     |NotAnIssue         |Passed         |Yes                    |Manually verified the control and passing as required by security guidelines|
|Manual                     |NotFixed           |RiskAck        |Yes                    |Manually verified the control and issue has to be fixed|


Description about the control results:

|Control Scan Result| Description|
|-------------------|------------|
|Passed             |Fully automated control. Azure resource/subscription configuration meeting the AzSDK control requirement|
|Verfiy             |Semi automated control. It would emit the required data in the log files which can be validated by the user/auditor.e.g. SQL DB IP ranges|
|Failed             |Fully automated contorl. Azure resource/subscription configuration not meeting the AzSDK control requirement|
|Error              |Automated contorl. Currently failing due to some exception. User needs to validate manually|
|Manual             |No automation as of now. User needs to validate manually|
|RiskAck            |Risk to be Acknowledged. It would trainsition to this control if the user is trying to attest a failed control|

Description about the attestation status values:

|Attestation Status | Description|
|-------------------|------------|
|None               | There is no attestation done for a given control. User can select this option duriung the workflow to skip the attestation|
|NotAnIssue         | User has verified the control data and attesting it as not a issue with proper justification|
|NotFixed           | User has verified the control data and attesting it as not fixed with proper justification stating the future fix plan|




### FAQs
#### What Azure resource types that can be checked?
Below resource types can be checked for validating the security controls 

|Resource Name	|Resource Type|
| ------------- | ----------- |
|AnalysisService |Microsoft.AnalysisServices/servers|
|AppService |Microsoft.Web/sites|
|Batch |Microsoft.Batch/batchAccounts|
|CDN |Microsoft.Cdn/profiles|
|CloudService |Microsoft.ClassicCompute/domainNames|
|DataFactory |Microsoft.DataFactory/dataFactories|
|DataLakeAnalytics |Microsoft.DataLakeAnalytics/accounts|
|DataLakeStore |Microsoft.DataLakeStore/accounts|
|ERvNet |Microsoft.Network/virtualNetworks|
|EventHub |Microsoft.Eventhub/namespaces|
|KeyVault |Microsoft.KeyVault/vaults|
|LogicApps |Microsoft.Logic/Workflows|
|NotificationHub |Microsoft.NotificationHubs/namespaces/notificationHubs|
|ODG |Microsoft.Web/connectionGateways|
|RedisCache |Microsoft.Cache/Redis|
|Search |Microsoft.Search/searchServices|
|ServiceBus |Microsoft.ServiceBus/namespaces|
|ServiceFabric |Microsoft.ServiceFabric/clusters|
|SQLDatabase |Microsoft.Sql/servers|
|StorageAccount |Microsoft.Storage/storageAccounts|
|TrafficManager |Microsoft.Network/trafficmanagerprofiles|
|VirtualMachine |Microsoft.Compute/virtualMachines|
|VirtualNetwork |Microsoft.Network/virtualNetworks|

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

**Note:**  
This is basic documentation for the "preview" feature. We will enhance this in the next sprint as we finalize the checks that are performed and incorporate feedback.

**Context:**  
Various teams currently request Express Route connectivity through the new myER portal. This process basically enables the CloudMS team to provision a special virtual network in the requesting team (client's) subscription with a virtual network which is connected to the Microsoft corporate ExpressRoute circuit. The ExpressRoute circuit itself is housed in a central (provider) subscription and the provisioned virtual network connects to it via a virtual network gateway. The CloudMS team creates a special resource group called "ERNetwork" (or "ERNetwork-DMZ" if you requested a connection to the enterprise DMZ and not corpnet) in the client subscription and this resource group is used to house and subsequently manage all resources that the provisioning process deploys (the vNet, the subnets, the gateway, the DNS configurations, etc.) Users in the subscription with appropriate RBAC can now deploy virtual machines in the vNet that is created and these virtual machines will be able to get onto the corporate network via the ExpressRoute connection.
Please see the writeups at the link below for more:
  [TBD]

Note: This feature applies to the ARM-model of Express Route connectivity setup (and not the old ASM-based model).

**Requirement**  
Because the vNet in the ERNetwork resource group is corpnet (or DMZ) connected, users/owners in the client subscription have to manage the networking resources that are involved in the ExpressRoute connectivity carefully. Various errors and misconfigurations can lead to gaps in enterprise network security. The CloudMS/NIS teams expect that the following discipline is observed by users of this service:
  [TBD]

A tool called "ScanPro" has been used to scan all subscriptions that have been provisioned with ER connectivity. 

However, we would like application teams that have ER-connectivity in their subscriptions to be more proactive and include automation in their regular suite of tests to check for security concerns with the ER-setup.

The current (preview) release of this feature can be invoked after installing the DevOps kit (per Installation Guide) and then (after logging in to Azure) running the following simple cmdlet:

```PowerShel
 Get-AzSDKExpressRouteNetworkSecurityStatus -SubscriptionId <SubscriptionId>
```

Just like other SVTs, this will create a summary ".CSV" file of the control evaluation and a detailed ".LOG" file that includes more information about each control and the outcome.

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
- Ensuring that the resource lock that's setup to keep these resources from being deleted is intact.
- Ensuring that the RBAC permissions for the account used to track compliance are intact.
- Setting up alerts to fire for any of the above actions in the subscription.
	
The above set of checks were designed in close collaboration with CloudMS and NIS teams.  

[Back to top…](Secure_Development_userguide.md#contents)
