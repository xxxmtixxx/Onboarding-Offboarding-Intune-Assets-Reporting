# **Onboarding/Offboarding Deployment**

## **Preparation**

### **If On-Prem:**

#### **Download/Install**:

- Open Windows PowerShell ISE as Admin on the server running Entra Connect.
- To prepare the server component that will handle the user creation, run the following one-liner:

```powershell
$url='https://github.com/xxxmtixxx/OnboardingOffboardingForm/archive/refs/heads/main.zip';$moduleName='OnboardingOffboardingForm';$tempPath=Join-Path $env:TEMP ($moduleName+'.zip');Invoke-WebRequest -Uri $url -OutFile $tempPath;$tempDir='.'+$moduleName+'_temp';$extractPath=Join-Path $HOME $tempDir;Expand-Archive -Path $tempPath -DestinationPath $extractPath -Force;$rootFiles=Get-ChildItem -Path (Join-Path $extractPath 'OnboardingOffboardingForm-main') -Filter *.ps1 | Where-Object { $_.Name -ne 'CloudOnlySetup.ps1' };$onboardingFolder=Join-Path $extractPath ('OnboardingOffboardingForm-main\OnboardingScripts\*');$targetOnboardingFolder='C:\OnboardingScripts';if (!(Test-Path $targetOnboardingFolder)) {New-Item -Path $targetOnboardingFolder -ItemType Directory | Out-Null};$rootFiles | Copy-Item -Destination $targetOnboardingFolder;Copy-Item -Path $onboardingFolder -Destination $targetOnboardingFolder -Recurse -Force;Remove-Item -Path $extractPath -Recurse -Force
```

- The script extracts everything to `C:\OnboardingScript`.

**Onboarding Server Setup.ps1** is a PowerShell script designed to automate the initial configuration of an Active Directory environment for new user onboarding. It performs a variety of tasks to ensure that the necessary infrastructure and accounts are in place for a smooth onboarding process.
    
    - Active Directory Integration: Imports the Active Directory module to facilitate AD-related operations.
    - File Path Management: Defines paths for source scripts, XML configurations, and log files, ensuring proper organization.
    - Logging: Initiates transcript logging to capture all console output, providing a detailed record for review.
    - Domain Information Retrieval: Gathers essential domain details like domain name and distinguished name for accurate scripting context.
    - Service Accounts Management: Checks for a "Service Accounts" OU, creates it if missing, and manages the service account used for onboarding.
    - Scheduled Task Configuration: Loads and modifies an XML file for a scheduled task with the current user's details and ensures its creation with the correct service account credentials.
    - Infrastructure Setup: Establishes a structured directory path for scripts and logs, creating necessary folders if they do not exist.
    - On-Premises Data Gateway: Includes functions to verify the installation of the On-Premises Data Gateway and to download and install it if needed.
    - Error Handling: Employs a try-catch block to manage exceptions and a finally block to ensure the transcript is properly stopped.

**CreateUsersFromCSV.ps1** is a PowerShell script that automates the creation of Active Directory users from a CSV file. It streamlines the process of setting up new users by handling various tasks in a systematic manner.

    - Module Import and Environment Setup: Imports necessary modules and configures the environment with domain details and file paths.
    - User Creation and Profile Setup: Processes user data from CSV files to create new AD users, generates passwords, and sets up their profiles.
    - Group Assignment: Assigns users to appropriate security groups based on their roles and requirements.
    - Home Directory Configuration: Manages home directories and permissions, ensuring users have access to their personal storage space.
    - Logging and Output: Logs actions taken during the process and generates output files with user credentials and group memberships.
    - Post-Processing Cleanup: Moves processed CSV files to a 'Completed' directory to maintain organization.
    - Azure AD Integration: Initiates a delta sync with Azure AD Connect to update Azure AD with the changes made in the local AD environment.

**CreateUsersScheduledTask.xml** is a scheduled task which will be modified and created during the `Onboarding Server Setup.ps1` process.

#### **Prepare/Run On-Prem Scripts and Configure Environment**:

