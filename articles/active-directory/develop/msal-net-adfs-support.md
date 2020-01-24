---
title: Apoio da AD FS em MSAL.NET Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o suporte da Ative Directory Federation Services (AD FS) na Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 6e40a03e70fa04855037165462761c56f13acc58
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695624"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Apoio dos Serviços da Federação de Diretório Ativo em MSAL.NET
Os Serviços da Federação de Diretórios Ativos (AD FS) no Windows Server permitem-lhe adicionar autenticação e autorização baseadas em OpenID Connect e OAuth 2.0 para aplicações que está a desenvolver. Essas aplicações podem, então, autenticar os utilizadores diretamente contra a AD FS. Para mais informações, leia [cenários AD FS para Desenvolvedores](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios).

A Microsoft Authentication Library for .NET (MSAL.NET) suporta dois cenários para autenticação contra AD FS:

- MSAL.NET conversa com o Azure Ative Directory, que por si só é *federado* com AD FS.
- MSAL.NET fala **diretamente** com uma autoridade da ADFS. Isto só é suportado pela AD FS 2019 e acima. Um dos cenários que este destaque é o suporte [do Azure Stack](https://azure.microsoft.com/overview/azure-stack/)


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL liga-se ao Azure AD, que é federado com AD FS
MSAL.NET suporta a ligação ao Azure AD, que assina em utilizadores geridos (utilizadores geridos em AD Azure) ou utilizadores federados (utilizadores geridos por outro fornecedor de identidade, como a AD FS). MSAL.NET não sabe o facto de os utilizadores serem federados. No que diz respeito, fala com a AD Azure.

A [autoridade](msal-client-application-configuration.md#authority) que você usa nesse caso é a autoridade usual (nome do host da autoridade + locatário, comum ou organizações).

### <a name="acquiring-a-token-interactively"></a>Adquirindo um token interativamente
Quando se chama o método `AcquireTokenInteractive`, a experiência do utilizador é tipicamente:

1. O usuário insere a ID da conta.
2. A Azure AD exibe brevemente a mensagem "Levando-o para a página da sua organização".
3. O utilizador é redirecionado para a página de inscrição do fornecedor de identidade. A página de entrada geralmente é personalizada com o logotipo da organização.

As versões AD FS suportadas neste cenário federado são AD FS v2, AD FS v3 (Windows Server 2012 R2) e AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Adquirir um símbolo utilizando a AcquireTokenByIntegratedAuthentication ou AcquireTokenByUsernamePassword
Ao adquirir um símbolo utilizando os métodos `AcquireTokenByIntegratedAuthentication` ou `AcquireTokenByUsernamePassword`, MSAL.NET leva o fornecedor de identidade a contactar com base no nome de utilizador.  MSAL.NET recebe um [token SAML 1.1](reference-saml-tokens.md) após contactar o fornecedor de identidade.  MSAL.NET fornece então o token SAML ao Azure AD como uma afirmação do utilizador (semelhante ao [fluxo em nome](msal-authentication-flows.md#on-behalf-of)) para recuperar um JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL liga-se diretamente ao AD FS
MSAL.NET suporta a ligação ao AD FS 2019, que é compatível com o Open ID Connect e compreende o PKCE e os âmbitos. Este suporte requer que um pacote de [serviçoS KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) seja aplicado ao Windows Server. Ao ligar-se diretamente ao AD FS, a autoridade que pretende utilizar para construir a sua aplicação é semelhante à `https://mysite.contoso.com/adfs/`.

Atualmente, não existem planos para apoiar uma ligação direta a:

- AD FS 16, uma vez que não suporta PKCE e ainda usa recursos, não âmbito
- AD FS v2, que não é compatível com o OIDC.

 Se precisar de apoiar cenários que exijam uma ligação direta ao AD FS 2016, utilize a versão mais recente da Biblioteca de [Autenticação de Diretórios Ativos do Azure](active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Quando tiver atualizado o seu sistema no local para AD FS 2019, poderá utilizar MSAL.NET.

## <a name="next-steps"></a>Passos seguintes

Para o caso federado, consulte [Configurar o comportamento de entrada Azure Active Directory para um aplicativo usando uma política de descoberta de realm inicial](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)
