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
ms.openlocfilehash: 9668d3b0b57e36fb95421f8b502b9b743be8eb31
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756223"
---
# <a name="security-tokens"></a>Tokens de segurança

Um fornecedor de identidade centralizado é especialmente útil para apps que têm utilizadores localizados em todo o mundo que não fazem necessariamente o sismo na rede da empresa. A plataforma de identidade da Microsoft autentica os utilizadores e fornece fichas de segurança, como [o token de acesso,](developer-glossary.md#access-token) [o token de atualização](developer-glossary.md#refresh-token)e [o token de ID,](developer-glossary.md#id-token)que permitem a uma [aplicação](developer-glossary.md#client-application) do cliente aceder a recursos protegidos num [servidor de recursos.](developer-glossary.md#resource-server)

Um **token de acesso** é um símbolo de segurança que é emitido por um servidor de [autorização](developer-glossary.md#authorization-server) como parte de um fluxo [OAuth 2.0.](active-directory-v2-protocols.md) Contém informações sobre o utilizador e a aplicação para a qual o símbolo se destina; que pode ser usado para aceder a APIs web e outros recursos protegidos. Para saber mais sobre como a plataforma de identidade da Microsoft emite acesso a fichas, consulte [tokens de acesso.](access-tokens.md)

Os tokens de acesso são válidos apenas por um curto período de tempo, por isso os servidores de autorização por vezes emitem um **token de atualização** ao mesmo tempo que o token de acesso é emitido. A aplicação do cliente pode então trocar este token de atualização para um novo token de acesso quando necessário. Para saber mais sobre como a plataforma de identidade da Microsoft utiliza fichas de atualização para revogar permissões, consulte [a revogação do Token](access-tokens.md#token-revocation).

**As fichas de identificação** são enviadas para a aplicação do cliente como parte de um fluxo [OpenID Connect.](v2-protocols-oidc.md) Podem ser enviados juntamente ou em vez de um token de acesso, e são usados pelo cliente para autenticar o utilizador. Para saber mais sobre como a plataforma de identidade da Microsoft emite fichas de identificação, consulte [fichas de identificação.](id-tokens.md)

> [!NOTE]
> Este artigo discute fichas de segurança usadas pelos protocolos OAuth2 e OpenID Connect. Muitas aplicações empresariais usam SAML para autenticar utilizadores. Consulte [a referência simbólica Azure AD SAML](reference-saml-tokens.md) para obter informações sobre afirmações DAL.

## <a name="validating-security-tokens"></a>Validação de fichas de segurança

Cabe à app para a qual o token foi gerado, a aplicação web que assinou o utilizador, ou a API web a ser chamada, para validar o token. O token é assinado pelo **Security Token Server (STS)** com uma chave privada. O STS publica a chave pública correspondente. Para validar um token, a aplicação verifica a assinatura utilizando a chave pública STS para validar que a assinatura foi criada usando a chave privada.

As fichas só são válidas por um período limitado de tempo. Normalmente, o STS fornece um par de fichas:

* Um símbolo de acesso para aceder à aplicação ou recurso protegido, e
* Um token de atualização usado para refrescar o token de acesso quando o token de acesso está perto de expirar.

Os tokens de acesso são passados para uma API web como o símbolo do portador no `Authorization` cabeçalho. Uma aplicação pode fornecer um token de atualização para o STS, e se o acesso do utilizador à app não foi revogado, receberá de volta um novo token de acesso e um novo token de atualização. É assim que o cenário de alguém sair da empresa é tratado. Quando o STS receber o token de atualização, não emitirá outro token de acesso válido se o utilizador deixar de ser autorizado.

## <a name="json-web-tokens-jwts-and-claims"></a>JSON Web Tokens (JWTs) e reivindicações

A plataforma de identidade da Microsoft implementa fichas de segurança como **JSON Web Tokens (JWTs)** que contêm **reclamações**. Uma vez que os JWTs são usados como fichas de segurança, esta forma de autenticação é por vezes chamada **de autenticação JWT**.

Uma [reclamação](developer-glossary.md#claim) fornece afirmações sobre uma entidade, como uma aplicação do cliente ou [proprietário de recursos,](developer-glossary.md#resource-owner)a outra entidade, como um servidor de recursos. Uma reclamação também pode ser referida como uma reclamação JWT ou uma reclamação da JSON Web Token.

As reclamações são pares de nome/valor que transmitem factos sobre o sujeito simbólico. Por exemplo, uma reclamação pode conter factos sobre o principal de segurança que foi autenticado pelo servidor de autorização. As alegações presentes num dado símbolo dependem de muitas coisas, incluindo o tipo de token, o tipo de credencial usado para autenticar o sujeito, a configuração da aplicação, e assim por diante.

As aplicações podem utilizar reclamações para várias tarefas, tais como:

* Validação do token
* Identificação do inquilino do [sujeito](developer-glossary.md#tenant) simbólico
* Exibindo informações do utilizador
* Determinação da autorização do sujeito

Uma reivindicação consiste em pares de valores-chave que fornecem informações como:

* Servidor de Token de Segurança que gerou o token
* Data quando o símbolo foi gerado
* Objeto (como o utilizador- exceto para os daemons)
* Público, que é a app para a qual o símbolo foi gerado
* App (o cliente) que pediu o token. No caso das aplicações web, isto pode ser o mesmo que o público

Para saber mais sobre como a plataforma de identidade da Microsoft implementa fichas e reclama informações, consulte [tokens de acesso](access-tokens.md) e [fichas de ID.](id-tokens.md)

## <a name="how-each-flow-emits-tokens-and-codes"></a>Como cada fluxo emite fichas e códigos

Dependendo da forma como o seu cliente é construído, pode utilizar um (ou vários) dos fluxos de autenticação suportados pela plataforma de identidade microsoft. Estes fluxos podem produzir uma variedade de fichas (fichas de identificação, tokens de atualização, fichas de acesso) bem como códigos de autorização, e requerem diferentes fichas para fazê-los funcionar. Este gráfico fornece uma visão geral:

|Fluxo | Requer | Ficha de ID | token de acesso | token refresh | código de autorização |
|-----|----------|----------|--------------|---------------|--------------------|
|[Fluxo de código de autorização](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Fluxo implícito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Fluxo híbrido OIDC](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Atualizar a redenção simbólica](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token refresh | x | x | x| |
|[Fluxo em-nome-de](v2-oauth2-on-behalf-of-flow.md) | token de acesso| x| x| x| |
|[Credenciais de cliente](v2-oauth2-client-creds-grant-flow.md) | | | x (apenas para aplicações)| | |

Os tokens emitidos através do modo implícito têm uma limitação de comprimento devido a ser passado de volta para o navegador através do URL (onde `response_mode` está `query` ou `fragment` ).  Alguns navegadores têm um limite no tamanho do URL que pode ser colocado na barra de navegador e falhar quando é muito longo.  Assim, estes tokens não têm `groups` nem `wids` reivindicações.

## <a name="next-steps"></a>Próximos passos

Para outros tópicos que abranjam os fundamentos básicos de autenticação e autorização:

* Consulte [a autenticação vs. autorização](authentication-vs-authorization.md) para conhecer os conceitos básicos de autenticação e autorização na plataforma de identidade da Microsoft.
* Consulte o [modelo de Aplicação](application-model.md) para saber mais sobre o processo de registo da sua aplicação para que possa integrar-se com a plataforma de identidade da Microsoft.
* Consulte o [fluxo de entrada de app](app-sign-in-flow.md) para saber sobre o fluxo de entrada de aplicações web, desktop e mobile na plataforma de identidade da Microsoft.
