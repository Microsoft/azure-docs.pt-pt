---
title: Bibliotecas de autenticação do Microsoft identity platform | Documentos da Microsoft
description: Bibliotecas de cliente compatível e bibliotecas de middleware de servidor, juntamente com a biblioteca relacionada, a origem e ligações de exemplo, para o ponto de final de plataforma de identidade Microsoft.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c3edfd9ef346407529eea1d887efd795e647808
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440819"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Bibliotecas de autenticação do Microsoft identity platform

O [ponto final de plataforma do Microsoft identity](active-directory-v2-compare.md) suporta os protocolos padrão da indústria de OAuth 2.0 e OpenID Connect 1.0. A Microsoft Authentication Library (MSAL) foi concebida para funcionar com o ponto de final de plataforma de identidade Microsoft. Também pode utilizar bibliotecas de código-fonte aberto que suportem o OAuth 2.0 e OpenID Connect 1.0.

Recomendamos que utilize bibliotecas escritas por especialistas de domínio de protocolo que siga uma metodologia de ciclo de vida de desenvolvimento da segurança (SDL). Essas bibliotecas incluem [Microsoft seguinte][Microsoft-SDL]. Se a entregar o código para os protocolos, deve seguir uma metodologia de como o Microsoft SDL. Preste muita atenção para as considerações de segurança nas especificações de padrões para cada protocolo.

> [!NOTE]
> Está à procura para o Azure Active Directory Authentication Library (ADAL)? Veja a [guia de biblioteca ADAL](active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Tipos de bibliotecas

O ponto de extremidade de plataforma de identidade Microsoft funciona com dois tipos de bibliotecas:

* **Bibliotecas de cliente**: Servidores e clientes nativos utilizam bibliotecas de cliente para adquirir os tokens de acesso para chamar um recurso, como o Microsoft Graph.
* **Bibliotecas de middleware de servidor**: Aplicações Web usam bibliotecas de middleware de servidor de início de sessão de utilizador. As APIs da Web utilizam bibliotecas de middleware de servidor para validar os tokens que são enviados por clientes nativos ou por outros servidores.

## <a name="library-support"></a>Suporte de bibliotecas

Bibliotecas vêm em duas categorias de suporte:

* **Suporte Microsoft**: A Microsoft fornece correções para essas bibliotecas e fez o SDL auditoria detalhada dessas bibliotecas.
* **Compatível**: A Microsoft tem testado essas bibliotecas em cenários básicos e tiver confirmado que elas funcionam com o ponto de final de plataforma de identidade Microsoft. A Microsoft não fornecer correções para essas bibliotecas e ainda não fez uma revisão dessas bibliotecas. Problemas e pedidos de funcionalidades devem ser direcionados para o projeto de código aberto da biblioteca.

Para obter uma lista de bibliotecas que funcionam com o ponto de extremidade de plataforma de identidade Microsoft, consulte as secções seguintes.

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de cliente suportados por Microsoft

Utilize bibliotecas de autenticação de cliente para adquirir um token para chamar uma API web protegida.

| Plataforma | Biblioteca | Transferência | Código de origem | Exemplo | Referência | Doc conceitual | Mapa |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Aplicação de página única](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Referência](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core/docs/classes/_useragentapplication_.useragentapplication.html) | [Docs conceituais](msal-overview.md)| [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | Angular JS de MSAL | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL Angular (pré-visualização) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplicativo de Desktop](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#conceptual-documentation) | [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | A MSAL Python (pré-visualização) | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Amostras](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | A MSAL Java (pré-visualização) | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Amostras](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | | | [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| ![iOS / Objective C ou swift](media/sample-v2-code/logo_iOS.png) | A MSAL obj_c (pré-visualização) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [aplicação iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL (pré-visualização) | [Repositório central](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplicação Android](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | | |

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliotecas de middleware de servidor suportados por Microsoft

Utilize bibliotecas de middleware para ajudar a proteger aplicações web e web APIs. Aplicações Web ou APIs escritas com o ASP.NET ou ASP.NET Core web usar as bibliotecas de middleware.

| Plataforma | Biblioteca | Transferência | Código-fonte | Exemplo | Referência
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | Segurança do ASP.NET |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[Aplicação MVC](quickstart-v2-aspnet-webapp.md) |[Referência da API do ASP.NET](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Extensões de IdentityModel para .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Aplicação MVC](quickstart-v2-aspnet-webapp.md) |[Referência](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | O Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Aplicação Web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatível

| Plataforma | Nome da biblioteca | Versão testada | Código de origem | Exemplo |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | Versão 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [Versão 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Biblioteca Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | [Versão 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Biblioteca Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | [Versão 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-client](https://github.com/panva/node-openid-client) | [Versão 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [O PHP liga Esportiva cliente oauth2](https://github.com/thephpleague/oauth2-client) | [Versão 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-oauth2: 1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| ![iOS](media/sample-v2-code/logo_iOS.png) ![Android](media/sample-v2-code/logo_Android.png) | [Autenticação da aplicação nativa do react](https://github.com/FormidableLabs/react-native-app-auth) | [Versão 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Autenticação da aplicação nativa do react](https://github.com/FormidableLabs/react-native-app-auth) | |

Para qualquer biblioteca compatíveis com os padrões, pode utilizar o ponto de extremidade de plataforma de identidade Microsoft. É importante saber onde obter suporte:

* Para problemas e pedidos de novas funcionalidades no código de biblioteca, contacte o proprietário de biblioteca.
* Para problemas e pedidos de novas funcionalidades na implementação de protocolo do lado do serviço, contacte a Microsoft.
* [Um pedido de funcionalidade de ficheiros](https://feedback.azure.com/forums/169401-azure-active-directory) para as funcionalidades adicionais que pretende ver no protocolo.
* [Criar um pedido de suporte](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) se encontrar um problema em que o ponto de extremidade de plataforma de identidade Microsoft não é compatível com OAuth 2.0 ou OpenID Connect 1.0.

## <a name="related-content"></a>Conteúdo relacionado

Para obter mais informações sobre o ponto de extremidade de plataforma de identidade Microsoft, consulte a [descrição geral da plataforma de identidade Microsoft][AAD-App-Model-V2-Overview].

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
