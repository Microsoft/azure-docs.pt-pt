---
title: Exemplos de código do Azure Active Directory B2C | Microsoft Docs
description: Exemplos de código para aplicações móveis, de ambiente de trabalho, Web e de página única do Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a75896dde7a23ea346e0af73ed145094e78812ca
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654498"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Exemplos de código do Azure Active Directory B2C

As tabelas seguintes fornecem ligações para exemplos de aplicações, incluindo iOS, Android, .NET e Node.js.

## <a name="mobile-and-desktop-apps"></a>Aplicações móveis e de ambiente de trabalho

| Sample | Descrição |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Um exemplo de iOS no Swift que autentica os utilizadores do Azure AD B2C e chama uma API com o OAuth 2.0 |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | Uma aplicação Android simples que mostra como utilizar a MSAL para autenticar os utilizadores através do Azure Active Directory B2C e aceder a uma API Web com os tokens resultantes. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Uma amostra que mostra como pode usar uma biblioteca de terceiros para construir uma aplicação iOS no Objective-C que autentica os utilizadores de identidade da Microsoft no nosso serviço de identidade Azure AD B2C. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | Uma amostra que mostra como pode usar uma biblioteca de terceiros para construir uma aplicação Android que autentica os utilizadores de identidade da Microsoft no nosso serviço de identidade B2C e chama uma API web usando fichas de acesso OAuth 2.0. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Um exemplo que mostra como uma aplicação .NET do Ambiente de Trabalho do Windows (WPF) pode iniciar a sessão de um utilizador com o Azure AD B2C, obter um token de acesso com MSAL.NET e chamar uma API. |
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Uma aplicação Xamarin Forms simples que mostra como utilizar a MSAL para autenticar os utilizadores através do Azure Active Directory B2C e aceder a uma API Web com os tokens resultantes. |

## <a name="web-apps-and-apis"></a>APIs e aplicações Web

| Sample | Descrição |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | Um exemplo combinado para uma aplicação Web .NET que chama uma API Web .NET, ambas protegidas através do Azure AD B2C. |
| [dotnetcore-webapp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | Uma aplicação web core ASP.NET que utiliza o OpenID Connect para iniciar sação nos utilizadores em Azure AD B2C. |
| [dotnetcore-webapp-msal-api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | Uma aplicação Web ASP.NET Core que pode iniciar a sessão de um utilizador com o Azure AD B2C, obter um token de acesso com MSAL.NET e chamar uma API. |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | Uma aplicação Node.js que permite configurar rápida e facilmente uma aplicação Web com o Express através do OpenID Connect. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | Um pequena API Web Node.js para o Azure AD B2C que mostra como proteger a sua API Web e aceitar tokens de acesso B2C através do Passport.js. |
| [ms-identidade-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | Demonstrar como integrar b2C da plataforma de identidade microsoft com uma aplicação web Python.  |

## <a name="single-page-apps"></a>Aplicações de página única

| Sample | Descrição |
|--------| ----------- |
| [ms-identidade-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) | Uma aplicação de uma página única (SPA) chamando uma API web. A autenticação é feita com Azure AD B2C utilizando MSAL.js. Esta amostra utiliza o fluxo de código de autorização com O PKCE. |
| [javascript-nodejs-management](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter1) | Uma aplicação de uma única página (SPA) chamando o Microsoft Graph para gerir os utilizadores num diretório B2C. A autenticação é feita com Azure AD B2C utilizando MSAL.js. Esta amostra utiliza o fluxo de código de autorização com O PKCE.|
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Uma aplicação de uma página única (SPA) chamando uma API web. A autenticação é feita com Azure AD B2C utilizando MSAL.js. Esta amostra usa o fluxo implícito.|

## <a name="consoledaemon-apps"></a>Aplicativos consola/Daemon

| Sample | Descrição |
|--------| ----------- |
| [javascript-nodejs-management](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter2) | Uma aplicação Node.js e express de consola daemon chamando o Microsoft Graph com a sua própria identidade para gerir os utilizadores num diretório B2C. A autenticação é feita com Azure AD B2C utilizando o nó MSAL. Esta amostra utiliza o fluxo de código de autorização.|
| [dotnetcore-b2c-conta-management](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) | Uma aplicação de consola .NET Core chamando o Microsoft Graph com a sua própria identidade para gerir os utilizadores num diretório B2C. A autenticação é feita com Azure AD B2C utilizando MSAL.NET. Esta amostra utiliza o fluxo de código de autorização.|

## <a name="saml-test-application"></a>Aplicação de teste SAML

| Sample | Descrição |
|--------| ----------- |
| [saml-sp-tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | Aplicação de teste SAML para testar Azure AD B2C configurado para funcionar como provedor de identidade SAML. |

## <a name="api-connectors"></a>Conectores de API

As tabelas que se seguem fornecem links para amostras de código para alavancar as APIs web nos fluxos do seu utilizador utilizando [conectores API](api-connectors-overview.md).

### <a name="azure-function-quickstarts"></a>Arranques rápidos da função Azure

| Sample                                                                                                                          | Descrição                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Esta amostra de Função De Azure Core .NET demonstra como limitar as inscrições a domínios de e-mail específicos e validar informações fornecidas pelo utilizador. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Esta Node.js amostra Azure Function demonstra como limitar as inscrições a domínios de e-mail específicos e validar informações fornecidas pelo utilizador.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Esta amostra python Azure Function demonstra como limitar as inscrições a domínios de e-mail específicos e validar informações fornecidas pelo utilizador.    |


### <a name="automated-fraud-protection-services--captcha"></a>Serviços automatizados de proteção contra fraudes & CAPTCHA
| Sample                                                                                                            | Descrição                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Arkose Labs fraude e proteção de abuso](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | Esta amostra mostra como proteger as suas inscrições de utilizador usando o serviço de fraude e proteção de abusos da Arkose Labs. |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | Esta amostra mostra como proteger as inscrições do utilizador utilizando um desafio reCAPTCHA para evitar abusos automatizados. |


### <a name="identity-verification"></a>Verificação de identidade

| Sample                                                                                                            | Descrição                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | Esta amostra mostra como verificar a identidade do utilizador como parte dos fluxos de inscrição utilizando um conector API para integrar com iDologia. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | Esta amostra mostra como verificar a identidade do utilizador como parte dos fluxos de inscrição utilizando um conector API para integrar com a Experian. |


### <a name="other"></a>Outro

| Sample                                                                                                            | Descrição                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Código de convite](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | Esta amostra demonstra como limitar a inscrição em públicos específicos utilizando códigos de convite.|
| [Amostras comunitárias de conector API](https://github.com/azure-ad-b2c/api-connector-samples) | Este repositório manteve amostras de cenários ativadas por conectores API.|
