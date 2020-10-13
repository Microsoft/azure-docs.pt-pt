---
title: Apoio da AD FS em MSAL.NET [ Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o suporte de Serviços da Federação de Diretório Ativo (AD FS) na Microsoft Authentication Library para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4107b0785ef4635e0e5e745a35b605d2634a220d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88166234"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Apoio aos Serviços da Federação Ativa em MSAL.NET
Os Serviços da Federação de Diretórios Ativos (AD FS) no Windows Server permitem-lhe adicionar a autenticação baseada em OpenID Connect e OAuth 2.0 e autorização às aplicações que está a desenvolver. Essas aplicações podem, então, autenticar os utilizadores diretamente contra AD FS. Para mais informações, leia [cenários de FS da AD para desenvolvedores.](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)

A Microsoft Authentication Library for .NET (MSAL.NET) suporta dois cenários para autenticação contra AD FS:

- MSAL.NET fala com o Azure Ative Directory, que por si só é *federado* com a AD FS.
- MSAL.NET fala **diretamente** com uma autoridade da ADFS. Isto só é suportado a partir de AD FS 2019 ou superior. Um dos cenários que este destaque é o suporte [do Azure Stack](https://azure.microsoft.com/overview/azure-stack/)


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL conecta-se ao Azure AD, que é federado com AD FS
MSAL.NET suporta a ligação ao Azure AD, que assina em utilizadores geridos (utilizadores geridos em AZure AD) ou utilizadores federados (utilizadores geridos por outro fornecedor de identidade, como AD FS). MSAL.NET não sabe do facto de os utilizadores serem federados. No que diz respeito a isso, fala com a Azure AD.

A [autoridade](msal-client-application-configuration.md#authority) que você usa neste caso é a autoridade habitual (nome de anfitrião de autoridade + inquilino, comum, ou organizações).

### <a name="acquiring-a-token-interactively"></a>Aquisição de um símbolo interativamente
Quando se chama o `AcquireTokenInteractive` método, a experiência do utilizador é tipicamente:

1. O utilizador introduz o ID da conta.
2. A Azure AD exibe brevemente a mensagem "Levá-lo para a página da sua organização".
3. O utilizador é redirecionado para a página de inscrição do fornecedor de identidade. A página de início de saúde é normalmente personalizada com o logótipo da organização.

As versões AD FS suportadas neste cenário federado são AD FS v2, AD FS v3 (Windows Server 2012 R2) e AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Aquisição de um token usando AcquireTokenByIntegratedAuthentication ou AcquireTokenByUsernamePassword
Ao adquirir um token utilizando os `AcquireTokenByIntegratedAuthentication` métodos ou `AcquireTokenByUsernamePassword` métodos, MSAL.NET o fornecedor de identidade contacte com base no nome de utilizador.  MSAL.NET recebe um [token SAML 1.1](reference-saml-tokens.md) depois de contactar o fornecedor de identidade.  MSAL.NET fornece então o símbolo SAML à Azure AD como uma afirmação do utilizador (semelhante ao [em nome do fluxo](msal-authentication-flows.md#on-behalf-of)) para recuperar um JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL conecta-se diretamente a AD FS
MSAL.NET suporta a ligação ao AD FS 2019, que é compatível com o Open ID Connect e compreende pkce e âmbitos. Este suporte requer que um pacote de [serviçoS KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) seja aplicado ao Windows Server. Ao ligar diretamente ao AD FS, a autoridade que pretende utilizar para construir a sua aplicação é semelhante a `https://mysite.contoso.com/adfs/` .

Atualmente, não existem planos para suportar uma ligação direta com:

- AD FS 16, uma vez que não suporta O PKCE e ainda utiliza recursos, não âmbito
- AD FS v2, que não é compatível com OIDC.

 Se necessitar de suportar cenários que exijam uma ligação direta ao AD FS 2016, utilize a versão mais recente da [Azure Ative Directory Authentication Library](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Quando tiver atualizado o seu sistema no local para AD FS 2019, poderá utilizar MSAL.NET.

## <a name="next-steps"></a>Passos seguintes

Para o caso federado, consulte o [sinal de Diretório Ativo Configure Azure em comportamento para uma aplicação utilizando uma política de Descoberta do Home Realm](../manage-apps/configure-authentication-for-federated-users-portal.md)