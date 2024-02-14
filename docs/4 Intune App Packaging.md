# **Intune App Packaging**

The first time you run `Add-IntuneMultiTenant` or `Remove-IntuneMultiTenant`, "`C:\IntuneMultiTenantManager\Win32Apps`" will be created to store the Intune Packages.

After you create the installer scripts, be sure to add the details to the `Requirements\applications.csv` file. See image below in file details.

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

## **Application CSV**: `C:\IntuneMultiTenantManager\Requirements\applications.csv`

**Description**: The `applications.csv` file is a comprehensive list that aids in the management, deployment, and uninstallation of software applications within the environment. Below is a detailed description of each column in the file:

    - DisplayName: The name of the application as it should appear in lists or any user-facing interface.
    - Description: A brief overview of the application, providing users or administrators with insights into its purpose or functionality. Typically set to DisplayName.
    - Publisher: The name of the company or individual that published or developed the application.
    - InstallExperience: Specifies the context in which the application should be installed (e.g., system or user rights).
    - SetupFile: The name of the PowerShell script used to install the application.
    - UninstallCommandLine: The command line instruction to uninstall the application. It may include specific uninstaller executables or use package managers like Chocolatey (choco) for certain applications. For some applications where uninstall commands are not applicable, this is marked as "N/A" (not applicable).
    - SecurityGroupName: Identifies the security group in Active Directory or another directory service that is granted access to the application. This is critical for managing application deployments based on security groups.
    -SecurityGroupNameDescription: This field provides a brief explanation or description of the security group. This will also be set in the field of the onboarding form so it can compare and retrieve the security group names.

![alt text](assets/Intune_App_Package_1.png?raw=true)

---

## **Credentials CSV**: `C:\IntuneMultiTenantManager\Requirements\applications.csv`

**Description**: The `credentials.csv` file stores the necessary authentication and configuration details for accessing our cloud services and applications securely. Below, you'll find explanations for each column in the file:

    - TenantID: The unique identifier for the tenant in the cloud service platform. This ID is crucial for directing authentication requests to the correct tenant environment.
    - Domain: The primary (*.onmicrosoft.com) domain associated with the tenant.
    - CertificateThumbprint: Used for secure authentication, the thumbprint corresponds to a specific certificate generated in Entra.
    - ClientID: The identifier for the client application that is registered within the tenant. This ID is used to authenticate the application when it requests access to resources.
    - TenantName: A human-readable name for the tenant, making it easier for administrators and users to identify the correct environment within documentation or when managing multiple tenants.

![alt text](assets/Intune_App_Package_2.png?raw=true)

## **Security and Confidentiality**
Given the sensitive nature of the information contained in the `credentials.csv file`, it's vital to ensure that this document is stored securely and is only accessible to authorized personnel. Mishandling of this information can lead to unauthorized access to our systems and data breaches.