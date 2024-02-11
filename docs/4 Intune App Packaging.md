# **Intune App Packaging**

The first time you run `Add-IntuneMultiTenant` or `Remove-IntuneMultiTenant`, "`C:\IntuneMultiTenantManager\Win32Apps`" will be created to store the Intune Packages.

---

## **Path**: `C:\IntuneMultiTenantManager\Win32Apps`

**Description**: This directory stores the packages to be uploaded to Intune. There is a specific folder structure used. Below is an example of AutoCAD 2024:

  - **\AutoCAD 2024**
      - **\APPLICATIONDETECTION**
          - `\ApplicationDetection.ps1`
      - **\LOGO**
          - `\logo.png`
      - **\OUTPUT**
          - `\Install-AutoCAD2024.intunewin`
      - **\SOURCE**
          - `\Install-AutoCAD2024.ps1`

---

## **Script**: `ApplicationDetection.ps1`

**Description**: This PowerShell script is designed to check the existence of a specified file on the system and provide feedback based on the result.

## **Modify these Variables**

- **$filePath**: Specify the path to the file you want to check.
    - Ex: `C:\Program Files\Autodesk\AutoCAD 2024\acad.exe`

---

## **File**: `logo.png`

**Description**: This is the `PNG` file to be loaded as the logo for the application in Intune.

---

## **Intune Script**: `Install-AutoCAD2024.intunewin`

**Description**: This Intune script will be generated automatically based on the `SOURCE PS1`. In this case `Install-AutoCAD2024.ps1`.

---

## **Script**: `Install-AutoCAD2024.ps1`

**Description**: This PowerShell script is the application installer. As long as the script can pull the installation media from an online or SMB source, it should install. Make sure the `PS1` works locally before trying to deploy package to Intune. Use `AnyAppInstaller` or any custom `PS1`.

---

## **AnyAppInstaller**: Automated Software Installation Script

- AnyAppInstaller is a PowerShell script designed to streamline the process of downloading and installing software from a specified URL or server file path. The script supports a variety of file types, including `EXE`, `MSI`, `MSIX`, and `ZIP` files.

- The sequence of operations begins with URL verification. If the URL points to a SharePoint location, the script appends "download=1" to ensure successful downloading. Following this, the script fetches the file name from the URL, preparing the local and server file paths.
- User Account Control (UAC) settings are then inspected and temporarily disabled to avoid installation prompts. The script also ensures the presence of a local temporary folder for the download.
- The script checks the availability of the file on the server. If found, it copies the file from the server to the local machine. Otherwise, the file is downloaded from the web.
- Upon successful download, the script identifies the file type and employs the relevant method for installation. For ZIP files, it extracts the contents and identifies the file type of the nested installer to determine the installation procedure.
- Post-installation, the script performs cleanup by deleting the installer and any extracted folders from the local machine. Finally, it reverts the UAC settings to their original state.
- In essence, AnyAppInstaller provides a robust solution for automating software downloads and installations, saving time and enhancing efficiency.

## **Modify these Variables**

- **$urlPath**: This variable should be set to the URL where the `EXE`, `MSI`, `MSIX`, or `ZIP` file is located. If the file is hosted on SharePoint, the script will automatically append `download=1` to the URL to ensure the file is downloaded.
	- Ex: `SharePoint Link`
- **$nestedInstallerFolderAndFile**: If the file to be installed is a `ZIP` file, this variable should be set to the name of the sub-folder or file that will be extracted from the `ZIP` file. The primary folder is not required. This variable is used to locate the installer within the extracted contents of the `ZIP` file.
	- Ex: `Setup.exe` or `sub-folder\Setup.exe`
- **$arguments**: This variable should be set to any arguments that need to be passed to the installer. These arguments will be used when running the installer.
	- Ex: `--silent`
- **$fileSharePath**: This variable should be set to the file path on the server where the file is located. The script will first check this location for the file before attempting to download it from the URL. If the file is found on the server, it will be copied to the local machine for installation.
	- Ex: `\\S-FP\IT\Software\AutoDesk`

---

## **Application CSV**: `C:\IntuneMultiTenantManager\Requirements\applications.csv`

**Description**: The `applications.csv` file is a comprehensive list that aids in the management, deployment, and uninstallation of software applications within the environment. Below is a detailed description of each column in the file:

- **DisplayName**: The name of the application as it should appear in lists or any user-facing interface.
- **Description**: A brief overview of the application, providing users or administrators with insights into its purpose or functionality. Typically set to DisplayName.
- **Publisher**: The name of the company or individual that published or developed the application.
- **InstallExperience**: Specifies the context in which the application should be installed (e.g., system or user rights).
- **SetupFile**: The name of the PowerShell script used to install the application.
- **UninstallCommandLine**: The command line instruction to uninstall the application. It may include specific uninstaller executables or use package managers like Chocolatey (choco) for certain applications. For some applications where uninstall commands are not applicable, this is marked as "N/A" (not applicable).
- **SecurityGroupName**: Identifies the security group in Active Directory or another directory service that is granted access to the application. This is critical for managing application deployments based on security groups.

---

## **Credentials CSV**: `C:\IntuneMultiTenantManager\Requirements\applications.csv`

**Description**: The `credentials.csv` file stores the necessary authentication and configuration details for accessing our cloud services and applications securely. Below, you'll find explanations for each column in the file:

- **TenantID**: The unique identifier for the tenant in the cloud service platform. This ID is crucial for directing authentication requests to the correct tenant environment.
- **Domain**: The primary (*.onmicrosoft.com) domain associated with the tenant.
- **CertificateThumbprint**: Used for secure authentication, the thumbprint corresponds to a specific certificate generated in Entra.
- **ClientID**: The identifier for the client application that is registered within the tenant. This ID is used to authenticate the application when it requests access to resources.
- **TenantName**: A human-readable name for the tenant, making it easier for administrators and users to identify the correct environment within documentation or when managing multiple tenants.

## **Security and Confidentiality**
Given the sensitive nature of the information contained in the `credentials.csv file`, it's vital to ensure that this document is stored securely and is only accessible to authorized personnel. Mishandling of this information can lead to unauthorized access to our systems and data breaches.