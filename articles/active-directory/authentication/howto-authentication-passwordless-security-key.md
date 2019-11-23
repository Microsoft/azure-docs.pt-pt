---
title: Passwordless security key sign (preview) - Azure Active Directory
description: Enable passwordless security key sign-in to Azure AD using FIDO2 security keys (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f87f1b2561b65590dfe29d7d2c8d1318e3d35e1
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381844"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Enable passwordless security key sign in (preview)

For enterprises that use passwords today and have a shared PC environment, security keys provide a seamless way for workers to authenticate without entering a username or password. Security keys provide improved productivity for workers, and have better security.

This document focuses on enabling security key based passwordless authentication. At the end of this article, you will be able to sign in to web-based applications with your Azure AD account using a FIDO2 security key.

|     |
| --- |
| FIDO2 security keys are a public preview feature of Azure Active Directory. For more information about previews, see  [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Requisitos

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Combined security information registration preview](concept-registration-mfa-sspr-combined.md)
- Compatible [FIDO2 security keys](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN requires Windows 10 version 1809 or higher**

To use security keys for logging in to web apps and services, you must have a browser that supports the WebAuthN protocol. These include Microsoft Edge, Chrome, Firefox, and Safari.

## <a name="prepare-devices-for-preview"></a>Prepare devices for preview

Devices that you will be piloting with must be running Windows 10 version 1809 or higher. The best experience is on Windows 10 version 1903 or higher.

## <a name="enable-passwordless-authentication-method"></a>Enable passwordless authentication method

### <a name="enable-the-combined-registration-experience"></a>Enable the combined registration experience

Registration features for passwordless authentication methods rely on the combined registration preview. Follow the steps in the article [Enable combined security information registration (preview)](howto-registration-mfa-sspr-combined.md), to enable the combined registration preview.

### <a name="enable-fido2-security-key-method"></a>Enable FIDO2 security key method

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Browse to **Azure Active Directory** > **Security** > **Authentication methods** > **Authentication method policy (Preview)** .
1. Under the method **FIDO2 Security Key**, choose the following options:
   1. **Enable** - Yes or No
   1. **Target** - All users or Select users
1. **Save** the configuration.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>User registration and management of FIDO2 security keys

1. Browse to [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Sign in if not already.
1. Click **Security Info**.
   1. If the user already has at least one Azure Multi-Factor Authentication method registered, they can immediately register a FIDO2 security key.
   1. If they don’t have at least one Azure Multi-Factor Authentication method registered, they must add one.
1. Add a FIDO2 Security key by clicking **Add method** and choosing **Security key**.
1. Choose **USB device** or **NFC device**.
1. Have your key ready and choose **Next**.
1. A box will appear and ask the user to create/enter a PIN for your security key, then perform the required gesture for the key, either biometric or touch.
1. The user will be returned to the combined registration experience and asked to provide a meaningful name for the key so the user can identify which one if they have multiple. Clique em **Seguinte**.
1. Click **Done** to complete the process.

## <a name="sign-in-with-passwordless-credential"></a>Sign in with passwordless credential

In the example below a user has already provisioned their FIDO2 security key. The user can choose to sign in on the web with their FIDO2 security key inside of a supported browser on Windows 10 version 1809 or higher.

![Security key sign-in Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Troubleshooting and feedback

If you would like to share feedback or encounter issues while previewing this feature, please share via the Windows Feedback Hub app.

1. Launch **Feedback Hub** and make sure you're signed in.
1. Submit feedback under the following categorization:
   1. Category: Security and Privacy
   1. Subcategory: FIDO
1. To capture logs, use the option: **Recreate my Problem**

## <a name="known-issues"></a>Problemas conhecidos

### <a name="security-key-provisioning"></a>Security key provisioning

Administrator provisioning and de-provisioning of security keys is not available in the public preview.

### <a name="upn-changes"></a>UPN changes

If a user’s UPN changes, you can no longer modify FIDO2 security keys to account for the change. The resolution is to reset the device and the user has to re-register their FIDO2 security keys.

## <a name="next-steps"></a>Passos seguintes

[FIDO2 security key Windows 10 sign in](howto-authentication-passwordless-security-key-windows.md)

[Enable FIDO2 authentication to on-premises resources](howto-authentication-passwordless-security-key-on-premises.md)

[Learn more about device registration](../devices/overview.md)

[Learn more about Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
