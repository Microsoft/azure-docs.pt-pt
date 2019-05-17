---
title: Protocolos de autenticação do Active Directory do Azure | Documentos da Microsoft
description: Uma descrição geral dos protocolos de autenticação suportado pelo Azure Active Directory (AD)
documentationcenter: dev-center-name
author: ryanwi
services: active-directory
manager: CelesteDG
editor: ''
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: rwike77
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: d36e4304c4775ac335c1cc405ee8c6a0208da52a
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546222"
---
# <a name="azure-active-directory-authentication-protocols"></a>Protocolos de autenticação do Azure Active Directory
Azure Active Directory (Azure AD) suporta vários dos protocolos de autenticação e autorização mais amplamente utilizados. Os tópicos nesta secção descrevem os protocolos suportados e sua implementação no Azure AD. Os tópicos incluída uma análise dos tipos de afirmação suportados, uma introdução ao uso dos metadados de Federação, detalhadas OAuth 2.0. e documentação de referência do protocolo SAML 2.0 e uma secção de resolução de problemas.

## <a name="authentication-protocols-articles-and-reference"></a>Protocolos de autenticação de artigos e referência
* [Importante informações sobre como assinar Rollover de chave no Azure AD](active-directory-signing-key-rollover.md) – Saiba mais sobre a cadência da chave, que pode efetuar para atualizar automaticamente a chave de alterações e discussão sobre como atualizar os cenários mais comuns de aplicação de assinatura do Azure AD.
* [Token e tipos de afirmação suportados](v1-id-and-access-tokens.md) -Saiba mais sobre as afirmações nos tokens de que o Azure AD emite.
* [Metadados de Federação](azure-ad-federation-metadata.md) -Saiba como localizar e interpretar os documentos de metadados que o Azure AD gera.
* [OAuth 2.0 no Azure AD](v1-protocols-oauth-code.md) -Saiba mais sobre a implementação de OAuth 2.0 no Azure AD.
* [OpenID Connect 1.0](v1-protocols-openid-connect-code.md) -Saiba como utilizar o OAuth 2.0, um protocolo de autorização, para autenticação.
* [Chamadas de serviço para serviço com as credenciais de cliente](v1-oauth2-client-creds-grant-flow.md) -Saiba como utilizar o fluxo de concessão de credenciais de cliente OAuth 2.0 para chamadas de serviço para serviço.
* [Chamadas de serviço para serviço com o On-Behalf-Of Flow](v1-oauth2-on-behalf-of-flow.md) -Saiba como utilizar o fluxo do OAuth 2.0 On-Behalf-Of para chamadas de serviço para serviço.
* [Referência do protocolo SAML](active-directory-saml-protocol-reference.md) -Saiba mais sobre os perfis de início de sessão único e SAML de fim de sessão único do Azure AD.

## <a name="see-also"></a>Consultar Também
[Guia para programadores do Azure Active Directory](v1-overview.md)

[Exemplos de código do Active Directory](sample-v1-code.md)
