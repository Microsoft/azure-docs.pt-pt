---
title: Exemplos de código para Azure Active Directory v 1.0 | Microsoft Docs
description: Fornece um índice de exemplos de código de Azure Active Directory (ponto de extremidade v 1.0), organizados por cenário.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bc671080a05e2c4e27fabcbab5cc40b9717dc85
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834892"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Exemplos de código de Azure Active Directory (ponto de extremidade v 1.0)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Você pode usar Microsoft Azure Active Directory (AD do Azure) para adicionar autenticação e autorização a seus aplicativos Web e APIs Web.

Esta seção fornece links para exemplos que você pode usar para saber mais sobre o ponto de extremidade v 1.0 do Azure AD. Esses exemplos mostram como isso é feito junto com trechos de código que você pode usar em seus aplicativos. Na página de exemplo de código, você encontrará tópicos detalhados do Read-me que ajudam com requisitos, instalação e configuração. E o código é comentado para ajudá-lo a entender as seções críticas.

> [!NOTE]
> Se você estiver interessado em exemplos de código do Azure AD v2, consulte [exemplos de código v 2.0 por cenário](sample-v2-code.md).

Para entender o cenário básico para cada tipo de exemplo, consulte [cenários de autenticação do Azure ad](authentication-scenarios.md).

