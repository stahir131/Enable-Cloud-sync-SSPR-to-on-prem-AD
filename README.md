### Enable Cloud sync SSPR writeback to on prem AD environment
Microsoft source: [here](https://learn.microsoft.com/en-us/entra/identity/authentication/tutorial-enable-cloud-sync-sspr-writeback)

This configuration enables users to reset their own password and have them synced back to the on-premises Active directory.
#### Prerequisites
1. A Microsoft Entra tenant with at least a Microsoft Entra ID P1
2. A Hybrid Identity Administrator account
3. Microsoft Entra ID configured for self-service password reset
4. An on-premises AD DS environment configured with Microsoft Entra Connect cloud sync version 1.1.977.0 or later.

#### Deployment steps
##### Step 1: Configure Microsoft Entra Connect cloud sync service account permissions
Permissions for cloud sync are configured by default.
##### Step 2: Enable password writeback in Microsoft Entra Connect cloud sync
This permission is configured by default as well.
##### Step 3: Enable password writeback for SSPR
When this option is enable, users who change or reset their password have that updated password synchronized back to the on-premises AD DS environment as well.
To verify and enable password writeback in SSPR:
1. Login to Entra admin portal as atleast Hybrid Identity administrator.
2. Browse to **Protection**> **Password reset** and select **On-premises integration**
3. Check the option for **Enable password write back for synced users** and othet two options. Select Save.


![image](https://github.com/user-attachments/assets/2bdc2e87-e9e3-4c34-91eb-107e75bee0b4)

##### Step 4: Modify the Password policies GPO on DC.
Edit the GPO for **Minimum password age** from 1 to 0. This would allow users to be able to reset their passowrd as many times in a day. 

One of the benefits of using Entra Connect Cloud Sync over Entra Connect Sync is that group ManagedSservice Account(gMSA) should have the following permissions set to writeback the passwords by default.
 - Reset password
 - Write permissions on lockoutTime
 - Write permissions on pwdLastSet
 - Extended rights for "Unexpire Password" on the root object of each domain in that forest, if not already set.

If these permissions aren't set, you can set the PasswordWriteBack permission on the service account by using the Set-AADCloudSyncPermissions cmdlet and on-premises enterprise administrator credentials:

Import-Module ‘C:\\Program Files\\Microsoft Azure AD Connect Provisioning Agent\\Microsoft.CloudSync.Powershell.dll’ 
Set-AADCloudSyncPermissions -PermissionType PasswordWriteBack -EACredential $(Get-Credential)

Also, you can manually set those permissions on AD.
Login to AD > Managed Service Accounts OU > Right-click on **provAgentgMSA** > **Properties** > **Secutiy** > **Advanced**
Click on Add.

**NOTE**: For passwords to be changed immediately, Minimum password age must be set to 0

### Configure account permissions for Microsoft Entra Connect
https://learn.microsoft.com/en-us/entra/identity/authentication/tutorial-enable-sspr-writeback#configure-password-writeback
