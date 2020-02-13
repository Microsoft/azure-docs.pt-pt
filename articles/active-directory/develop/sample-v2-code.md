---
title: Exemplos de código para a plataforma Microsoft Identity | Microsoft Docs
description: Fornece um índice de exemplos de código disponíveis da plataforma de identidade da Microsoft (ponto de extremidade v 2.0), organizado por cenário.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0282336cb5df7d010571a15e9d1f9d93b82d461e
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160327"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Exemplos de código da plataforma Microsoft Identity (ponto de extremidade v 2.0)

Você pode usar a plataforma de identidade da Microsoft para:

- Adicione autenticação e autorização para seus aplicativos Web e APIs da Web.
- Exigir um token de acesso para acessar uma API Web protegida.

Este artigo descreve brevemente e fornece links para exemplos do ponto de extremidade da plataforma Microsoft Identity. Esses exemplos mostram como isso é feito e também fornecem trechos de código que você pode usar em seus aplicativos. Na página de exemplo de código, você encontrará tópicos detalhados do Leiame que ajudam com requisitos, instalação e configuração. Os comentários dentro do código ajudam você a entender as seções críticas.

> [!NOTE]
> Se estiver interessado em amostras v1.0, consulte amostras de [código Azure AD (v1.0 endpoint)](../azuread-dev/sample-v1-code.md).

Para compreender o cenário básico de cada tipo de amostra, consulte os tipos de [aplicativos para o ponto final da plataforma de identidade da Microsoft](v2-app-types.md).

