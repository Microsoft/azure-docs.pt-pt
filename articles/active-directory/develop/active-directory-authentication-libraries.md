---
title: Azure Active Directory bibliotecas de autenticação | Microsoft Docs
description: A ADAL (biblioteca de autenticação do Azure AD) permite que os desenvolvedores de aplicativos cliente autentiquem facilmente os usuários em nuvem ou no local Active Directory (AD) e, em seguida, obtenham tokens de acesso para proteger chamadas de API.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53db2ba94167168b8bcb2da6c9ee4299a7e784e8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374180"
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory bibliotecas de autenticação

A ADAL (biblioteca de autenticação de Azure Active Directory) v 1.0 permite que os desenvolvedores de aplicativos autentiquem usuários em nuvem ou Active Directory local (AD) e obtenham tokens para proteger chamadas de API. A ADAL facilita a autenticação para desenvolvedores por meio de recursos como:

- Cache de token configurável que armazena tokens de acesso e tokens de atualização
- Atualização automática de token quando um token de acesso expira e um token de atualização está disponível
- Suporte para chamadas de método assíncrono

> [!NOTE]
> Procurando as bibliotecas do Azure AD v 2.0 (MSAL)? Faça check-out do [Guia de biblioteca do MSAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de cliente com suporte da Microsoft

| Plataforma | Biblioteca | Transferir | Código-fonte | Exemplo | Referência
| --- | --- | --- | --- | --- | --- |
| Cliente .NET, Windows Store, UWP, Xamarin iOS e Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplicativo de desktop](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referência](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory?view=azure-dotnet) |
| Cliente .NET, Windows Store, Windows Phone 8,1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Aplicativo de desktop](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL. js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Aplicação de página única](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[aplicativo iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referência](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplicativo Android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[NPM](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Aplicação Web Node.Js](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Referência](https://docs.microsoft.com/javascript/api/adal-node/?view=azure-node-latest) |
| Java |ADAL4J |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Aplicação Web Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Referência](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Aplicativo Web Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Referência](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Bibliotecas de servidores com suporte da Microsoft

| Plataforma | Biblioteca | Transferir | Código-fonte | Exemplo | Referência
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN para AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[Aplicativo MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN para OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Aplicação Web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN para WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[Aplicativo Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Extensões de protocolo de identidade para .NET 4,5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Manipulador de JWT para .NET 4,5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Passaporte do Azure AD |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [API Web](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Cenários

Aqui estão três cenários comuns para usar a ADAL em um cliente que acessa um recurso remoto:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Autenticando usuários de um aplicativo cliente nativo em execução em um dispositivo

Nesse cenário, um desenvolvedor tem um cliente móvel ou um aplicativo de área de trabalho que precisa acessar um recurso remoto, como uma API da Web. A API da Web não permite chamadas anônimas e deve ser chamada no contexto de um usuário autenticado. A API Web é pré-configurada para confiar em tokens de acesso emitidos por um locatário específico do Azure AD. O Azure AD é pré-configurado para emitir tokens de acesso para esse recurso. Para invocar a API Web do cliente, o desenvolvedor usa a ADAL para facilitar a autenticação com o Azure AD. A maneira mais segura de usar a ADAL é fazer com que ela processe a interface do usuário para coletar as credenciais do usuário (renderizado como janela do navegador).

A ADAL facilita a autenticação do usuário, obtém um token de acesso e o token de atualização do Azure AD e, em seguida, chama a API da Web usando o token de acesso.

Para obter um exemplo de código que demonstra esse cenário usando a autenticação do Azure AD, consulte [aplicativo nativo do WPF de cliente para API da Web](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Autenticando um aplicativo cliente confidencial em execução em um servidor Web

Nesse cenário, um desenvolvedor tem um aplicativo em execução em um servidor que precisa acessar um recurso remoto, como uma API da Web. A API da Web não permite chamadas anônimas, portanto, ela deve ser chamada de um serviço autorizado. A API Web é pré-configurada para confiar em tokens de acesso emitidos por um locatário específico do Azure AD. O Azure AD é pré-configurado para emitir tokens de acesso para esse recurso para um serviço com credenciais de cliente (ID do cliente e segredo). A ADAL facilita a autenticação do serviço com o Azure AD retornando um token de acesso que pode ser usado para chamar a API da Web. A ADAL também lida com o gerenciamento do tempo de vida do token de acesso armazenando em cache e renovando-o conforme necessário. Para obter um exemplo de código que demonstra esse cenário, consulte [aplicativo de console de daemon para API da Web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Autenticação de um aplicativo cliente confidencial em execução em um servidor, em nome de um usuário

Nesse cenário, um desenvolvedor tem um aplicativo Web em execução em um servidor que precisa acessar um recurso remoto, como uma API da Web. A API da Web não permite chamadas anônimas, portanto, ela deve ser chamada de um serviço autorizado em nome de um usuário autenticado. A API Web é pré-configurada para confiar em tokens de acesso emitidos por um locatário específico do Azure AD, e o Azure AD é pré-configurado para emitir tokens de acesso para esse recurso a um serviço com credenciais de cliente. Depois que o usuário é autenticado no aplicativo Web, o aplicativo pode obter um código de autorização para o usuário do Azure AD. O aplicativo Web pode usar a ADAL para obter um token de acesso e atualizar o token em nome de um usuário usando o código de autorização e as credenciais do cliente associadas ao aplicativo do Azure AD. Depois que o aplicativo Web estiver em posse do token de acesso, ele poderá chamar a API da Web até que o token expire. Quando o token expira, o aplicativo Web pode usar a ADAL para obter um novo token de acesso usando o token de atualização que foi recebido anteriormente. Para obter um exemplo de código que demonstra esse cenário, consulte [Native Client to Web API to Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Consulte também

- [O guia do desenvolvedor do Azure Active Directory](v1-overview.md)
- [Cenários de autenticação do Azure Active Directory](v1-authentication-scenarios.md)
- [Exemplos de código Azure Active Directory](sample-v1-code.md)
