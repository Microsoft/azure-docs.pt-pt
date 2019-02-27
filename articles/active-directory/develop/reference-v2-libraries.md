---
title: Bibliotecas de autenticação v2.0 do Active Directory do Azure | Documentos da Microsoft
description: Bibliotecas de cliente compatível e bibliotecas de middleware de servidor e biblioteca relacionada, origem e ligações de exemplos, para o ponto de final de v2.0 do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2018
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf1e7c4acca147020612e6d33d0e72afb4be0865
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869977"
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Bibliotecas de autenticação de v2.0 do Azure Active Directory

O [ponto final de v2.0 do Azure Active Directory (Azure AD)](active-directory-v2-compare.md) suporta os protocolos padrão da indústria de OAuth 2.0 e OpenID Connect 1.0. A Microsoft Authentication Library (MSAL) foi concebida para funcionar com o ponto de final de v2.0 do Azure AD. Também é possível usar bibliotecas de código-fonte aberto que suportem o OAuth 2.0 e OpenID Connect 1.0.

É recomendável que use bibliotecas escritas por especialistas de domínio de protocolo que siga uma metodologia de ciclo de vida de desenvolvimento da segurança (SDL), como [aquele seguido pela Microsoft][Microsoft-SDL]. Se optar por codificar manualmente para os protocolos, deve seguir uma metodologia de como o SDL e o pagamento da Microsoft requer muita atenção às considerações de segurança nas especificações de padrões para cada protocolo.

> [!NOTE]
> Está à procura para a biblioteca de versão 1.0 do Azure AD (ADAL)? Check-Out do [guia de biblioteca ADAL](active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Tipos de bibliotecas

Ponto final de v2.0 do Azure AD funciona com dois tipos de bibliotecas:

* **Bibliotecas de cliente**: Servidores e clientes nativos utilizam bibliotecas de cliente para obter os tokens de acesso para chamar um recurso, como o Microsoft Graph.
* **Bibliotecas de middleware de servidor**: Aplicações Web utilizam bibliotecas de middleware de servidor para o início de sessão do utilizador. As APIs da Web utilizam bibliotecas de middleware de servidor para validar os tokens que são enviados por clientes nativos ou por outros servidores.

## <a name="library-support"></a>Suporte de bibliotecas

Bibliotecas vêm em duas categorias de suporte:

* **Suporte Microsoft**: A Microsoft fornece correções para essas bibliotecas e fez o SDL auditoria detalhada dessas bibliotecas.
* **Compatível**: A Microsoft testou essas bibliotecas em cenários básicos e confirmado que elas funcionam com o ponto final v2.0. A Microsoft não fornece correções para essas bibliotecas e não fez uma revisão dessas bibliotecas. Problemas e pedidos de funcionalidades devem ser direcionados para o projeto de código aberto da biblioteca.

Para obter uma lista de bibliotecas que funcionam com o ponto final v2.0, consulte as secções seguintes neste artigo.

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de cliente suportados por Microsoft

Bibliotecas de autenticação de cliente são utilizadas para adquirir um token para chamar uma API Web protegido

| Plataforma | Biblioteca | Transferência | Código de origem | Sample | Referência | Doc conceitual | Mapa |
| --- | --- | --- | --- | --- | --- | --- | ---|
| ![JavaScript](media/sample-v2-code/logo_js.png) | Msal (pré-visualização) | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  [Aplicação de página única](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  | [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki)|
|![Angular JS](media/sample-v2-code/logo_angular.png) | Angular JS de MSAL | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | Angular(Preview) MSAL | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | A MSAL .NET (pré-visualização) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplicativo de Desktop](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#conceptual-documentation) | [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![iOS / Objective C ou swift](media/sample-v2-code/logo_iOS.png) | A MSAL obj_c (pré-visualização) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [aplicação iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL (pré-visualização) | [Repositório central](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplicação Android](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | | |

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliotecas de middleware de servidor suportados por Microsoft

Bibliotecas de middleware são utilizadas para proteger aplicações Web e Web APIs. Para a aplicação web ou API Web escritos com ASP.NET ou ASP.NET Core, as bibliotecas de middleware são utilizadas pelo ASP.NET / ASP.NET Core

| Plataforma | Biblioteca | Transferência | Código-fonte | Sample | Referência
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | Segurança do ASP.NET |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[Segurança do ASP.NET (GitHub)](https://github.com/aspnet/Security) |[Aplicação MVC](quickstart-v2-aspnet-webapp.md) |[Referência da API do ASP.NET](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Extensões de IdentityModel para .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Aplicação MVC](quickstart-v2-aspnet-webapp.md) |[Referência](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | O Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Aplicação Web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatível

| Plataforma | Nome da biblioteca | Versão testada | Código de origem | Sample |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [Versão 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [O PHP liga Esportiva cliente oauth2](https://github.com/thephpleague/oauth2-client) | [Versão 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1<br />omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
![iOS](media/sample-v2-code/logo_iOS.png) |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Exemplo de aplicação nativa](active-directory-v2-devquickstarts-ios.md) |

Para qualquer biblioteca compatíveis com os padrões, pode utilizar o ponto final v2.0, portanto, é importante saber onde obter suporte.

* Para problemas e pedidos de novas funcionalidades no código de biblioteca, contacte o proprietário de biblioteca.
* Para problemas e pedidos de novas funcionalidades na implementação de protocolo do lado do serviço, contacte a Microsoft.
* [Um pedido de funcionalidade de ficheiros](https://feedback.azure.com/forums/169401-azure-active-directory) para as funcionalidades adicionais que gostaria de ver no protocolo.
* [Criar um pedido de suporte](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) se encontrar um problema em que o ponto de final de v2.0 do Azure AD não está em conformidade com OAuth 2.0 ou OpenID Connect 1.0.

## <a name="related-content"></a>Conteúdo relacionado

Para obter mais informações sobre o ponto de final de v2.0 do Azure AD, consulte a [visão geral v2.0 do modelo de aplicação do Azure AD][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
