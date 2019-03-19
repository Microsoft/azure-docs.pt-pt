---
title: Exemplos de código do Azure Active Directory | Documentos da Microsoft
description: Fornece exemplos de código, organizados por cenário de um índice do Azure Active Directory (v1.0 ponto final).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c199df2012e6ce3b3de5f34989cf5cbc135fdc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101028"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Exemplos de código do Azure Active Directory (v1.0 ponto final)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Pode utilizar o Microsoft Azure Active Directory (Azure AD) para adicionar a autenticação e autorização para seus aplicativos web e web APIs.

Esta secção fornece ligações para exemplos que pode utilizar para saber mais sobre o ponto de extremidade de versão 1.0 do Azure AD. Estes exemplos mostram-lhe como é feito, juntamente com os trechos de código que pode usar em seus aplicativos. Na página de exemplo de código, encontrará leitura detalhada-me tópicos de ajudam com os requisitos de instalação e configuração. E o código é comentado para ajudar a compreender as seções críticas.

> [!NOTE]
> Se estiver interessado em exemplos de código do Azure AD V2, veja [exemplos de código v2.0 por cenário](sample-v2-code.md).

Para compreender o cenário básico para cada tipo de exemplo, veja [cenários de autenticação do Azure AD](authentication-scenarios.md).

