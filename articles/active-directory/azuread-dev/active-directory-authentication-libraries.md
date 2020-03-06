---
title: Bibliotecas de Autenticação de Diretório Sinuoso Azure Microsoft Docs
description: A Biblioteca de Autenticação AD Azure (ADAL) permite que os desenvolvedores de aplicações de clientes autentiquem facilmente os utilizadores à cloud ou no local Ative Directory (AD) e, em seguida, obtenham acesso a fichas para garantir chamadas API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: fd880c98878f1f778047cd2d60b115b857ad0809
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377464"
---
# <a name="azure-active-directory-authentication-libraries"></a>Bibliotecas de autenticação de diretório ativo Azure

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A Biblioteca de Autenticação de Diretórios Ativos Azure (ADAL) v1.0 permite que os desenvolvedores de aplicações autentiquem os utilizadores à cloud ou no local Ative Directory (AD), e obtenham fichas para garantir chamadas API. A ADAL facilita a autenticação para os desenvolvedores através de funcionalidades como:

- Cache ficha configurável que armazena fichas de acesso e fichas de atualização
- Atualização automática de token quando um token de acesso expira e um token de atualização está disponível
- Apoio a chamadas de métodos assíncronos

> [!NOTE]
> À procura das bibliotecas Azure AD v2.0 (MSAL)? Confira o guia da [biblioteca MSAL.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)
>
>

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de clientes suportadas pela Microsoft

| Plataforma | Biblioteca | Transferência | Código Fonte | Sample | Referência
| --- | --- | --- | --- | --- | --- |
| .NET Client, Windows Store, UWP, Xamarin iOS e Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplicativo de ambiente de trabalho](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referência](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory?view=azure-dotnet) |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Aplicação de página única](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[aplicativo iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referência](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplicativo Android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Aplicação Web Node.Js](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Referência](https://docs.microsoft.com/javascript/api/overview/azure/activedirectory) |
| Java |ADAL4J |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Aplicação Web Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Referência](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[App web Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Referência](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Bibliotecas de servidores suportadas pela Microsoft

| Plataforma | Biblioteca | Transferência | Código Fonte | Sample | Referência
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN para AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[MVC App](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN para OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Aplicação Web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN para A WS-Federação |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[Aplicação Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Extensões do Protocolo de Identidade para .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Manipulador JWT para .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Passaporte Azure AD |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [API Web](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Cenários

Aqui estão três cenários comuns para a utilização da ADAL num cliente que acede a um recurso remoto:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Autenticação de utilizadores de uma aplicação de cliente nativo em execução num dispositivo

Neste cenário, um desenvolvedor tem um cliente móvel ou aplicação de desktop que precisa de aceder a um recurso remoto, como uma API web. A API web não permite chamadas anónimas e deve ser chamada no contexto de um utilizador autenticado. A Web API é pré-configurada para confiar em fichas de acesso emitidas por um inquilino azure específico. A Azure AD está pré-configurada para emitir fichas de acesso para esse recurso. Para invocar a Web API do cliente, o desenvolvedor utiliza a ADAL para facilitar a autenticação com a Azure AD. A forma mais segura de usar o ADAL é torná-la a interface do utilizador para a recolha de credenciais de utilizador (renderizada como janela do navegador).

A ADAL facilita a autenticação do utilizador, obtém um token de acesso e atualização do Token da Azure AD, e depois liga para a Web API utilizando o token de acesso.

Para obter uma amostra de código que demonstre este cenário utilizando a autenticação para a AD Azure, consulte a [Aplicação WPF do Cliente Nativo para a Web API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Autenticar uma aplicação de cliente confidencial em execução num servidor web

Neste cenário, um desenvolvedor tem uma aplicação em execução num servidor que precisa de aceder a um recurso remoto, como uma API web. A API web não permite chamadas anónimas, pelo que deve ser chamada de um serviço autorizado. A Web API é pré-configurada para confiar em fichas de acesso emitidas por um inquilino azure específico. A Azure AD está pré-configurada para emitir fichas de acesso para esse recurso a um serviço com credenciais de cliente (ID do cliente e segredo). A ADAL facilita a autenticação do serviço com a Azure AD devolvendo um sinal de acesso que pode ser usado para chamar a Web API. A ADAL também lida com a gestão da vida útil do token de acesso, apertando-o e renovando-o conforme necessário. Para obter uma amostra de código que demonstre este cenário, consulte a Aplicação da [consola Daemon para a Web API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Autenticar uma aplicação de cliente confidencial em funcionamento num servidor, em nome de um utilizador

Neste cenário, um desenvolvedor tem uma aplicação web a funcionar num servidor que precisa de aceder a um recurso remoto, como uma API web. A API web não permite chamadas anónimas, pelo que deve ser chamada de um serviço autorizado em nome de um utilizador autenticado. A Web API está pré-configurada para confiar em fichas de acesso emitidas por um inquilino específico da AD Azure, e a Azure AD está pré-configurada para emitir fichas de acesso para esse recurso a um serviço com credenciais de cliente. Uma vez autenticado o utilizador na aplicação web, a aplicação pode obter um código de autorização para o utilizador a partir do Azure AD. A aplicação web pode então usar a ADAL para obter um token de acesso e um sinal de atualização em nome de um utilizador usando o código de autorização e credenciais de cliente associadas à aplicação da Azure AD. Uma vez que a aplicação web esteja na posse do token de acesso, pode ligar para a Web API até que o token expire. Quando o token expirar, a aplicação web pode usar a ADAL para obter um novo sinal de acesso utilizando o token de atualização que foi previamente recebido. Para obter uma amostra de código que demonstre este cenário, consulte o [cliente nativo da Web API para a Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Veja Também

- [O guia do desenvolvedor do Diretório Ativo Azure](v1-overview.md)
- [Cenários de autenticação para o diretório Azure Ative](v1-authentication-scenarios.md)
- [Amostras de código de diretório ativo Azure](sample-v1-code.md)
