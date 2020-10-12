---
title: Autenticação vs autorização / Rio Azure
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
ms.openlocfilehash: 095bc5594cf9b6eaf0df7929ea83c25a401793e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86252118"
---
# <a name="authentication-vs-authorization"></a>Autenticação vs autorização

Este artigo define a autenticação e autorização e cobre brevemente como pode utilizar a plataforma de identidade da Microsoft para autenticar e autorizar utilizadores nas suas aplicações web, APIs web ou apps que chamem APIs web protegidas. Se vir um termo que não conhece, experimente o nosso [glossário](developer-glossary.md) ou os [nossos vídeos de plataforma de identidade da Microsoft](identity-videos.md) que abranjam conceitos básicos.

## <a name="authentication"></a>Autenticação

**A autenticação** é o processo de provar que é quem diz ser. Por vezes, o termo é abreviado como AuthN. A plataforma de identidade da Microsoft implementa o protocolo [OpenID Connect](https://openid.net/connect/) para o tratamento da autenticação.

## <a name="authorization"></a>Autorização

**Autorização** é o ato de conceder permissão a uma parte autenticada para fazer algo. Especifica quais os dados a que pode aceder e o que pode fazer com esses dados. Por vezes, o termo é abreviado como AuthZ. A plataforma de identidade microsoft implementa o protocolo [OAuth 2.0](https://oauth.net/2/) para o manuseamento da autorização.

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>Autenticação e autorização utilizando a plataforma de identidade da Microsoft

Em vez de criar apps que cada um mantenha o seu próprio nome de utilizador e informações de senha, que incorre numa elevada carga administrativa quando precisa de adicionar ou remover utilizadores através de várias aplicações, as aplicações podem delegar essa responsabilidade num fornecedor de identidade centralizado.

O Azure Ative Directory (Azure AD) é um fornecedor de identidade centralizado na nuvem. Delegar a autenticação e autorização para o mesmo permite cenários como políticas de Acesso Condicional que exigem que um utilizador esteja num local específico, o uso da [autenticação multi-factor](../authentication/concept-mfa-howitworks.md) (por vezes referida como autenticação de dois fatores ou 2FA), bem como permitir que um utilizador assine uma vez e depois seja automaticamente inscrito em todas as aplicações web que partilham o mesmo diretório centralizado. Esta capacidade é referida como **Single Sign On (SSO)**.

A plataforma de identidade da Microsoft simplifica a autorização e a autenticação para os desenvolvedores de aplicações, fornecendo identidade como um serviço, com suporte para protocolos padrão da indústria como OAuth 2.0 e OpenID Connect, bem como bibliotecas de código aberto para diferentes plataformas para ajudá-lo a começar a codificar rapidamente. Permite que os desenvolvedores construam aplicações que assinem todas as identidades da Microsoft, obter tokens para ligar para [o Microsoft Graph](https://developer.microsoft.com/graph/), outras APIs da Microsoft, ou APIs que os desenvolvedores construíram.

Segue-se uma breve comparação dos vários protocolos utilizados pela plataforma de identidade microsoft:

* **OAuth vs OpenID Connect**: OAuth é usado para autorização e OpenID Connect (OIDC) é usado para autenticação. O OpenID Connect é construído em cima do OAuth 2.0, pelo que a terminologia e o fluxo são semelhantes entre os dois. Pode até autenticar um utilizador (utilizando o OpenID Connect) e obter autorização para aceder a um recurso protegido que o utilizador possui (utilizando o OAuth 2.0) num único pedido. Para obter mais informações, consulte [os protocolos OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md) e [o protocolo OpenID Connect](v2-protocols-oidc.md).
* **OAuth vs SAML**: OAuth é utilizado para autorização e SAML é utilizado para a autenticação. Consulte [a plataforma de identidade da Microsoft e o fluxo de afirmação do portador da OAuth 2.0 SAML](v2-saml-bearer-assertion.md) para obter mais informações sobre como os dois protocolos podem ser usados em conjunto para autenticar um utilizador (usando o SAML) e obter autorização para aceder a um recurso protegido (utilizando o OAuth 2.0).
* **OpenID Connect vs SAML**: Tanto o OpenID Connect como o SAML são utilizados para autenticar um utilizador e são utilizados para ativar o Single Sign On. A autenticação SAML é comumente utilizada com fornecedores de identidade, tais como Ative Directory Federation Services (ADFS) federados à Azure AD e, portanto, é frequentemente utilizado em aplicações empresariais. O OpenID Connect é comumente utilizado para apps que estão puramente na nuvem, tais como aplicações móveis, web sites e APIs da web.

## <a name="next-steps"></a>Passos seguintes

Para outros tópicos que abranjam os fundamentos básicos de autenticação e autorização:

* Consulte [fichas de segurança](security-tokens.md) para saber como os tokens de acesso, tokens de atualização e fichas de identificação são usados em autorização e autenticação.
* Consulte o [modelo de Aplicação](application-model.md) para saber mais sobre o processo de registo da sua aplicação para que possa integrar-se com a plataforma de identidade da Microsoft.
* Consulte o [fluxo de entrada de app](app-sign-in-flow.md) para saber sobre o fluxo de entrada de aplicações web, desktop e mobile na plataforma de identidade da Microsoft.

* Para saber mais sobre os protocolos que a plataforma de identidade da Microsoft implementa, consulte [os protocolos OAuth 2.0 e OpenID Connect na plataforma de identidade da Microsoft.](active-directory-v2-protocols.md)
* Consulte [o protocolo SAML single Sign-On](single-sign-on-saml-protocol.md) para obter mais informações sobre como a plataforma de identidade da Microsoft suporta o Sign-On único.
* Consulte [o único s-on para aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md) para obter mais informações sobre as diferentes formas de implementar um único s-on na sua aplicação.
