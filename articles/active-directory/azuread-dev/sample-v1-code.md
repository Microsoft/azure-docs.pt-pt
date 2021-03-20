---
title: Amostras de código para Azure Ative Directory v1.0 | Microsoft Docs
description: Fornece um índice de amostras de código Azure Ative (v1.0 endpoint), organizadas por cenário.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: sample
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 662a0f53a5d5e8e02ef3b29640009fd07bf4f1d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652149"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Amostras de código do Azure Ative Directory (ponto final v1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Pode utilizar o Microsoft Azure Ative Directory (Azure AD) para adicionar autenticação e autorização às suas aplicações web e APIs web.

Esta secção fornece links para amostras que pode usar para saber mais sobre o ponto final Azure AD v1.0. Estas amostras mostram-lhe como é feito juntamente com os cortes de código que pode usar nas suas aplicações. Na página de amostras de código, você encontrará tópicos detalhados de leitura-me que ajudam com requisitos, instalação e configuração. E o código é comentado para ajudá-lo a entender as secções críticas.

> [!NOTE]
> Se estiver interessado em amostras de código Azure AD V2, consulte [por cenário amostras de código v2.0](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Para compreender o cenário básico de cada tipo de amostra, consulte [os cenários de autenticação para Azure AD](v1-authentication-scenarios.md).

Também pode contribuir para as nossas amostras no GitHub. Para saber como, consulte [as amostras e documentação do Microsoft Azure Ative Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplicações de uma página única

Esta amostra mostra como escrever uma aplicação de uma página segura com Azure AD.

| Plataforma | Chama a sua própria API | Chama outra API Web |
|--|--|--|
| ![Esta imagem mostra o logótipo JavaScript](media/sample-v2-code/logo-js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
| ![Esta imagem mostra o logótipo Angular JS](media/sample-v2-code/logo-angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi) |

## <a name="web-applications"></a>Aplicações Web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Aplicações Web assinando em utilizadores, chamando Microsoft Graph, ou uma API Web com a identidade do utilizador

As seguintes amostras ilustram as aplicações web que assinam os utilizadores. Algumas destas aplicações também chamam o Microsoft Graph ou a sua própria API Web, em nome do utilizador inscrito.

| Plataforma | Apenas sinais nos utilizadores | Chama o Microsoft Graph | Chama outra ASP.NET ou ASP.NET Core 2.0 Web API |
|--|--|--|--|
| ![Esta imagem mostra o logotipo do Núcleo ASP.NET](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(Gráfico AAD) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore) |
| ![Esta imagem mostra o logótipo do Quadro de ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (Gráfico AAD) |
| ![Esta imagem mostra o logótipo python](media/sample-v2-code/logo-python.png) |  | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |
| ![Esta imagem mostra o log de Java](media/sample-v2-code/logo-java.png) |  | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect) |
| ![Esta imagem mostra o logótipo PHP](media/sample-v2-code/logo-php.png) |  | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web) |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Aplicações web que demonstram controlo de acesso baseado em funções (autorização)

As amostras que se seguem mostram como implementar o controlo de acesso baseado em funções (RBAC). O RBAC é utilizado para restringir as permissões de certas funcionalidades numa aplicação web a determinados utilizadores. Os utilizadores são autorizados dependendo se pertencem a um **grupo AD Azure** ou têm uma determinada **função de** aplicação .

| Plataforma | Sample |
|--|--|
| ![Esta imagem mostra o logótipo do Quadro de ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Uma aplicação web .NET 4.5 MVC que utiliza **funções** AD Azure para autorização |

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Aplicações de clientes públicos e desktop e móveis que ligam para o Microsoft Graph ou para uma API Web

As seguintes amostras ilustram as aplicações do cliente público (aplicações desktop/mobile) que acedem ao Microsoft Graph ou a uma API web em nome de um utilizador. Dependendo dos dispositivos e plataformas, as aplicações podem inscrever-se nos utilizadores de diferentes formas (fluxos/subvenções):

- Interativamente
- Silenciosamente (com autenticação integrada do Windows no Windows, ou nome de utilizador/palavra-passe)
- Delegando o acesso interativo a outro dispositivo (fluxo de código do dispositivo utilizado em dispositivos que não fornecem controlos web)

Aplicação ao cliente | Plataforma | Fluxo/Subvenção | Chama o Microsoft Graph | Chama uma API web ASP.NET ou ASP.NET Core 2.x
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)           | ![Esta imagem mostra o logótipo .NET/C#](media/sample-v2-code/logo-net.png)  | Interativo | Parte do [dotnet-nativo-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-nativo-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validação](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobile (UWP)            | .![Esta imagem mostra o .NET/C#/UWP](media/sample-v2-code/logo-windows.png)   | Interativo | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Esta amostra utiliza [WAM,](/windows/uwp/security/web-account-manager)não [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (aplicação UWP usando ADAL.NET para chamar um único inquilino Web API) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (aplicação UWP usando ADAL.NET para chamar uma API Web multi-arrendatário)|
Mobile (Android, iOS, UWP)   | ![Esta imagem mostra o .NET/C# (Xamarin)](media/sample-v2-code/logo-xamarin.png) | Interativo | [dotnet-nativo-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobile (Android)           | ![Esta imagem mostra o logótipo do Android](media/sample-v2-code/logo-android.png) | Interativo |   [androide](https://github.com/Azure-Samples/active-directory-android) |
Mobile (iOS)           | ![Esta imagem mostra iOS / Objetivo C ou Swift](media/sample-v2-code/logo-ios.png) | Interativo |   [nativosClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Desktop (Consola)          | ![Esta imagem mostra o logótipo .NET/C#](media/sample-v2-code/logo-net.png) | Nome de utilizador / Senha </p>  Autenticação Integrada do Windows. | | [dotnet-nativo-sem cabeça](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Desktop (Consola)          | ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo-java.png) | Nome de utilizador / Senha | | [java-nativo-sem cabeça](https://github.com/Azure-Samples/active-directory-java-native-headless)
Desktop (Consola)           | ![Esta imagem mostra o logótipo .NET Core/C#](media/sample-v2-code/logo-netcore.png) | Fluxo de código do dispositivo | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Aplicações Daemon (acedendo a APIs web com a identidade da aplicação)

As amostras que se seguem mostram aplicações de desktop ou web que acedem ao Microsoft Graph ou a uma API web sem utilizador (com a identidade da aplicação).

Aplicação ao cliente | Plataforma | Fluxo/Subvenção | Chama uma API web ASP.NET ou ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- 
App Daemon (Consola)          | ![Esta imagem mostra o logótipo .NET Framework](media/sample-v2-code/logo-netframework.png) | Credenciais de cliente com app secret ou certificado | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credencial](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
App Daemon (Consola)         | ![Esta imagem mostra o logótipo .NET Core](media/sample-v2-code/logo-netcore.png) | Credenciais de cliente com certificado| [dotnetcore-daemon-certificate-credencial](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET Web App  | ![Esta imagem mostra o logótipo .NET Framework](media/sample-v2-code/logo-netframework.png) | Credenciais de cliente | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>APIs da Web

### <a name="web-api-protected-by-azure-active-directory"></a>API web protegida pelo Azure Ative Directory

A amostra que se segue mostra como proteger uma API web node.js com Azure AD.

Nas secções anteriores deste artigo, também pode encontrar outras amostras que ilustram uma aplicação do cliente **que ASP.NET** ou ASP.NET Core **Web API**. Estas amostras não são mencionadas novamente nesta secção, mas você vai encontrá-las na última coluna das tabelas acima ou abaixo

| Plataforma | Sample |
|--------|-------------------|
| ![Esta imagem mostra o logótipo Node.js](media/sample-v2-code/logo-nodejs.png)  | [nó-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Web API chamando Microsoft Graph ou outra API Web

As seguintes amostras demonstram uma API web que chama outra API web. A segunda amostra mostra como lidar com o Acesso Condicional.

| Plataforma |  Chama o Microsoft Graph | Chama outra ASP.NET ou ASP.NET Core 2.0 Web API |
| -------- |  --------------------- | ------------------------- |
| ![Esta imagem mostra o logótipo do Quadro de ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Outras amostras do Microsoft Graph

Para amostras e tutoriais que demonstrem diferentes padrões de utilização para a API do Gráfico microsoft, incluindo a autenticação com AZure AD, consulte [as amostras comunitárias do Microsoft Graph & Tutoriais](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Ver também

- [Guia do Programador de Diretórios Azure Ative](v1-overview.md)
- [Bibliotecas de autenticação de diretório ativo Azure](active-directory-authentication-libraries.md)
- [Microsoft Graph API conceptual e referência](/graph/use-the-api)