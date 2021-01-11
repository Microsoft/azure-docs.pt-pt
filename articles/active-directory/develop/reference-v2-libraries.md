---
title: Bibliotecas de autenticação de plataformas de identidade da Microsoft
description: Bibliotecas de clientes compatíveis e bibliotecas de middleware de servidor, juntamente com links relacionados com biblioteca, origem e amostra, para o ponto final da plataforma de identidade da Microsoft.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: a7792e989444c87ba80088f8a850cacb4aa0dc3c
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064577"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Bibliotecas de autenticação de plataformas de identidade da Microsoft

O ponto final da [plataforma de identidade da Microsoft](../azuread-dev/azure-ad-endpoint-comparison.md) suporta os protocolos OAuth 2.0 e OpenID Connect 1.0 padrão da indústria. A Microsoft Authentication Library (MSAL) foi concebida para funcionar com o ponto final da plataforma de identidade da Microsoft. Também pode utilizar bibliotecas de código aberto que suportam OAuth 2.0 e OpenID Connect 1.0.

Recomendamos que utilize bibliotecas escritas por peritos em domínio de protocolo que sigam uma metodologia de Ciclo de Vida para o Desenvolvimento de Segurança (SDL). Tais metodologias incluem [a que a Microsoft segue.][Microsoft-SDL] Se entregar código para os protocolos, deve seguir uma metodologia como o Microsoft SDL. Preste muita atenção às considerações de segurança nas especificações de normas de cada protocolo.

> [!NOTE]
> Procura a Biblioteca de Autenticação do Diretório Ativo Azure (ADAL)? Consulte o guia da [biblioteca ADAL.](../azuread-dev/active-directory-authentication-libraries.md)

## <a name="types-of-libraries"></a>Tipos de bibliotecas

O ponto final da plataforma de identidade da Microsoft funciona com dois tipos de bibliotecas:

* **Bibliotecas de clientes**: Clientes nativos e servidores usam bibliotecas de clientes para adquirir fichas de acesso para chamar um recurso como o Microsoft Graph.
* **Bibliotecas de middleware do servidor**: As aplicações web utilizam bibliotecas de middleware do servidor para o início de s início do utilizador. As APIs web utilizam bibliotecas de middleware de servidor para validar fichas que são enviadas por clientes nativos ou por outros servidores.

## <a name="library-support"></a>Suporte à biblioteca

As bibliotecas vêm em duas categorias de suporte:

* **Suportado pela Microsoft**: A Microsoft fornece correções para estas bibliotecas e fez diligências SDL nestas bibliotecas.
* **Compatível**: A Microsoft testou estas bibliotecas em cenários básicos e confirmou que funcionam com o ponto final da plataforma de identidade da Microsoft. A Microsoft não fornece correções para estas bibliotecas e ainda não fez uma revisão destas bibliotecas. As questões e os pedidos de funcionalidade devem ser direcionados para o projeto de código aberto da biblioteca.

Para obter uma lista de bibliotecas que funcionem com o ponto final da plataforma de identidade da Microsoft, consulte as seguintes secções.

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de clientes suportadas pela Microsoft

Utilize bibliotecas de autenticação de clientes para adquirir um símbolo para chamar uma API web protegida.

