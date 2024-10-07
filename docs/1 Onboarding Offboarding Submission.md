# **Onboarding/Offboarding Submission**

## **Form is submitted by someone in HR**:

![alt text](assets/Form_1.png?raw=true)

## **The Power Automate flow is triggered the item is added to the SharePoint List**:
- Flow variables are initialized.
- Flow parses JSON for manager’s email address.
- Flow waits for Manager's approval.
 
![alt text](assets/Flow_1.png?raw=true)

## **Manager receives email/Teams message and approves with comments**:

![alt text](assets/Approval_Email_1.png?raw=true)

- Flow updates the form with Managers comment.
- Flow updates variables for the flow to continue.
- Flow waits for submitter's approval.

![alt text](assets/Flow_2.png?raw=true)

## **Submitter receives email/Teams message with managers comments**:

![alt text](assets/Approval_Teams_1.png?raw=true)

## **Submitter updates information and approves from email/Teams message**:

![alt text](assets/Form_2.png?raw=true)

## **The Email/Teams messages gets updated**:

![alt text](assets/Approval_Teams_2.png?raw=true)

- Flow gets current data after submitter's changes.
- Flow updates the form with “Approved”.
- Flow gets current data again with approval status.
- Flow variables are updated.

![alt text](assets/Flow_3.png?raw=true)

---

# **If On-Prem**:

- Flow adds all current data to an array.
- Flow creates a CSV table with array.
- Flow drops the CSV file on the Data Gateway.
- The Data Gateway has a scheduled task and executable waiting for the CSV file to be dropped.

![alt text](assets/Flow_4.png?raw=true)

## **Once a CSV is found, the Data Gateway processes the CSV**:

- Schedule task executable creates a user.
- Schedule task executable adds user to security groups for Mapped Drives, Applications, and Licenses.
- Schedule task executable creates home directory with permissions and adds to AD object.
- Schedule task executable logs everything.
- Schedule task executable creates TXT file with creds and drops in folder for the flow to grab.

## **The flow loops over the directory searching for the specific TXT file for the user. Once found**:

- Flow updates variables from the contents of the file.
- Flow exit the loop.

![alt text](assets/Flow_5.png?raw=true)

---

# **Cloud**:

## **The flow verifies if fiels are populated and creates the user accordingly**:

![alt text](assets/Flow_7.png?raw=true)

## **Security Group Discovery and Assignment**:

- Flow connects to Entra to discover Security Groups.
- Flow sets variable for discovered Security Groups.
- Flow parses Security Groups for the display names.
- Flow parses Mapped Drives, Applications, and Licenses for form selections.

![alt text](assets/Flow_8.png?raw=true)

- Flow compares Security Groups to Mapped Drives, Applications, and Licenses selections.

![alt text](assets/Flow_9.png?raw=true)

- Flow adds user to selected Security Groups.

![alt text](assets/Flow_10.png?raw=true)

---

## **Final email with creds and clean-up**:

- Flow sends final email with creds.
- Flow moves mail from Inbox to Onboarding folder in service account mailbox.
- Flow recreates list item in Onboarding Completed SharePoint List.
- Flow deletes list item from Onboarding Form SharePoint List.
 
![alt text](assets/Flow_6.png?raw=true)

![alt text](assets/Final_Email.png?raw=true)