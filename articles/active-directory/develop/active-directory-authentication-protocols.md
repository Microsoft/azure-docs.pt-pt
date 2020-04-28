---
title: Protocolos de autenticação da plataforma de identidade microsoft
description: Uma visão geral dos protocolos de autenticação suportados pela plataforma de identidade da Microsoft
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 41ea41b4d7c181dad9246653a68c329387ac5381
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80884686"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Protocolos de autenticação da plataforma de identidade microsoft

A plataforma de identidade da Microsoft suporta vários dos protocolos de autenticação e autorização mais utilizados. Os tópicos nesta secção descrevem os protocolos suportados e a sua implementação na plataforma de identidade da Microsoft. Os tópicos incluíam uma revisão dos tipos de reclamação suportados, uma introdução ao uso de metadados da federação, detalhado OAuth 2.0. e documentação de referência de protocolo SAML 2.0, e uma secção de resolução de problemas.

## <a name="authentication-protocols-articles-and-reference"></a>Artigos e referência de protocolos de autenticação

* [Informações importantes sobre a assinatura](active-directory-signing-key-rollover.md) de key rollover na plataforma de identidade da Microsoft – Saiba mais sobre a cadência de chave de transferência da plataforma de identidade da Microsoft, alterações que pode fazer para atualizar a chave automaticamente, e discussão sobre como atualizar os cenários de aplicação mais comuns.
* [Suporte token and Claim Types](id-tokens.md) - Saiba mais sobre as alegações nos tokens que a plataforma de identidade da Microsoft emite.
* [OAuth 2.0 na plataforma de identidade da Microsoft](v2-oauth2-auth-code-flow.md) - Conheça a implementação do OAuth 2.0 na plataforma de identidade da Microsoft.
* [OpenID Connect 1.0](v2-protocols-oidc.md) - Saiba como utilizar o OAuth 2.0, um protocolo de autorização, para autenticação.
* [Serviço de Atendimento Chamadas com Credenciais de Cliente](v2-oauth2-client-creds-grant-flow.md) - Saiba como utilizar credenciais de cliente OAuth 2.0 concedem fluxo de serviço para chamadas de serviço.
* [Serviço de Atendimento Chamadas com On-Behalf-Of Flow](v2-oauth2-on-behalf-of-flow.md) - Aprenda a utilizar o fluxo OAuth 2.0 On-Behalf-Of para o serviço às chamadas de serviço.
* Referência do [Protocolo SAML](active-directory-saml-protocol-reference.md) - Conheça os perfis SAML de inscrição única e de assinatura única da plataforma de identidade da Microsoft.

## <a name="see-also"></a>Consulte também

* [Visão geral da plataforma de identidade da Microsoft](v2-overview.md)
* [Amostras de código de diretório ativo](sample-v2-code.md)
