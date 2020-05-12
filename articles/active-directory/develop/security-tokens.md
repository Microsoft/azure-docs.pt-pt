---
title: Fichas de segurança Azure
titleSuffix: Microsoft identity platform
description: Conheça os fundamentos das fichas de segurança na plataforma de identidade da Microsoft (v2.0).
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
ms.openlocfilehash: def198a15710d0aff4a943300eedc338a7772e46
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115800"
---
# <a name="security-tokens"></a>Fichas de segurança

Um fornecedor de identidade centralizado é especialmente útil para apps que têm utilizadores localizados em todo o mundo que não necessariamente se inscrevem na rede da empresa. A plataforma de identidade da Microsoft autentica os utilizadores e fornece fichas de segurança, tais como token de [acesso,](developer-glossary.md#access-token) [token de atualização](developer-glossary.md#refresh-token)e [ficha de identificação,](developer-glossary.md#id-token)que permitem a uma [aplicação de cliente](developer-glossary.md#client-application) aceder a recursos protegidos num servidor de [recursos.](developer-glossary.md#resource-server)

Um **sinal de acesso** é um símbolo de segurança que é emitido por um servidor de [autorização](developer-glossary.md#authorization-server) como parte de um fluxo [OAuth 2.0.](active-directory-v2-protocols.md) Contém informações sobre o utilizador e a aplicação para a qual se destina o símbolo; que podem ser usados para aceder a APIs web e outros recursos protegidos. Para saber mais sobre como a plataforma de identidade da Microsoft emite fichas de acesso, consulte [fichas](access-tokens.md)de acesso .

Os tokens de acesso são válidos apenas por um curto período de tempo, pelo que os servidores de autorização por vezes emitirão um token de **atualização** ao mesmo tempo que o token de acesso é emitido. A aplicação do cliente pode então trocar este token de atualização por um novo token de acesso quando necessário. Para saber mais sobre como a plataforma de identidade da Microsoft usa fichas de atualização para revogar permissões, consulte a revogação de [Token](access-tokens.md#token-revocation).

**As fichas de identificação** são enviadas para a aplicação do cliente como parte de um fluxo [OpenID Connect.](v2-protocols-oidc.md) Podem ser enviados juntamente ou em vez de um sinal de acesso, e são utilizados pelo cliente para autenticar o utilizador. Para saber mais sobre como a plataforma de identidade da Microsoft emite fichas de identificação, consulte [fichas de identificação](id-tokens.md).

> [!NOTE]
> Este artigo discute fichas de segurança usadas pelos protocolos OAuth2 e OpenID Connect. Muitas aplicações empresariais usam o SAML para autenticar os utilizadores. Consulte a referência simbólica [Azure AD SAML](reference-saml-tokens.md) para obter informações sobre as afirmações da SAML.

## <a name="validating-security-tokens"></a>Validação de fichas de segurança

Cabe à aplicação para a qual o token foi gerado, à aplicação web que assinou no utilizador, ou à Web API ser chamada, para validar o token. O símbolo é assinado pelo **Security Token Server (STS)** com uma chave privada. O STS publica a chave pública correspondente. Para validar um símbolo, a aplicação verifica a assinatura utilizando a chave pública STS para validar que a assinatura foi criada usando a chave privada.

Os tokens só são válidos por um período limitado de tempo. Normalmente, o STS fornece um par de fichas:

* Um sinal de acesso para aceder à aplicação ou recurso protegido, e
* Um token refrescante usado para refrescar o token de acesso quando o token de acesso está perto de expirar.

As fichas de acesso são passadas para uma API web como o símbolo do portador no `Authorization` cabeçalho. Uma aplicação pode fornecer um token de atualização para o STS, e se o acesso do utilizador à aplicação não foi revogado, receberá de volta um novo token de acesso e um novo token de atualização. É assim que o cenário de alguém deixar a empresa é tratado. Quando o STS receber o token de atualização, não emitirá outro sinal de acesso válido se o utilizador deixar de ser autorizado.

## <a name="json-web-tokens-jwts-and-claims"></a>JSON Web Tokens (JWTs) e reclamações

A plataforma de identidade da Microsoft implementa tokens de segurança como **JSON Web Tokens (JWTs)** que contêm **reclamações**. Uma vez que os JWTs são usados como fichas de segurança, esta forma de autenticação é por vezes chamada **de autenticação JWT**.

Uma [reclamação](developer-glossary.md#claim) fornece afirmações sobre uma entidade, como uma aplicação de cliente ou proprietário de [recursos,](developer-glossary.md#resource-owner)a outra entidade, como um servidor de recursos. Uma reclamação também pode ser referida como uma reclamação JWT ou jSON Web Token.

As alegações são pares de nome/valor que transmitem factos sobre o assunto simbólico. Por exemplo, uma reclamação pode conter factos sobre o diretor de segurança que foi autenticado pelo servidor de autorização. As alegações presentes num dado sinal ão de penderá de muitas coisas, incluindo o tipo de ficha, o tipo de credencial usada para autenticar o sujeito, a configuração da aplicação, e assim por diante.

As aplicações podem utilizar reclamações para várias tarefas, tais como:

* Validando o símbolo
* Identificando o [inquilino](developer-glossary.md#tenant) do sujeito simbólico
* Exibindo informações do utilizador
* Determinação da autorização do sujeito

Uma reivindicação consiste em pares de valor-chave que fornecem informações como:

* Servidor de Token de Segurança que gerou o símbolo
* Data em que o símbolo foi gerado
* Objeto (como o utilizador-- exceto para daemons)
* Público, que é a app para a qual o token foi gerado
* App (o cliente) que pediu o símbolo. No caso das aplicações web, este pode ser o mesmo que o público

Para saber mais sobre como a plataforma de identidade da Microsoft implementa tokens e informações de reclamação, consulte [fichas](access-tokens.md) de acesso e [fichas de identificação](id-tokens.md).

## <a name="how-each-flow-emits-tokens-and-codes"></a>Como cada fluxo emite fichas e códigos

Dependendo da forma como o seu cliente é construído, pode utilizar um (ou vários) fluxos de autenticação suportados pela plataforma de identidade da Microsoft. Estes fluxos podem produzir uma variedade de tokens (tokens de identificação, fichas de atualização, fichas de acesso) bem como códigos de autorização, e requerem diferentes fichas para fazê-los funcionar. Este gráfico fornece uma visão geral:

|Fluxo | Requer | Ficha de identificação | ficha de acesso | ficha refrescante | código de autorização |
|-----|----------|----------|--------------|---------------|--------------------|
|[Fluxo de código de autorização](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Fluxo implícito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Fluxo híbrido oIDC](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Refrescar redenção de token](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | ficha refrescante | x | x | x| |
|[Fluxo em-nome-de](v2-oauth2-on-behalf-of-flow.md) | ficha de acesso| x| x| x| |
|[Credenciais de cliente](v2-oauth2-client-creds-grant-flow.md) | | | x (apenas app)| | |

Os tokens emitidos através do modo implícito têm uma limitação de comprimento devido a ser passado de volta para o navegador através do URL (onde `response_mode` está `query` ou `fragment` ).  Alguns navegadores têm um limite no tamanho do URL que pode ser colocado na barra do navegador e falhar quando é muito longo.  Assim, estas fichas não têm `groups` nem `wids` reclamam.

## <a name="next-steps"></a>Passos seguintes

Para outros tópicos que abranjam a autenticação e os fundamentos de autorização:

* Consulte a [Autenticação vs. autorização](authentication-vs-authorization.md) para conhecer os conceitos básicos de autenticação e autorização na plataforma de identidade da Microsoft.
* Consulte o [modelo de Aplicação](application-model.md) para conhecer o processo de registo da sua aplicação para que possa integrar-se na plataforma de identidade da Microsoft.
* Consulte o [fluxo de entrada de apps](app-sign-in-flow.md) para saber sobre o fluxo de entrada de aplicações web, desktop e mobile na plataforma de identidade da Microsoft.
