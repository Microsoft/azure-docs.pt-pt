---
title: Suporte AD FS (MSAL para Java)
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o suporte da Federação de Diretórios Ativos (AD FS) na Microsoft Authentication Library for Java (MSAL4j).
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
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 5d01d1143563637e21aaa06a3f997c1507e4d8f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88114783"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Apoio aos Serviços da Federação De Diretório Ativo no MSAL para a Java

Os Serviços da Federação de Diretórios Ativos (AD FS) no Windows Server permitem-lhe adicionar autenticação e autorização baseadas em OpenID Connect e OAuth 2.0 à sua aplicação Microsoft Authentication Library for Java (MSAL for Java). Uma vez integrada, a sua aplicação pode autenticar os utilizadores em FS AD, federados através do Azure AD. Para obter mais informações sobre cenários, consulte [os Cenários FS da AD para Desenvolvedores.](/windows-server/identity/ad-fs/ad-fs-development)

Uma aplicação que usa o MSAL para a Java falará com o Azure Ative Directory (Azure AD), que depois federa para a AD FS.

O MSAL para a Java conecta-se ao Azure AD, que assina em utilizadores geridos em AD AZure (utilizadores geridos) ou utilizadores geridos por outro fornecedor de identidade, como o AD FS (utilizadores federados). MSAL para Java não sabe que um utilizador é federado. Simplesmente fala com a Azure AD.

A [autoridade](msal-client-application-configuration.md#authority) que você usa neste caso é a autoridade habitual (nome de anfitrião de autoridade + inquilino, comum, ou organizações).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Adquira um símbolo interativamente para um utilizador federado

Quando liga `ConfidentialClientApplication.AcquireToken()` ou `PublicClientApplication.AcquireToken()` com `AuthorizationCodeParameters` `DeviceCodeParameters` ou, a experiência do utilizador é tipicamente:

1. O utilizador introduz o ID da conta.
2. O Azure AD exibe brevemente "Levá-lo para a página da sua organização", e o utilizador é redirecionado para a página de inscrição do fornecedor de identidade. A página de início de saúde é normalmente personalizada com o logótipo da organização.

As versões AD FS suportadas neste cenário federado são:
- Serviços da Federação De Diretório Ativo FS v2
- Serviços da Federação de Diretório Ativo v3 (Windows Server 2012 R2)
- Serviços da Federação De Diretório Ativo v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Adquirir um token via nome de utilizador e senha

Quando adquire um token usando `ConfidentialClientApplication.AcquireToken()` ou `PublicClientApplication.AcquireToken()` com `IntegratedWindowsAuthenticationParameters` `UsernamePasswordParameters` ou, MSAL para Java consegue que o fornecedor de identidade entre em contato com base no nome de utilizador. A MSAL para a Java recebe um [token SAML 1.1](reference-saml-tokens.md) do fornecedor de identidade, que depois fornece à Azure AD que devolve o JSON Web Token (JWT).

## <a name="next-steps"></a>Passos seguintes

Para o caso federado, consulte o [sinal de Diretório Ativo Configure Azure em comportamento para uma aplicação utilizando uma política de Descoberta do Home Realm](../manage-apps/configure-authentication-for-federated-users-portal.md)