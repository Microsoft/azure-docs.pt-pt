---
title: Exemplos de código de plataforma de identidade Microsoft | Documentos da Microsoft
description: Fornece exemplos de código de plataforma (ponto final v2.0), organizados por cenário de um índice de identidades da Microsoft disponíveis.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ceae71766c3e51a84cd09e8ac88740353e783bea
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482523"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Exemplos de código de plataforma do Microsoft identity (ponto final v2.0)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Pode usar a plataforma de identidade da Microsoft para:

- Adicionar autenticação e autorização às suas aplicações web e web APIs.
- Exigir um token de acesso para aceder a uma API web protegida.

Este artigo descreve resumidamente e fornece links para amostras para o ponto de extremidade de plataforma de identidade Microsoft. Estes exemplos mostram-lhe como ele é feito e também fornecem trechos de código que pode usar em seus aplicativos. Na página de exemplo de código, encontrará tópicos Leiame detalhadas que o ajudam a com os requisitos de instalação e configuração. Comentários dentro do código de ajudar a compreender as seções críticas.

> [!NOTE]
> Se estiver interessado em v1.0 exemplos, veja [exemplos de código do Azure AD (v1.0 ponto final)](sample-v1-code.md).

Para compreender o cenário básico para cada tipo de exemplo, veja [tipos de aplicações para o ponto de final de plataforma de identidade Microsoft](v2-app-types.md).

Também pode contribuir para os exemplos no GitHub. Para saber como, veja [Microsoft Azure Active Directory amostras e documentação](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplicativos de página única

Estes exemplos mostram como escrever uma aplicação de página única protegida com plataforma de identidade da Microsoft. Estes exemplos utilizam um dos tipos de msal.

| Plataforma | Descrição | Ligação |
| -------- | --------------------- | -------- |
| ![Esta imagem mostra o logótipo de JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chamadas Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Esta imagem mostra o logótipo de JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chama o B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Esta imagem mostra o logótipo de JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | API da web de próprio chamadas |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Esta imagem mostra o logótipo do Angular JS](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Chamadas Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![Esta imagem mostra o logótipo Angular](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Chamadas Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Aplicações Web

As seguintes amostras ilustram aplicativos da web que iniciar sessão dos utilizadores. Alguns exemplos demonstram, também, o aplicativo chamar o Microsoft Graph ou o seu próprio web API com a identidade do utilizador.

| Plataforma | Apenas inicia sessão dos utilizadores | Inicia sessão dos utilizadores e chama o Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Esta imagem mostra o logótipo do ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [Tutorial de utilizadores inicia sessão do ASP.NET Core WebApp](https://aka.ms/aspnetcore-webapp-sign-in) | Mesmo de exemplo no [aplicação Web ASP.NET Core chama Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fase |
| ![Esta imagem mostra o logótipo do ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Início rápido do ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Esta imagem mostra o logótipo de node. js](media/sample-v2-code/logo_nodejs.png)  |                   | [Guia de introdução do node. js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Esta imagem mostra o logótipo do Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Aplicações de dispositivos móveis e computadores cliente público

Os exemplos seguintes mostram cliente público aplicações (aplicações de ambiente de trabalho ou móveis), que acessar a API do Microsoft Graph ou o seu próprio web API em nome de um utilizador. Todos esses aplicativos de cliente utilizam a biblioteca de autenticação da Microsoft (MSAL).

| Aplicação de cliente | Plataforma | Fluxo/concessão | Chamadas Microsoft Graph | Chama uma API web do ASP.NET Core 2.0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Ambiente de trabalho (WPF)      | ![Esta imagem mostra o .NET /C# logótipo](media/sample-v2-code/logo_NET.png) | [interativo](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Ambiente de trabalho (consola)   | ![Esta imagem mostra o .NET /C# logótipo (ambiente de trabalho)](media/sample-v2-code/logo_NET.png) | [Autenticação integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Ambiente de trabalho (consola)   | ![Esta imagem mostra o .NET /C# logótipo (ambiente de trabalho)](media/sample-v2-code/logo_NETcore.png) | [Nome de Utilizador/Palavra-passe](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Móveis (Android, iOS, UWP)   | ![Esta imagem mostra o .NET /C# logótipo (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [interativo](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Móveis (iOS)       | ![Esta imagem mostra iOS/Objective-C ou Swift](media/sample-v2-code/logo_iOS.png) | [interativo](msal-authentication-flows.md#interactive) |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Móveis (Android)   | ![Esta imagem mostra o logótipo do Android](media/sample-v2-code/logo_Android.png) | [interativo](msal-authentication-flows.md#interactive) |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |  |

## <a name="daemon-applications"></a>Aplicações daemon

Os exemplos seguintes mostram uma aplicação que acede o Microsoft Graph API com a sua própria identidade (com nenhum utilizador).

| Aplicação de cliente | Plataforma | Fluxo/concessão | Chamadas Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Consola | ![Esta imagem mostra o logotipo .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Credenciais de cliente](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Aplicação Web | ![Esta imagem mostra o logótipo do ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Credenciais de cliente](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Aplicativos sem periféricos

O exemplo seguinte mostra uma aplicação de cliente pública em execução num dispositivo sem um navegador da web. A aplicação pode ser uma ferramenta de linha de comando, uma aplicação em execução no Linux ou Mac ou um aplicativo de IoT. O exemplo inclui uma aplicação a aceder ao Microsoft Graph API, no nome de um utilizador que inicia sessão interativamente noutro dispositivo (por exemplo, um telemóvel). Esta aplicação de cliente utiliza a biblioteca de autenticação da Microsoft (MSAL).

| Aplicação de cliente | Plataforma | Fluxo/concessão | Chamadas Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Ambiente de trabalho (consola)   | ![Esta imagem mostra o .NET /C# logótipo (ambiente de trabalho)](media/sample-v2-code/logo_NETcore.png) | [Fluxo de código de dispositivo](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>APIs da Web

Os exemplos seguintes mostram como proteger uma API web com o ponto de final de plataforma de identidade Microsoft e como chamar uma API de downstream da web API.

| Plataforma | Exemplo |
| -------- | ------------------- |
| ![Esta imagem mostra o logótipo do ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core API web (serviço) de [dotnet-nativo-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Esta imagem mostra o logótipo do ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Web API (serviço) de [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |

## <a name="other-microsoft-graph-samples"></a>Outros exemplos do Microsoft Graph

Para saber mais sobre [amostras](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) e tutoriais que demonstram os diferentes padrões de uso para a Graph API da Microsoft, incluindo a autenticação com o Azure AD, consulte [exemplos da Comunidade do Microsoft Graph e tutoriais](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Consulte também

- [Guia do programador do Active Directory (v1.0) do Azure](v1-overview.md)
- [Azure AD Graph API conceitual e de referência](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Biblioteca de auxiliar de API do Graph do Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
