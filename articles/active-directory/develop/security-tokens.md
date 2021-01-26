---
title: Fichas de segurança | Rio Azure
titleSuffix: Microsoft identity platform
description: Conheça os fundamentos dos tokens de segurança na plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6d9f5538d377be1414089e591559344bde4f381a
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795633"
---
# <a name="security-tokens"></a>Tokens de segurança

Um fornecedor de identidade centralizado é especialmente útil para apps que têm utilizadores localizados em todo o mundo que não fazem necessariamente o sismo na rede da empresa. A plataforma de identidade da Microsoft autentica os utilizadores e fornece fichas de segurança, tais como [tokens de acesso,](developer-glossary.md#access-token) [tokens de atualização](developer-glossary.md#refresh-token)e [fichas de identificação.](developer-glossary.md#id-token) As fichas de segurança permitem que uma [aplicação](developer-glossary.md#client-application) do cliente aceda a recursos protegidos num [servidor de recursos.](developer-glossary.md#resource-server)

**Ficha de acesso**: Um token de acesso é um símbolo de segurança emitido por um servidor de [autorização](developer-glossary.md#authorization-server) como parte de um fluxo [OAuth 2.0.](active-directory-v2-protocols.md) Contém informações sobre o utilizador e o recurso para o qual o símbolo se destina. A informação pode ser usada para aceder a APIs web e outros recursos protegidos. Os tokens de acesso são validados por recursos para garantir o acesso a uma aplicação de clientes. Para saber mais sobre como a plataforma de identidade da Microsoft emite acesso a fichas, consulte [tokens de acesso.](access-tokens.md)

**Refresh token**: Como os tokens de acesso são válidos por um curto período de tempo, os servidores de autorização por vezes emitirão um token de atualização ao mesmo tempo que o token de acesso é emitido. A aplicação do cliente pode então trocar este token de atualização para um novo token de acesso quando necessário. Para saber mais sobre como a plataforma de identidade da Microsoft utiliza fichas de atualização para revogar permissões, consulte [a revogação do Token](access-tokens.md#token-revocation).

**Ficha de ID**: As fichas de identificação são enviadas para a aplicação do cliente como parte de um fluxo [OpenID Connect.](v2-protocols-oidc.md) Podem ser enviados ao lado ou em vez de um símbolo de acesso. As fichas de identificação são utilizadas pelo cliente para autenticar o utilizador. Para saber mais sobre como a plataforma de identidade da Microsoft emite fichas de identificação, consulte [fichas de identificação.](id-tokens.md)

> [!NOTE]
> Este artigo discute fichas de segurança usadas pelos protocolos OAuth2 e OpenID Connect. Muitas aplicações empresariais usam SAML para autenticar utilizadores. Para obter informações sobre as afirmações da SAML, consulte [a referência simbólica do Azure Ative Directory SAML](reference-saml-tokens.md).

## <a name="validate-security-tokens"></a>Validar fichas de segurança

Cabe à app para a qual o token foi gerado, a aplicação web que assinou no utilizador, ou a API web ser chamada para validar o token. O token é assinado pelo servidor de autorização com uma chave privada. O servidor de autorização publica a chave pública correspondente. Para validar um token, a aplicação verifica a assinatura utilizando a chave pública do servidor de autorização para validar que a assinatura foi criada usando a chave privada.

As fichas são válidas por apenas um período limitado de tempo. Normalmente, o servidor de autorização fornece um par de fichas, tais como:

* Um token de acesso, que acede à aplicação ou ao recurso protegido.
* Um token refresh, que é usado para refrescar o token de acesso quando o token de acesso está perto de expirar.

Os tokens de acesso são passados para uma API web como o símbolo do portador no `Authorization` cabeçalho. Uma aplicação pode fornecer um token de atualização para o servidor de autorização. Se o acesso do utilizador à aplicação não foi revogado, receberá de volta um novo token de acesso e um novo token de atualização. É assim que o cenário de alguém sair da empresa é tratado. Quando o servidor de autorização receber o token de atualização, não emitirá outro token de acesso válido se o utilizador deixar de ser autorizado.

## <a name="json-web-tokens-and-claims"></a>JSON Web Tokens e reivindicações

A plataforma de identidade da Microsoft implementa fichas de segurança como JSON Web Tokens (JWTs) que contêm *reclamações*. Uma vez que os JWTs são usados como fichas de segurança, esta forma de autenticação é por vezes chamada *de autenticação JWT*.

Uma [reclamação](developer-glossary.md#claim) fornece afirmações sobre uma entidade, como uma aplicação do cliente ou [proprietário de recursos,](developer-glossary.md#resource-owner)a outra entidade, como um servidor de recursos. Uma reclamação também pode ser referida como uma reclamação JWT ou uma reclamação da JSON Web Token.

As reclamações são nomes ou pares de valor que transmitem factos sobre o sujeito simbólico. Por exemplo, uma alegação pode conter factos sobre o principal de segurança que foi autenticado pelo servidor de autorização. As alegações presentes num símbolo específico dependem de muitas coisas, como o tipo de token, o tipo de credencial usado para autenticar o sujeito, e a configuração da aplicação.

As aplicações podem utilizar reclamações para várias tarefas, tais como:

* Valide o símbolo.
* Identifique o inquilino do [sujeito](developer-glossary.md#tenant)simbólico.
* Mostrar as informações do utilizador.
* Determine a autorização do sujeito.

Uma reivindicação consiste em pares de valores-chave que fornecem informações como:

* Servidor de Ficha de Segurança que gerou o símbolo.
* Data quando o símbolo foi gerado.
* Objeto (como o utilizador- exceto os daemons).
* Público, que é a app para a qual o símbolo foi gerado.
* App (o cliente) que pediu o token. No caso das aplicações web, esta aplicação pode ser a mesma que o público.

Para saber mais sobre como a plataforma de identidade da Microsoft implementa fichas e informações de reclamação, consulte [tokens de acesso](access-tokens.md) e [fichas de ID.](id-tokens.md)

## <a name="how-each-flow-emits-tokens-and-codes"></a>Como cada fluxo emite fichas e códigos

Dependendo da forma como o seu cliente é construído, pode utilizar um (ou vários) dos fluxos de autenticação suportados pela plataforma de identidade da Microsoft. Estes fluxos podem produzir vários tokens (fichas de identificação, tokens de atualização, fichas de acesso) e códigos de autorização. Requerem fichas diferentes para que funcionem. Esta tabela fornece uma visão geral.

|Fluxo | Requer | Ficha de ID | Ficha de acesso | Refresh token | Código de autorização |
|-----|----------|----------|--------------|---------------|--------------------|
|[Fluxo de código de autorização](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Fluxo implícito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Fluxo híbrido OIDC](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Atualizar a redenção simbólica](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | Refresh token | x | x | x| |
|[Fluxo em-nome-de](v2-oauth2-on-behalf-of-flow.md) | Ficha de acesso| x| x| x| |
|[Credenciais de cliente](v2-oauth2-client-creds-grant-flow.md) | | | x (apenas app)| | |

Os tokens emitidos através do modo implícito têm uma limitação de comprimento porque são passados de volta para o navegador através do URL, onde `response_mode` está `query` ou `fragment` . Alguns navegadores têm um limite no tamanho do URL que pode ser colocado na barra de navegador e falhar quando é muito longo. Como resultado, estes tokens não têm `groups` ou `wids` reivindicam.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a autenticação e autorização na plataforma de identidade da Microsoft, consulte os seguintes artigos:

* Para conhecer os conceitos básicos de autenticação e autorização, consulte [autenticação vs. autorização.](authentication-vs-authorization.md)
* Para saber mais sobre o registo da sua candidatura para integração, consulte [o modelo de Aplicação.](application-model.md)
* Para saber mais sobre o fluxo de entrada de web, desktop e aplicativos móveis, consulte o [fluxo de entrada de app](app-sign-in-flow.md).
