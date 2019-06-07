---
title: Suporte do AD FS na biblioteca de autenticação da Microsoft para o .NET | Azure
description: Saiba mais sobre o suporte de serviços de Federação do Active Directory (AD FS) no Microsoft Authentication Library para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5963c5e83b5af3848edd934328caa1f095bd184
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676732"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Suporte de serviços de Federação do Active Directory em MSAL.NET
Serviços de Federação do Active Directory (AD FS) no Windows Server permite-lhe adicionar OpenID Connect e OAuth 2.0 com base em autenticação e autorização para aplicações que está desenvolvendo e têm essas aplicações autenticar os utilizadores diretamente com o AD FS. Para obter mais informações, leia [cenários do AD FS para programadores](/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers).

Biblioteca de autenticação da Microsoft para .NET (MSAL.NET) oferece suporte a dois cenários de autenticação relativamente do AD FS:

- MSAL.NET fala para o Azure Active Directory, que por si só é *federado* com o AD FS.
- Fala MSAL.NET *diretamente* a uma autoridade de AD FS, em que a versão do AD FS é OpenID Connect em conformidade (a partir de AD FS 2019). Ligar diretamente ao AD FS permite MSAL.NET autenticar com as aplicações em execução [do Azure Stack](https://azure.microsoft.com/overview/azure-stack/).

## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>A MSAL estabelece ligação ao Azure AD, que está Federado com o AD FS
MSAL.NET oferece suporte a ligar ao Azure AD, que inicia sessão geridos dos utilizadores (utilizadores geridos no Azure AD) ou federadas utilizadores (utilizadores geridos pelo outro fornecedor de identidade do AD FS). MSAL.NET não sabe sobre o fato de que os utilizadores forem federados. Como ele diz respeito, fala com o Azure AD.

O [autoridade](msal-client-application-configuration.md#authority) utilização, neste caso, é a habitual autoridade (nome de anfitrião de autoridade + inquilino, comum ou organizações).

### <a name="acquiring-a-token-interactively"></a>Adquirir um token de forma interativa
Quando chama o `AcquireTokenInteractive` método, a experiência do usuário é normalmente:

1. O utilizador introduz o respetivo ID de conta.
2. O Azure AD rapidamente apresenta a mensagem "A levá-lo para a página da sua organização".
3. O utilizador é redirecionado para a página de início de sessão do fornecedor de identidade. A página de início de sessão, normalmente, é personalizada com o logótipo da organização.

Versões suportadas do AD FS neste cenário federado são v2 do AD FS, do AD FS v3 (Windows Server 2012 R2) e o AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Adquirir um token com AcquireTokenByIntegratedAuthentication ou AcquireTokenByUsernamePassword
Ao adquirir um token através do `AcquireTokenByIntegratedAuthentication` ou `AcquireTokenByUsernamePassword` métodos, MSAL.NET obtém o fornecedor de identidade entrar em contacto com base no nome de utilizador.  MSAL.NET recebe uma [token SAML 1.1](reference-saml-tokens.md) após contatar o fornecedor de identidade.  MSAL.NET, em seguida, fornece o token SAML para o Azure AD como uma asserção de utilizador (semelhantes para o [fluxo em-nome-de](msal-authentication-flows.md#on-behalf-of)) para recuperar um JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>A MSAL liga-se diretamente para o AD FS
MSAL.NET oferece suporte a ligar ao AD FS 2019, que é abrir ID do Connect em conformidade. Ao ligar-se diretamente para o AD FS, a autoridade de que pretende utilizar para criar seu aplicativo é semelhante ao `https://mysite.contoso.com/adfs/`.

Atualmente, não existem não existem planos para oferecer suporte a uma conexão direta com o AD FS 2016 ou o AD FS v2 (que não são OpenID Connect em conformidade). Se precisar de suportar os cenários que requerem uma ligação direta para o AD FS 2016, utilize a versão mais recente do [do Azure Active Directory Authentication Library](active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Quando tiver atualizado o seu sistema no local para o AD FS 2019, poderá usar MSAL.NET.
