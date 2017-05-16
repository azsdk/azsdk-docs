# Installation Guide

**Release Version: 2.0.17xxxx.yy**  
>**Pre-requisites**: PowerShell 5.0 or higher. Azure PowerShell 3.3.0 or higher.

	
1. First verify that prerequisites are already installed:  
   1. Ensure that you have PowerShell version 5.0 or higher. (In the PowerShell ISE console window, type **$PSVersionTable** and look at PSVersion.) If it is older, you should update PS from [here](https://www.microsoft.com/en-us/download/details.aspx?id=54616).  
   ![PowerShell Version](../Images/00_PS_Version.png)

   2. Confirm that you have Azure PowerShell modules by verifying the presence of a representative module using the following command:  
    ```PowerShell
	Get-Module -ListAvailable AzureRm 	
    ```
     The output should look like below (version for AzureRM should be > 3.3.0):  
   ![AzureRM Version](../Images/00_AzureRM_Version.png)  
    		
	If you don't get any output (or the version is older), please install Azure PowerShell modules from [here](https://docs.microsoft.com/en-us/PowerShell/azure/install-azurerm-ps?view=azurermps-3.7.0).
	
2. Install the AzSDK security PS module  
	  
```PowerShell
  Install-Module AzSDK -Scope CurrentUser  
```
