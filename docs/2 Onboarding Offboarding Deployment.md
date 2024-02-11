# **Onboarding/Offboarding Deployment**

## **Preparation**

### **If On-Prem:**
- **Initiate** the `Prepare Onboarding/Offboarding Data Gateway` script in `Datto` against a `secondary DC`.
    - Script will:
        - Copy all files into `C:\OnboardingScript`
- **Remote** into the machine.
- **Customize** `CreateUserFromCSV.ps1` with client details.
    - `$externalDomain`: Specify the external domain name.
        - Ex: `'google.com'`
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
- **Convert** `CreateUserFromCSV.ps1` to `EXE`.
- **Backup** `CreateUserFromCSV.ps1` to SharePoint and **delete** it from the `server`. (Need to add this to the below script.)
- **Execute**: `C:\OnboardingScripts\Onboarding Server Setup.ps1`. Script will:
    - Create the sa.onboarding service account.
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
- **Execute**: `C:\Onboarding-Offboarding Project\Cloud Only Setup.ps1`.
    - Script will:
        - Create the `sa.onboarding` service account.
- **Assign** `sa.onboarding` required licenses: (I need to verify this.)
    - Microsoft 365 Business Premium or Microsoft E3
    - Microsoft Power Automate Free
    - Power Apps Premium 
- **Create** `Inbox > Onboarding` folder in the mailbox.

---

## **SharePoint Site and Lists Creation**
- **Execute**: `C:\Onboarding-Offboarding Project\Create SharePoint Site and Lists.ps1`.
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
- **Paste** contents from: `C:\Onboarding-Offboarding Project\Instructions\SharePoint View Format.txt` (Need to add this to the above script.)
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
- **Select** the modified `ZIP`.
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
- **Click** `Upload` and select the modified `ZIP`.
- **Select** the `connections`.
- **Click** `Import`.