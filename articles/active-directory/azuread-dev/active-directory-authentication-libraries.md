---
title: Bibliotecas de autenticação de diretório ativo Azure | Microsoft Docs
description: A Azure AD Authentication Library (ADAL) permite que os desenvolvedores de aplicações do cliente autuçam facilmente os utilizadores para cloud ou on-in Ative Directy (AD) e, em seguida, obter fichas de acesso para garantir chamadas API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: reference
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7b6ffd885e1be2dd59cea87cacd6e5fd5e0f8f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861175"
---
# <a name="azure-active-directory-authentication-libraries"></a>Bibliotecas de Autenticação do Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A Azure Ative Directory Authentication Library (ADAL) v1.0 permite que os desenvolvedores de aplicações autentiquem os utilizadores para cloud ou no local Ative Directy (AD), e obter fichas para garantir chamadas API. O ADAL facilita a autenticação para os desenvolvedores através de funcionalidades como:

- Cache de token configurável que armazena tokens de acesso e tokens refrescantes
- Atualização automática de token quando um token de acesso expira e um token de atualização está disponível
- Apoio a chamadas de método assíncronos

> [!NOTE]
> Procura as bibliotecas Azure AD v2.0 (MSAL)? Check-out o [guia da biblioteca MSAL](../develop/reference-v2-libraries.md).
>
>

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de clientes suportadas pela Microsoft

| Plataforma | Biblioteca | Download | Código Fonte | Sample | Referência
| --- | --- | --- | --- | --- | --- |
| .NET Cliente, Windows Store, UWP, Xamarin iOS e Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplicação de ambiente de trabalho](../develop/quickstart-v2-windows-desktop.md) |[Referência](/dotnet/api/microsoft.identitymodel.clients.activedirectory) |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Aplicação de página única](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[aplicativo iOS](../develop/quickstart-v2-ios.md) | [Referência](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplicativo Android](../develop/quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Aplicação Web Node.Js](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Referência](/javascript/api/overview/azure/activedirectory) |
| Java |ADAL4J |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Aplicação Web Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Referência](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Aplicativo web python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Referência](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Bibliotecas de servidores suportadas pela Microsoft

| Plataforma | Biblioteca | Download | Código Fonte | Sample | Referência
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN para AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[Aplicativo MVC](../develop/quickstart-v2-aspnet-webapp.md) | |
| .NET |OWIN para OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Aplicação Web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN para WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[Aplicativo MVC Web](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Extensões do Protocolo de Identidade para .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |JWT Handler para .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Passaporte Azure AD |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [API Web](../develop/authentication-flows-app-scenarios.md)| |

## <a name="scenarios"></a>Cenários

Aqui estão três cenários comuns para a utilização do ADAL num cliente que acede a um recurso remoto:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Autenticação de utilizadores de uma aplicação de cliente nativo em execução num dispositivo

Neste cenário, um desenvolvedor tem um cliente móvel ou aplicação de desktop que precisa de aceder a um recurso remoto, como uma API web. A API web não permite chamadas anónimas e deve ser chamada no contexto de um utilizador autenticado. A API web está pré-configurada para confiar em fichas de acesso emitidas por um inquilino específico da AD Azure. A Azure AD está pré-configurada para emitir fichas de acesso para esse recurso. Para invocar a API web do cliente, o desenvolvedor utiliza a ADAL para facilitar a autenticação com Azure AD. A forma mais segura de usar o ADAL é fazê-lo tornar a interface do utilizador para recolher credenciais de utilizador (renderizada como janela do navegador).

A ADAL facilita a autenticação do utilizador, obtém um token de acesso e atualização do token a partir da Azure AD e, em seguida, ligue para a API web usando o token de acesso.

Para obter uma amostra de código que demonstre este cenário utilizando a autenticação para Azure AD, consulte [a Aplicação WPF do cliente nativo para a API Web.](https://github.com/azureadsamples/nativeclient-dotnet)

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Autenticação de uma aplicação de cliente confidencial em execução num servidor web

Neste cenário, um desenvolvedor tem uma aplicação em execução num servidor que precisa de aceder a um recurso remoto, como uma API web. A API web não permite chamadas anónimas, pelo que deve ser chamada de um serviço autorizado. A API web está pré-configurada para confiar em fichas de acesso emitidas por um inquilino específico da AD Azure. A Azure AD está pré-configurada para emitir fichas de acesso para esse recurso a um serviço com credenciais de cliente (ID do cliente e segredo). A ADAL facilita a autenticação do serviço com a Azure AD devolvendo um token de acesso que pode ser usado para chamar a API web. A ADAL também lida com a gestão da vida útil do token de acesso, caching-lo e renová-lo conforme necessário. Para obter uma amostra de código que demonstre este cenário, consulte [a aplicação da consola Daemon para a API Web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Autenticação de uma aplicação de cliente confidencial em execução num servidor, em nome de um utilizador

Neste cenário, um desenvolvedor tem uma aplicação web em execução num servidor que precisa de aceder a um recurso remoto, como uma API web. A API web não permite chamadas anónimas, pelo que deve ser chamada de um serviço autorizado em nome de um utilizador autenticado. A API web está pré-configurada para confiar em fichas de acesso emitidas por um inquilino específico da AZure AD, e a Azure AD está pré-configurada para emitir tokens de acesso para esse recurso a um serviço com credenciais de cliente. Uma vez que o utilizador seja autenticado na aplicação web, a aplicação pode obter um código de autorização para o utilizador a partir de Azure AD. A aplicação web pode então usar a ADAL para obter um token de acesso e atualização token em nome de um utilizador usando o código de autorização e credenciais de cliente associadas à aplicação a partir de Azure AD. Uma vez que a aplicação web esteja na posse do token de acesso, pode ligar para a API web até que o token expire. Quando o token expirar, a aplicação web pode usar a ADAL para obter um novo token de acesso usando o token de atualização que foi previamente recebido. Para obter uma amostra de código que demonstre este cenário, consulte [o cliente nativo da Web API para a Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Consulte também

- [O guia do programador do Azure Ative Directory](v1-overview.md)
- [Cenários de autenticação para diretório Azure Ative](v1-authentication-scenarios.md)
- [Amostras de código do Diretório Ativo Azure](sample-v1-code.md)