- **Customize** `CreateUserFromCSV.ps1` with client details.
    - `$externalDomain`: Specify the external domain name.
        - Ex: `'yourdomain.com'`
    - `$homeDrive`: Specify Home Drive Letter.
        - Ex: `'U:'`
    - `$homeDirectory`: Specify the shared Home Drive path.
        - Ex: `'\\Server\Users'`
    - `$securityGroupOU`: Specify the Security Group OU.
        - Ex: `'Security Groups'`
    - `$domainUsersOU`: Specify the Domain Users OU.
        - Ex: `'Domain Users'`
    - `$csvFilePath`: Specify the CSV path.
        - Ex: `'C:\OnboardingScripts\Onboarding Staging'`
    - `$destinationPath`: Specify the destination path.
        - Ex: `'C:\OnboardingScripts\Onboarding Complete'`
    - `$logPath`: Specify the log path.
        - Ex: `'C:\OnboardingScripts\Logs'`
- **Execute**: `C:\OnboardingScripts\Onboarding Server Setup.ps1`. Script will:
    - Create the 'Security Group Sync' OU if it doesn't exist.
    - Create the 'Service Account OU' if it doesn't exist.
    - Create the 'sa.onboarding' service account.
    - Create the Scheduled Task.
    - Download/install the On-Prem Data Gateway.
    - Sync Entra Connect.
- **Assign** `sa.onboarding` required licenses: (I need to verify this.)
    - Microsoft 365 Business Premium or Microsoft E3
    - Microsoft Power Automate Free
    - Power Apps Premium
- **Create** `Inbox > Onboarding` folder in the mailbox.
- **Configure** the `Data Gateway` with service account.

### **If Cloud Only:**

#### **Download/Install**:

- Open Windows PowerShell ISE as Admin on any machine you'd like.
- To prepare the cloud, run the following one-liner to download the `Cloud Only Setup` and `SharePoint Migration` scripts migration scripts:

```powershell
$url='https://github.com/xxxmtixxx/OnboardingOffboardingForm/archive/refs/heads/main.zip';$moduleName='OnboardingOffboardingForm';$tempPath=Join-Path $env:TEMP ($moduleName+'.zip');Invoke-WebRequest -Uri $url -OutFile $tempPath;$tempDir='.'+$moduleName+'_temp';$extractPath=Join-Path $HOME $tempDir;Expand-Archive -Path $tempPath -DestinationPath $extractPath -Force;$rootFiles=Get-ChildItem -Path (Join-Path $extractPath 'OnboardingOffboardingForm-main') -Filter *.ps1;$targetOnboardingFolder='C:\OnboardingScripts';if (!(Test-Path $targetOnboardingFolder)) {New-Item -Path $targetOnboardingFolder -ItemType Directory | Out-Null};$rootFiles | Copy-Item -Destination $targetOnboardingFolder;Remove-Item -Path $extractPath -Recurse -Force
```

- It will extract to `C:\OnboardingScripts`.

- To download the `FlowPowerAppsMigrator`, run the following one-liner:

```powershell
$url='https://github.com/xxxmtixxx/FlowPowerAppsMigrator/archive/refs/heads/master.zip';$tempPath=Join-Path $env:TEMP 'FlowPowerAppsMigrator.zip';Invoke-WebRequest -Uri $url -OutFile $tempPath;Expand-Archive -Path $tempPath -DestinationPath 'C:\FlowPowerAppsMigrator' -Force;Remove-Item -Path $tempPath -Force
```

- It will extract to `C:\FlowPowerAppsMigrator`.

#### **Run Cloud Only Scripts and Configure Environment**

- **Execute**: `C:\OnboardingScripts\CloudOnlySetup.ps1`.
    - Script will:
        - Create the `sa.onboarding` service account.
- **Assign** `sa.onboarding` required licenses: (I need to verify this.)
    - Microsoft 365 Business Premium or Microsoft E3
    - Microsoft Power Automate Free
    - Power Apps Premium 
- **Create** `Inbox > Onboarding` folder in the mailbox.

---

