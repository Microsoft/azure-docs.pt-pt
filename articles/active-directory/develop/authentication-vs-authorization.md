---
title: Autenticação vs. autorização | Rio Azure
titleSuffix: Microsoft identity platform
description: Conheça os fundamentos da autenticação e autorização na plataforma de identidade da Microsoft (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 5087278e5c89514cd43b7ca871a58f18e0fa98f2
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678151"
---
# <a name="authentication-vs-authorization"></a>Autenticação vs. autorização

Este artigo define autenticação e autorização. Também cobre brevemente como pode utilizar a plataforma de identidade da Microsoft para autenticar e autorizar utilizadores nas suas aplicações web, APIs web ou apps que chamem APIs web protegidas. Se vir um termo que não conhece, experimente o nosso [glossário](developer-glossary.md) ou os [nossos vídeos de plataforma de identidade da Microsoft](identity-videos.md), que abrangem conceitos básicos.

## <a name="authentication"></a>Autenticação

*A autenticação* é o processo de provar que é quem diz ser. Às vezes é encurtado para *AuthN.* A plataforma de identidade da Microsoft utiliza o protocolo [OpenID Connect](https://openid.net/connect/) para lidar com a autenticação.

## <a name="authorization"></a>Autorização

*Autorização* é o ato de conceder permissão a uma parte autenticada para fazer algo. Especifica quais os dados a que pode aceder e o que pode fazer com esses dados. A autorização é por vezes encurtada para *AuthZ*. A plataforma de identidade da Microsoft utiliza o protocolo [OAuth 2.0](https://oauth.net/2/) para manusear a autorização.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Autenticação e autorização utilizando a plataforma de identidade da Microsoft

Criar aplicações que cada um mantenha o seu próprio nome de utilizador e informações de palavra-passe incorre num elevado fardo administrativo quando é necessário adicionar ou remover utilizadores através de várias aplicações. Em vez disso, as suas aplicações podem delegar essa responsabilidade num fornecedor de identidade centralizado.

O Azure Ative Directory (Azure AD) é um fornecedor de identidade centralizado na nuvem. Delegar a autenticação e a autorização permite cenários como:

- Políticas de acesso condicional que exigem que um utilizador esteja num local específico.
- A utilização da [autenticação multi-factor](../authentication/concept-mfa-howitworks.md), que por vezes é chamada de autenticação de dois fatores ou 2FA.
- Permitir que um utilizador entre uma vez e depois seja automaticamente inscrito em todas as aplicações web que partilham o mesmo diretório centralizado. Esta capacidade *chama-se único sinal de sso (SSO)*.

A plataforma de identidade da Microsoft simplifica a autorização e a autenticação para os desenvolvedores de aplicações, fornecendo a identidade como um serviço. Suporta protocolos padrão da indústria e bibliotecas de código aberto para diferentes plataformas para ajudá-lo a começar a codificar rapidamente. Permite que os desenvolvedores construam aplicações que assinem todas as identidades da Microsoft, obtenham fichas para ligar para [o Microsoft Graph,](https://developer.microsoft.com/graph/)acedam às APIs da Microsoft ou acedam a outras APIs que os desenvolvedores construíram.

Este vídeo explica a plataforma de identidade da Microsoft e os fundamentos da autenticação moderna: 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Aqui está uma comparação dos protocolos que a plataforma de identidade da Microsoft utiliza:

* **OAuth versus OpenID Connect**: A plataforma utiliza o OAuth para autorização e o OpenID Connect (OIDC) para autenticação. O OpenID Connect é construído em cima do OAuth 2.0, pelo que a terminologia e o fluxo são semelhantes entre os dois. Pode até autenticar um utilizador (através do OpenID Connect) e obter autorização para aceder a um recurso protegido que o utilizador possui (através da OAuth 2.0) num único pedido. Para obter mais informações, consulte [os protocolos OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md) e [o protocolo OpenID Connect](v2-protocols-oidc.md).
* **OAuth versus SAML**: A plataforma utiliza OAuth 2.0 para autorização e SAML para autenticação. Para obter mais informações sobre como utilizar estes protocolos em conjunto para autenticar um utilizador e obter autorização para aceder a um recurso protegido, consulte a [plataforma de identidade da Microsoft e o fluxo de afirmação do portador da OAuth 2.0 SAML](./scenario-token-exchange-saml-oauth.md).
* **OpenID Connect versus SAML**: A plataforma utiliza o OpenID Connect e o SAML para autenticar um utilizador e ativar um único sinal de acesso. A autenticação SAML é comumente usada com fornecedores de identidade, tais como Ative Directory Federation Services (AD FS) federados para Azure AD, por isso é frequentemente usado em aplicações empresariais. O OpenID Connect é comumente utilizado para apps que estão puramente na nuvem, tais como aplicações móveis, websites e APIs web.

## <a name="next-steps"></a>Passos seguintes

Para outros tópicos que abrangem os fundamentos básicos de autenticação e autorização:

* Para saber como os tokens de acesso, tokens de atualização e fichas de identificação são usados em autorização e autenticação, consulte [fichas de segurança.](security-tokens.md)
* Para conhecer o processo de registo da sua aplicação para que possa integrar-se na plataforma de identidade da Microsoft, consulte o [modelo de Aplicação.](application-model.md)