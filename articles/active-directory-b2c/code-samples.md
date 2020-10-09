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
ms.openlocfilehash: 63a79df8e540e88d0f1e3f4c5b8d339ff03c877f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91854295"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Exemplos de código do Azure Active Directory B2C

As tabelas seguintes fornecem ligações para exemplos de aplicações, incluindo iOS, Android, .NET e Node.js.

## <a name="mobile-and-desktop-apps"></a>Aplicações móveis e de ambiente de trabalho

| Sample | Descrição |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Um exemplo de iOS no Swift que autentica os utilizadores do Azure AD B2C e chama uma API com o OAuth 2.0 |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | Uma aplicação Android simples que mostra como utilizar a MSAL para autenticar os utilizadores através do Azure Active Directory B2C e aceder a uma API Web com os tokens resultantes. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Um exemplo que mostra como pode utilizar uma biblioteca de terceiros para criar uma aplicação iOS no Objective-C que autentique os utilizadores de identidade Microsoft no nosso serviço de identidade do Azure AD B2C. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | Um exemplo que mostra como pode utilizar uma biblioteca de terceiros para criar uma aplicação Android que autentique os utilizadores de identidade Microsoft no nosso serviço de identidade B2C e chame uma API Web com os tokens de acesso do OAuth 2.0. |
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
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Uma aplicação de página única (SPA) que chama uma API Web. A autenticação é feita com Azure AD B2C utilizando MSAL.js. |

## <a name="saml-test-application"></a>Aplicação de teste SAML

| Sample | Descrição |
|--------| ----------- |
| [saml-sp-tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | Aplicação de teste SAML para testar Azure AD B2C configurado para funcionar como provedor de identidade SAML. |

## <a name="api-connectors"></a>Conectores de API

As tabelas que se seguem fornecem links para amostras de código para alavancar as APIs web nos fluxos do seu utilizador utilizando [conectores API](api-connectors-overview.md).

## <a name="api-connector-azure-function-quickstarts"></a>Inícios rápidos de função api azure

| Sample                                                                                                                          | Descrição                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Esta amostra de Função De Azure Core .NET demonstra como limitar as inscrições a domínios de e-mail específicos e validar informações fornecidas pelo utilizador. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Esta Node.js amostra Azure Function demonstra como limitar as inscrições a domínios de e-mail específicos e validar informações fornecidas pelo utilizador.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Esta amostra python Azure Function demonstra como limitar as inscrições a domínios de e-mail específicos e validar informações fornecidas pelo utilizador.    |