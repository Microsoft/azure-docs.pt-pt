---
title: AD FS support in Microsoft Authentication Library for Java
titleSuffix: Microsoft identity platform
description: Learn about Active Directory Federation Services (AD FS) support in Microsoft Authentication Library for Java (MSAL4j).
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3233e1460dd6ce3faf943e7140eb076ecd173c35
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457686"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Active Directory Federation Services support in MSAL for Java

Active Directory Federation Services (AD FS) in Windows Server enables you to add OpenID Connect and OAuth 2.0 based authentication and authorization to your Microsoft Authentication Library for Java (MSAL for Java) app. Once integrated, your app can authenticate users in AD FS, federated through Azure AD. For more information about scenarios, see [AD FS Scenarios for Developers](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers).

An app that uses MSAL for Java will talk to Azure Active Directory (Azure AD), which then federates to AD FS.

MSAL for Java connects to Azure AD, which signs in users that are managed in Azure AD (managed users) or users managed by another identity provider such as AD FS (federated users). MSAL for Java doesn't  know that a user is federated. It simply talks to Azure AD.

The [authority](msal-client-application-configuration.md#authority) you use in this case is the usual authority (authority host name + tenant, common, or organizations).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Acquire a token interactively for a federated user

When you call `ConfidentialClientApplication.AcquireToken()` or `PublicClientApplication.AcquireToken()` with `AuthorizationCodeParameters` or `DeviceCodeParameters`, the user experience is typically:

1. The user enters their account ID.
2. Azure AD briefly displays "Taking you to your organization's page", and the user is redirected to the sign-in page of the identity provider. The sign-in page is usually customized with the logo of the organization.

The supported AD FS versions in this federated scenario are:
- Active Directory Federation Services FS v2
- Active Directory Federation Services v3 (Windows Server 2012 R2)
- Active Directory Federation Services v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Acquire a token via username and password

When you acquire a token using `ConfidentialClientApplication.AcquireToken()` or `PublicClientApplication.AcquireToken()` with `IntegratedWindowsAuthenticationParameters` or `UsernamePasswordParameters`, MSAL for Java gets the identity provider to contact based on the username. MSAL for Java gets a [SAML 1.1 token](reference-saml-tokens.md) token from the identity provider, which it then provides to Azure AD which returns the JSON Web Token (JWT).

## <a name="see-also"></a>Ver também

For the federated case, see [Configure Azure Active Directory sign in behavior for an application by using a Home Realm Discovery policy](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)