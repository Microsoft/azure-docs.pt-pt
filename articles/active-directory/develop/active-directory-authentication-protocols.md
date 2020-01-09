---
title: Protocolos de autenticação da plataforma de identidade da Microsoft | Microsoft Docs
description: Uma visão geral dos protocolos de autenticação com suporte na plataforma de identidade da Microsoft
author: rwike77
services: active-directory
manager: CelesteDG
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94917d601de24388600bae3434f1d03a35bffd33
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424768"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Protocolos de autenticação da plataforma Microsoft Identity

A plataforma de identidade da Microsoft dá suporte a vários dos protocolos de autenticação e autorização mais amplamente usados. Os tópicos nesta seção descrevem os protocolos com suporte e sua implementação na plataforma Microsoft Identity. Os tópicos incluíram uma revisão dos tipos de declaração com suporte, uma introdução ao uso de metadados de Federação, o OAuth 2,0 detalhado. e a documentação de referência do protocolo SAML 2,0 e uma seção de solução de problemas.

## <a name="authentication-protocols-articles-and-reference"></a>Artigos e referência de protocolos de autenticação

* [Informações importantes sobre a substituição de chave de assinatura na plataforma de identidade da Microsoft](active-directory-signing-key-rollover.md) – saiba mais sobre a cadência da substituição de chave de assinatura da plataforma de identidade da Microsoft, as alterações que você pode fazer para atualizar a chave automaticamente e discussão sobre como atualizar os cenários de aplicativos mais comuns.
* [Tipos de declaração e token com suporte](id-tokens.md) -saiba mais sobre as declarações nos tokens que a plataforma de identidade da Microsoft emite.
* [Oauth 2,0 na plataforma de identidade da Microsoft](v2-oauth2-auth-code-flow.md) -saiba mais sobre a implementação do OAuth 2,0 na plataforma Microsoft Identity.
* [OpenID connect 1,0](v2-protocols-oidc.md) -saiba como usar o OAuth 2,0, um protocolo de autorização, para autenticação.
* [Chamadas de serviço a serviço com credenciais de cliente](v2-oauth2-client-creds-grant-flow.md) – saiba como usar o fluxo de concessão de credenciais de cliente do OAuth 2,0 para chamadas de serviço a serviço.
* [Serviço para chamadas de serviço com fluxo em nome de](v2-oauth2-on-behalf-of-flow.md) -saiba como usar o fluxo em nome de OAuth 2,0 para chamadas de serviço a serviço.
* [Referência de protocolo SAML](active-directory-saml-protocol-reference.md) -saiba mais sobre os perfis SAML de logon único e logout único da plataforma de identidade da Microsoft.

## <a name="see-also"></a>Ver também

* [Visão geral da plataforma Microsoft Identity](v2-overview.md)
* [Exemplos de código Active Directory](sample-v2-code.md)