## **SharePoint Site and Lists Creation**
- **Execute**: `C:\OnboardingScripts\SharePoint Migration`.
- **Login** as the `service account` for source and destination tenants.
    - Script will:
        - Connect to source and destination tenants
        - Create the SharePoint site
        - Clone the lists
        - Create the fields
        - Set the QuickLinks
- **Populate** `Security Group Descriptions` in `AD`.
- **Go** to the `Onboarding Form list` in `SharePoint`.
- **Update** the dropdown selections to match the `Security Group Descriptions`.
- **Customize** SharePoint View: `All items > Format Current View > Advanced`
- **Paste** below code: (Need to add this to the above script.)

```JSON
{
  "$schema": "https://developer.microsoft.com/json-schemas/sp/v2/row-formatting.schema.json",
  "commandBarProps": {
    "commands": [
      {
        "key": "new",
        "text": "Start Onboarding Request"
      }
    ]
  }
}
```

- **Verify** `Settings > List Settings > Title` is set to Not Required. (Need to add this to the above script.)

---

## **Export Power Apps Form**
- **Go** to the `Onboarding Form list` in `SharePoint`.
- **Navigate** to `Integrate > Power Apps > Customize Form`.
- **Click** on `Share Button` in `Power Apps`.
- **Close** the new window that will appear with an error.
- **Click** `Export Package`.
- **Name**: `“Onboarding & Offboarding Hub”`
- **Remove** the `environment`.
- **Change** `IMPORT SETUP` from "`Update`" to "`Create As New`".
- **Click** `Export`. Power App Form ZIP file will download.

---

## **Export Power Automate Flow**
- **Visit [Power Apps](https://make.powerautomate.com)**
- **Click** `My Flows`.
- **Open** the Flow.
- **Select** `Export > Package (.zip)`.
- **Give** it the name: “`Create AD User`” or “`Create O365 User`”
- **Change** `IMPORT SETUP` from "`Update`" to "`Create As New`".
- **Click** `Export`. Power Automate Flow ZIP file will download.

---

## **Move ZIP Files**
- **Move** the exported `ZIP` files to: `C:\FlowPowerAppsMigrator\src`.
- **Execute**: `C:\FlowPowerAppsMigrator\Migrate-Packages.bat`.
    - Script will:
        - Connect to source and destination tenants.
        - Modify the ZIP files with destination tenant details.
        - Place the modified ZIPs in `C:\FlowPowerAppsMigrator\dist`.

---

## **Create Connections for Power Appa/Automate**
- **Visit [Power Automate](https://make.powerautomate.com)**
- **Navigate** to `More > Connection > + New Connection`.
- **Add** the following `connectors` using the `service account`:
    - `Approvals`
    - `HTTP with Microsoft Entra ID (preauthorized)`
        - `https://graph.microsoft.com/`
    - `Microsoft Entra ID`
    - `Office 365 Outlook`
    - `SharePoint`

---

## **Import Power Apps Form**
- **Visit [Power Apps](https://make.powerapps.com)**
- **Navigate** to `Apps > Import Canvas App`
- **Select** the modified `ZIP` from `C:\FlowPowerAppsMigrator\dist`.
- **Click** `Import`.

## **Customize/Save/Publish the Power App Form**
- **Go** to the `Onboarding Form list` in `SharePoint`.
- **Navigate** to `Integrate > Power Apps > Customize Form`.
- **Customize** colors on form.
- **Add** client logo.
- **Click** `Publish` in `Power Apps`.

## **Customize Security Groups, SharePoint List, Power App Form**
- **Make** `Security Group Descriptions` in `AD` the same as the dropdown selections in the `SharePoint Onboarding List`.
- **Customize** colors on form.
- **Add** client logo.

## **Verify the Form**
- **Check** if the form is working properly in `SharePoint`. If not, follow the steps provided to fix the issue.

---

## **Import Flow**
- **Visit [Power Automate](https://make.powerautomate.com)**
- **Navigate** to `My Flows`
- **Click** `Import > Import Package (Legacy)`
- **Click** `Upload` and select the modified `ZIP` from `C:\FlowPowerAppsMigrator\dist`.
- **Select** the `connections`.
- **Click** `Import`.