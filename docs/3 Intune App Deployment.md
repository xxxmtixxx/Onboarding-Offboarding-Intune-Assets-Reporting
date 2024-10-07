# **Intune App Deployment**

**Description**:
This suite of PowerShell scripts designed to streamline the management of Microsoft Intune applications across multiple Azure AD tenants. This project simplifies complex tasks such as adding new tenants, managing application lifecycles, and handling security group configurations both in the cloud and on-premises environments.

The project includes a set of tools that automate the installation of the IntuneWin32App-MultiTenant module, facilitate the addition and removal of tenant applications, and manage security groups. 

## **API Download/Install**:

The module can be installed by running the following one-liner:

```powershell
$documentsPath=[Environment]::GetFolderPath('MyDocuments');$url='https://github.com/xxxmtixxx/IntuneWin32App-MultiTenant/archive/refs/heads/main.zip';$moduleName='IntuneWin32App-MultiTenant';$modulePath=Join-Path $documentsPath 'WindowsPowerShell\Modules';$tempPath=Join-Path $env:TEMP ($moduleName+'.zip');Invoke-WebRequest -Uri $url -OutFile $tempPath;$tempDir='.'+$moduleName+'_temp';$extractPath=Join-Path $HOME $tempDir;Expand-Archive -Path $tempPath -DestinationPath $extractPath -Force;$sourceFolder=Join-Path $extractPath 'IntuneWin32App-MultiTenant-main';$destinationFolder=Join-Path $modulePath $moduleName;$managerFolder=Join-Path $extractPath ('IntuneWin32App-MultiTenant-main\IntuneMultiTenantManager');$targetManagerFolder='C:\IntuneMultiTenantManager';if (!(Test-Path $destinationFolder)) {New-Item -Path $destinationFolder -ItemType Directory | Out-Null};Copy-Item -Path "$sourceFolder\*" -Destination $destinationFolder -Recurse -Force;if (!(Test-Path $targetManagerFolder)) {New-Item -Path $targetManagerFolder -ItemType Directory | Out-Null};Copy-Item -Path "$managerFolder\*" -Destination $targetManagerFolder -Recurse -Force;Remove-Item -Path $extractPath -Recurse -Force
```

---

## **How to Run**:

