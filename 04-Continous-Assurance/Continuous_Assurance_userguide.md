# Baseline Continuous Assurance
### Contents
- [Overview](Continuous_Assurance_userguide.md#overview)
- [Setting up Continuous Assurance - Step by Step](Continuous_Assurance_userguide.md#setting-up-continuous-assurance---step-by-step)
- [Baseline Continuous Assurance - how it works (under the covers)](Continuous_Assurance_userguide.md#baseline-continuous-assurance---how-it-works-under-the-covers)
- [Update existing Continuous Assurance Automation Account](Continuous_Assurance_userguide.md#update-existing-continuous-assurance-automation-account)
- [Remove Continuous Assurance Automation Account](Continuous_Assurance_userguide.md#remove-continuous-assurance-automation-account)
- [Fetch details of an existing Continuous Assurance Automation Account](Continuous_Assurance_userguide.md#fetch-details-of-an-existing-continuous-assurance-automation-account)
- [FAQ](Continuous_Assurance_userguide.md#faq)

-----------------------------------------------------------------
### Overview 
The basic idea behind Continuous Assurance (CA) is to create the ability to check for "drift" from what is considered a secure snapshot of a system. Support for Continuous Assurance lets us treat security truly as a 'state' as opposed to a 'point in time' achievement. 

There can be two types of drift: 
1. Drift involving 'baseline' configuration:
This involves settings that have a fixed number of possible states (often pre-defined/statically determined ones). For instance, a SQL DB can have TDE encryption turned ON or OFF…or a Storage Account may have auditing turned ON however the log retention period may be less than 365 days. 	 
2. Drift involving 'stateful' configuration: There are settings which cannot be constrained within a finite set of well-known states. For instance, the IP addresses configured to have access to a SQL DB can be any (arbitrary) set of IP addresses. In such scenarios, usually human judgment is initially required to determine whether a particular configuration should be considered 'secure' or not. However, once that is done, it is important to ensure that there is no "stateful drift" from the attested configuration. (E.g., if, in a troubleshooting session, someone adds the IP address of a developer machine to the list, the Continuous Assurance feature should be able to identify the drift and generate notifications/alerts or even trigger 'auto-remediation' depending on the severity of the change). 

**Note-1**: At present, the AzSDK supports the first kind of drift tracking. In H2, we will be focusing on 'stateful drift'. Besides, drift tracking, we will also be adding support for two other aspects of "staying compliant". First of them is the simple concept that if new, more secure options become available for a feature, it should be possible to detect that a particular application or solution can benefit from it and notify/alert the owners concerned. In a way this can be thought of as facilitating "positive" security drift. The other aspect is about supporting "operational hygiene". In this area, we will add the ability to systematically remind an app team about the security hygiene tasks that they need to periodically perform (key rotation, access reviews,  removing inactive/dormant power users, etc.).

**Note-2**: If you have already installed Continuous Assurance Automation Account (Name: AzSDKCCAutomationAccount) before 5th May, 2017; you require to run 'Install-AzSDKContinuousAssurance' command again by following steps in 'Setting up Continuous Assurance - Step by Step' section below to install new version.

[Back to top…](Continuous_Assurance_userguide.md#contents)
### Setting up Continuous Assurance - Step by Step
In this section, we will walk through the steps of setting up a subscription and application(s) for Continuous Assurance coverage. 
To get started, we need the following:
1. User must have 'Owner' access to the Subscription.
2. Decide a name for the Azure Active Directory(AD) Application that will be created in the above subscription. This AAD App would be exported with user's self-signed certificate. For runbook authentication, connection asset named AzureRunAsConnection would be created in Automation Account which holds the applicationId, tenantId, subscriptionId, and certificate thumbprint. 
3. *Target OMS workspace ID and sharedKey. (the OMS workspace can be in a different subscription, see note below)


**\*Note**: CA leverages an OMS repository for aggregating security scan results, you must determine which OMS workspace you will use to view the security state of your subscription and applications (If you don't have an OMS repository please follow the steps in [Setting up the AzSDK OMS Solution](../05-Alerting-and-Monitoring/Alert_Montoring_userguide.md) ). This can be a single workspace that is shared by multiple applications which may themselves be in different subscriptions. Alternately, you can have an OMS workspace that is dedicated to monitoring a single application as well. (In gist, use whatever workspace that is being used to monitor other aspects like availability, performance, etc. for your application.)

**Step-1: Setup**  
1. Open the PowerShell ISE and login to your Azure account (using **Login-AzureRmAccount**).  
2. Run the '**Install-AzSDKContinuousAssurance**' command with required parameters given in below table. 

```PowerShell
	Install-AzSDKContinuousAssurance -SubscriptionId <SubscriptionId> `
		[-AutomationAccountLocation <AutomationAccountLocation>] `
	        -ResourceGroupNames <ResourceGroupNames> `
	        -OMSWorkspaceId <OMSWorkspaceId> `
	        -OMSSharedKey <OMSSharedKey> `
	        -AzureADAppName <AzureADAppName>
```

|Param Name|Purpose|Required?|Default value|Comments|
|----|----|----|----|----|
|SubscriptionId|Subscription ID of the Azure subscription in which an Automation Account for Continuous Assurance will be created |TRUE|None||
|AutomationAccountLocation|The location in which this cmdlet creates the Automation Account|FALSE|EastUS2|To obtain valid locations, use the Get-AzureRMLocation cmdlet|
|ResourceGroupNames|Comma separated list of resource groups within which the application resources are contained.|TRUE|None||
|OMSWorkspaceId|Workspace ID of OMS which is used to monitor security scan results|TRUE|None||
|OMSSharedKey|Shared key of OMS which is used to monitor security scan results|TRUE|None||
|AzureADAppName|Name for the Azure Active Directory(AD) Application that will be created in the subscription for running the runbook|TRUE|None||

The above command triggers the process of setting up various artifacts in the application subscription. 

**Note**: Completion of this one-time setup activity can take up to 2 hours. (This is because one of the things that setup does is download and add PowerShell modules for Azure PS library and for AzSDK. This is a slow and sometimes flaky process and, as a result, the setup internally retries failed downloads. The PG is aware of this challenge and are working on a resolution.)




**Step-2: Verifying that CA Setup is complete**  
**1:** In the Azure portal, select the application subscription that was used above and search for resources of type Automation Account. You should see an Automation Account created by the name 'AzSDKContinuousAssurance'. Clicking on it will display the contents of the Automation Account (something that looks like the below, the counts shown may vary a little):

 ![04_CA_Overview_Screen](../Images/04_CA_Overview_Screen.png)

**2:** Click on the 'Assets' tile for the Automation Account and select 'Modules'. It should show 'Status' column value for all modules as 'Available' (The counts shown may vary).

 ![04_CA_Modules](../Images/04_CA_Modules.png)
	
**3:** Click on 'Runbooks' tile. It should show the following runbook:

 ![04_CA_RunBooks](../Images/04_CA_RunBooks.png)

**4:** Click on 'Schedules' tile. It should show the scheduling details of runbook. You can change the schedule timings according to your need. Default schedule is created as below. First job will run ten minutes after the installation:

 ![04_CA_Schedule](../Images/04_CA_Schedule.png)

**5:** Click on 'Run As Accounts' tile. It should show the following account:

 ![04_CA_Run_as_Account](../Images/04_CA_Run_as_Account.png)

**Step-3: Verifying that all required modules are downloaded successfully (After two hours of installation)**

**1**: Click on the 'Modules' tile for the Automation Account. 'AzSDK' module should be listed there. 'Status' column value for all modules should be 'Available' as below.

 ![04_CA_Downloaded_Modules](../Images/04_CA_Downloaded_Modules.png)
 
**Step-4: Verifying CA Runbook execution and OMS connectivity**  
Once CA setup and modules download are completed successfully, the runbooks will automatically execute periodically (once a day) and scan the subscription and the specified resource groups for the application(s) for security issues. The outcomes of these scans will get stored in a storage account created by the installation (format : azsdk\<YYYYMMDDHHMMSS> e.g. azsdk20170505181008) and follows a similar structure as followed by standalone SVT execution (CSV file, LOG file, etc.).    

The results of the control evaluation are also routed to the OMS repository for viewing via a security dashboard.  
  
Let us verify that the runbook output is generated as expected and that the OMS connectivity is setup and working correctly.

**1:** Verify that CSV file and LOG file are getting generated as expected.  
 
1. Go to Storage Explorer and look for a storage account with a name in azsdk<YYYYMMDDHHMMSS> format in your subscription in 'AzSDKRG' resource group.
2. Find a container called 'azsdkexecutionlogs' in this storage account.
3. There should be a ZIP file named using a timestamp based on the date time for the manual execution in this container (most likely the ZIP file with the most recent creation date). 
4. Download the ZIP file and extract its contents locally. The folder structure will be similar to how SVTs/Subscription Health scan generate when run locally. 
5. In a single zip file you will find two folders (name format: Timestamp). One folder contains reports of Subscription Health scan and another folder contains reports of application(s) resource groups security scan.
	
 ![04_CA_Storage_Container](../Images/04_CA_Storage_Container.png)

**2:** Verify that data is being sent to the target OMS workspace   

1. Go to the OMS dashboard that we used to setup CA above.
2. In the 'Search' window, enter Type=AzSDK_CL Source_s=CC. Source will be changed to 'CA' in future.
3. You should see results similar to the below:
	
 ![04_CA_OMS](../Images/04_CA_OMS.PNG)

Once CA is setup in the subscription, an app team can start leveraging the OMS Solution from AzSDK as a one-stop dashboard for visibility of security state. Please follow the steps in the OMS solution setup (in Alerting & Monitoring sub-section of this notebook) to enable that part.

[Back to top…](Continuous_Assurance_userguide.md#contents)
### Baseline Continuous Assurance - how it works (under the covers)
The baseline CA feature is about tracking baseline configuration drift. This is achieved by enabling support for running AzSDK SVTs/SS-Health via automation runbook. 

The CA installation script that sets up CA creates the following resources in your subscription:

- Resource group (Name : AzSDKRG) :- 
To host all the Continuous Assurance artifacts
- Storage account (Format : azsdkYYYYMMDDHHMMSS) :- To store the daily results of CA scans. The storage account is named with a timestamp-suffix applied to 'azsdk'(e.g. azsdk20170420111140)
- Azure AD App and Service Principal :- This is used as the runtime identification of the automation runbook. Adds SPN to 'Reader' role on the subscription and contributor role on the resource group containing Automation Account.
- Automation Account (Name : AzSDKContinuousAssurance) :- Creates the following assets within the Automation Account,
   - Runbook (Name : Continuous_Assurance_Runbook) - To download/update Azure/AzSDK modules and scan subscription/app resource groups  
   - Variables 
      - AppResourceGroupNames 
      - OMSWorkspaceId 
      - OMSSharedKey 
      - ReportLogsStorageAccountName
   - Azure Run As Account - To authenticate runbook at runtime  
      This account uses below certificate and connection.  
      AzureRunAsCertificate - This certificate gets expired after six months of installation  
      AzureRunAsConnection - This connection is created using service principal with a AzureRunAsCertificate
   - Two schedules to trigger the runbook :-
      - Scan_Schedule - This is to trigger job to scan subscription and app resource groups
      - Next_Run_Schedule - This is temporary schedule created by runbook to retry download of modules
   - Modules - Downloaded by the runbook
   
About 63 assets are created overall.

#### Next Steps
Once CA is setup in the subscription, an app team can start leveraging the OMS Solution from AzSDK as a one-stop dashboard for visibility of security state.

[Back to top…](Continuous_Assurance_userguide.md#contents)
### Update existing Continuous Assurance Automation Account
1. Open the PowerShell ISE and login to your Azure account (using **Login-AzureRmAccount**).  
2. Run the '**Update-AzSDKContinuousAssurance**' command with required parameters given in below table. 

```PowerShell
Update-AzSDKContinuousAssurance -SubscriptionId <SubscriptionId> `
    [-ResourceGroupNames <ResourceGroupNames>] `
    [-OMSWorkspaceId <OMSWorkspaceId>] `
    [-OMSSharedKey <OMSSharedKey>] `
    [-AzureADAppName <AzureADAppName>] `
    [-UpdateCertificate]
```

|Param Name|Purpose|Required?|Default value|Comments
|----|----|----|----|----|
|SubscriptionId|Subscription ID of the Azure subscription in which Automation Account exists |TRUE|None|
|ResourceGroupNames|Add this parameter if you want to update the comma separated list of resource groups within which the application resources are contained|FALSE|None|
|OMSWorkspaceId|Add this parameter if you want to update the workspace ID of OMS which is used to monitor security scan results|FALSE|None|
|OMSSharedKey|Add this parameter if you want to update the shared key of OMS which is used to monitor security scan results|FALSE|None|
|AzureADAppName|Add this parameter if you want to update the connection (used for running the runbook) with new AD App and Service principal|FALSE|None|This is useful if existing connection is changed/removed by mistake
|UpdateCertificate|Add this switch to renew/update certificate. This is useful when certificate gets expired after six months of installation|FALSE|None|

[Back to top…](Continuous_Assurance_userguide.md#contents)
### Remove Continuous Assurance Automation Account
1. Open the PowerShell ISE and login to your Azure account (using **Login-AzureRmAccount**).  
2. Run the '**Remove-AzSDKContinuousAssurance**' command as below. 

```PowerShell
Remove-AzSDKContinuousAssurance -SubscriptionId <SubscriptionId>  [-DeleteStorageReports] 
```
|Param Name |Purpose |Required?	|Default value	|Comments|
|-----|-----|-----|----|-----|
|SubscriptionId	|Subscription ID of the Azure subscription in which Automation Account exists |TRUE |None||	 
|DeleteStorageReports |Add this switch to delete AzSDK execution reports from storage account. This will delete whole storage container where reports are stored. |FALSE |None||	

[Back to top…](Continuous_Assurance_userguide.md#contents)
### Fetch details of an existing Continuous Assurance Automation Account
1. Open the PowerShell ISE and login to your Azure account (using **Login-AzureRmAccount**).  
2. Run the '**Get-AzSDKContinuousAssurance**' command as below. 
```PowerShell
Get-AzSDKContinuousAssurance -SubscriptionId <SubscriptionId> 
```
**Note:** This command is compatible only for Automation Account installed after 5th May, 2017 AzSDK release.

[Back to top…](Continuous_Assurance_userguide.md#contents)
### FAQ
#### Troubleshooting
Please reach out to us at AzSDKSupExt@microsoft.com if you face any issues with this feature.  
[Back to top…](Continuous_Assurance_userguide.md#contents)