| Plataforma | Biblioteca | Transferir | Código de origem | Sample | Referência | Doc conceptual | Mapa |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Aplicação de página única](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Referência](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/) | [Docs conceptuais](msal-overview.md)| [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
![Angular](media/sample-v2-code/logo_angular.png) | MSAL Angular | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | [Angular SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) | [Referência](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-angular/) | [Docs conceptuais](msal-overview.md) | [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplicação de ambiente de trabalho](/windows/apps/desktop/) | [MSAL.NET](/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview&preserve-view=true) |[Docs conceptuais](msal-overview.md) | [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Ícone do núcleo .NET](media/sample-v2-code/logo_NETCore.png) | Microsoft Identity Web  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Web) |[GitHub](https://github.com/AzureAD/microsoft-identity-web) | [Amostras](https://aka.ms/ms-id-web/samples) | [Microsoft.Identity.Web](/dotnet/api/microsoft.identity.web?view=azure-dotnet-preview&preserve-view=true) |[Docs conceptuais](https://aka.ms/ms-id-web/conceptual-doc) | [Mapa](https://github.com/AzureAD/microsoft-identity-web/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | Pitão MSAL | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Amostras](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [LerTheDocs](https://msal-python.rtfd.io/) | [Rio Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://search.maven.org/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Amostras](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Referência](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Rio Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS e macOS | MSAL iOS e macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [aplicativo iOS](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [app macOS](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Referência](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Docs conceptuais](msal-overview.md) | |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Repositório central](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplicativo Android](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Docs conceptuais](msal-overview.md) |[Mapa](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliotecas de middleware suportadas pelo microsoft

Utilize bibliotecas de middleware para ajudar a proteger aplicações web e APIs web. Aplicativos web ou APIs web escritos com ASP.NET ou ASP.NET Core use as bibliotecas de middleware.

| Plataforma | Biblioteca | Transferir | Código fonte | Sample | Referência
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | Segurança ASP.NET |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[Aplicativo MVC](quickstart-v2-aspnet-webapp.md) |[referência ASP.NET API](/dotnet/api/?view=aspnetcore-2.0&preserve-view=true) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Extensões do Modelo de Identidade para .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Aplicativo MVC](quickstart-v2-aspnet-webapp.md) |[Referência](/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet&preserve-view=true) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Passaporte Azure AD |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Aplicação Web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Bibliotecas suportadas pela Microsoft por SISTEMA / idioma

Em termos de sistemas operativos suportados vs idiomas, o mapeamento é o seguinte:

| Plataforma    | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.Net (.NET FW, Core, UWP)| ASP.NET Core, MSAL.Net (.NET Core) | ASP.NET Core, MSAL.Net (macOS)       | MSAL.Net (Xamarin.iOS) | MSAL.Net (Xamarin.Android)|
| Swift <br> Objective-C |            |            | [MSAL para iOS e macOS](msal-overview.md) | [MSAL para iOS e macOS](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | Pitão MSAL | Pitão MSAL | Pitão MSAL |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) Node.js | Passport.node | Passport.node | Passport.node |

Ver também [Cenários por plataformas e idiomas suportados](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Bibliotecas de clientes compatíveis

| Plataforma | Nome da biblioteca | Versão testada | Código de origem | Sample |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | Versão 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
|![Vue](media/sample-v2-code/logo_vue.png)|[Vue MSAL](https://github.com/mvertopoulos/vue-msal) | Versão 3.0.3 |[vue-msal](https://github.com/mvertopoulos/vue-msal) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Escriba Java](https://github.com/scribejava/scribejava) | [Versão 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [EscribaJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Biblioteca gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | [Versão 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Biblioteca gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Pedidos-OAuthlib](https://github.com/requests/requests-oauthlib) | [Versão 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Pedidos-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-cliente](https://github.com/panva/node-openid-client) | [Versão 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-cliente](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [O oauth2-cliente da PhP League](https://github.com/thephpleague/oauth2-client) | [Versão 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-cliente](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth2: 1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS, & Android  | [Reagir Auth de Aplicação Nativa](https://github.com/FormidableLabs/react-native-app-auth) | [Versão 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Reagir Auth de Aplicação Nativa](https://github.com/FormidableLabs/react-native-app-auth) | |

Para qualquer biblioteca conforme os padrões, pode utilizar o ponto final da plataforma de identidade da Microsoft. É importante saber onde ir para o apoio:

* Para problemas e novos pedidos de funcionalidades no código da biblioteca, contacte o proprietário da biblioteca.
* Para problemas e novos pedidos de funcionalidades na implementação do protocolo do lado do serviço, contacte a Microsoft.
* [Arquiva um pedido de funcionalidade](https://feedback.azure.com/forums/169401-azure-active-directory) para funcionalidades adicionais que pretende ver no protocolo.
* [Crie um pedido de suporte](../../azure-portal/supportability/how-to-create-azure-support-request.md) se encontrar um problema em que o ponto final da plataforma de identidade da Microsoft não esteja em conformidade com o OAuth 2.0 ou o OpenID Connect 1.0.

## <a name="related-content"></a>Conteúdo relacionado

Para obter mais informações sobre o ponto final da plataforma de identidade da Microsoft, consulte a [visão geral][AAD-App-Model-V2-Overview]da plataforma de identidade da Microsoft .

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: ./tutorial-v2-windows-desktop.md
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
[ServerLib-Net4-Owin-Oidc-Sample]: ./tutorial-v2-asp-webapp.md
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
