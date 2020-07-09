---
title: Protocolos de autenticação da plataforma de identidade da Microsoft
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80884686"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Protocolos de autenticação da plataforma de identidade da Microsoft

A plataforma de identidade da Microsoft suporta vários dos protocolos de autenticação e autorização mais utilizados. Os tópicos nesta secção descrevem os protocolos suportados e a sua implementação na plataforma de identidade da Microsoft. Os tópicos incluíam uma revisão dos tipos de reclamações apoiados, uma introdução à utilização de metadados da federação, o OAuth 2.0 detalhado. e documentação de referência do protocolo SAML 2.0, e uma secção de resolução de problemas.

## <a name="authentication-protocols-articles-and-reference"></a>Artigos e referências de protocolos de autenticação

* [Informações importantes sobre a assinatura do Rollover da chave na plataforma de identidade](active-directory-signing-key-rollover.md) da Microsoft – Saiba mais sobre a cadência de capotamento da chave de assinatura da Microsoft, as alterações que pode fazer para atualizar a chave automaticamente e a discussão sobre como atualizar os cenários de aplicação mais comuns.
* [Supported Token and Claim Types](id-tokens.md) - Saiba mais sobre as alegações nos tokens que a Microsoft tem problemas na plataforma de identidade.
* [OAuth 2.0 na plataforma de identidade da Microsoft](v2-oauth2-auth-code-flow.md) - Saiba mais sobre a implementação do OAuth 2.0 na plataforma de identidade da Microsoft.
* [OpenID Connect 1.0](v2-protocols-oidc.md) - Saiba como utilizar o OAuth 2.0, um protocolo de autorização, para autenticação.
* [Serviço a Chamadas de Serviço com Credenciais](v2-oauth2-client-creds-grant-flow.md) de Cliente - Saiba como usar o fluxo de concessão de credenciais de cliente OAuth 2.0 para serviço a chamadas de serviço.
* [Serviço a Chamadas de Serviço com On-Behalf-Of Flow](v2-oauth2-on-behalf-of-flow.md) - Saiba como usar o fluxo de nome de OAuth 2.0 para o serviço para chamadas de serviço.
* [Referência do Protocolo SAML](active-directory-saml-protocol-reference.md) - Conheça os perfis SAML de assinatura única e único sinal de assinatura da plataforma de identidade da Microsoft.

## <a name="see-also"></a>Veja também

* [Visão geral da plataforma de identidade da Microsoft](v2-overview.md)
* [Amostras de código de diretório ativo](sample-v2-code.md)
