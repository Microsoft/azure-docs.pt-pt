---
title: Access controls in Azure Active Directory Conditional Access
description: Learn how access controls in Azure Active Directory Conditional Access work.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: cac92da744b3d5b7aeaa325c7cc564a3d7e2abdd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380814"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>What are access controls in Azure Active Directory Conditional Access?

With [Azure Active Directory (Azure AD) Conditional Access](../active-directory-conditional-access-azure-portal.md), you can control how authorized users access your cloud apps. In a Conditional Access policy, you define the response ("do this") to the reason for triggering your policy ("when this happens").

![Controlo](./media/controls/10.png)

In the context of Conditional Access,

- "**When this happens**" is called **conditions**
- "**Then do this**" is called **access controls**

The combination of a condition statement with your controls represents a Conditional Access policy.

![Controlo](./media/controls/61.png)

Each control is either a requirement that must be fulfilled by the person or system signing in, or a restriction on what the user can do after signing in.

There are two types of controls:

- **Grant controls** - To gate access
- **Session controls** - To restrict access within a session

This topic explains the various controls that are available in Azure AD Conditional Access. 

## <a name="grant-controls"></a>Grant controls

With grant controls, you can either block access altogether or allow access with additional requirements by selecting the desired controls. For multiple controls, you can require:

- All selected controls to be fulfilled (*AND*)
- One selected control to be fulfilled (*OR*)

