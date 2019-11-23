---
title: How to manage devices using the Azure portal | Microsoft Docs
description: Learn how to use the Azure portal to manage devices.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c8e94a1b15ad8cd55019f9351c82f58130e472d
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420613"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Manage device identities using the Azure portal

With device identity management in Azure Active Directory (Azure AD), you can ensure that your users are accessing your resources from devices that meet your standards for security and compliance.

Este artigo:

- Assumes that you are familiar with the [introduction to device identity management in Azure Active Directory](overview.md)
- Provides you with information about managing your device identities using the Azure AD portal

## <a name="manage-device-identities"></a>Gerir identidades do dispositivo

The Azure AD portal provides you with a central place to manage your device identities. You can get to this place by either using a [direct link](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) or by following these manual steps:

1. Sign in to the [Azure portal](https://portal.azure.com) as administrator.

2. Search for and select **Azure Active Directory** or select it from the **Home** page.

3. In the **Manage** section, click **Devices**.

   ![Configure device settings](./media/device-management-azure-portal/74.png)

The **Devices** page enables you to:

- Configure your device settings
- Locate devices
- Perform device identity management tasks
- Review device-related audit logs  
  
## <a name="configure-device-settings"></a>Configure device settings

To manage your device identities using the Azure AD portal, your devices need to be either [registered or joined](overview.md) to Azure AD. As an administrator, you can fine-tune the process of registering and joining devices by configuring the device settings.

![Configure device settings](./media/device-management-azure-portal/22.png)

The device settings page enables you to configure:

![Manage an Intune device](./media/device-management-azure-portal/21.png)

- **Users may join devices to Azure AD** - This setting enables you to select the users who can register their devices as Azure AD joined devices. The default is **All**.

> [!NOTE]
> **Users may join devices to Azure AD** setting is only applicable to Azure AD join on Windows 10.

- **Additional local administrators on Azure AD joined devices** - You can select the users that are granted local administrator rights on a device. Users added here are added to the *Device Administrators* role in Azure AD. Global administrators in Azure AD and device owners are granted local administrator rights by default. This option is a premium edition capability available through products such as Azure AD Premium or the Enterprise Mobility Suite (EMS).
- **Users may register their devices with Azure AD** - You need to configure this setting to allow Windows 10 personal, iOS, Android, and macOs devices to be registered with Azure AD. If you select **None**, devices are not allowed to register with Azure AD. Enrollment with Microsoft Intune or Mobile Device Management (MDM) for Office 365 requires registration. If you have configured either of these services, **ALL** is selected and **NONE** is not available.
- **Require Multi-Factor Auth to join devices** - You can choose whether users are required to provide an additional authentication factor to join their device to Azure AD. The default is **No**. We recommend requiring multi-factor authentication when registering a device. Before you enable multi-factor authentication for this service, you must ensure that multi-factor authentication is configured for the users that register their devices. For more information on different Azure multi-factor authentication services, see [getting started with Azure multi-factor authentication](../authentication/concept-mfa-whichversion.md). 

> [!NOTE]
> **Require Multi-Factor Auth to join devices** setting applies to devices that are either Azure AD joined or Azure AD registered. This setting does not apply to hybrid Azure AD joined devices.

- **Maximum number of devices** - This setting enables you to select the maximum number of Azure AD joined or Azure AD registered devices that a user can have in Azure AD. If a user reaches this quota, they are not be able to add additional devices until one or more of the existing devices are removed. The default value is **20**.

> [!NOTE]
> **Maximum number of devices** setting applies to devices that are either Azure AD joined or Azure AD registered. This setting does not apply to hybrid Azure AD joined devices.

- **Users may sync settings and app data across devices** - By default, this setting is set to **NONE**. Selecting specific users or groups or ALL allows the user’s settings and app data to sync across their Windows 10 devices. Learn more on how sync works in Windows 10.
This option is a premium capability available through products such as Azure AD Premium or the Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Locate devices

You have two options to locate registered and joined devices:

- **All devices** in the **Manage** section of the **Devices** page  

   ![All devices](./media/device-management-azure-portal/41.png)

- **Devices** in the **Manage** section of a **User** page

   ![All devices](./media/device-management-azure-portal/43.png)

With both options, you can get to a view that:

- Enables you to search for devices using the display name or device ID as filter.
- Provides you with detailed overview of registered and joined devices
- Enables you to perform common device management tasks

![All devices](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* If you see a device that is "Hybrid Azure AD joined" with a state "Pending" under the REGISTERED column, it indicates that the device has been synchronized from Azure AD connect and is waiting to complete registration from the client. Read more on how to [plan your Hybrid Azure AD join implementation](hybrid-azuread-join-plan.md). Additional information can be found in the article, [Devices frequently asked questions](faq.md).
>
>   ![Pending devices](./media/device-management-azure-portal/75.png)
>
>* For some iOS devices, the device names containing apostrophes can potentially use different characters that look like apostrophes. So searching for such devices is a little tricky - if you are not seeing search results correctly, ensure that the search string contains matching apostrophe character.

## <a name="device-identity-management-tasks"></a>Device identity management tasks

As a global administrator or cloud device administrator, you can manage the registered or joined devices. Intune Service administrators can:

- Update devices  - Examples are daily operations such as enabling/disabling devices
- Delete devices – When a device is retired and should be deleted in Azure AD

This section provides you with information about common device identity management tasks.

### <a name="manage-an-intune-device"></a>Manage an Intune device

If you are an Intune administrator, you can manage devices marked as **Microsoft Intune**. If the device is not enrolled with Microsoft Intune the "Manage" option will be greyed out.

![Manage an Intune device](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Enable / disable an Azure AD device

To enable / disable a device, you have two options:

- The tasks menu ("...") on the **All devices** page

   ![Manage an Intune device](./media/device-management-azure-portal/71.png)

- The toolbar on the **Devices** page

   ![Manage an Intune device](./media/device-management-azure-portal/32.png)

**Remarks:**

- You need to be a global administrator or cloud device administrator in Azure AD to enable / disable a device. 
- Disabling a device prevents a device from successfully authenticating with Azure AD, thereby preventing the device from accessing your Azure AD resources that are guarded by device CA or using your WH4B credentials.
- Disabling the device will revoke both the Primary Refresh Token (PRT) and any Refresh Tokens (RT) on the device.

### <a name="delete-an-azure-ad-device"></a>Delete an Azure AD device

To delete a device, you have two options:

- The tasks menu ("...") on the **All devices** page

   ![Manage an Intune device](./media/device-management-azure-portal/72.png)

- The toolbar on the **Devices** page

   ![Delete a device](./media/device-management-azure-portal/34.png)

**Remarks:**

- You need to be a global administrator or an Intune administrator in Azure AD to delete a device.
- Deleting a device:
   - Prevents a device from accessing your Azure AD resources.
   - Removes all details that are attached to the device, for example, BitLocker keys for Windows devices.  
   - Represents a non-recoverable activity and is not recommended unless it is required.

If a device is managed by another management authority (for example, Microsoft Intune), make sure that the device has been wiped / retired before deleting the device in Azure AD. Review how to [manage stale devices](device-management-azure-portal.md) before deleting any devices.

### <a name="view-or-copy-device-id"></a>View or copy device ID

You can use a device ID to verify the device ID details on the device or using PowerShell during troubleshooting. To access the copy option, click the device.

![View a device ID](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>View or copy BitLocker keys

You can view and copy the BitLocker keys to help users to recover their encrypted drive. These keys are only available for Windows devices that are encrypted and have their keys stored in Azure AD. You can copy these keys when accessing details of the device.

![View BitLocker keys](./media/device-management-azure-portal/36.png)

To view or copy the BitLocker keys, you need to be either the owner of the device, or a user that has at least one of the following roles assigned:

- Cloud Device Administrator
- Administrador Global
- Helpdesk Administrator
- Administrador de Serviços do Intune
- Security Administrator
- Security Reader

> [!NOTE]
> Hybrid Azure AD Joined Windows 10 devices do not have an owner. So, if you are looking for a device by owner and didn't find it, search by the device ID.

## <a name="audit-logs"></a>Registos de auditoria

Device activities are available through the activity logs. These logs include activities triggered by the device registration service and by users:

- Device creation and adding owners / users on the device
- Changes to device settings
- Device operations such as deleting or updating a device

Your entry point to the auditing data is **Audit logs** in the **Activity** section of the **Devices** page.

![Registos de auditoria](./media/device-management-azure-portal/61.png)

Um registo de auditoria tem uma vista de lista predefinida que mostra:

- The date and time of the occurrence
- The targets
- The initiator / actor (who) of an activity
- The activity (what)

![Registos de auditoria](./media/device-management-azure-portal/63.png)

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Registos de auditoria](./media/device-management-azure-portal/64.png)

Para limitar os dados comunicados a um nível que funcione para si, pode filtrar os dados de auditoria através dos seguintes campos:

- Categoria
- Tipo de recurso de atividade
- Atividade
- Intervalo de datas
- Visar
- Initiated By (Actor)

In addition to the filters, you can search for specific entries.

![Registos de auditoria](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Passos seguintes

[How to manage stale devices in Azure AD](manage-stale-devices.md)
