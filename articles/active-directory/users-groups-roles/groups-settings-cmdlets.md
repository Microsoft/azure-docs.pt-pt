---
title: Configure group settings using PowerShell - Azure AD | Microsoft Docs
description: How manage the settings for groups using Azure Active Directory cmdlets
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef0bfcb8c82d3f3caf90500e8852ca9e02c725aa
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382978"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlets do Azure Active Directory para configurar definições de grupo

This article contains instructions for using Azure Active Directory (Azure AD) PowerShell cmdlets to create and update groups. This content applies only to Office 365 groups (sometimes called unified groups).

> [!IMPORTANT]
> Some settings require an Azure Active Directory Premium P1 license. For more information, see the [Template settings](#template-settings) table.

For more information on how to prevent non-administrator users from creating security groups, set `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` as described in [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

Office 365 Groups settings are configured using a Settings object and a SettingsTemplate object. Initially, you don't see any Settings objects in your directory, because your directory is configured with the default settings. To change the default settings, you must create a new settings object using a settings template. Settings templates are defined by Microsoft. There are several different settings templates. To configure Office 365 group settings for your directory, you use the template named "Group.Unified". To configure Office 365 group settings on a single group, use the template named "Group.Unified.Guest". This template is used to manage guest access to an Office 365 group. 

The cmdlets are part of the Azure Active Directory PowerShell V2 module. For instructions how to download and install the module on your computer, see the article [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/). You can install the version 2 release of the module from [the PowerShell gallery](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-powershell-cmdlets"></a>Instalar cmdlets do PowerShell

Certifique-se de que desinstala qualquer versão anterior do módulo Azure Active Directory PowerShell para Graph para o Windows PowerShell e instala o [Azure Active Directory PowerShell para Graph - Versão de Pré-visualização Pública 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) antes de executar os comandos do PowerShell.

1. Abra a aplicação Windows PowerShell como administrador.
2. Desinstale qualquer versão anterior do AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. Instale a versão mais recente do AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>Create settings at the directory level
These steps create settings at directory level, which apply to all Office 365 groups in the directory. The Get-AzureADDirectorySettingTemplate cmdlet is available only in the [Azure AD PowerShell Preview module for Graph](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. In the DirectorySettings cmdlets, you must specify the ID of the SettingsTemplate you want to use. If you do not know this ID, this cmdlet returns the list of all settings templates:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   This cmdlet call returns all templates that are available:
  
   ```powershell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. To add a usage guideline URL, first you need to get the SettingsTemplate object that defines the usage guideline URL value; that is, the Group.Unified template:
  
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Next, create a new settings object based on that template:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Then update the usage guideline value:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Then apply the setting:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. You can read the values using:

   ```powershell
   $Setting.Values
   ```  
## <a name="update-settings-at-the-directory-level"></a>Update settings at the directory level
To update the value for UsageGuideLinesUrl in the setting template, simply edit the URL with Step 4 above, then perform Step 5 to set the new value.

To remove the value of UsageGuideLinesUrl, edit the URL to be an empty string using Step 4 above:

   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
Then perform Step 5 to set the new value.

## <a name="template-settings"></a>Template settings
Here are the settings defined in the Group.Unified SettingsTemplate. Unless otherwise indicated, these features require an Azure Active Directory Premium P1 license. 

| **Definição** | **Descrição** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Type: Boolean<li>Default: True |The flag indicating whether Office 365 group creation is allowed in the directory by non-admin users. This setting does not require an Azure Active Directory Premium P1 license.|
|  <ul><li>GroupCreationAllowedGroupId<li>Tipo: Cadeia<li>Default: “” |GUID of the security group for which the members are allowed to create Office 365 groups even when EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Tipo: Cadeia<li>Default: “” |A link to the Group Usage Guidelines. |
|  <ul><li>ClassificationDescriptions<li>Tipo: Cadeia<li>Default: “” | A comma-delimited list of classification descriptions. The value of ClassificationDescriptions is only valid in this format:<br>$setting[“ClassificationDescriptions”] ="Classification:Description,Classification:Description"<br>where Classification matches the strings in the ClassificationList.<br>This setting does not apply when EnableMIPLabels == True.|
|  <ul><li>DefaultClassification<li>Tipo: Cadeia<li>Default: “” | The classification that is to be used as the default classification for a group if none was specified.<br>This setting does not apply when EnableMIPLabels == True.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Tipo: Cadeia<li>Default: “” | String of a maximum length of 64 characters that defines the naming convention configured for Office 365 groups. For more information, see [Enforce a naming policy for Office 365 groups](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Tipo: Cadeia<li>Default: “” | Comma-separated string of phrases that users will not be permitted to use in group names or aliases. For more information, see [Enforce a naming policy for Office 365 groups](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Type: Boolean<li>Default: “False” | Do not use
|  <ul><li>AllowGuestsToBeGroupOwner<li>Type: Boolean<li>Default: False | Boolean indicating whether or not a guest user can be an owner of groups. |
|  <ul><li>AllowGuestsToAccessGroups<li>Type: Boolean<li>Default: True | Boolean indicating whether or not a guest user can have access to Office 365 groups content.  This setting does not require an Azure Active Directory Premium P1 license.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Tipo: Cadeia<li>Default: “” | The url of a link to the guest usage guidelines. |
|  <ul><li>AllowToAddGuests<li>Type: Boolean<li>Default: True | A boolean indicating whether or not is allowed to add guests to this directory. <br>This setting may be overridden and become read-only if *EnableMIPLabels* is set to *True* and a guest policy is associated with the sensitivity label assigned to the group. |
|  <ul><li>ClassificationList<li>Tipo: Cadeia<li>Default: “” |A comma-delimited list of valid classification values that can be applied to Office 365 Groups. <br>This setting does not apply when EnableMIPLabels == True.|
|  <ul><li>EnableMIPLabels<li>Type: Boolean<li>Default: “False” |The flag indicating whether sensitivity labels published in Microsoft 365 Compliance Center can be applied to Office 365 Groups. For more information, see [Assign Sensitivity Labels for Office 365 groups](groups-assign-sensitivity-labels.md). |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Example: Configure Guest policy for groups at the directory level
1. Get all the setting templates:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. To set guest policy for groups at the directory level, you need Group.Unified template
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Next, create a new settings object based on that template:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Then update AllowAddGuests setting
   ```powershell
   $Setting["AllowAddGuests"] = $False
   ```  
5. Then apply the setting:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. You can read the values using:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Read settings at the directory level

If you know the name of the setting you want to retrieve, you can use the below cmdlet to retrieve the current settings value. In this example, we're retrieving the value for a setting named "UsageGuidelinesUrl." 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
These steps read settings at directory level, which apply to all Office groups in the directory.

1. Read all existing directory settings:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   This cmdlet returns a list of all directory settings:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Read all settings for a specific group:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Read all directory settings values of a specific directory settings object, using Settings ID GUID:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   This cmdlet returns the names and values in this settings object for this specific group:
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>Remove settings at the directory level
This step removes settings at directory level, which apply to all Office groups in the directory.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Create settings for a specific group

1. Search for the settings template named "Groups.Unified.Guest"
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Retrieve the template object for the Groups.Unified.Guest template:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
   ```
3. Create a new settings object from the template:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Set the setting to the required value:
   ```powershell
   $SettingCopy["AllowAddGuests"]=$False
   ```
5. Get the ID of the group you want to apply this setting to:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Create the new setting for the required group in the directory:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. To verify the settings, run this command:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Update settings for a specific group
1. Get the ID of the group whose setting you want to update:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Retrieve the setting of the group:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Update the setting of the group as you need, e.g.
   ```powershell
   $Setting["AllowAddGuests"] = $True
   ```
4. Then get the ID of the setting for this specific group:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   You will get a response similar to this:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Then you can set the new value for this setting:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. You can read the value of the setting to make sure it has been updated correctly:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Cmdlet syntax reference
You can find more Azure Active Directory PowerShell documentation at [Azure Active Directory Cmdlets](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Additional reading

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
