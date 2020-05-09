---
title: Autenticação vs. autorização Azure
titleSuffix: Microsoft identity platform
description: Conheça os fundamentos da autenticação e autorização na plataforma de identidade da Microsoft (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: b905b8f3fe99b83bafdd61b1daa25549354c5275
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926787"
---
# <a name="authentication-vs-authorization"></a>Autenticação vs. autorização

Este artigo define a autenticação e a autorização e cobre brevemente como pode usar a plataforma de identidade da Microsoft para autenticar e autorizar utilizadores nas suas aplicações web, APIs web ou aplicações que chamam APIs protegidos da Web. Se vir um termo que não conhece, experimente o nosso [glossário](developer-glossary.md) ou os [nossos vídeos](identity-videos.md) da plataforma de identidade Microsoft que cobrem conceitos básicos.

## <a name="authentication"></a>Autenticação

**A autenticação** é o processo de provar que é quem diz ser. Por vezes, o termo é abreviado como AuthN. A plataforma de identidade da Microsoft implementa os protocolos [OpenID Connect](https://openid.net/connect/) e [SAML 2.0](http://docs.oasis-open.org/security/saml/Post2.0/sstc-saml-tech-overview-2.0.html) para manusear a autenticação.

## <a name="authorization"></a>Autorização

**Autorização** é o ato de conceder uma autorização autenticada para fazer algo. Especifica quais os dados a que pode aceder e o que pode fazer com esses dados. Por vezes, o termo é abreviado como AuthZ. A plataforma de identidade microsoft implementa o protocolo [OAuth 2.0](https://oauth.net/2/) para manusear a autorização.

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>Autenticação e autorização utilizando plataforma de identidade da Microsoft

Em vez de criar aplicações que cada um mantenha o seu próprio nome de utilizador e informações de palavra-passe, que incorre num elevado fardo administrativo quando precisa de adicionar ou remover utilizadores através de várias aplicações, as aplicações podem delegar essa responsabilidade num fornecedor de identidade centralizado.

O Azure Ative Directory (Azure AD) é um fornecedor de identidade centralizado na nuvem. Delegar a autenticação e autorização permite que cenários como as políticas de Acesso Condicional que exijam que um utilizador esteja num local específico, o uso da autenticação multifactor, bem como permitir que um utilizador assine uma vez e depois seja automaticamente inscrito em todas as aplicações web que partilhem o mesmo diretório centralizado. Esta capacidade é referida como **Single Sign On (SSO)**.

A plataforma de identidade da Microsoft simplifica a autenticação e a autorização para os desenvolvedores de aplicações, fornecendo identidade como serviço, com suporte para protocolos padrão da indústria como OAuth 2.0, OpenID Connect e SAML 2.0, bem como bibliotecas de código aberto para diferentes plataformas para ajudá-lo a começar a codificar rapidamente. Permite que os desenvolvedores construam aplicações que assinam todas as identidades da Microsoft, obter fichas para ligar para o [Microsoft Graph](https://developer.microsoft.com/graph/), outras APIs da Microsoft ou APIs que os desenvolvedores construíram. Para mais informações, consulte a [evolution of Microsoft identity platform](about-microsoft-identity-platform.md).

Segue-se uma breve comparação dos vários protocolos utilizados pela plataforma de identidade da Microsoft:

* **OAuth vs. OpenID Connect**: OAuth é utilizado para autorização e o OpenID Connect (OIDC) é utilizado para a autenticação. O OpenID Connect é construído em cima do OAuth 2.0, pelo que a terminologia e o fluxo são semelhantes entre os dois. Pode até autenticar um utilizador (utilizando o OpenID Connect) e obter autorização para aceder a um recurso protegido que o utilizador possui (utilizando o OAuth 2.0) num pedido. Para mais informações, consulte os [protocolos OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md) e [o protocolo OpenID Connect](v2-protocols-oidc.md).
* **OAuth vs. SAML**: OAuth é utilizado para autorização e o SAML é utilizado para autenticação. Consulte a plataforma de identidade da Microsoft e o fluxo de afirmação do [portador OAuth 2.0 SAML](v2-saml-bearer-assertion.md) para obter mais informações sobre como os dois protocolos podem ser usados em conjunto para autenticar um utilizador (usando o SAML) e obter autorização para aceder a um recurso protegido (usando OAuth 2.0).
* **OpenID Connect vs. SAML**: Tanto o OpenID Connect como o SAML são utilizados para autenticar um utilizador e são utilizados para ativar o Single Sign On. A autenticação SAML é comumente utilizada com fornecedores de identidade, como os Serviços da Federação de Diretórios Ativos (ADFS) federados para a Azure AD e, portanto, são frequentemente utilizados em aplicações empresariais.

## <a name="next-steps"></a>Passos seguintes

Para outros tópicos que abranjam a autenticação e os fundamentos de autorização:

* Consulte [fichas](security-tokens.md) de segurança para saber como os tokens de acesso, fichas de atualização e fichas de identificação são usados na autenticação e autorização.
* Consulte o [modelo de Aplicação](application-model.md) para conhecer o processo de registo da sua aplicação para que possa integrar-se na plataforma de identidade da Microsoft.
* Consulte o [fluxo de entrada de apps](app-sign-in-flow.md) para saber sobre o fluxo de entrada de aplicações web, desktop e mobile na plataforma de identidade da Microsoft.

Para saber mais sobre os protocolos que a plataforma de identidade da Microsoft implementa:

* Consulte [os protocolos OAuth 2.0 e OpenID Connect na plataforma de identidade da Microsoft](active-directory-v2-protocols.md) para obter mais informações sobre os padrões OpenID Connect e OAuth 2.0.
* Consulte o [protocolo SAML de acesso único](single-sign-on-saml-protocol.md) para obter mais informações sobre como a plataforma de identidade da Microsoft suporta o Single Sign-On.