- After installation, close and reopen Windows PowerShell ISE as Admin.
- Run the following command to allow scripts to run:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
```
- Browse to `C:\IntuneMultiTenantManager`.
- Open `Add-IntuneMultiTenant.ps1` or `Remove-IntuneMultiTenant.ps1` and click Run.
- Choose `Yes to All` or `Yes` when prompted.

### **Add a new tenant**:

This option will create an Azure AD application, generate a self-signed certificate, configure necessary permissions through Microsoft Graph, and add the tenant to `credentials.csv`.

- Select `1: Add a new tenant`.

![alt text](assets/Intune_App_Deploy_1.png?raw=true)

- Authenticate with the tenant using a domain admin account.
- The `self-signed certificate` is generated.

![alt text](assets/Intune_App_Deploy_18.png?raw=true)

- An `App Registration` is created in `Azure` called Microsoft Intune PowerShell.

![alt text](assets/Intune_App_Deploy_19.png?raw=true)

- A link will be generated. Go to it in a browser.
- Click the `elliptical`, and `Add to configured permissions`.

![alt text](assets/Intune_App_Deploy_2.png?raw=true)

![alt text](assets/Intune_App_Deploy_3.png?raw=true)

- Click `Yes, add`.

![alt text](assets/Intune_App_Deploy_4.png?raw=true)

- Click `Grant admin concent for...`.

![alt text](assets/Intune_App_Deploy_5.png?raw=true)

- Click `Yes`.

![alt text](assets/Intune_App_Deploy_6.png?raw=true)

- Type `yes` in the powershell window and hit `Enter`.

![alt text](assets/Intune_App_Deploy_13.png?raw=true)

- The script will exit after this.

---

### **Run against an existing tenant**:

This option will allow you to select an existing tenant in the `credentials.csv`.

- Select `2: Run against an existing tenant`.

![alt text](assets/Intune_App_Deploy_7.png?raw=true)

- Select the tenant to run against.

![alt text](assets/Intune_App_Deploy_8.png?raw=true)

---

### **Run against an all tenants**:

This option will allow you to select all existing tenants in the `credentials.csv`.

- Select `3: Run against all`.

![alt text](assets/Intune_App_Deploy_9.png?raw=true)

---

### **Select Application to install**:

This option will allow you to select one or all applications to install in `applications.csv` and run agains the tenant(s) you selected.

The Security Groups **MUST** already exist in the tenant before the application will install.

![alt text](assets/Intune_App_Deploy_10.png?raw=true)

A Final Report is created after everything finishes. Here you can see a few outcomes.

- 7-Zip does not exit in Intune, so it was added.
- A Security Group does not exist for Adobe Acrobat.
- The rest of the apps already exist in Intune.

![alt text](assets/Intune_App_Deploy_11.png?raw=true)

---

### **Select Application to remove**:

This option will allow you to select one application to remove in `applications.csv` and run agains the tenant(s) you selected.

![alt text](assets/Intune_App_Deploy_12.png?raw=true)

A Final Report is created after it finishes.

![alt text](assets/Intune_App_Deploy_14.png?raw=true)

---

### **Create Security Groups in Cloud**:

This option will allow you to create the security groups defined in `applications.csv` in the tenant you selected.

- Browse to `C:\IntuneMultiTenantManager`.
- Open `Create-SecurityGroupsCloud.ps1`.

A Final Report is created after it finishes.

![alt text](assets/Intune_App_Deploy_15.png?raw=true)

---

### **Generate Script and Create Security Groups On-Prem**:

This option will allow you to generate the script required to create the security groups defined in `applications.csv` in the on-prem AD server.

- Browse to `C:\IntuneMultiTenantManager`.
- Open `Generate-SecurityGroupsOnPrem.ps1`.

![alt text](assets/Intune_App_Deploy_16.png?raw=true)

- Copy `Generate-SecurityGroupsOnPrem.ps1` to the on-prem AD server and run it.

A Final Report is created after it finishes.

![alt text](assets/Intune_App_Deploy_17.png?raw=true)

## **Script**: `C:\IntuneMultiTenantManager\Add-IntuneMultiTenant.ps1`

This PowerShell script is designed to facilitate the management of Microsoft Intune applications across multiple tenants. It automates tasks such as the addition of new tenants, executing operations on existing tenants, and handling application installations. The script features a user-friendly interface to guide administrators through various options, including adding a new tenant, running actions on a specific tenant, or applying operations across all tenants.

	- Initialization of variables and preparation of the environment, including defining paths for storing applications and credentials.
	- Installation of necessary PowerShell modules for Azure AD, Microsoft Graph, and Intune application management.
	- Dynamic downloading and importing of a custom module for Intune Win32 application management across multiple tenants.
	- A user interaction section to capture the administrator's intent, whether it's to add a new tenant, operate on an existing one, or execute actions across all tenants.
	- For new tenants, the script supports the creation of an Azure AD application, generating a self-signed certificate, and configuring necessary permissions through Microsoft Graph for comprehensive application management.
	- For existing tenants, it enables the selection of a specific tenant or all tenants and processes applications based on pre-defined criteria in CSV files.
	- Comprehensive error handling and validation checks to ensure smooth script execution and user decisions.
	- Final report generation that summarizes the operations performed and their outcomes.

---

## **Script**: `C:\IntuneMultiTenantManager\Remove-IntuneMultiTenant.ps1`

**Description**:
This PowerShell script is crafted to streamline the management of Win32 applications within Microsoft Intune across multiple tenants. It's specifically designed to handle the removal of applications, in addition to facilitating the addition of new tenants and the management of existing ones. Through a user-friendly command-line interface, the script provides administrators with options to add a new tenant, work with a specified existing tenant, or apply actions across all tenants, focusing on application removal.

	- Configuration of essential paths and the importation of necessary PowerShell modules for interacting with Azure AD, Microsoft Graph Authentication, and Intune application management capabilities.
	- A streamlined process for adding new tenants that includes creating an Azure AD application, generating a self-signed certificate, and configuring required permissions via Microsoft Graph to enable comprehensive application management within the new tenant.
	- An interactive user interface that guides administrators through selecting a tenant and choosing applications for removal, based on listings in a CSV file. This ensures that application portfolios can be accurately managed and updated across multiple tenants.
	- Enhanced security measures, including certificate-based authentication for Azure AD, to ensure secure access and management operations.
	- Detailed procedures for removing specified Win32 applications from the selected tenant(s), leveraging the script’s ability to authenticate and execute removal commands securely.
	- Final report generation that summarizes the operations performed and their outcomes.

---

**Note:** Both scripts above require the `credentials.csv` and `applications.csv` files for their operations, as they rely on this data to manage applications across multiple tenants. Ensure these CSV files are up-to-date and located in the specified paths (`Requirements\credentials.csv` and `Requirements\applications.csv`) relative to the script's root directory. Additionally, the scripts are part of a larger PowerShell module, `IntuneWin32App-MultiTenant`, which contains various functions that these scripts utilize for managing Intune applications across multiple tenants.

---

## **Script**: `C:\IntuneMultiTenantManager\Create-SecurityGroupsCloud.ps1`

**Description**:
This script will allow for the creation of `Security Groups` in the `Cloud` with the Security Group Name and Descriptions found in `Requirements\applications.csv`. 

---

## **Script**: `C:\IntuneMultiTenantManager\Generate-SecurityGroupsOnPrem.ps1`

**Description**:
This script will generate `Create-SecurityGroupsOnPrem.ps1` with the Security Group Name and Descriptions found in `Requirements\applications.csv`.

## **Script**: `C:\IntuneMultiTenantManager\Create-SecurityGroupsOnPrem.ps1`

**Description**:
This script will allow for the createion of `Security Groups` in the `On-Prem` environment after being generated with `Generate-SecurityGroupsOnPrem.ps1`.