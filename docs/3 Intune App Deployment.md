# **Intune App Deployment**

## **API Download/Install**

The module can be installed by unzipping the master zip into one of your powershell modules folder, or by running the following one-liner:

```powershell
$documentsPath=[Environment]::GetFolderPath('MyDocuments');$url='https://github.com/xxxmtixxx/IntuneWin32App-MultiTenant/archive/refs/heads/main.zip';$moduleName='IntuneWin32App-MultiTenant';$modulePath=Join-Path $documentsPath 'WindowsPowerShell\Modules';$tempPath=Join-Path $env:TEMP ($moduleName+'.zip');Invoke-WebRequest -Uri $url -OutFile $tempPath;$tempDir='.'+$moduleName+'_temp';$extractPath=Join-Path $HOME $tempDir;Expand-Archive -Path $tempPath -DestinationPath $extractPath -Force;$sourceFolder=Join-Path $extractPath 'IntuneWin32App-MultiTenant-main';$destinationFolder=Join-Path $modulePath $moduleName;$managerFolder=Join-Path $extractPath ('IntuneWin32App-MultiTenant-main\IntuneMultiTenantManager');$targetManagerFolder='C:\IntuneMultiTenantManager';if (!(Test-Path $destinationFolder)) {New-Item -Path $destinationFolder -ItemType Directory | Out-Null};Copy-Item -Path "$sourceFolder\*" -Destination $destinationFolder -Recurse -Force;if (!(Test-Path $targetManagerFolder)) {New-Item -Path $targetManagerFolder -ItemType Directory | Out-Null};Copy-Item -Path "$managerFolder\*" -Destination $targetManagerFolder -Recurse -Force
```

---

## **How to Run**

After installation, close and reopen Windows PowerShell ISE.

---

## **Command**: `Add-IntuneMultiTenant`

**Description:**
This PowerShell script is designed to facilitate the management of Microsoft Intune applications across multiple tenants. It automates tasks such as the addition of new tenants, executing operations on existing tenants, and handling application installations. The script features a user-friendly interface to guide administrators through various options, including adding a new tenant, running actions on a specific tenant, or applying operations across all tenants.

Key functionalities include:

- Initialization of variables and preparation of the environment, including defining paths for storing applications and credentials.
- Installation of necessary PowerShell modules for Azure AD, Microsoft Graph, and Intune application management.
- Dynamic downloading and importing of a custom module for Intune Win32 application management across multiple tenants.
- A user interaction section to capture the administrator's intent, whether it's to add a new tenant, operate on an existing one, or execute actions across all tenants.
- For new tenants, the script supports the creation of an Azure AD application, generating a self-signed certificate, and configuring necessary permissions through Microsoft Graph for comprehensive application management.
- For existing tenants, it enables the selection of a specific tenant or all tenants and processes applications based on pre-defined criteria in CSV files.
- Comprehensive error handling and validation checks to ensure smooth script execution and user decisions.
- Final report generation that summarizes the operations performed and their outcomes.

---

## **Command**: `Remove-IntuneMultiTenant`

**Description:**
This PowerShell script is crafted to streamline the management of Win32 applications within Microsoft Intune across multiple tenants. It's specifically designed to handle the removal of applications, in addition to facilitating the addition of new tenants and the management of existing ones. Through a user-friendly command-line interface, the script provides administrators with options to add a new tenant, work with a specified existing tenant, or apply actions across all tenants, focusing on application removal.

Key functionalities include:

- Configuration of essential paths and the importation of necessary PowerShell modules for interacting with Azure AD, Microsoft Graph Authentication, and Intune application management capabilities.
- A streamlined process for adding new tenants that includes creating an Azure AD application, generating a self-signed certificate, and configuring required permissions via Microsoft Graph to enable comprehensive application management within the new tenant.
- An interactive user interface that guides administrators through selecting a tenant and choosing applications for removal, based on listings in a CSV file. This ensures that application portfolios can be accurately managed and updated across multiple tenants.
- Enhanced security measures, including certificate-based authentication for Azure AD, to ensure secure access and management operations.
- Detailed procedures for removing specified Win32 applications from the selected tenant(s), leveraging the script’s ability to authenticate and execute removal commands securely.
- Final report generation that summarizes the operations performed and their outcomes.

---

**Note:** Both scripts above require the `credentials.csv` and `applications.csv` files for their operations, as they rely on this data to manage applications across multiple tenants. Ensure these CSV files are up-to-date and located in the specified paths (`Requirements\credentials.csv` and `Requirements\applications.csv`) relative to the script's root directory. Additionally, the scripts are part of a larger PowerShell module, `IntuneWin32App-MultiTenant`, which contains various functions that these scripts utilize for managing Intune applications across multiple tenants.

---

## **Roadmap**:

## **Command**: `Create-SecurityGroupsCloud`

**Description:**
This script is currently under development, but will allow for the creation of `Security Groups` in the `Cloud`. 

## **Command**: `Create-SecurityGroupsOnPrem`

**Description:**
This script is currently under development, but will allow for the createion of `Security Groups` in the `On-Prem` environment. Will be included with `Prepare Onboarding/Offboarding Data Gateway` script and `Datto`.