![Controlo](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Autenticação multifator

You can use this control to require multi-factor authentication to access the specified cloud app. This control supports the following multi-factor providers:

- Multi-Factor Authentication do Azure
- An on-premises multi-factor authentication provider, combined with Active Directory Federation Services (AD FS).

Using multi-factor authentication helps protect resources from being accessed by an unauthorized user who might have gained access to the primary credentials of a valid user.

### <a name="compliant-device"></a>Dispositivo conforme

You can configure Conditional Access policies that are device-based. The objective of a device-based Conditional Access policy is to only grant access to the selected cloud apps from [managed devices](require-managed-devices.md). Requiring a device to be marked as compliant is one option you have to limit access to managed devices. A device can be marked as compliant by Intune (for any device OS) or by your third-party MDM system for Windows 10 devices. Third-party MDM systems for device OS types other than Windows 10 are not supported. 

Your device needs to be registered to Azure AD before it can be marked as compliant. To register a device, you have three options: 

- Dispositivos registados no Azure AD
- Dispositivos associados ao Azure AD  
- Dispositivos híbridos associados ao Azure AD

These three options are discussed in the article [What is a device identity?](../devices/overview.md)

For more information, see [how to require managed devices for cloud app access with Conditional Access](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Hybrid Azure AD joined device

Requiring a Hybrid Azure AD joined device is another option you have to configure device-based Conditional Access policies. This requirement refers to Windows desktops, laptops, and enterprise tablets that are joined to an on-premises Active Directory. If this option is selected, your Conditional Access policy grants access to access attempts made with devices that are joined to your on-premises Active Directory and your Azure Active Directory.  

For more information, see [set up Azure Active Directory device-based Conditional Access policies](require-managed-devices.md).

### <a name="approved-client-app"></a>Approved client app

Because your employees use mobile devices for both personal and work tasks, you might want to have the ability to protect company data accessed using devices even in the case where they are not managed by you.
You can use [Intune app protection policies](https://docs.microsoft.com/intune/app-protection-policy) to help protect your company’s data independent of any mobile-device management (MDM) solution.

With approved client apps, you can require a client app that attempts to access your cloud apps to support [Intune app protection policies](https://docs.microsoft.com/intune/app-protection-policy). For example, you can restrict access to Exchange Online to the Outlook app. A Conditional Access policy that requires approved client apps is  also known as [app-based Conditional Access policy](app-based-conditional-access.md). For a list of supported approved client apps, see [approved client app requirement](technical-reference.md#approved-client-app-requirement).

### <a name="app-protection-policy-preview"></a>App protection policy (preview)

Because your employees use mobile devices for both personal and work tasks, you might want to have the ability to protect company data accessed using devices even in the case where they are not managed by you.
You can use [Intune app protection policies](https://docs.microsoft.com/intune/app-protection-policy) to help protect your company’s data independent of any mobile-device management (MDM) solution.

With app protection policy, you can limit access to client applications that have reported to Azure AD has having received [Intune app protection policies](https://docs.microsoft.com/intune/app-protection-policy). For example, you can restrict access to Exchange Online to the Outlook app that has an Intune app protection policy. A Conditional Access policy that requires app protection policy is also known as [app protection-based Conditional Access policy](app-protection-based-conditional-access.md). 

Your device must be registered to Azure AD before an application can be marked as policy protected.

For a list of supported policy protected client apps, see [app protection policy requirement](technical-reference.md#app-protection-policy-requirement).

### <a name="terms-of-use"></a>Termos de utilização

You can require a user in your tenant to consent to the terms of use before being granted access to a resource. As an administrator, you can configure and customize terms of use by uploading a PDF document. If a user falls in scope of this control access to an application is only granted if the terms of use have been agreed.

## <a name="custom-controls-preview"></a>Custom controls (preview)

Custom controls are a capability of the Azure Active Directory Premium P1 edition. When using custom controls, your users are redirected to a compatible service to satisfy further requirements outside of Azure Active Directory. To satisfy this control, a user’s browser is redirected to the external service, performs any required authentication or validation activities, and is then redirected back to Azure Active Directory. Azure Active Directory verifies the response and, if the user was successfully authenticated or validated, the user continues in the Conditional Access flow.

These controls allow the use of certain external or custom services as Conditional Access controls, and generally extend the capabilities of Conditional Access.

Providers currently offering a compatible service include:

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

For more information on those services, contact the providers directly.

### <a name="creating-custom-controls"></a>Creating custom controls

To create a custom control, you should first contact the provider that you wish to utilize. Each non-Microsoft provider has its own process and requirements to sign up, subscribe, or otherwise become a part of the service, and to indicate that you wish to integrate with Conditional Access. At that point, the provider will provide you with a block of data in JSON format. This data allows the provider and Conditional Access to work together for your tenant, creates the new control and defines how Conditional Access can tell if your users have successfully performed verification with the provider.

Custom controls cannot be used with Identity Protection's automation requiring multi-factor authentication or to elevate roles in Privileged Identity Manager (PIM).

Copy the JSON data and then paste it into the related textbox. Do not make any changes to the JSON unless you explicitly understand the change you’re making. Making any change could break the connection between the provider and Microsoft and potentially lock you and your users out of your accounts.

The option to create a custom control is in the **Manage** section of the **Conditional Access** page.

![Controlo](./media/controls/82.png)

Clicking **New custom control**, opens a blade with a textbox for the JSON data of your control.  

![Controlo](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Deleting custom controls

To delete a custom control, you must first ensure that it isn’t being used in any Conditional Access policy. Once complete:

1. Go to the Custom controls list
1. Click …  
1. Selecione **Eliminar**.

### <a name="editing-custom-controls"></a>Editing custom controls

To edit a custom control, you must delete the current control and create a new control with the updated information.

## <a name="session-controls"></a>Session controls

Session controls enable limited experience within a cloud app. The session controls are enforced by cloud apps and rely on additional information provided by Azure AD to the app about the session.

![Controlo](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Use app enforced restrictions

You can use this control to require Azure AD to pass device information to the selected cloud apps. The device information enables the cloud apps to know whether a connection is initiated from a compliant or domain-joined device. This control only supports SharePoint Online and Exchange Online as selected cloud apps. When selected, the cloud app uses the device information to provide users, depending on the device state, with a limited or full experience.

Para saber mais, consulte:

- [Enabling limited access with SharePoint Online](https://aka.ms/spolimitedaccessdocs)
- [Enabling limited access with Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Passos seguintes

- If you want to know how to configure a Conditional Access policy, see [Require MFA for specific apps with Azure Active Directory Conditional Access](app-based-mfa.md).
- If you are ready to configure Conditional Access policies for your environment, see the [best practices for Conditional Access in Azure Active Directory](best-practices.md).
