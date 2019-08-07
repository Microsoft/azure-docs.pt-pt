---
title: Azure Active Directory protocolos de autenticação | Microsoft Docs
description: Uma visão geral dos protocolos de autenticação com suporte do Azure Active Directory (AD)
documentationcenter: dev-center-name
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: f292d4804adf7e1a58e5c2097f689aac182ff783
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835490"
---
# <a name="azure-active-directory-authentication-protocols"></a>Azure Active Directory protocolos de autenticação
O Azure Active Directory (AD do Azure) dá suporte a vários dos protocolos de autenticação e autorização mais amplamente usados. Os tópicos nesta seção descrevem os protocolos com suporte e sua implementação no Azure AD. Os tópicos incluíram uma revisão dos tipos de declaração com suporte, uma introdução ao uso de metadados de Federação, o OAuth 2,0 detalhado. e a documentação de referência do protocolo SAML 2,0 e uma seção de solução de problemas.

## <a name="authentication-protocols-articles-and-reference"></a>Artigos e referência de protocolos de autenticação
* [Informações importantes sobre a substituição de chave de assinatura no Azure ad](active-directory-signing-key-rollover.md) – saiba mais sobre a cadência de substituição de chave de assinatura do Azure AD, alterações que você pode fazer para atualizar a chave automaticamente e discussão sobre como atualizar os cenários de aplicativos mais comuns.
* [Tipos de declaração e token com suporte](v1-id-and-access-tokens.md) -saiba mais sobre as declarações nos tokens que o Azure ad emite.
* [Metadados de Federação](azure-ad-federation-metadata.md) -saiba como localizar e interpretar os documentos de metadados que o Azure ad gera.
* [Oauth 2,0 no Azure ad](v1-protocols-oauth-code.md) -saiba mais sobre a implementação do OAuth 2,0 no Azure AD.
* [OpenID connect 1,0](v1-protocols-openid-connect-code.md) -saiba como usar o OAuth 2,0, um protocolo de autorização, para autenticação.
* [Chamadas de serviço a serviço com credenciais de cliente](v1-oauth2-client-creds-grant-flow.md) – saiba como usar o fluxo de concessão de credenciais de cliente do OAuth 2,0 para chamadas de serviço a serviço.
* [Serviço para chamadas de serviço com fluxo em nome de](v1-oauth2-on-behalf-of-flow.md) -saiba como usar o fluxo em nome de OAuth 2,0 para chamadas de serviço a serviço.
* [Referência de protocolo SAML](active-directory-saml-protocol-reference.md) -saiba mais sobre os perfis SAML de logon único e logout único do Azure AD.

## <a name="see-also"></a>Consultar Também
[Guia do desenvolvedor de Azure Active Directory](v1-overview.md)

[Exemplos de código Active Directory](sample-v1-code.md)
