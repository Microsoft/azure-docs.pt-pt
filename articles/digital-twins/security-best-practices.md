---
title: Understand security best practices - Azure Digital Twins | Microsoft Docs
description: Learn about security best practices for Azure Digital Twins and the Internet of Things.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: f5c0f6d9f6f7f35722f3df5f35dc1da945f21b9a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229043"
---
# <a name="azure-digital-twins-security-best-practices"></a>Azure Digital Twins security best practices

Azure Digital Twins security enables precise access to specific resources and actions in your IoT graph. It does so through granular role and permission management called [role-based access control](./security-role-based-access-control.md).

Azure Digital Twins also uses other security features that are present in Azure IoT, including Azure Active Directory (Azure AD). For that reason, configuring and securing applications built on Azure Digital Twins involves using many of the same [Azure IoT security practices](../iot-fundamentals/iot-security-best-practices.md) currently recommended.

This article summarizes key best practices to follow.

> [!IMPORTANT]
> To ensure maximal security for your IoT space, review additional security resources. Make sure to include your device vendors.

> [!TIP]
> Use [Azure Security Center for IoT](https://docs.microsoft.com/azure/asc-for-iot/) to help detect IoT security threats and vulnerabilities.

## <a name="iot-security-best-practices"></a>Melhores práticas de segurança de IoT

Some key practices to safely secure your IoT devices include:

> [!div class="checklist"]
> * Secure each device that's connected to your IoT space in a tamper-proof way.
> * Limit the role of each device, sensor, and person within your IoT space. If compromised, the effect is minimized.
> * Consider the potential use of device IP address filtering and port restriction.
> * Limit I/O and device bandwidth to improve performance. Rate-limiting can improve security by preventing denial-of-service attacks.
> * Keep device firmware, operating system, and software up to date.
> * Periodically audit and review device, software, network, and gateway security best practices as they continue to improve and evolve.

Some key practices to safely secure an IoT space include:

> [!div class="checklist"]
> * Encrypt saved, stored, or persistent data.
> * Require passwords or keys to be periodically changed or refreshed.
> * Carefully restrict access and permissions by role. See the section [Role-based access control best practices](#role-based-access-control-best-practices) below.
> * Consider a divided network topology so that devices on each network are isolated from the others.
> * Use powerful encryption. Require long passwords, use secure protocols, and [multi-factor authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

[Monitor](./how-to-configure-monitoring.md) IoT resources to watch for outliers, threats, or resource parameters that fall outside the range of usual operation. Use Azure Analytics for monitoring management.

> [!IMPORTANT]
> Read Azure [IoT security best practices](../iot-fundamentals/iot-security-best-practices.md) to begin a comprehensive IoT security strategy.

> [!NOTE]
> For more information on event processing and monitoring, see [Route events and messages with Azure Digital Twins](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory best practices

Azure Digital Twins uses [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/) to authenticate users and protect applications. Azure Active Directory supports authentication for a variety of modern architectures. They're all based on industry-standard protocols such as OAuth 2.0 or OpenID Connect. A few key practices to secure your IoT space for Azure Active Directory include:

> [!div class="checklist"]
> * Store Azure Active Directory app secrets and keys in a secure location, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Use a certificate issued by a trusted [certificate authority](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) rather than app secrets to authenticate.
> * Limit OAuth 2.0 scope of access for a token.
> * Verify the length of time a token is valid and whether a token remains valid.
> * Set appropriate lengths of time that tokens are valid for.
> * Refresh expired tokens.

## <a name="role-based-access-control-best-practices"></a>Role-based access control best practices

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Passos seguintes

* To learn more about Azure IoT best practices, read [IoT security best practices](../iot-fundamentals/iot-security-best-practices.md).

* To learn about role-based access control, read [Role-based access control](./security-role-based-access-control.md).

* To learn about authentication, read [Authenticate with APIs](./security-authenticating-apis.md).