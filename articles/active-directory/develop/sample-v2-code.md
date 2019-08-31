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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c62bf4a887bb6d3b24cdd1234a586c61df17df4
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183112"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Exemplos de código da plataforma Microsoft Identity (ponto de extremidade v 2.0)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Você pode usar a plataforma de identidade da Microsoft para:

- Adicione autenticação e autorização para seus aplicativos Web e APIs da Web.
- Exigir um token de acesso para acessar uma API Web protegida.

Este artigo descreve brevemente e fornece links para exemplos do ponto de extremidade da plataforma Microsoft Identity. Esses exemplos mostram como isso é feito e também fornecem trechos de código que você pode usar em seus aplicativos. Na página de exemplo de código, você encontrará tópicos detalhados do Leiame que ajudam com requisitos, instalação e configuração. Os comentários dentro do código ajudam você a entender as seções críticas.

> [!NOTE]
> Se você estiver interessado em exemplos do v 1.0, consulte [exemplos de código do Azure AD (ponto de extremidade v 1.0)](sample-v1-code.md).

Para entender o cenário básico para cada tipo de exemplo, consulte [tipos de aplicativo para o ponto de extremidade da plataforma Microsoft Identity](v2-app-types.md).

Você também pode contribuir com os exemplos no GitHub. Para saber como, consulte [exemplos de Microsoft Azure Active Directory e documentação](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplicativos de página única

Estes exemplos mostram como escrever um aplicativo de página única protegido com a plataforma de identidade da Microsoft. Esses exemplos usam um dos tipos de MSAL. js.

| Plataforma | Descrição | Ligação |
| -------- | --------------------- | -------- |
| ![Esta imagem mostra o logotipo](media/sample-v2-code/logo_js.png) JavaScript [JavaScript (MSAL. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chamadas Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Esta imagem mostra o logotipo](media/sample-v2-code/logo_js.png) JavaScript [JavaScript (MSAL. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chamadas B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Esta imagem mostra o logotipo](media/sample-v2-code/logo_js.png) JavaScript [JavaScript (MSAL. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chama sua própria API Web |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Esta imagem mostra o logotipo](media/sample-v2-code/logo_angular.png) do angular js [JavaScript (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Chamadas Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![Esta imagem mostra o logotipo](media/sample-v2-code/logo_angular.png) angular [JavaScript (MSAL angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Chamadas Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Aplicações Web

Os exemplos a seguir ilustram aplicativos Web que conectam usuários. Alguns exemplos também demonstram o aplicativo que chama Microsoft Graph ou sua própria API Web com a identidade do usuário.

| Plataforma | Somente usuários do | Entra em usuários e chamadas Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Esta imagem mostra o logotipo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | [Tutorial de usuários de entrada do ASP.NET Core WebApp](https://aka.ms/aspnetcore-webapp-sign-in) | O mesmo exemplo no [aplicativo Web ASP.NET Core chama Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fase |
| ![Esta imagem mostra o logotipo do ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Início rápido do ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png)  |                   | [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp): um aplicativo Web MSAL4J que chama o Microsoft Graph |
| ![Esta imagem mostra o logotipo do node. js](media/sample-v2-code/logo_nodejs.png)  |                   | [Início rápido do node. js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Esta imagem mostra o logotipo do Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Aplicativos cliente públicos de desktop e móveis

Os exemplos a seguir mostram aplicativos cliente públicos (aplicativos móveis ou de desktop) que acessam a API de Microsoft Graph ou sua própria API Web no nome de um usuário. Todos esses aplicativos cliente usam a MSAL (biblioteca de autenticação da Microsoft).

| Aplicativo cliente | Plataforma | Fluxo/concessão | Chamadas Microsoft Graph | Chama uma API Web do ASP.NET Core 2,0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Área de trabalho (WPF)      | ![Esta imagem mostra o .NET/C# logotipo](media/sample-v2-code/logo_NET.png) | [interativo](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Área de trabalho (console)   | ![Esta imagem mostra o logotipo .NETC# /(Desktop)](media/sample-v2-code/logo_NET.png) | [Autenticação integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Área de trabalho (console)   | ![Esta imagem mostra o logotipo .NETC# /(Desktop)](media/sample-v2-code/logo_NETcore.png) | [Nome de Utilizador/Palavra-passe](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Móvel (Android, iOS, UWP)   | ![Esta imagem mostra o logotipo doC# .net/(Xamarin)](media/sample-v2-code/logo_xamarin.png) | [interativo](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Móvel (iOS)       | ![Esta imagem mostra o iOS/Objective-C ou Swift](media/sample-v2-code/logo_iOS.png) | [interativo](msal-authentication-flows.md#interactive) |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Móvel (Android)   | ![Esta imagem mostra o logotipo do Android](media/sample-v2-code/logo_Android.png) | [interativo](msal-authentication-flows.md#interactive) |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |  |

## <a name="daemon-applications"></a>Aplicativos daemon

Os exemplos a seguir mostram um aplicativo que acessa a API Microsoft Graph com sua própria identidade (sem usuário).

| Aplicativo cliente | Plataforma | Fluxo/concessão | Chamadas Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Consola | ![Esta imagem mostra o logotipo do .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Credenciais do cliente](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Aplicação Web | ![Esta imagem mostra o logotipo do ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Credenciais do cliente](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Aplicativos sem periféricos

O exemplo a seguir mostra um aplicativo cliente público em execução em um dispositivo sem um navegador da Web. O aplicativo pode ser uma ferramenta de linha de comando, um aplicativo em execução no Linux ou Mac, ou um aplicativo de IoT. O exemplo apresenta um aplicativo que acessa a API Microsoft Graph, no nome de um usuário que entra de forma interativa em outro dispositivo (como um telefone celular). Este aplicativo cliente usa a biblioteca de autenticação da Microsoft (MSAL).

| Aplicativo cliente | Plataforma | Fluxo/concessão | Chamadas Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Área de trabalho (console)   | ![Esta imagem mostra o logotipo .NETC# /(Desktop)](media/sample-v2-code/logo_NETcore.png) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>APIs da Web

Os exemplos a seguir mostram como proteger uma API Web com o ponto de extremidade da plataforma de identidade da Microsoft e como chamar uma API downstream da API da Web.

| Plataforma | Sample |
| -------- | ------------------- |
| ![Esta imagem mostra o logotipo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | API Web do ASP.NET Core (serviço) de [dotnet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Esta imagem mostra o logotipo do ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | API Web (serviço) de [MS-Identity-ASPNET-webAPI-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |

## <a name="other-microsoft-graph-samples"></a>Outros exemplos de Microsoft Graph

Para saber mais sobre [exemplos](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) e tutoriais que demonstram padrões de uso diferentes para a API Microsoft Graph, incluindo a autenticação com o Azure AD, consulte [exemplos da comunidade Microsoft Graph & tutoriais](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Consulte também

- [Guia do desenvolvedor de Azure Active Directory (v 1.0)](v1-overview.md)
- [Referência e conceitual do Azure AD API do Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Biblioteca auxiliar do Azure AD API do Graph](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
