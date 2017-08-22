# Installation Guide
> <h4>IMPORTANT:</h4> If you are from MSIT, please install via instructions at http://aka.ms/azsdkdocs so that IT-specific policies get setup for you. <u>Do not</u> use this page.


**Release Version: 2.4.xx**  
>**Pre-requisites**:
> - PowerShell 5.0 or higher. 
	
1. First verify that prerequisites are already installed:  
    Ensure that you have PowerShell version 5.0 or higher by typing **$PSVersionTable** in the PowerShell ISE console window and looking at the PSVersion in the output as shown below.) 
 If the PSVersion is older than 5.0, update PowerShell from [here](https://www.microsoft.com/en-us/download/details.aspx?id=54616).  
   ![PowerShell Version](../Images/00_PS_Version.png)   

2. Install the Secure DevOps Kit for Azure (AzSDK) PS module:  
	  
```PowerShell
  Install-Module AzSDK -Scope CurrentUser
```

Note: You may need to use `-AllowClobber` and `-Force` options with the Install-Module command 
above if you have a different version of AzureRM installed on your machine. 
AzSDK depends on a specific version of AzureRM and installs that during the installation above.  

------------------------------------------------
### FAQs

#### How to run AzSDK commands when both AzureRM 4.0 and AzureRM 3.8 are present?
**Approach 1:**
1. Open a new PowerShell session. 
2. Run any of the AzSDK commands directly “Get-AzSDKAzureServicesSecurityStatus”.  
>Note: If you try to login first and then PS would by default load 4.0.1 which would start failing with 3.8.0

**Approach 2:**
1. Open a new PowerShell session
2. Import-Module -Name AzureRM -RequiredVersion 3.8.0
3. Login-AzureRmAccount
4. Run any AzSDK commands.

#### Should I run PowerShell ISE as administrator or regular user?
Please run PowerShell ISE as a regular user. The AzSDK has been thoroughly tested to run in normal user (non-elevated) mode. As much as possible, please do not launch your PS sessions in "Administrator" mode. There is nothing that the AzSDK does that needs elevated privileges on the local system. Even the installation command itself uses a '-Scope CurrentUser' parameter internally.  

#### Error message: "Running scripts is disabled on this system..."
This is an indication that PowerShell script loading and execution is disabled on your machine. You will need to enable it before the AzSDK installation script (which itself is a PowerShell script) can run. 
```PowerShell
Get-ExecutionPolicy -Scope CurrentUser
```
If you run above command in the PS console, you will likely see that the policy level is either 'Restricted' or 'Undefined'. For AzSDK cmdlets to run, it needs to be set to 'RemoteSigned'.
To resolve this issue run the following command in your PS console:
```PowerShell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```
The execution policy setting will be remembered and all future PS consoles opened in non-Admin (CurrentUser) mode will apply the 'RemoteSigned' execution policy.

#### Error message: "WARNING: The version '3.x.y' of module 'AzureRM.Profile' is currently in use. Retry the operation after closing..."
If you see multiple warning such as the above during setup, it is likely that one or more PowerShell instances are running and have AzureRm modules loaded which are conflicting with the AzSDK installation. In such as case, it is best to ensure that all PS sessions (including the current one) are closed and start the installer in a fresh PS ISE session.
In dire circumstances, you may need to close/kill all instances of PowerShell sessions running on your system (including VS if you have PS plugin installed). In that case, make sure you have saved any work in those sessions and then run the following in a Windows Cmd console:
```PowerShell
taskkill /im PowerShell_ise.exe & taskkill /im PowerShell.exe & taskkill /im PowerShellToolsProcessHost.exe
```  
#### Error message: "The property 'Id' cannot be found on this object. Verify that the property exists..."
This is typically caused by a version mis-match for underlying AzureRm PowerShell modules that are used by AzSDK! The AzSDK installation process will typically install AzureRm modules corresponding to the version that it depends on. However, it is possible that you also have a previous version of AzureRm on your machine.  

PowerShell works with the concept of 'sessions'. Each PowerShell ISE or command prompt window is its own independent session. By default, when any cmdlet is run, PS will load the corresponding module (code) in the memory for that session. After that, subsequent references to cmdlets in that module will use the loaded module. Usually, the module loading follows a particular heuristic (based on version number, PS module path order, admin v. non-admin PS launch mode, etc.)  

The above error message is an indication that an AzSDK cmdlet is being run in a PowerShell session that already had an older version of AzureRm loaded in memory (which may be due to something as simple as doing a Login-AzureRmAccount and *then* installing AzSDK in that session). In most circumstances, one of the following remedies should work:
- Close the PS session and open a new one. In the new session, do an "*Import-Module AzureRm -RequiredVersion 4.1.0*" before running anything else (e.g., Login-AzureRmAccount)
- Close the PS session and open a new one. In the new session, do an "*Import-Module AzSDK*" before running anything else. (This will force-load the correct version of AzureRm that AzSDK needs.). 
  - If you suspect that you may have multiple versions of AzSDK itself installed, then use "*Import-Module AzSDK -RequiredVersion 2.4.0*" (July release).  

#### Message: "Warning : Microsoft Azure PowerShell collects data about how users use PowerShell cmdlets..."
The AzSDK depends upon AzureRm PowerShell modules. AzureRm modules are created/maintained by the Azure product team and provide the core PowerShell libraries to interact with different Azure services. For example, you'd use the AzureRm Storage module to create/work with a storage account, etc.  

The AzSDK setup (when you use the "iwr..." command) installs the required version of AzureRm. It is possible that this is the first time your system is being setup for AzureRm. In such a situation, you will get a 'data collection' related notice/warning from AzureRm. You can choose to 'accept' or 'decline' permission to collect data. The AzSDK functionality will not be affected by that.  
