---
title: Suporte AD FS (MSAL for Java)
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o suporte da Ative Directory Federation Services (AD FS) na Microsoft Authentication Library for Java (MSAL4j).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76696219"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Apoio dos Serviços da Federação de Diretório Ativo na MSAL para Java

Os Serviços da Federação de Diretórios Ativos (AD FS) no Windows Server permitem-lhe adicionar autenticação e autorização baseadas em OpenID Connect e OAuth 2.0 à sua microsoft Authentication Library for Java (MSAL for Java). Uma vez integrada, a sua aplicação pode autenticar utilizadores em AD FS, federado através do Azure AD. Para obter mais informações sobre cenários, consulte [Cenários AD FS para Desenvolvedores](/windows-server/identity/ad-fs/ad-fs-development).

Uma aplicação que utiliza o MSAL para a Java falará com o Azure Ative Directory (Azure AD), que depois federa para a AD FS.

A MSAL para a Java liga-se ao Azure AD, que assina em utilizadores que são geridos em Azure AD (utilizadores geridos) ou utilizadores geridos por outro fornecedor de identidade, como a AD FS (utilizadores federados). A MSAL para a Java não sabe que um utilizador é federado. Simplesmente fala com a AD Azure.

A [autoridade](msal-client-application-configuration.md#authority) que usa neste caso é a autoridade habitual (nome anfitrião da autoridade + inquilino, comum ou organizações).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Adquirir um símbolo interativamente para um utilizador federado

Quando liga `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` ou `AuthorizationCodeParameters` `DeviceCodeParameters`com ou, a experiência do utilizador é tipicamente:

1. O utilizador introduz o ID da conta.
2. A Azure AD exibe brevemente "Levando-o para a página da sua organização", e o utilizador é redirecionado para a página de entrada do fornecedor de identidade. A página de login é geralmente personalizada com o logótipo da organização.

As versões AD FS suportadas neste cenário federado são:
- Serviços da Federação de Diretório SFS v2
- Serviços da Federação de Diretórios Ativos v3 (Windows Server 2012 R2)
- Serviços da Federação de Diretório Ativo v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Adquirir um símbolo através do nome de utilizador e da palavra-passe

Quando adquire um símbolo `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` utilizando `IntegratedWindowsAuthenticationParameters` `UsernamePasswordParameters`ou com ou, mSAL para java recebe o fornecedor de identidade para contactar com base no nome de utilizador. MSAL for Java recebe um símbolo [SAML 1.1](reference-saml-tokens.md) do fornecedor de identidade, que fornece então à Azure AD que devolve o JSON Web Token (JWT).

## <a name="next-steps"></a>Passos seguintes

Para o caso federado, consulte [configure Azure Ative Directory sign in behavior for a application using a Home Realm Discovery policy](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)