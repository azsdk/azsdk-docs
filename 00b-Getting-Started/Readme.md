# Getting started with the Secure DevOps Kit for Azure!

If you have just installed the Secure DevOps Kit for Azure (a.k.a. AzSDK) and are not familiar with 
its functionality, then you can get started with the 2 most basic use cases of AzSDK by going through 
the following getting started guides:
- [Scan the security of your subscription](GettingStarted_SubscriptionSecurity.md)
- [Scan the security of your cloud application](GettingStarted_AzureServiceSecurity.md)  

Thereafter, you can explore individual features further using the table of contents below which has 
pointers to full help on individual features by feature area.

> **Note:** If you have not installed the DevOps Kit yet, follow the instructions in the [installation guide](../00a-Setup/Readme.md) and then come back here.

The overall set of features in the Secure DevOps Kit for Azure are organized by the 6 areas as shown 
in the table below:  


|Feature Area | Secure DevOps Kit Feature|
|-------------|--------------------------|
[Subscription Security](../01-Subscription-Security/Readme.md) | <ul><li>Subscription Security Health Check</li> <li>Subscription Provisioning<ul><li> Alerts Configuration</li>  </li> <li>ARM Policy Configuration</li> <li>Azure Security Center (ASC) Configuration</li><li>Access control (RBAC) Hygiene</li>  </ul> </li></ul>
[Secure Development](../02-Secure-Development/Readme.md) | <ul><li>Security Verification Tests (SVTs) </li><li>Security IntelliSense VS Editor Extension </li></ul>
[Security in CICD](../03-Security-In-CICD/Readme.md) | <ul><li>AzSDK-SVTs VSTS extension for injecting security tests in a CICD pipeline </li></ul>
[Continuous Assurance](../04-Continous-Assurance/Readme.md) | <ul><li>Security scanning of Azure subscription and applications via automation runbooks</li></ul>
[Alerting & Monitoring](../05-Alerting-and-Monitoring/Readme.md) | <ul><li>Leveraging OMS towards:<ul><li>Single pane view of security across dev ops stages</li><li>Security alerts based on various search conditions.</li></ul></li></ul>
[Cloud Risk Governance](../06-Security-Telemetry/Readme.md) | <ul><li>Support for control state attestation and security governance dashboards. </li></ul> 

## List Of AzSDK commands

|Command|What it does|	Role/Permission|
|----|----|-----|
|Get-AzSDKAzureServicesSecurityStatus|Scans a set of RGs (or the entire subscription)|Reader on subscription or respective RGs|
|Get-AzSDKContinuousAssurance|Validates the status of Continuous Assurance automation account including the condition of various artifacts such as storage account, schedules, runbooks, SPN/connection, required modules, etc.|Reader on subscription.|
|Get-AzSDKControlsStatus|Single cmdlet that combines Get-AzSDKSubscriptionSecurityStatus, Get-AzSDKAzureServicesSecurityStatus|Union of permissions.|
|Get-AzSDKExpressRouteNetworkSecurityStatus	|Validate secure configuration of ER-connected vNets. Also validates custom/supporting protections |Reader on ERNetwork, Reader on sub.|
|Get-AzSDKSubscriptionSecurityStatus|Scans an Azure subscription for security best practices and configuration baselines for things such as alerts, ARM policy, RBAC, ASC, etc.|Reader on subscription|
|Get-AzSDKSupportedResourceTypes|Lists the currently supported Azure service types in AzSDK. Basically, all resources in this list have SVTs available and these SVTs will be invoked whenever Get-AzSDKAzureServicesSecurityStatus is run.|NA.|
|Install-AzSDKContinuousAssurance|Sets up continuous assurance for a subscription. This creates various artifacts such as resource group, storage account and automation account| Owner on subscription.|
|Install-AzSDKOMSSolution|Creates and deploys an OMS view in a subscription that has an OMS workspace. The OMS view provides visibility to application state across dev ops stages. It also creates alerts, common search queries, etc.	|Reader on subscription.|
|Remove-AzSDKAlerts|Removes the alerts configured by AzSDK.|Owner on subscription.|
|Remove-AzSDKARMPolicies|Removes the ARM policy configured by AzSDK.|Owner on subscription.|
|Remove-AzSDKContinuousAssurance|Removes the AzSDK CA setup (including, optionally, the container being used for storing reports).|Reader on subscription.|
|Remove-AzSDKSubscriptionRBAC|Removes the RBAC setup by AzSDK. By default "mandatory" central accounts are not removed and "deprecated" accounts are always removed.|Owner on subscription.|
|Remove-AzSDKSubscriptionSecurity|Removes the configuration done via Set-AzSDKSubscriptionSecurity. It invokes the individual remove commands for RBAC, ARM policy, Alerts and ASC.|Owner on subscription.|
|Set-AzSDKAlerts|Sets up activity alerts for the subscription. Includes alerts for subscription and resource specific activities. Alerts can be scopes to subscription or RGs.<br>This is internally called by Set-AzSDKSubscriptionSecurity.|Owner on subscription.
|Set-AzSDKARMPolicies|Sets up a core set of ARM policies in a subscription.<br>This is internally called by Set-AzSDKSubscriptionSecurity.|Owner on subscription.|
|Set-AzSDKAzureSecurityCenterPolicies|Sets up ASC policies and security points of contact. <br>This is internally called by Set-AzSDKSubscriptionSecurity.|Reader on subscription.|
|Set-AzSDKEventHubSettings|Configures AzSDK to send scan results to the provided EventHub. Currently available only in 'ad hoc' or 'SDL' mode.|NA|	
|Set-AzSDKLocalControlTelemetrySettings|The command configures the AzSDK toolkit to send data to the given Applications Insights account from user's machine.|NA|
|Set-AzSDKOMSSettings|Configures AzSDK to send scan results to the provided OMS workspace. Events can be sent to OMS from 'ad hoc'/SDL mode (via this configuration) or from CICD by specifying OMS settings in a variable or from CA by specifying OMS settings in the CA installation command.|Reader on subscription.|
|Set-AzSDKPolicySettings|Configures the server URL that is used by AzSDK to download controls and config JSON. If this is not called, AzSDK runs in an 'org-neutral' mode using a generic policy. Once this command is called, AzSDK gets provisioned with the URL of a server/CDN where it can download control/config JSON from.|Reader on subscription.|
|Set-AzSDKSubscriptionRBAC|Sets up RBAC for a subscription. Configures "mandatory" accounts by default and function/scenario specific accounts if additional "tags" are provided.|Owner on subscription.|
|Set-AzSDKSubscriptionSecurity|Master command that takes combined inputs and invokes the individual setup commands for RBAC, ARM policy, Alerts and ASC.|Owner on subscription.|
|Set-AzSDKUsageTelemetryLevel|Command to switch the default TM level for AzSDK. The generic version of AzSDK comes with 'Anonymous' level telemetry. The other levels supported is 'None'. |NA|	
|Set-AzSDKWebhookSettings|Configures AzSDK to send scan results to the provided webhook. Currently available only in 'ad hoc' or 'SDL' mode.<br>This capability can be used to receive AzSDK scan results in arbitrary downstream systems. (E.g., Splunk)|NA|
|Update-AzSDKContinuousAssurance|Changes the parameters with which CA is currently setup. Can be used to change Resource Groups, OMS Workspace ID, OMS Shared Key, Connection in Run as Account, Update/Renew Certificate in Run as Account.|Reader on subscription.|



