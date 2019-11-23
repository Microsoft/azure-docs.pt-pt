---
title: Conditional Access - Combined security information - Azure Active Directory
description: Create a custom Conditional Access policy to require a trusted location for security info registration
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5864424f003ce9254a6452d8374d78c54516f2bc
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322739"
---
# <a name="conditional-access-require-trusted-location-for-mfa-registration"></a>Conditional Access: Require trusted location for MFA registration

Securing when and how users register for Azure Multi-Factor Authentication and self-service password reset is now possible with user actions in Conditional Access policy. This preview feature is available to organizations who have enabled the [combined registration preview](../authentication/concept-registration-mfa-sspr-combined.md). This functionality may be enabled in organizations where they want users to register for Azure Multi-Factor Authentication and SSPR from a central location such as a trusted network location during HR onboarding. For more information about creating trusted locations in Conditional Access, see the article [What is the location condition in Azure Active Directory Conditional Access?](../conditional-access/location-condition.md#named-locations)

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Create a policy to require registration from a trusted location

The following policy applies to all selected users, who attempt to register using the combined registration experience, and blocks access unless they are connecting from a location marked as trusted network.

1. In the **Azure portal**, browse to **Azure Active Directory** > **Conditional Access**.
1. Select **New policy**.
1. In Name, Enter a Name for this policy. For example, **Combined Security Info Registration on Trusted Networks**.
1. Under **Assignments**, click **Users and groups**, and select the users and groups you want this policy to apply to.

   > [!WARNING]
   > Users must be enabled for the [combined registration preview](../authentication/howto-registration-mfa-sspr-combined.md).

1. Under **Cloud apps or actions**, select **User actions**, check **Register security information (preview)** .
1. Under **Conditions** > **Locations**.
   1. Configure **Yes**.
   1. Include **Any location**.
   1. Exclude **All trusted locations**.
   1. Click **Done** on the Locations blade.
   1. Click **Done** on the Conditions blade.
1. Under **Access controls** > **Grant**.
   1. Click **Block access**.
   1. Em seguida, clique em **Selecionar**.
1. Set **Enable policy** to **On**.
1. Em seguida, clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes

[Conditional Access common policies](concept-conditional-access-policy-common.md)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md)
