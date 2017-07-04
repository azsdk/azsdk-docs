# Installation Guide

**Release Version: 2.3.xx**  
>**Pre-requisites**:
> - PowerShell 5.0 or higher. 
	
1. First verify that prerequisites are already installed:  
    Ensure that you have PowerShell version 5.0 or higher by typing **$PSVersionTable** in the PowerShell ISE console window and looking at the PSVersion in the output as shown below.) 
If the PSVersion is older, you should update PowerShell from [here](https://www.microsoft.com/en-us/download/details.aspx?id=54616).  
   ![PowerShell Version](../Images/00_PS_Version.png)   

2. Install the Secure DevOps Kit for Azure (AzSDK) PS module:  
	  
```PowerShell
  Install-Module AzSDK -Scope CurrentUser
```

Note: You may need to use `-AllowClobber` and `-Force` options if you are already having a different version of AzureRM installed on your machine.