Você também pode contribuir com nossos exemplos no GitHub. Para saber como, consulte [exemplos de Microsoft Azure Active Directory e documentação](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplicativos de página única

Este exemplo mostra como gravar um aplicativo de página única protegido com o Azure AD.

 Plataforma | Chama sua própria API | Chama outra API Web
 -------- |  --------------------- | ------------------ 
![Esta imagem mostra o logotipo do JavaScript](media/sample-v2-code/logo_js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Esta imagem mostra o logotipo do JS do angular](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Aplicações Web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Aplicativos Web entrando em usuários, chamando Microsoft Graph ou uma API Web com a identidade do usuário

Os exemplos a seguir ilustram usuários de assinatura de aplicativos Web. Alguns desses aplicativos também chamam o Microsoft Graph ou sua própria API da Web, no nome do usuário conectado.

 Plataforma | Somente usuários do | Chama Microsoft Graph ou AAD Graph| Chama outra API Web ASP.NET ou ASP.NET Core 2,0
 -------- | ------------------- | --------------------- | -------------------------
![Esta imagem mostra o logotipo do ASP.NET](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![Esta imagem mostra o logotipo do ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [webApp-openidconnect-dotnet](quickstart-v1-aspnet-webapp.md) </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD Graph) |
![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Esta imagem mostra o log do Java](media/sample-v2-code/logo_java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Esta imagem mostra o logotipo do PHP](media/sample-v2-code/logo_php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Aplicativos Web que demonstram o controle de acesso baseado em função (autorização)

Os exemplos a seguir mostram como implementar o RBAC (controle de acesso baseado em função). O RBAC é usado para restringir as permissões de determinados recursos em um aplicativo Web para determinados usuários. Os usuários são autorizados dependendo se eles pertencem a um **grupo do Azure ad** ou têm uma determinada **função**de aplicativo.

Plataforma | Exemplo |
 -------- | ------------------- |
![Esta imagem mostra o logotipo do ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Um aplicativo Web .NET 4,5 MVC que usa **funções** do Azure ad para autorização

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Aplicativos cliente públicos de desktop e móveis que chamam Microsoft Graph ou uma API da Web

Os exemplos a seguir ilustram aplicativos cliente públicos (aplicativos deskto/pmobile) que acessam o Microsoft Graph ou uma API da Web no nome de um usuário. Dependendo dos dispositivos e das plataformas, os aplicativos podem conectar usuários de diferentes maneiras (fluxos/concessões):

- Interativamente
- Silenciosamente (com a autenticação integrada do Windows no Windows ou nome de usuário/senha)
- Delegando a entrada interativa a outro dispositivo (fluxo de código do dispositivo usado em dispositivos que não fornecem controles da Web)

Aplicativo cliente | Plataforma | Fluxo/concessão | Chamadas Microsoft Graph | Chama uma API Web ASP.NET ou ASP.NET Core 2. x
------------------ | -------- | ---------- | -------------------- | -------------------------
Área de trabalho (WPF)           | ![Esta imagem mostra o .NET/C# logotipo](media/sample-v2-code/logo_NET.png)  | Interativo | Parte de [dotnet-nativa-](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) multidirecionamento | [Dotnet-Native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Móvel (UWP)            | .![Esta imagem mostra o .NET/C#/UWP](media/sample-v2-code/logo_Windows.png)   | Interativo | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Este exemplo usa [WAM](/windows/uwp/security/web-account-manager), não [Adal.net](https://aka.ms/adalnet)|  [dotnet-Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (Aplicativo UWP usando ADAL.NET para chamar uma API Web de locatário único) </p> [dotnet-webAPI-multilocatário-Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (Aplicativo UWP usando ADAL.NET para chamar uma API Web multilocatário)|
Móvel (Android, iOS, UWP)   | ![Esta imagem mostra o .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interativo | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Móvel (Android)           | ![Esta imagem mostra o logotipo do Android](media/sample-v2-code/logo_Android.png) | Interativo |   [android](https://github.com/Azure-Samples/active-directory-android) |
Móvel (iOS)           | ![Esta imagem mostra o iOS/objetivo C ou Swift](media/sample-v2-code/logo_iOS.png) | Interativo |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Área de trabalho (console)          | ![Esta imagem mostra o .NET/C# logotipo](media/sample-v2-code/logo_NET.png) | Nome de usuário/senha </p>  Autenticação Integrada do Windows | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Área de trabalho (console)          | ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_Java.png) | Nome de usuário/senha | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Área de trabalho (console)           | ![Esta imagem mostra o .NET Core/C# logo](media/sample-v2-code/logo_NETcore.png) | Fluxo de código do dispositivo | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Aplicativos de daemon (acessando APIs Web com a identidade do aplicativo)

Os exemplos a seguir mostram aplicativos da Web ou de área de trabalho que acessam o Microsoft Graph ou uma API da Web sem usuário (com a identidade do aplicativo).

Aplicativo cliente | Plataforma | Fluxo/concessão | Chama uma API Web ASP.NET ou ASP.NET Core 2,0
------------------ | -------- | ---------- | -------------------- 
Aplicativo daemon (console)          | ![Esta imagem mostra o logotipo do .NET](media/sample-v2-code/logo_NETframework.png) | Credenciais do cliente com o segredo do aplicativo ou certificado | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Aplicativo daemon (console)         | ![Esta imagem mostra o logotipo do .NET](media/sample-v2-code/logo_NETcore.png) | Credenciais do cliente com certificado| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Aplicativo Web ASP.NET  | ![Esta imagem mostra o logotipo do .NET](media/sample-v2-code/logo_NETframework.png) | Credenciais de cliente | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>APIs da Web

### <a name="web-api-protected-by-azure-active-directory"></a>API Web protegida por Azure Active Directory

O exemplo a seguir mostra como proteger uma API Web node. js com o Azure AD.

Nas seções anteriores deste artigo, você também pode encontrar outros exemplos que ilustram um aplicativo cliente que **chama** uma **API Web**ASP.net ou ASP.NET Core. Esses exemplos não são mencionados novamente nesta seção, mas você os encontrará na última coluna das tabelas acima ou abaixo

| Plataforma | Exemplo |
|--------|-------------------|
| ![Esta imagem mostra o logotipo do node. js](media/sample-v2-code/logo_nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>API Web chamando Microsoft Graph ou outra API Web

Os exemplos a seguir demonstram uma API Web que chama outra API da Web. O segundo exemplo mostra como lidar com o acesso condicional.

| Plataforma |  Chamadas Microsoft Graph | Chama outra API Web ASP.NET ou ASP.NET Core 2,0 |
| -------- |  --------------------- | ------------------------- |
| ![Esta imagem mostra o logotipo do ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Outros exemplos de Microsoft Graph

Para obter exemplos e tutoriais que demonstram padrões de uso diferentes para a API Microsoft Graph, incluindo a autenticação com o Azure AD, consulte [exemplos da comunidade Microsoft Graph & tutoriais](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Consulte também

[Guia do desenvolvedor de Azure Active Directory](v1-overview.md)

[Azure Active Directory bibliotecas de autenticação](active-directory-authentication-libraries.md)

[Referência e conceitual do Azure AD API do Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteca auxiliar do Azure AD API do Graph](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