Você também pode contribuir com os exemplos no GitHub. Para saber como, consulte [amostras e documentação do Microsoft Azure Ative Directory.](https://github.com/Azure-Samples?page=3&query=active-directory)

## <a name="single-page-applications"></a>Aplicativos de página única

Estes exemplos mostram como escrever um aplicativo de página única protegido com a plataforma de identidade da Microsoft. Esses exemplos usam um dos tipos de MSAL. js.

| Plataforma | Descrição | Ligação |
| -------- | --------------------- | -------- |
| ![Esta imagem mostra o logótipo do JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chamadas Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Esta imagem mostra o logótipo do JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chamadas B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Esta imagem mostra o logótipo do JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chama sua própria API Web |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Esta imagem mostra o logótipo Angular JS](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Chamadas Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MsalAngularjsDemoApp)
| ![Esta imagem mostra o logótipo angular](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Chamadas Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Aplicações Web

Os exemplos a seguir ilustram aplicativos Web que conectam usuários. Alguns exemplos também demonstram o aplicativo que chama Microsoft Graph ou sua própria API Web com a identidade do usuário.

| Plataforma | Somente usuários do | Entra em usuários e chamadas Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Esta imagem mostra o logotipo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | [tutor itutorial de utilizadores de sinais do Core WebApp ASP.NET](https://aka.ms/aspnetcore-webapp-sign-in) | Mesma amostra na [ASP.NET Core Web App chama a](https://aka.ms/aspnetcore-webapp-call-msgraph) fase do Microsoft Graph |
| ![Esta imagem mostra o logotipo do ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET Quickstart](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restrito-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Esta imagem mostra o logotipo do node. js](media/sample-v2-code/logo_nodejs.png)  |                   | [Node.js Quickstart](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Esta imagem mostra o logotipo do Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Aplicativos cliente públicos de desktop e móveis

Os exemplos a seguir mostram aplicativos cliente públicos (aplicativos móveis ou de desktop) que acessam a API de Microsoft Graph ou sua própria API Web no nome de um usuário. Todos esses aplicativos cliente usam a MSAL (biblioteca de autenticação da Microsoft).

| Aplicação do cliente | Plataforma | Fluxo/concessão | Chamadas Microsoft Graph | Chama uma API Web do ASP.NET Core 2,0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Área de trabalho (WPF)      | ![Esta imagem mostra o .NET/C# logotipo](media/sample-v2-code/logo_NET.png) | [interativo](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-nativo-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Área de trabalho (console)   | ![Esta imagem mostra o logotipo .NETC# /(Desktop)](media/sample-v2-code/logo_NET.png) | [Autenticação Integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Área de trabalho (console)   | ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png) | [Autenticação Integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Área de trabalho (console)   | ![Esta imagem mostra o logotipo .NETC# /(Desktop)](media/sample-v2-code/logo_NETcore.png) | [Nome de Utilizador/Palavra-passe](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Ambiente de trabalho (Consola) com WAM  | ![Esta imagem mostra o logotipo .NETC# /(Desktop)](media/sample-v2-code/logo_NETcore.png) | [interativo com a WAM](msal-authentication-flows.md#interactive) |[dotnet-nativo-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Área de trabalho (console)   | ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png) | [Nome de Utilizador/Palavra-passe](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Área de trabalho (console)   | ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png) | [Nome de Utilizador/Palavra-passe](msal-authentication-flows.md#usernamepassword) |[ms-identidade-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Móvel (Android, iOS, UWP)   | ![Esta imagem mostra o logotipo doC# .net/(Xamarin)](media/sample-v2-code/logo_xamarin.png) | [interativo](msal-authentication-flows.md#interactive) |[xamarin-nativo-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Móvel (iOS)       | ![Esta imagem mostra o iOS/Objective-C ou Swift](media/sample-v2-code/logo_iOS.png) | [interativo](msal-authentication-flows.md#interactive) |[ios-swift-objc-nativo-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-nativo-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Área de trabalho (macOS)       | macOS | [interativo](msal-authentication-flows.md#interactive) |[macOS-swift-objc-nativo-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Móvel (Android-Java)   | ![Esta imagem mostra o logotipo do Android](media/sample-v2-code/logo_Android.png) | [interativo](msal-authentication-flows.md#interactive) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Móvel (Android-Kotlin)   | ![Esta imagem mostra o logotipo do Android](media/sample-v2-code/logo_Android.png) | [interativo](msal-authentication-flows.md#interactive) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Aplicativos daemon

Os exemplos a seguir mostram um aplicativo que acessa a API Microsoft Graph com sua própria identidade (sem usuário).

| Aplicação do cliente | Plataforma | Fluxo/concessão | Chamadas Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Consola | ![Esta imagem mostra o logotipo do .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Credenciais de cliente](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Aplicação Web | ![Esta imagem mostra o logotipo do ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Credenciais de cliente](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Consola | ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png) | [Credenciais de cliente](msal-authentication-flows.md#client-credentials) | [ms-identidade-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Consola | ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png) | [Credenciais de cliente](msal-authentication-flows.md#client-credentials) | [ms-identidade-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Aplicativos sem periféricos

O exemplo a seguir mostra um aplicativo cliente público em execução em um dispositivo sem um navegador da Web. O aplicativo pode ser uma ferramenta de linha de comando, um aplicativo em execução no Linux ou Mac, ou um aplicativo de IoT. O exemplo apresenta um aplicativo que acessa a API Microsoft Graph, no nome de um usuário que entra de forma interativa em outro dispositivo (como um telefone celular). Este aplicativo cliente usa a biblioteca de autenticação da Microsoft (MSAL).

| Aplicação do cliente | Plataforma | Fluxo/concessão | Chamadas Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Área de trabalho (console)   | ![Esta imagem mostra o logotipo .NETC# /(Desktop)](media/sample-v2-code/logo_NETcore.png) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code) |[dotnetcore-dispositivocodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Área de trabalho (console)   | ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Área de trabalho (console)   | ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>APIs da Web

Os exemplos a seguir mostram como proteger uma API Web com o ponto de extremidade da plataforma de identidade da Microsoft e como chamar uma API downstream da API da Web.

| Plataforma | Sample |
| -------- | ------------------- |
| ![Esta imagem mostra o logotipo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | ASP.NET Core web API (serviço) de [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Esta imagem mostra o logotipo do ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Web API (serviço) de [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png) | Web API (serviço) de [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions como APIs Web

Os exemplos a seguir mostram como proteger uma função do Azure usando HttpTrigger e expondo uma API Web com o ponto de extremidade da plataforma de identidade da Microsoft e como chamar uma API downstream da API da Web.

| Plataforma | Sample |
| -------- | ------------------- |
| ![Esta imagem mostra o logotipo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | ASP.NET Core web API (serviço) Função Azure de [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Esta imagem mostra o logotipo do node. js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Web API (serviço) de [NodeJS e passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png)</p>Python | Web API (serviço) da [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Esta imagem mostra o logotipo do node. js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Web API (serviço) de [NodeJS e passport-azure-ad usando em nome de](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Outros exemplos de Microsoft Graph

Para conhecer [amostras](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) e tutoriais que demonstrem diferentes padrões de utilização para a Microsoft Graph API, incluindo a autenticação com a AD Azure, consulte [amostras e tutoriais](https://github.com/microsoftgraph/msgraph-community-samples)microsoft Graph Community .

## <a name="see-also"></a>Veja também

- [Guia de desenvolvedor estoque ativo azure (v1.0)](../azuread-dev/v1-overview.md)
- [Conceito e referência do gráfico azure AD API](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Biblioteca de Ajudante api gráfico azure API](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
