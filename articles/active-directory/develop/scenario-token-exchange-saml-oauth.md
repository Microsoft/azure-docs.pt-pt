---
title: Cenário de troca de símbolos de plataforma de identidade da Microsoft com SAML e OIDC/OAuth no Azure Ative Directory
description: Saiba mais sobre cenários comuns de troca de símbolos ao trabalhar com a SAML e a OIDC/OAuth no Azure Ative Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 92d0dad86b3f048eb96dd7b17ed09f6e20d7cde2
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063302"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>Cenários de troca de símbolos de plataforma de identidade da Microsoft com SAML e OIDC/OAuth

SAML e OpenID Connect (OIDC) / OAuth são protocolos populares usados para implementar Sign-On (SSO). Algumas aplicações só podem implementar SAML e outras só podem implementar OIDC/OAuth. Ambos os protocolos usam fichas para comunicar segredos. Para saber mais sobre o SAML, consulte [o protocolo Single Sign-On SAML](single-sign-on-saml-protocol.md). Para saber mais sobre o OIDC/OAuth, consulte [os protocolos OAuth 2.0 e OpenID Connect na plataforma de identidade da Microsoft.](active-directory-v2-protocols.md)

Este artigo traça um cenário comum onde uma aplicação implementa o SAML mas é necessário ligar para a API do Gráfico, que utiliza o OIDC/OAuth. São fornecidas orientações básicas para as pessoas que trabalham com este cenário.

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>Cenário: Você tem um token SAML e quer chamar a API gráfico
Muitas aplicações são implementadas com SAML. No entanto, a API do Gráfico utiliza os protocolos OIDC/OAuth. É possível, embora não trivial, adicionar a funcionalidade OIDC/OAuth a uma aplicação SAML. Uma vez que a funcionalidade OAuth esteja disponível numa aplicação, a API do Gráfico pode ser utilizada.

A estratégia geral é adicionar a pilha OIDC/OAuth à sua aplicação. Com a sua aplicação que implementa ambos os padrões pode usar um cookie de sessão. Não estás a trocar um símbolo explicitamente. Está a iniciar sessão com um utilizador com o SAML, o que gera um cookie de sessão. Quando a API do Gráfico invoca um fluxo de OAuth, utilize o cookie de sessão para autenticar. Esta estratégia pressupõe o passe de verificação de acesso condicional e o utilizador é autorizado.

> [!NOTE]
> A biblioteca recomendada para adicionar o comportamento OIDC/OAuth é a Microsoft Authentication Library (MSAL). Para saber mais sobre o MSAL, consulte [a visão geral da Biblioteca de Autenticação da Microsoft (MSAL)](msal-overview.md). A biblioteca anterior chamava-se Ative Directory Authentication Library (ADAL), no entanto não é recomendada, uma vez que a MSAL está a substituí-la.

## <a name="next-steps"></a>Próximos passos
- [Fluxos de autenticação e cenários de aplicação](authentication-flows-app-scenarios.md)
