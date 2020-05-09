---
title: Bibliotecas de autenticação da plataforma de identidade Microsoft
description: Bibliotecas de clientes compatíveis e bibliotecas de middleware de servidor, juntamente com ligações relacionadas com bibliotecas, fontes e amostras, para o ponto final da plataforma de identidade da Microsoft.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9e1480ad58390ea8c424bc6032940b4c743210e3
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982072"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Bibliotecas de autenticação da plataforma de identidade Microsoft

O ponto final da [plataforma de identidade da Microsoft](active-directory-v2-compare.md) suporta os protocolos OAuth 2.0 e OpenID Connect 1.0. A Microsoft Authentication Library (MSAL) foi concebida para funcionar com o ponto final da plataforma de identidade da Microsoft. Também pode utilizar bibliotecas de código aberto que suportam OAuth 2.0 e OpenID Connect 1.0.

Recomendamos que utilize bibliotecas escritas por especialistas em domínio sinuoso que sigam uma metodologia de Ciclo de Vida de Desenvolvimento de Segurança (SDL). Tais metodologias incluem [a que a Microsoft segue.][Microsoft-SDL] Se entregar código para os protocolos, deve seguir uma metodologia como o Microsoft SDL. Preste muita atenção às considerações de segurança nas especificações de normas para cada protocolo.

> [!NOTE]
> Está à procura da Biblioteca de Autenticação de Diretórios Ativos Azure (ADAL)? Confira o guia da [biblioteca ADAL.](../azuread-dev/active-directory-authentication-libraries.md)

## <a name="types-of-libraries"></a>Tipos de bibliotecas

O ponto final da plataforma de identidade da Microsoft funciona com dois tipos de bibliotecas:

* **Bibliotecas de clientes**: Clientes e servidores nativos usam bibliotecas de clientes para adquirir fichas de acesso para chamar um recurso como o Microsoft Graph.
* **Bibliotecas de middleware do servidor**: As aplicações web usam bibliotecas de middleware do servidor para iniciar sessão do utilizador. As APIs da Web utilizam bibliotecas de middleware do servidor para validar fichas que são enviadas por clientes nativos ou por outros servidores.

## <a name="library-support"></a>Suporte da biblioteca

As bibliotecas vêm em duas categorias de apoio:

* **Suportado pela Microsoft**: A Microsoft fornece correções para estas bibliotecas e tem feito diligências de sdl nestas bibliotecas.
* **Compatível**: A Microsoft testou estas bibliotecas em cenários básicos e confirmou que trabalham com o ponto final da plataforma de identidade da Microsoft. A Microsoft não fornece correções para estas bibliotecas e não fez uma revisão destas bibliotecas. As questões e pedidos de funcionalidades devem ser direcionados para o projeto de código aberto da biblioteca.

Para obter uma lista de bibliotecas que funcionem com o ponto final da plataforma de identidade da Microsoft, consulte as seguintes secções.

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de clientes apoiadas pela Microsoft

Utilize bibliotecas de autenticação de clientes para adquirir um símbolo para chamar uma API web protegida.

| Plataforma | Biblioteca | Transferência | Código de origem | Sample | Referência | Doc conceptual | Mapa |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Aplicação de página única](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Referência](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/) | [Docs conceptuais](msal-overview.md)| [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
![Angular](media/sample-v2-code/logo_angular.png) | MSAL Angular | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | [Angular SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) | [Referência](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-angular/) | [Docs conceptuais](msal-overview.md) | [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[Nuget](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplicação de ambiente de trabalho](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Docs conceptuais](msal-overview.md) | [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Amostras](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [Ler TheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Amostras](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Referência](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS & macOS | MSAL iOS e macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [aplicação iOS](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [app macOS](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Referência](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Docs conceptuais](msal-overview.md) | |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Repositório central](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplicativo Android](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Docs conceptuais](msal-overview.md) |[Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliotecas middleware de servidor suportadas pela Microsoft

Utilize bibliotecas de middleware para ajudar a proteger aplicações web e APIs web. Aplicações web ou APIs web escritos com ASP.NET ou ASP.NET Core usam as bibliotecas middleware.

| Plataforma | Biblioteca | Transferência | Código Fonte | Sample | Referência
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET Segurança |[Nuget](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[App MVC](quickstart-v2-aspnet-webapp.md) |[referência ASP.NET API](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Extensões de Modelo de Identidade para .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [App MVC](quickstart-v2-aspnet-webapp.md) |[Referência](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Passaporte Azure AD |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Aplicação Web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Bibliotecas suportadas pela Microsoft por OS/ idioma

Em termos de sistemas operativos suportados vs linguagens, o mapeamento é o seguinte:

|             | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.Net (.NET FW, Core, UWP)| ASP.NET Core, MSAL.Net (.NET Core) | ASP.NET Core, MSAL.Net (macOS)       | MSAL.Net (Xamarin.iOS) | MSAL.Net (Xamarin.Android)|
| Swift <br> Objective-C |            |            | [MSAL para iOS e macOS](msal-overview.md) | [MSAL para iOS e macOS](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Nó.Js](media/sample-v2-code/logo_nodejs.png) Node.JS | Passaporte.nó | Passaporte.nó | Passaporte.nó |

Ver também [Cenários por plataformas e idiomas suportados](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Bibliotecas de clientes compatíveis

| Plataforma | Nome da biblioteca | Versão testada | Código de origem | Sample |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Olá.js](https://adodson.com/hello.js/) | Versão 1.13.5 |[Olá.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
|![Rio Vue](media/sample-v2-code/logo_vue.png)|[Vue MSAL](https://github.com/mvertopoulos/vue-msal) | Versão 3.0.3 |[vue-msal](https://github.com/mvertopoulos/vue-msal) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Escriba Java](https://github.com/scribejava/scribejava) | [Versão 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [Escriba Java](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Biblioteca Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | [Versão 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Biblioteca Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Pedidos-OAuthlib](https://github.com/requests/requests-oauthlib) | [Versão 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Pedidos-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-cliente](https://github.com/panva/node-openid-client) | [Versão 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-cliente](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [A Liga PHP oauth2-cliente](https://github.com/thephpleague/oauth2-client) | [Versão 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-cliente](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-oauth2: 1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS, & Android  | [Reagir App Nativa Auth](https://github.com/FormidableLabs/react-native-app-auth) | [Versão 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Reagir App Nativa Auth](https://github.com/FormidableLabs/react-native-app-auth) | |

Para qualquer biblioteca compatível com padrões, pode utilizar o ponto final da plataforma de identidade da Microsoft. É importante saber onde ir para o apoio:

* Para questões e novos pedidos de funcionalidades no código da biblioteca, contacte o proprietário da biblioteca.
* Para problemas e novos pedidos de funcionalidades na implementação do protocolo do lado do serviço, contacte a Microsoft.
* [Preencha um pedido](https://feedback.azure.com/forums/169401-azure-active-directory) de funcionalidade para funcionalidades adicionais que deseja ver no protocolo.
* [Crie um pedido](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) de suporte se encontrar um problema em que o ponto final da plataforma de identidade da Microsoft não esteja em conformidade com o OAuth 2.0 ou o OpenID Connect 1.0.

## <a name="related-content"></a>Conteúdo relacionado

Para obter mais informações sobre o ponto final da plataforma de identidade da Microsoft, consulte a visão geral da [plataforma de identidade da Microsoft.][AAD-App-Model-V2-Overview]

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
