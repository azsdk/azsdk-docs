# Installation Guide

**Release Version: 2.2.xx**  
>**Pre-requisites**:
> - PowerShell 5.0 or higher. 
	
1. First verify that prerequisites are already installed:  
    Ensure that you have PowerShell version 5.0 or higher. (In the PowerShell ISE console window, type **$PSVersionTable** and look at PSVersion.) If it is older, you should update PS from [here](https://www.microsoft.com/en-us/download/details.aspx?id=54616).  
   ![PowerShell Version](../Images/00_PS_Version.png)   

2. Install the AzSDK security PS module:  
	  
```PowerShell
  Install-Module AzSDK -Scope CurrentUser
```

Use `-AllowClobber` and `-Force` option if you are already having a different version of AzureRM installed on your machine