Também pode contribuir com nossos exemplos no GitHub. Para saber como, veja [Microsoft Azure Active Directory amostras e documentação](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplicativos de página única

Este exemplo mostra como escrever uma aplicação de página única protegida com o Azure AD.

 Plataforma | Chama a sua própria API | Chama outra API Web
 -------- |  --------------------- | ------------------ 
![JavaScript](media/sample-v2-code/logo_js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Angular JS](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Aplicações Web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Aplicativos Web a iniciar sessão dos utilizadores, chamando o Microsoft Graph ou uma API Web com a identidade do utilizador

As seguintes amostras ilustram aplicativos Web, os utilizadores de assinatura. Alguns desses aplicativos também chamam o Microsoft Graph ou o seu próprio Web API, no nome do utilizador com sessão iniciada.

 Plataforma | Apenas inicia sessão dos utilizadores | Chamadas Microsoft Graph ou Graph do AAD| Chama outro ASP.NET ou API Web do ASP.NET Core 2.0
 -------- | ------------------- | --------------------- | -------------------------
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) <p/>(Graph do AAD) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) <p/> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) <p/> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)<p/> (Graph do AAD) |
![Python](media/sample-v2-code/logo_python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Java](media/sample-v2-code/logo_java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![PHP](media/sample-v2-code/logo_php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Aplicativos Web demonstrar o controlo de acesso baseado em funções (autorização)

Os exemplos seguintes mostram como implementar o controlo de acesso baseado em funções (RBAC). RBAC é utilizado para restringir as permissões de determinadas funcionalidades numa aplicação web a determinados utilizadores. Os utilizadores estão autorizados dependendo se pertencem a um **grupo do Azure AD** ou tem um determinado aplicativo **função**.

Plataforma | Sample |
 -------- | ------------------- |
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) <p/>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Uma aplicação web de MVC do .NET 4.5 que utiliza o Azure AD **funções** para autorização

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Aplicações de dispositivos móveis e computadores cliente pública chamada Microsoft Graph ou uma API Web

As seguintes amostras ilustram aplicativos de cliente público (aplicações de ambiente de trabalho/móvel) que acedem à Microsoft Graph ou uma API Web no nome de um utilizador. Consoante os dispositivos e plataformas, aplicativos podem iniciar sessão no utilizadores de formas diferentes (fluxos/concessões): 

- interativamente,
- silenciosamente (com a autenticação integrada do Windows no Windows ou o nome de utilizador/palavra-passe), 
- ou até mesmo delegando o interativo início de sessão para outro dispositivo (fluxo de código de dispositivo utilizado nos dispositivos que não oferecem controles da web).

Aplicação de cliente | Plataforma | Fluxo/concessão | Chamadas Microsoft Graph | Chama um ASP.NET ou ASP.NET Core 2.x API Web
------------------ | -------- | ---------- | -------------------- | -------------------------
Ambiente de trabalho (WPF)           | ![.NET/C#](media/sample-v2-code/logo_NET.png)  | Interativo | Parte da [nativo-dotnet-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobile (UWP)            | .![.NET/C#/UWP](media/sample-v2-code/logo_Windows.png)   | Interativo | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Este exemplo utiliza [WAM](https://docs.microsoft.com/windows/uwp/security/web-account-manager), e não [ADAL.NET](https://aka.ms/adalnet)|  [DotNet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (aplicação de UWP com ADAL.NET para chamar um Web API de inquilino único) </p> [DotNet-webapi-multi-inquilino-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (aplicação de UWP com ADAL.NET para chamar um Web API de multi-inquilino)|
Móveis (Android, iOS, UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interativo | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Móveis (Android)           | ![Android / Java](media/sample-v2-code/logo_Android.png) | Interativo |   [android](https://github.com/Azure-Samples/active-directory-android) |
Móveis (iOS)           | ![iOS / Objective C ou swift](media/sample-v2-code/logo_iOS.png) | Interativo |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Ambiente de trabalho (consola)          | ![.NET/C#](media/sample-v2-code/logo_NET.png) | Nome de utilizador / palavra-passe </p>  Autenticação Integrada do Windows | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Ambiente de trabalho (consola)          | ![Consola de Java](media/sample-v2-code/logo_Java.png) | Nome de utilizador / palavra-passe | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Ambiente de trabalho (consola)           | ![.NET core / c#](media/sample-v2-code/logo_NETcore.png) | Fluxo de código do dispositivo | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Aplicações daemon (aceder a APIs da Web com a identidade da aplicação)

Os exemplos seguintes mostram aplicativos de área de trabalho ou web que acessam o Microsoft Graph ou de uma web API com nenhum utilizador (com a identidade da aplicação).

Aplicação de cliente | Plataforma | Fluxo/concessão | Chama uma API ASP.NET ou Web ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- 
Aplicação de daemon (consola)          | ![.NET](media/sample-v2-code/logo_NETframework.png) | Credenciais de cliente com o segredo da aplicação ou certificado | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Aplicação de daemon (consola)         | ![.NET](media/sample-v2-code/logo_NETcore.png) | Credenciais de cliente com certificado| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Aplicação ASP.NET Web  | ![.NET](media/sample-v2-code/logo_NETframework.png) | Credenciais de cliente | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>APIs da Web

### <a name="web-api-protected-by-azure-active-directory"></a>API Web protegida pelo Azure Active Directory

O exemplo a seguir mostra como proteger uma API com o Azure AD da web de node. js.

Nas secções anteriores deste artigo, também pode encontrar outros exemplos que ilustram uma aplicação de cliente **chamar** um ASP.NET ou ASP.NET Core **Web API**. Estes exemplos não são novamente mencionados nesta secção, mas vai encontrá-las na última coluna das tabelas acima ou abaixo

Plataforma | Sample
 -------- | -------------------
![PHP](media/sample-v2-code/logo_nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi)

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>API de Web chamada Microsoft Graph ou outra API Web

Os exemplos seguintes demonstram uma web API, que chama a API web de outra. O segundo exemplo mostra como lidar com acesso condicional.

 Plataforma |  Chamadas Microsoft Graph | Chama outro ASP.NET ou API Web do ASP.NET Core 2.0
 -------- |  --------------------- | -------------------------
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="other-microsoft-graph-samples"></a>Outros exemplos do Microsoft Graph

Para exemplos e tutoriais que demonstram os diferentes padrões de uso para a Graph API da Microsoft, incluindo a autenticação com o Azure AD, consulte [exemplos de Comunidade do Microsoft Graph e tutoriais](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Consulte também

[Guia para programadores do Azure Active Directory](v1-overview.md)

[Bibliotecas de autenticação do Active Directory do Azure](active-directory-authentication-libraries.md)

[Azure AD Graph API conceitual e de referência](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteca de auxiliar de API do Graph do Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
