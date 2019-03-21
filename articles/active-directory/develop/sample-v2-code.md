---
title: Exemplos de código do Azure Active Directory | Documentos da Microsoft
description: Fornece exemplos de código, organizados por cenário de um índice de disponíveis do Azure Active Directory (ponto final V2).
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b518235efa4cd236afd8f79cb70cf30696931fdb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086773"
---
# <a name="azure-active-directory-code-samples-v20-endpoint"></a>Exemplos de código do Azure Active Directory (ponto final v2.0)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Pode usar a plataforma de identidade da Microsoft para:

- Adicionar autenticação e autorização às suas aplicações web e web APIs.
- Exigir um token de acesso para aceder a uma API web protegida.

Este artigo descreve resumidamente e fornece links para amostras para o ponto de final de v2.0 do Azure AD. Estes exemplos mostram-lhe como isso é feito, juntamente com os trechos de código que pode usar em seus aplicativos. Na página de exemplo de código, encontrará Leiame detalhadas tópicos de ajudam com requisitos de instalação e configurar. Comentários dentro do código estão lá ajudar a compreender as seções críticas.

> [!NOTE]
> Se estiver interessado em v1.0 exemplos, veja [exemplos de código do Azure AD (v1.0 ponto final)](sample-v1-code.md).

Para compreender o cenário básico para cada tipo de exemplo, veja [tipos de aplicações para o ponto de final de v2.0 do Azure Active Directory](v2-app-types.md).

Também pode contribuir para os exemplos no GitHub. Para saber como, veja [Microsoft Azure Active Directory amostras e documentação](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications-spa"></a>Aplicativos de página única (SPA)

Estes exemplos mostram como escrever uma aplicação de página única protegida com o Azure AD. Estes exemplos utilizam um dos tipos de msal:

* [Biblioteca de autenticação da Microsoft para JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)
* [Biblioteca de autenticação da Microsoft para o Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)
* [Biblioteca de autenticação da Microsoft para o AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)

  Plataforma |  Chamadas Microsoft Graph
  -------- |  ---------------------
  ![JavaScript](media/sample-v2-code/logo_js.png) JavaScript (msal.js)  | [javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2)
  ![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (MSAL AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
  ![Angular](media/sample-v2-code/logo_angular.png) JavaScript (MSAL Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>Aplicações Web

As seguintes amostras ilustram aplicativos da web que iniciar sessão dos utilizadores. Alguns exemplos demonstram, também, o aplicativo chamar o Microsoft Graph ou o seu próprio web API com a identidade do utilizador.

 Plataforma | Apenas inicia sessão dos utilizadores | Inicia sessão dos utilizadores e chama o Microsoft Graph
 -------- | ------------------- | ---------------------------------
![Núcleo de ASP.NET](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.1 | [Tutorial de utilizadores inicia sessão do ASP.NET Core WebApp](https://aka.ms/aspnetcore-webapp-sign-in) | Mesmo de exemplo no [aplicação Web ASP.NET Core chama Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fase
![ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Início rápido do ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p>[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [Guia de introdução do node. js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp)

## <a name="desktop-and-mobile-public-client-apps"></a>Aplicações de dispositivos móveis e computadores cliente público

Os exemplos seguintes mostram cliente público aplicações (aplicações de ambiente de trabalho/móvel) que acedam a API do Microsoft Graph ou o seu próprio Web API em nome de um utilizador. Todos esses aplicativos de cliente utilizam bibliotecas de autenticação da Microsoft (MSAL).

Aplicação de cliente | Plataforma | Fluxo/concessão | Chamadas Microsoft Graph | Chama uma API Web ASP.NET Core 2.0
------------------ | -------- |  ----------| ---------- | -------------------------
Ambiente de trabalho (WPF)      | ![.NET/C#](media/sample-v2-code/logo_NET.png) | Interativo | [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://github.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Ambiente de trabalho (consola)   | ![.NET / c# (Desktop)](media/sample-v2-code/logo_NET.png) | Autenticação Integrada do Windows |[dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2)
Ambiente de trabalho (consola)   | ![.NET / c# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Nome de utilizador/palavra-passe |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)
Mobile (UWP)   | ![.NET/C# (UWP)](media/sample-v2-code/logo_windows.png) | Interativo |[dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Móveis (Android, iOS, UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interativo |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |
Móveis (iOS)       | ![iOS / Objective C ou swift](media/sample-v2-code/logo_iOS.png) | Interativo |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Móveis (Android)   | ![Android / Java](media/sample-v2-code/logo_Android.png) | Interativo |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>Aplicações daemon

Os exemplos seguintes mostram uma aplicação que acede o Microsoft Graph API com a sua própria identidade (com nenhum utilizador).

Aplicação de cliente | Plataforma | Fluxo/concessão | Chamadas Microsoft Graph
------------------ | -------- | ---------- | --------------------
Consola | ![.NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | Credenciais de cliente | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)
Aplicação Web | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | Credenciais de cliente | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

## <a name="headless-applications"></a>Aplicativos sem periféricos

O exemplo seguinte mostra uma aplicação de cliente pública em execução num dispositivo sem um navegador da web. A aplicação pode ser uma ferramenta de linha de comandos, ou em execução no Linux/Mac ou um aplicativo de IoT. O exemplo inclui uma aplicação a aceder a Graph API da Microsoft em nome de um utilizador que inicia sessão interativamente noutro dispositivo (por exemplo, um telemóvel). Esse aplicativo de cliente usa as bibliotecas de autenticação da MicroSoft (MSAL).

Aplicação de cliente | Plataforma | Fluxo/concessão | Chamadas Microsoft Graph
------------------ | -------- |  ----------| ----------
Ambiente de trabalho (consola)   | ![.NET / c# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Fluxo de código do dispositivo |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2)

## <a name="web-apis"></a>APIs da Web

O exemplo a seguir mostra como proteger uma API web com o ponto de final de v2.0 do Azure AD. Esta API é exercida por um aplicativo do WPF, mas ele pode ser chamado por qualquer aplicação.

Plataforma | Sample
 -------- | -------------------
![.NET/C#](media/sample-v2-code/logo_NET.png) | WebAPI (serviço) de [dotnet-nativo-aspnetcore-v2](https://github.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)

## <a name="other-microsoft-graph-samples"></a>Outros exemplos do Microsoft Graph

Para saber mais sobre [amostras](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) e tutoriais que demonstram os diferentes padrões de uso para a Graph API da Microsoft, incluindo a autenticação com o Azure AD, consulte [exemplos da Comunidade do Microsoft Graph e tutoriais](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Consulte também

[Guia para programadores do Azure Active Directory](v1-overview.md)

[Azure AD Graph API conceitual e de referência](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteca de auxiliar de API do Graph do Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
